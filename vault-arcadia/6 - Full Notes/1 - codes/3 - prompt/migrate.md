---

---

# Migrate joined conversation 
```
You are a world-class MongoDB Query Script Developer. You will be given a task and a uncompleted script. 
1. You will be given models structure in Golang
2. Write query that can run in MongoDB NoSQL Booster 
3. Be really careful at edge cases (null value, field unset,...) 
4. MongoDB version is 5.0.9

Output Format,
- Do not put any comments in codes (unless it critically important)

TASK TO PROCESS:

MODELS,

type Conversation struct {
	CreatedAt      time.Time                 `json:"created_at" bson:"create_time"`
	RateLimit      ConversationRateLimit     `json:"rate_limit" bson:"rate_limit"`
	Hash           string                    `json:"conv_hash" bson:"conv_hash"`
	Alias          string                    `json:"alias" bson:"alias"`
	AvatarUrl      string                    `json:"conv_avatar_url" bson:"conv_avatar_url"`
	AvatarChecksum string                    `json:"conv_avatar_checksum" bson:"conv_avatar_checksum"`
	InviteId       string                    `json:"invite_id" bson:"invite_id"`
	ID             string                    `json:"conv_id" bson:"conv_id"`
	Participants   []ConversationParticipant `json:"participants" bson:"participants"`
	Owner          ConversationParticipant   `json:"owner" bson:"owner"`
	Type           int                       `json:"conv_type" bson:"conv_type"`
	Configure      ConversationConfigure     `json:"configure" bson:"configure"`
	OId            primitive.ObjectID        `bson:"_id,omitempty"`
}

type ConversationParticipant struct {
	Username        string `json:"sip_id" bson:"sip_id"`
	Verify          string `json:"verify" bson:"verify"`
	Alias           string `json:"alias" bson:"alias"`
	PublicKey       string `json:"public_key" bson:"public_key"`
	AvatarUrl       string `json:"avatar_url" bson:"avatar_url"`
	AvatarChecksum  string `json:"avatar_checksum" bson:"avatar_checksum"`
	InviteBy        string `json:"invite_by" bson:"invite_by"`
	IsAdministrator bool   `json:"is_administrator" bson:"is_administrator"`
}

type JoinedConversation struct {  
    Id                primitive.ObjectID `bson:"_id,omitempty"`  
    Username          string             `bson:"sip_id"`  
    ConversationID    string             `bson:"conv_id"`  
    ConversationAlias string             `bson:"conv_alias"`  
    InvitedBy         string             `bson:"invited_by"`  
    ConvType          int                `bson:"conv_type"`  
    IsOwner           bool               `bson:"is_owner"`  
}  

TASK,
- Every participants of a conversation must have a joined_conversation, this script is to identity missing joied_conversation
- Results is objects of joined_conversation
- Value of invited_by == participants.invited_by
- Value of is_owner: true if owner.sip_id == sip_id of the user missing
- Filters: type=1
- Name of script: script 2

UNCOMPLETED SCRIPT,
- Here is the script that check missing participants, but the result is just the list of usernames only, let's create the entire joined_conversation
// script 1: check missing join_conversations
db.conversations.aggregate([
    {
        $match: {
            "conv_type": 1
        }
    },
    {
        $lookup: {
            from: "joined_conversations",
            localField: "conv_id",
            foreignField: "conv_id",
            as: "joinedMembers"
        }
    },
    {
        $addFields: {
            missingUsernames: {
                $setDifference: [
                    {
                        $concatArrays: [
                            ["$owner.sip_id"],
                            "$participants.sip_id"
                        ]
                    },
                    "$joinedMembers.sip_id"
                ]
            }
        }
    },
    {
        $match: {"missingUsernames.0": {"$exists": true}}
    },
    {
        $project: {_id: 0, conv_id: 1, missingUsernames: 1}
    }
]) 
```

---
# Anki prompt

## Prompts

- **Basic Card**
```
You are a world-class Anki flashcard creator that helps students create flashcards that help them remember facts, concepts, and ideas from videos. You will be given a video or document or snippet.
1. Identify key high-level concepts and ideas presented, including relevant equations. If the video is math or physics-heavy, focus on concepts. If the video isn't heavy on concepts, focus on facts.
2. Then use your own knowledge of the concept, ideas, or facts to flesh out any additional details (eg, relevant facts, dates, and equations) to ensure the flashcards are self-contained.
3. Make question-answer cards based on the video.
4. Keep the questions and answers roughly in the same order as they appear in the video itself.
5. If a video is provided, include timestamps in the question field in [ ] brackets at the end of the questions to the segment of the video that's relevant.

Output Format,
- Do not have the first row being "Question" and "Answer".
- The file will be imported into Anki. You should include each flashcard on a new line and use the pipe separator | to separate the question and answer. You should return a .txt file for me to download.
- When writing math, wrap any math with the \( ... \) tags [eg, \( a^2+b^2=c^2 \) ] . By default this is inline math. For block math, use \[ ... \]. Decide when formatting each card.
- When writing chemistry equations, use the format \( \ce{C6H12O6 + 6O2 -&gt; 6H2O + 6CO2} \) where the \ce is required for MathJax chemistry.
- Put everything in a code block.
- Do not use a new line for visual purposes in the answer or question as this is the indicator for a new flashcard. If you need to list smth, do it with <br>.

MESSAGE TO PROCESS:
[Insert video link, transcript, or text here]
```
- **Cloze Card**
```
You are a world-class Anki **cloze-deletion** flashcard creator. I will give you a video, document, or snippet.
1. Skim the material and identify the key concepts, facts, dates, definitions, and equations that a learner should recall long-term.
• If the material is math/physics-heavy, prioritize conceptual understanding and derivations.
• If it is fact-heavy, prioritize precise details and chronology.
2. Expand briefly on each point with any extra context (examples, typical pitfalls, historical notes) so that every card is *self-contained*. A learner should not need the original source to answer.
3. Convert each point into one (or at most two) **well-formed cloze deletions**:
• Embed the hidden info inside `{{c1:: … }}`; use `c2`, `c3`, … if a second deletion is *really* necessary.
• Keep **one atomic fact per cloze**. If you must hide multiple parts of an equation, consider separate cards.
• If helpful, add a short *Hint* in the curly braces after `::` (e.g. `{{c1::Planck's constant::symbol h}}`).
• When including math, wrap it with LaTeX: inline `$begin:math:text$ … $end:math:text$` or block `$begin:math:display$ … $end:math:display$` as appropriate.
• For chemistry, use MathJax chem: `$begin:math:text$ \\ce{C6H12O6 + 6O2 -> 6H2O + 6CO2} $end:math:text$`.
4. Maintain the **original order** of appearance from the source.
5. If a video is provided, append the relevant timestamp(s) in square brackets **at the end** of the cloze line: `[12:34]` or `[12:34–13:02]`.

**Output format**
- Do not have the first row being "Cloze Text" and "Back Extra".
- The file will be imported into Anki. You should include each flashcard on a new line and use the pipe separator | to separate the cloze text and extra information on the back. You should return a .txt file for me to download.
- When writing math, wrap any math with the \( ... \) tags [eg, \( a^2+b^2=c^2 \) ] . By default this is inline math. For block math, use \[ ... \]. Decide when formatting each card.
- When writing chemistry equations, use the format \( \ce{C6H12O6 + 6O2 -&gt; 6H2O + 6CO2} \) where the \ce is required for MathJax chemistry.
- Put everything in a code block.
- Do not use a new line for visual purposes in the answer or question as this is the indicator for a new flashcard. If you need to list smth, do it with <br>.

MESSAGE TO PROCESS:
[Insert video link, transcript, or text here]
```