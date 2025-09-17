# UC_G05 Delete Game

## Primary Actors
Admin

## Secondary Actors
None

## Trigger
The Admin clicks the "Delete" button for a specific game in the Game Management section.

## Description
As an Admin, I want to deactivate a game, so that I can remove outdated or incorrect content from active use while preserving student progress data.

## Preconditions
The user must use an Admin account to log into the system.
The selected game must exist in the database.

## Postconditions
The game is deactivated (is_active = false) in the database.
The system displays the following success toast message (MSG19).
If the deactivation fails, the system displays an error message (MSG20) and the game status is not changed.

## Normal Sequence/Flow
1. The Admin clicks the "Game Management" section.
2. The System displays the list of games with the detailed information and the action column.
3. The Admin clicks the "Delete" button of the desired game in the action column.
4. The System shows a confirmation dialog: "Are you sure you want to deactivate this game?".
5. The Admin confirms deactivation by clicking "Deactivate".
6. The system validates that the game exists:
   - If the game does not exist in the system, the system displays the message (MSG27).
7. The system checks for student progress:
   - Check if any students have accessed this game through StudentGameProgress or LearningPath.
   - If student progress exists, the system displays warning message (MSG28): "Students have already accessed this game. It will be deactivated but data preserved."
8. If validation passes, the system sets the game's is_active field to false.
9. The system also deactivates related Learning Path Items that reference this game.
10. The system displays a success toast message (MSG19).
11. The system refreshes the list to show the updated games (deactivated games may be hidden or marked as inactive).

## Alternative Sequence/Flow
None

## Exception Sequence/Flow
Step 6, 7, 8, error during deactivating the game: If the game deactivation fails (e.g., due to connection errors or game not found in the system), the system will display the following error toast message (MSG20, MSG21, MSG27, MSG28).

## Business Rules
Games are soft-deleted by setting is_active = false (not permanently removed).
Student progress data must be preserved for tracking purposes.
Related Learning Path Items referencing this game must also be deactivated.
GameWord relationships are maintained but the game becomes inactive.