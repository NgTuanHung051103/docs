# UC_W01: Create Word

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
- The Admin clicks the **"Create Word"** option in the management section.  
- Or the Admin clicks **"Add New Word"** while creating/editing a Game.

### Description
As an Admin, I want to create a new word, so that I can expand the vocabulary repository or link it directly to a Game.

### Preconditions
- The user must use an Admin account to log into the system.

### Postconditions
- A new Word has been created and stored in the system.
- If created during Game Editing, the Word is automatically linked to that Game.

### Normal Sequence/Flow
**Flow A (Management)**
1. Admin selects **Create Word**.  
2. System displays the create form.  
3. Admin enters word details (word, image, level, type, note).  
4. Admin clicks **Save**.  
5. System validates input and stores the word.  
6. System shows a success message.  

**Flow B (Game Editing)**
1. Admin opens **Create/Edit Game**.  
2. Admin clicks **Add New Word**.  
3. System shows a popup to enter word details.  
4. Admin clicks **Save**.  
5. System validates input and stores the word.  
6. System creates the relation `GameWord`.  
7. System shows a success message.  

### Alternative Sequence/Flow
- None.  

### Exception Sequence/Flow
- If required fields are missing → `"Please fill out this field"`.  
- If word already exists → `"Word already exists"`.  
- If system error occurs → `"Failed to create word"`.  

### Mockup Design

### Error Messages & Validation Messages
- `"word.invalid.text"` = "Word text cannot be empty."  
- `"word.exists"` = "This word already exists."  
- `"word.create.fail"` = "Failed to create word."  

### Messages
- `"word.create.success"` = "Word created successfully."  

### When These Messages Occur
- On successful create → success message.  
- On validation fail or duplicate → error message.  

### Business Rules
- Word text must be unique.  
- Words can be created independently in management or linked directly in a Game.  

### Diagram Components Overview
- Actors: Admin, System  
- Entities: Word, GameWord, Game