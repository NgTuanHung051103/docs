# UC_W03 Update Word

## Primary Actors
Admin

## Secondary Actors
None

## Trigger
The Admin clicks the "Edit" icon button of a word in the Word Management section.

## Description
As an Admin, I want to update the details (e.g., word text, image, level, type, note) of an existing word, so that I can correct information, refine the vocabulary system.

## Preconditions
The user must use an Admin account to log into the system.
There must be at least one created word in the system.

## Postconditions
The word has been updated in the database.
The system displays the following success toast message (MSG17).
If the update fails, the system displays an error message (MSG18) or message following error and data is not changed.

## Normal Sequence/Flow
1. The Admin clicks the "Word Management" section.
2. The System displays the list of words with the detailed information and the action column.
3. The Admin clicks the "Edit" button of the desired word in the action column.
4. The System shows the Update Word form with existing information filled in: Word Text, Image, Level, Type, Note.
5. The Admin edits the fields and clicks the "Save" button.
6. The system validates the inputs:
   - If the record does not exist in the system, the system displays the message (MSG27).
   - If the Word Text is empty, the system displays the message (MSG29).
   - If the Word Text has more than 255 characters, the system displays the toast message (MSG30).
   - If the Note has more than 1000 characters, the system displays the toast message (MSG31).
   - If a new Image is uploaded and not selected properly, the system displays the message (MSG50).
   - If the new Image's size is bigger than 5MB, the system displays the message (MSG51).
   - If the new Image type is not allowed (must be jpeg, jpg, png, gif, webp), the system displays the message (MSG55).
   - If the Level is not selected, the system displays the message (MSG52).
   - If the Type is not selected, the system displays the message (MSG53).
   - If the word text already exists (for another word), the system displays the message (MSG54).
7. If validation passes, the system starts a database transaction.
8. Within the transaction, the system performs the following operations:
   - If a new image is provided, validates and uploads it to MinIO using uploadToMinIO(file, "words") helper
   - Generates unique filename for the new uploaded image
   - Removes old image from MinIO if a new image is uploaded successfully
   - Updates the word record with new information
   - Ensures word text uniqueness across the system (excluding current word)
   - Maintains GameWord relationships integrity
9. If all operations succeed, the system commits the transaction.
10. The system displays a success toast message.
11. The system refreshes the list to show the updated word.

## Alternative Sequence/Flow
None

## Exception Sequence/Flow
Step 7, 8, 9: error during updating the word: If any operation within the transaction fails (e.g., due to connection errors, MinIO upload failure, or uniqueness constraint violation), the system will rollback the transaction and display the following error toast message (MSG18, MSG21, MSG27, MSG29, MSG30, MSG31, MSG50, MSG51, MSG52, MSG53, MSG54, MSG55).

## Business Rules
All UPDATE operations must use database transactions to ensure data consistency.
Word text must remain unique across the system after update (excluding the current word being updated).
Updated words maintain their relationships with games through GameWord.
Image upload to MinIO and database record update must be atomic.
Only allowed image types (jpeg, jpg, png, gif, webp) with maximum size 5MB are accepted for updates.
All uploaded files must be validated using validateKidReadingFiles() before processing.
Old images are automatically removed from MinIO when new images are uploaded successfully.
If any part of the update process fails, the entire operation is rolled back.  