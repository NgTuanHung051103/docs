# UC_W02 View list of words

## Primary Actors
Admin

## Secondary Actors
None

## Trigger
The Admin clicks on the "Words" option in the management section.

## Description
As an Admin, I want to view a list of available words, so that I can efficiently browse and select relevant words.

## Preconditions
The user must use an Admin account to log into the system.

## Postconditions
The Admin can perform search and status change operations on the words list.
The Admin can open the "Create new" dialog to create a new word or open the "Edit" dialog to edit the selected word.

## Normal Sequence/Flow
1. The Admin clicks the Words management section.
2. The system shows a table of words with the following information of the word in each column like word text, image, level, type, note and action column.
3. The Admin can search for the word by word text or note using the search text box.
4. The system updates the table to display only words matching the search term.
5. The Admin can filter words by:
   - Status (Active/Deactive) using dropdown filter
   - Level (Beginner, Intermediate, Advanced) using dropdown filter
   - Type (Noun, Verb, Adjective, etc.) using dropdown filter
6. The system applies the selected filters and updates the table accordingly.
7. The Admin can sort words by clicking column headers:
   - Sort by Word Text (ascending/descending)
   - Sort by Level (ascending/descending)
   - Sort by Type (ascending/descending)
   - Sort by Created Date (ascending/descending)
8. The system applies the sort order and refreshes the table.
9. The Admin can navigate through the list of words by clicking on the page numbers at the bottom of the table.
10. The system immediately refreshes the table to show only words in that page.
11. The Admin can also change the number of words displayed per page (10, 25, 50, 100).
12. The system displays the number of words in a page that match the selected number record per page.

## Alternative Sequence/Flow
None

## Exception Sequence/Flow
Step 3,4,5,6,7,8: error while fetching words: If the connection error, the system displays a toast error message (MSG21, MSG22).

## Business Rules
All search, filter, sort, and pagination operations must be performed in a single database query using Sequelize findAndCountAll().
The system must support pagination for word lists.
Admin can search and filter words by text, note, level, and type.
Filter and sort parameters are preserved during pagination navigation.
Default sort order is by word text (alphabetical ascending).
Words marked as inactive are displayed with visual indicators.  
