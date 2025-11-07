type AppConfig struct {  
    Permissions []int     `bson:"permissions"`  
    Policy      AppPolicy `bson:"policy"`  
    Public      bool      `bson:"public"`  
    Active      bool      `bson:"active"`  
}  
  
type AppPolicy struct {  
    Groups        []AppPolicyGroup     `bson:"groups"`  
    Users         []primitive.ObjectID `bson:"users"`  
    ExcludedUsers []primitive.ObjectID `bson:"excluded_users"`  
}  
  
type AppPolicyGroup struct {  
    Roles []primitive.ObjectID `bson:"roles"`  
    Id    primitive.ObjectID   `bson:"id"`  
}  
  
type App struct {  
    UpdatedAt   time.Time          `bson:"updated_at"`  
    CreatedAt   time.Time          `bson:"created_at"`  
    AvatarUrl   string             `bson:"avatar_url"`  
    Name        string             `bson:"name"`  
    Description string             `bson:"description"`  
    Config      AppConfig          `bson:"config"`  
    Id          primitive.ObjectID `bson:"_id,omitempty"`  
}