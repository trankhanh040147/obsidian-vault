# Code Review: TKPI-3 - API Manage Team

  

**Branch:** `features/TKPI-3_api_manage_team`

**Target:** `develop`

**Reviewer:** Code Review

**Date:** 2025-12-01

  

---

  

## Summary

  

This MR implements team management features including CRUD operations for teams, team membership management, role assignment, and ownership transfer. Overall, the implementation is well-structured with good separation of concerns across controller, service, and database layers.

  

---

  

## Critical Issues 🔴

  

### 1. Pagination Order Bug in `GetListUsersFromTeamFilter`

**File:** `Backend/database/db_team_user.go` (Lines 231-234)

  

```go

limitStage := bson.D{{"$limit", getUsersIn.Limit}}

skipStage := bson.D{{"$skip", skip}}

  

pipeline := mongo.Pipeline{

matchStage,

lookupMembers,

unwindStage,

limitStage, // ❌ WRONG: $limit before $skip

skipStage,

}

```

  

**Problem:** The `$limit` stage is placed before `$skip` in the aggregation pipeline. This means:

- Page 1: Returns first N records ✅

- Page 2+: Returns empty results ❌

  

**Fix:** Swap the order to `skipStage` then `limitStage`:

```go

pipeline := mongo.Pipeline{

matchStage,

lookupMembers,

unwindStage,

skipStage, // ✅ Skip first

limitStage, // ✅ Then limit

}

```

  

---

  

### 2. Same Pagination Bug in `GetListTeamsByUser`

**File:** `Backend/database/db_team_user.go` (Lines 287-295)

  

Same issue as above - `$skip` and `$limit` are in wrong order.

  

---

  

### 3. Missing Transaction Rollback for Team Creation

**File:** `Backend/service/team_service.go` (Lines 50-75)

  

```go

func (teamService *sTeam) CreateTeam(ctx context.Context, ...) error {

// ...

insertedId, err := teamService.db.InsertOne(ctx, teamEntity)

if err != nil {

return err

}

  

err = TeamUser().AddUsers(ctx, domain.AddTeamUserIn{

TeamId: insertedId,

// ...

})

if err != nil {

return err // ❌ Team is created but users aren't added - orphan team!

}

return nil

}

```

  

**Problem:** If `AddUsers` fails after team creation, an orphan team will exist in the database.

  

**Suggestion:** Wrap both operations in a MongoDB transaction:

```go

session, err := client.StartSession()

if err != nil {

return err

}

defer session.EndSession(ctx)

  

_, err = session.WithTransaction(ctx, func(sessCtx mongo.SessionContext) (interface{}, error) {

insertedId, err := teamService.db.InsertOne(sessCtx, teamEntity)

if err != nil {

return nil, err

}

return nil, TeamUser().AddUsers(sessCtx, ...)

})

```

  

---

  

### 4. Admin Can Delete Other Admins

**File:** `Backend/controller/team_controller.go` (Lines 192-223)

  

```go

func (c *controllerTeam) DeleteTeamUser(fiberCtx *fiber.Ctx) error {

// ...

if teamUserRequester.Role != consts.RoleTeamAdmin && teamUserRequester.Role != consts.RoleTeamOwner {

return exception.CodeUserNotHavePermission

}

// ❌ No check if target user is Owner/Admin

err = service.TeamUser().DeleteTeamUser(fiberCtx.Context(), userId, teamId)

}

```

  

**Problem:** An Admin can remove another Admin or even the Owner from the team.

  

**Suggestion:** Add validation to prevent:

- Admins from removing other Admins or Owner

- Only Owner should be able to remove Admins

  

---

  

## High Priority Issues 🟠

  

### 5. Duplicate User in Team Creation Not Properly Handled

**File:** `Backend/database/db_team_user.go` (Lines 127-147)

  

```go

func (teamUserDb *sDbTeamUser) BulkInsertTeamUsers(ctx context.Context, teamUsers []entity.TeamUser) error {

// ...

res, err := teamUserDb.Collection.InsertMany(ctx, teamUserItf, opts)

if err != nil {

if res != nil && len(res.InsertedIDs) > 0 {

return fmt.Errorf("inserted success for %d members", len(res.InsertedIDs)) // ❌ This returns an error message but is confusing

}

return err

}

return nil

}

```

  

**Problem:** When some users already exist in the team (due to unique index), the function returns a confusing error message that sounds like success.

  

**Suggestion:** Return a proper partial success error or handle duplicates before insert:

```go

if err != nil {

if mongo.IsDuplicateKeyError(err) && res != nil && len(res.InsertedIDs) > 0 {

// Log a warning but don't fail - some users were duplicates

return nil

}

return err

}

```

  

---

  

### 6. Owner Can Be Assigned Different Role via AssignRoleUser

**File:** `Backend/controller/team_controller.go` (Lines 269-300)

  

```go

func (c *controllerTeam) AssignRoleUser(fiberCtx *fiber.Ctx) error {

// Validates requester is Owner ✅

// But doesn't check if target user is the Owner ❌

err = service.TeamUser().AssignRoleUser(fiberCtx.Context(), domain.AssignRoleTeamIn{...})

}

```

  

**Problem:** The owner could accidentally demote themselves using this endpoint (even though validation prevents assigning "Owner" role, they can demote themselves to "Admin" or "User").

  

**Suggestion:** Add check to prevent owner from being assigned a role:

```go

if userId == userCtx.UserID {

return exception.CodeCannotModifyOwnRole

}

```

  

---

  

### 7. LeaveTeam Uses Wrong Request DTO

**File:** `Backend/controller/team_controller.go` (Lines 348-372)

  

```go

func (c *controllerTeam) LeaveTeam(fiberCtx *fiber.Ctx) error {

var request domain.TransferOwnerRequest // ❌ Using TransferOwnerRequest instead of LeaveTeamRequest

err := fiberCtx.BodyParser(&request)

```

  

**Problem:** Using `TransferOwnerRequest` which has a `UserId` field that isn't used. Should use `LeaveTeamRequest`.

  

---

  

## Medium Priority Issues 🟡

  

### 8. Missing Error Check in `CreateIndexes`

**File:** `Backend/database/database.go` (Lines 34-63)

  

```go

func CreateIndexes(db *mongo.Database) {

// ...

err := CreateIndex(db.Collection(consts.MongoDBCollectionUser.String()), indexUserNameConfig)

// ❌ `err` is not checked here

  

err = CreateIndex(db.Collection(consts.MongoDBCollectionTeam.String()), indexTeamNameConfig)

// ❌ Again not checked

  

err = CreateIndex(db.Collection(consts.MongoDBCollectionTeamUser.String()), indexTeamUserConfig)

  

if err != nil { // Only checks last error

log.Fatal(err)

}

}

```

  

**Fix:** Check each error or collect all errors:

```go

var errs []error

if err := CreateIndex(...); err != nil {

errs = append(errs, err)

}

// ... repeat for each

if len(errs) > 0 {

log.Fatal(errs)

}

```

  

---

  

### 9. Inconsistent Validation for Team Description

**File:** `Backend/model/domain/team.go`

  

```go

type CreateTeamRequest struct {

Desc string `validate:"omitempty,max=500"` // Max 500 chars

}

  

type UpdateTeamRequest struct {

Desc *string `validate:"omitempty,max=30"` // Max 30 chars ❌

}

```

  

**Problem:** Create allows 500 characters but Update only allows 30.

  

---

  

### 10. No Sorting in List Queries

**Files:** `Backend/database/db_team_user.go`

  

The pagination queries `GetListUsersFromTeamFilter` and `GetListTeamsByUser` don't include a `$sort` stage. This could lead to inconsistent results across pages.

  

**Suggestion:** Add sorting, e.g., by `createdAt`:

```go

sortStage := bson.D{{"$sort", bson.M{"createdAt": -1}}}

```

  

---

  

### 11. Session Validation Not Checked in JWT Middleware

**File:** `Backend/middleware/jwt.go`

  

```go

func JwtMiddleware() fiber.Handler {

return func(c *fiber.Ctx) error {

// ...

claims, err := utils.VerifyJWT(tokenString, configuration.GetPublicKey())

if err != nil {

return err

}

// ❌ claims.SessionToken is stored but never validated against Redis

utils.CtxManage().SetCtx(c, consts.ManageCtxUser, domain.UserCtx{...SessionToken: claims.SessionToken})

return c.Next()

}

}

```

  

**Problem:** The session token in JWT should be validated against Redis to ensure the session is still valid (not revoked). Currently, even after logout, old access tokens are still valid until they expire.

  

**Suggestion:** Add Redis session validation in the middleware.

  

---

  

## Low Priority / Suggestions 🟢

  

### 12. Consider Adding Indexes for Better Query Performance

Add indexes for frequently queried fields:

- `TeamUsers.userId` - for `GetListTeamsByUser`

- `TeamUsers.teamId` - for `GetListUsersFromTeamFilter`

  

---

  

### 13. Magic Numbers Should Be Constants

**File:** `Backend/controller/team_controller.go`

  

```go

page := 1

limit := 10

```

  

**Suggestion:** Define as constants:

```go

const (

DefaultPage = 1

DefaultLimit = 10

MaxLimit = 100

)

```

  

Also consider adding max limit validation to prevent DoS via large limit values.

  

---

  

### 14. Error Messages Could Be More Descriptive

**File:** `Backend/database/db_team_user.go` (Line 210)

  

```go

return nil, fmt.Errorf("fail to transfer owner")

```

  

**Suggestion:** Include more context:

```go

return nil, fmt.Errorf("failed to transfer ownership from user %s to user %s: %w", userRequestId.Hex(), userId.Hex(), err)

```

  

---

  

### 15. Consider Using Soft Delete for Teams

Currently, deleting a team hard-deletes both the team and all team_user records. Consider:

- Adding `deletedAt` field for soft delete

- Keeping audit trail of who deleted and when

  

---

  

### 16. Response Inconsistency

**File:** `Backend/controller/team_controller.go`

  

- `GetTeamById` returns `domain.TeamResponse` directly

- `GetListTeamByUser` returns `[]domain.TeamResponse` directly

- Other endpoints return `exception.CodeSuccess`

  

**Suggestion:** Standardize response format, perhaps wrapping data in a common response structure.

  

---

  

### 17. Missing Unit Tests

No unit tests were included in this MR. Critical business logic should have test coverage:

- Team creation with users

- Permission checks (Admin vs Owner)

- Ownership transfer

- Pagination logic

  

---

  

### 18. API Route Naming Conventions

**File:** `Backend/routes/team.go`

  

```go

teamUserApi.Get("/users-team/:id", teamController.GetListUsersByTeam)

teamUserApi.Get("/user-teams", teamController.GetListTeamByUser)

```

  

**Suggestion:** Consider more RESTful naming:

- `GET /teams/:id/users` instead of `/team-users/users-team/:id`

- `GET /users/me/teams` instead of `/team-users/user-teams`

  

---

  

## Code Quality Observations ✅

  

### Positives:

1. Good separation of concerns (Controller → Service → Database)

2. Proper use of interfaces for dependency injection

3. Input validation using struct tags

4. Proper error handling with custom error codes

5. MongoDB transactions used for `TransferOwner` operation

6. Unique indexes for data integrity

7. Context propagation throughout the call chain

  

---

  

## Summary

  

| Priority | Count |

|----------|-------|

| 🔴 Critical | 4 |

| 🟠 High | 3 |

| 🟡 Medium | 4 |

| 🟢 Low/Suggestions | 7 |

  

**Recommendation:** Please fix the **Critical** and **High** priority issues before merging. The pagination bugs will cause immediate user-facing issues.