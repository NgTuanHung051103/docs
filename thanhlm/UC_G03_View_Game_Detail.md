# UC_G03 View Game Detail

## Primary Actors
Admin

## Secondary Actors
None

## Trigger
The Admin clicks on a specific game in the list.

## Description
As an Admin, I want to view details of a game (name, type, description, words) so that I can review its information.

## Preconditions
The user must use an Admin account to log into the system.
Game must exist in the system.

## Postconditions
Game details are displayed with complete information.
The system displays the following success message (MSG15).
If the game is not found or an error occurs, the system displays the relevant error message (MSG16).

## Normal Sequence/Flow
1. The Admin clicks the Games management section.
2. The system displays the list of games.
3. The Admin clicks on a specific game item in the list.
4. The system validates that the game exists:
   - If the game does not exist in the system, the system displays the message (MSG27).
5. If validation passes, the system fetches the game's details from the database.
6. The system displays the details including name, type, description, status, and associated words.
7. The system shows the complete game information to the Admin.

## Alternative Sequence/Flow
None

## Exception Sequence/Flow
Step 4, 5, error during fetching game details: If the game detail loading fails (e.g., due to connection errors or game not found in the system), the system will display the following error toast message (MSG16, MSG21, MSG27).

## Business Rules
Game details must include all associated words through GameWord relationships.
Only existing games can be viewed in detail.