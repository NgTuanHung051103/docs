# UC_G02: View List of Games

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
The Admin clicks on the **"Games"** option in the management section.

### Description
As an Admin, I want to view a paginated list of games so that I can efficiently browse, search, filter, and manage them.

### Preconditions
- The user must use an Admin account to log into the system.  
- The system must have games data available.  

### Postconditions
- The list of games has been displayed.  

### Normal Sequence/Flow
1. Admin selects **Games** in management section.  
2. System fetches game data with pagination.  
3. System displays games list.  

### Alternative Sequence/Flow
- None.  

### Exception Sequence/Flow
- If no games exist → System shows `"No games available"`.  
- If database error occurs → System shows `"Failed to load games"`.  

### Mockup Design
+-----------------------------------------------------+
| Game Management |
+-----------------------------------------------------+

Name	Type	Status	Learning Path
Wordle	Puzzle	Active	Path A
FlashCard	Memory	Deactive	Path B
...	...	...	...
+-----------------------------------------------------+		
### Error Messages & Validation Messages
- `"game.list.empty"` = "No games available."
- `"game.list.fail"` = "Failed to load game list."

### Messages
- `"game.list.success"` = "Games fetched successfully."

### When These Messages Occur
- On successful fetch → success message.
- On empty list or DB error → error message.

### Business Rules
- Each Game must belong to at least one Learning Path Item.
- Games marked as inactive are still displayed but cannot be played.

### Diagram Components Overview
- Actors: Admin, System
- Entities: Game, LearningPathItem
