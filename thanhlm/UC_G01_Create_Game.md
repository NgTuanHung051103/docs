# UC_G01 Create Game

## Primary Actors
Admin

## Secondary Actors
None

## Trigger
The Admin clicks on the "Create Game" option in the management section.

## Description
As an Admin, I want to create a new game with name, type, description, and words, so that I can add new interactive activities into the system.

## Preconditions
The user must use an Admin account to log into the system.

## Postconditions
A new game is created and stored in the system.
The system displays the following success toast message (MSG15).
If validation fails or an error occurs, the system displays the relevant error message and prevents the game from being created, then displays an error message (MSG16).

## Normal Sequence/Flow
1. The Admin clicks the "Create Game" option in the management section.
2. The system shows the create dialog with fields: Name, Type, Description, and Words.
3. The Admin fills in the form and clicks the "Save" button.
4. The system validates the input fields as follows:
   - If the Name is empty, the system displays the message (MSG29).
   - If the Name has more than 255 characters, the system displays the toast message (MSG30).
   - If the Type is not selected, the system displays the message (MSG52).
   - If the Description has more than 1000 characters, the system displays the toast message (MSG31).
5. If validation fails, the form will not be submitted and the relevant error messages will be displayed.
6. If validation passes, the system starts a database transaction.
7. Within the transaction, the system performs the following operations:
   - Creates the new game record with is_active = false by default
   - Creates GameWord relationships for each selected word
   - Auto-increments and assigns sequence_order for the game
8. If all operations succeed, the system commits the transaction.
9. The system displays a success toast message (MSG15).
10. The dialog is closed.
11. The system refreshes the list of games to include the new game.

## Alternative Sequence/Flow
None

## Exception Sequence/Flow
Step 6, 7, 8: error during create the game: If any operation within the transaction fails (e.g., due to connection errors or database constraints), the system will rollback the transaction and display the following error toast message (MSG16, MSG21, MSG29, MSG30, MSG31).

## Business Rules
All CREATE operations must use database transactions to ensure data consistency.
Game is created with status = deactive by default.
Sequence order is auto-incremented within the transaction.
Related GameWord referencing this Game must be created corresponding to each word saved in the game.
If any part of the creation process fails, the entire operation is rolled back.
