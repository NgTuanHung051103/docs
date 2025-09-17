# UC_W01 Create Word

## Primary Actors
Admin

## Secondary Actors
None

## Trigger
The Admin clicks on the "Create Word" option in the management section.

## Description
As an Admin, I want to create a new word, so that I can expand the vocabulary repository for the learning system.

## Preconditions
The user must use an Admin account to log into the system.

## Postconditions
A new word is created and stored in the system.
The system displays the following success toast message (MSG15).
If validation fails or an error occurs, the system displays the relevant error message and prevents the word from being created, then displays an error message (MSG16).

## Normal Sequence/Flow
1. The Admin clicks the "Word Management" section.
2. The system shows the list of words and action buttons.
3. The Admin clicks the "Create" button.
4. The system shows the create dialog with fields: Word Text, Image, Level, Type, and Note.
5. The Admin fills in the form and clicks the "Save" button.
6. The system validates the input fields as follows:
   - If the Word Text is empty, the system displays the message (MSG29).
   - If the Word Text has more than 255 characters, the system displays the toast message (MSG30).
   - If the Note has more than 1000 characters, the system displays the toast message (MSG31).
   - If the Image is not selected, the system displays the message (MSG50).
   - If the Image's size is bigger than 5MB, the system displays the message (MSG51).
   - If the Image type is not allowed (must be jpeg, jpg, png, gif, webp), the system displays the message (MSG55).
   - If the Level is not selected, the system displays the message (MSG52).
   - If the Type is not selected, the system displays the message (MSG53).
   - If the word already exists, the system displays the message (MSG54).
7. If validation fails, the form will not be submitted and the relevant error messages will be displayed.
8. If validation passes, the system starts a database transaction.
9. Within the transaction, the system performs the following operations:
   - Validates and uploads the image to MinIO using uploadToMinIO(file, "words") helper
   - Generates unique filename for the uploaded image
   - Creates the new word record with is_active = true by default
   - Ensures word text uniqueness across the system
10. If all operations succeed, the system commits the transaction.
11. The system displays a success toast message (MSG15).
12. The dialog is closed.
13. The system refreshes the list of words to include the new word.

## Alternative Sequence/Flow
None

## Exception Sequence/Flow
Step 8, 9, 10: error during create the word: If any operation within the transaction fails (e.g., due to connection errors, MinIO upload failure, or uniqueness constraint violation), the system will rollback the transaction and display the following error toast message (MSG16, MSG21, MSG29, MSG30, MSG31, MSG50, MSG51, MSG52, MSG53, MSG54, MSG55).

## Business Rules
All CREATE operations must use database transactions to ensure data consistency.
Word text must be unique across the system.
Words can be created independently and later linked to games through GameWord relationships.
Image upload to MinIO and database record creation must be atomic.
Only allowed image types (jpeg, jpg, png, gif, webp) with maximum size 5MB are accepted.
All uploaded files must be validated using validateKidReadingFiles() before processing.
Generate unique filenames when uploading to MinIO to prevent conflicts.
If any part of the creation process fails, the entire operation is rolled back.