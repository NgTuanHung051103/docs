# UC_W04 Delete Word

## Primary Actors
Admin

## Secondary Actors
None

## Trigger
The Admin clicks the "Delete" button for a specific word in the Word Management section.

## Description
As an Admin, I want to deactivate a word, so that I can remove unused or incorrect vocabulary from active use while preserving learning data.

## Preconditions
The user must use an Admin account to log into the system.
The selected word must exist in the database.

## Postconditions
The word is deactivated (is_active = false) in the database.
The system displays the following success toast message (MSG19).
If the deactivation fails, the system displays an error message (MSG20) and the word status is not changed.

## Normal Sequence/Flow
1. The Admin clicks the "Word Management" section.
2. The System displays the list of words with the detailed information and the action column.
3. The Admin clicks the "Delete" button of the desired word in the action column.
4. The System shows a confirmation dialog: "Are you sure you want to deactivate this word?".
5. The Admin confirms deactivation by clicking "Deactivate".
6. The system validates that the word exists:
   - If the word does not exist in the system, the system displays the message (MSG27).
7. The system checks for student progress:
   - Check if any students have interacted with games containing this word.
   - If student progress exists, the system displays warning message (MSG28): "Students have already learned with this word. It will be deactivated but data preserved."
8. If validation passes, the system sets the word's is_active field to false.
9. The system maintains GameWord relationships but marks the word as inactive.
10. The system displays a success toast message (MSG19).
11. The system refreshes the list to show the updated words (deactivated words may be hidden or marked as inactive).

## Alternative Sequence/Flow
None

## Exception Sequence/Flow
Step 6, 7, 8, error during deactivating the word: If the word deactivation fails (e.g., due to connection errors or word not found in the system), the system will display the following error toast message (MSG20, MSG21, MSG27, MSG28).

## Business Rules
Words are soft-deleted by setting is_active = false (not permanently removed).
Student learning data must be preserved for tracking purposes.
GameWord relationships are maintained but the word becomes inactive.
Deactivated words cannot be used in new games but existing games retain the relationships.  
