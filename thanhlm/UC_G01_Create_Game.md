# UC_G01: Create Game

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
The Admin clicks the **"Create Game"** option in the management section.

### Description
As an Admin, I want to create a new game with name, type, description, and words, so that I can add new interactive activities into the system.

### Preconditions
- The user must use an Admin account to log into the system.

### Postconditions
- A new game has been created and stored in the system.

### Normal Sequence/Flow
1. Admin selects **Create Game**.  
2. System displays the create form.  
3. Admin enters game name, type, description, and words.  
4. Admin clicks **Save**.  
5. System validates input and stores the game.  
6. System shows a success message.  

### Alternative Sequence/Flow
- None.  

### Exception Sequence/Flow
- If required fields are missing → System shows `"Please fill out this field"`.  
- If system error occurs → System shows `"Failed to create game"`. 

### Mockup Design

### Error Messages & Validation Messages
- `"game.invalid.name"` = "Game name cannot be empty."
- `"game.invalid.type"` = "Invalid game type."
- `"game.create.fail"` = "Failed to create game."

### Messages
- `"game.create.success"` = "Game created successfully."

### When These Messages Occur
- On successful create → success message.
- On validation fail → error message.

### Business Rules
- Game is created with status = deactive.
- Sequence order is auto-incremented.
- Related GameWord referencing this Game must be create corresponding to each word saved in the game.
### Diagram Components Overview
- Actors: Admin, System
- Entities: Game, LearningPathItem, GameWord, Word
