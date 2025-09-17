# UC_G02 View list of games

## Primary Actors
Admin

## Secondary Actors
None

## Trigger
The Admin clicks on the "Games" option in the management section.

## Description
As an Admin, I want to view a list of available games, so that I can efficiently browse and select relevant games.

## Preconditions
The user must use an Admin account to log into the system.

## Postconditions
The Admin can perform search and status change operations on the games list.
The Admin can open the "Create new" dialog to create a new game or open the "Edit" dialog to edit the selected game.

## Normal Sequence/Flow
1. The Admin clicks the Games management section.
2. The system shows a table of games with the following information of the game in each column like name, type, description, status and action column.
3. The Admin can search for the game by name or description using the search text box.
4. The system updates the table to display only games matching the search term.
5. The Admin can filter games by:
   - Status (Active/Deactive) using dropdown filter
   - Type (Puzzle, Memory, etc.) using dropdown filter
   - Learning Path using dropdown filter
6. The system applies the selected filters and updates the table accordingly.
7. The Admin can sort games by clicking column headers:
   - Sort by Name (ascending/descending)
   - Sort by Type (ascending/descending) 
   - Sort by Status (ascending/descending)
   - Sort by Created Date (ascending/descending)
8. The system applies the sort order and refreshes the table.
9. The Admin can navigate through the list of games by clicking on the page numbers at the bottom of the table.
10. The system immediately refreshes the table to show only games in that page.
11. The Admin can also change the number of games displayed per page (10, 25, 50, 100).
12. The system displays the number of games in a page that match the selected number record per page.

## Alternative Sequence/Flow
None

## Exception Sequence/Flow
Step 3,4,5,6,7,8: error while fetching games: If the connection error, the system displays a toast error message (MSG21, MSG22).

## Business Rules
All search, filter, sort, and pagination operations must be performed in a single database query using Sequelize findAndCountAll().
Each Game must belong to at least one Learning Path Item.
Games marked as inactive are displayed with visual indicators but cannot be played.
Filter and sort parameters are preserved during pagination navigation.
Default sort order is by creation date (newest first).
