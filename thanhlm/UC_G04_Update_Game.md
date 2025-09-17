# UC_G04 Update Game

## Primary Actors
Admin

## Secondary Actors
None

## Trigger
The Admin clicks the "Edit" icon button of a game in the Game Management section.

## Description
As an Admin, I want to update the details (e.g., name, description) of an existing game, so that I can correct information, refine the game system.

## Preconditions
The user must use an Admin account to log into the system.
There must be at least one created game in the system.

## Postconditions
The game has been updated in the database.
The system displays the following success toast message (MSG17).
If the update fails, the system displays an error message (MSG18) or message following error and data is not changed.

## Normal Sequence/Flow
1. The Admin clicks the "Game Management" section.
2. The System displays the list of games with the detailed information and the action column.
3. The Admin clicks the "Edit" button of the desired game in the action column.
4. The System shows the Update Game form with existing information filled in: Name, Description, Type, Status.
5. The Admin edits the fields and clicks the "Save" button.
6. The system validates the inputs:
   - If the record does not exist in the system, the system displays the message (MSG27).
   - If the Name is empty, the system displays the message (MSG29).
   - If the Name has more than 255 characters, the system displays the toast message (MSG30).
   - If the Description has more than 1000 characters, the system displays the toast message (MSG31).
   - If the Type is not selected, the system displays the message (MSG52).
7. If validation passes, the system starts a database transaction.
8. Within the transaction, the system performs the following operations:
   - Updates the game record with new information
   - Updates GameWord relationships if words are modified
   - Maintains data integrity across related tables
9. If all operations succeed, the system commits the transaction.
10. The system displays a success toast message.
11. The system refreshes the list to show the updated game.

## Alternative Sequence/Flow
None

## Exception Sequence/Flow
Step 7, 8, 9: error during updating the game: If any operation within the transaction fails (e.g., due to connection errors or not found game in the system), the system will rollback the transaction and display the following error toast message (MSG18, MSG21, MSG27,...).

## Business Rules
All UPDATE operations must use database transactions to ensure data consistency.
Game name must not be empty.
Status must be either Active or Deactive.
If any part of the update process fails, the entire operation is rolled back.
GameWord relationships must be updated atomically with the game record.