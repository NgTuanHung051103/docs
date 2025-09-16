# UC_GM03: View Game Detail

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
The Admin clicks on a specific game in the list.

### Description
As an Admin, I want to view details of a game (name, type, description, words) so that I can review its information.

### Preconditions
- Game must exist in the system.  

### Postconditions
- Game details are displayed.  

### Normal Sequence/Flow
1. Admin clicks on a game item.  
2. System fetches the game’s details.  
3. System displays the details (including words).  

### Exception Sequence/Flow
- If game not found → `"Game not found"`.  
- If error occurs → `"Failed to load game details"`.  

### Messages
- `"Game details loaded successfully"`

### When These Messages Occur
- On DB error → error message.

### Business Rules

### Diagram Components Overview
- Actors: Admin, System
- Entities: Game, LearningPathItem, GameWord, Word