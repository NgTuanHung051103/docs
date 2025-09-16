# UC_G03: Update Game

## Use Case Details
### Primary Actors
Admin

### Secondary Actors
System

### Trigger
The Admin clicks the **Edit** button for a specific Game.

### Description
As an Admin, I want to update the details of an existing game, so that I can correct or improve its information.

### Preconditions
- Admin must be logged in.
- The selected Game must exist in the database.

### Postconditions
- The Game information is updated and stored in the database.

### Normal Sequence/Flow
1. Admin clicks **Edit** on a specific Game.
2. System displays the current Game details in an editable form.
3. Admin updates one or more fields (name, description, type, status, words).
4. Admin clicks **Save**.
5. System saves the updated Game and shows confirmation.

### Exception Sequence/Flow
- E1: Invalid input (e.g., empty name) → Show `"game.invalid.update"`.
- E2: Game not found → Show `"game.notfound"`.
- E3: Database error → Show `"game.update.fail"`.

### Mockup Design
+---------------------------------+
| Edit Game: Wordle |
+---------------------------------+
| Name: [Wordle_______] |
| Description: [Puzzle Game] |
| Type: [Puzzle v] |
| Status: [Active v] |
| |
| [Cancel] [Save] |
+---------------------------------+

### Error Messages & Validation Messages
- `"game.invalid.update"` = "Game update data is invalid."
- `"game.notfound"` = "Game not found."
- `"game.update.fail"` = "Failed to update game."

### Messages
- `"game.update.success"` = "Game updated successfully."

### When These Messages Occur
- On successful update → success message.
- On invalid input or DB error → error message.

### Business Rules
- Game name must not be empty.
- Status must be either Active or Deactive.

### Diagram Components Overview
- Actors: Admin, System
- Entities: Game, LearningPathItem, GameWord, Word