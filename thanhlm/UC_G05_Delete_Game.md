# UC_G04: Delete Game

## Use Case Details
### Primary Actors
Admin

### Secondary Actors
System

### Trigger
The Admin clicks the **Delete** button for a specific Game.

### Description
As an Admin, I want to delete a game, so that I can remove outdated or incorrect content.

### Preconditions
- Admin must be logged in.
- The selected Game must exist in the database.

### Postconditions
- The Game is permanently deleted from the database.

### Normal Sequence/Flow
1. Admin clicks **Delete** on a specific Game.
2. System asks for confirmation: "Are you sure you want to delete this game?".
3. Admin confirms deletion.
4. System removes the Game from the database.
5. System shows success message.

### Exception Sequence/Flow
- E1: Game not found → Show `"game.notfound"`.
- E2: Database error → Show `"game.delete.fail"`.

### Mockup Design
+-------------------------------------------+
| Confirm Deletion |
+-------------------------------------------+
| Are you sure you want to delete this game?|
| |
| [Cancel] [Delete] |
+-------------------------------------------+

### Error Messages & Validation Messages
- `"game.notfound"` = "Game not found."
- `"game.delete.fail"` = "Failed to delete game."

### Messages
- `"game.delete.success"` = "Game deleted successfully."

### When These Messages Occur
- On successful delete → success message.
- On missing game or DB error → error message.

### Business Rules
- Deleted Games are permanently removed (not soft-deleted).
- Related Learning Path Items referencing this Game must also be removed.
- Related GameWord referencing this Game must also be removed.

### Diagram Components Overview
- Actors: Admin, System
- Entities: Game, LearningPathItem, GameWord