# UC_W03: Update Word

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
The Admin clicks the **"Edit"** button of a Word in the list.

### Description
As an Admin, I want to update an existing word, so that I can correct or refine its details.

### Preconditions
- The word must exist in the system.

### Postconditions
- The word details are updated in the system.

### Normal Sequence/Flow
1. Admin selects a Word to edit.  
2. System displays the edit form with current details.  
3. Admin modifies word details.  
4. Admin clicks **Save**.  
5. System validates input and updates the word.  
6. System shows a success message.  

### Alternative Sequence/Flow
- None.  

### Exception Sequence/Flow
- If Word not found → `"Word not found"`.  
- If validation fails → `"Please fill out this field"`.  
- If system error occurs → `"Failed to update word"`.  

### Mockup Design

### Error Messages & Validation Messages
- `"word.invalid.text"` = "Word text cannot be empty."  
- `"word.update.fail"` = "Failed to update word."  
- `"word.notfound"` = "Word not found."  

### Messages
- `"word.update.success"` = "Word updated successfully."  

### When These Messages Occur
- On successful update → success message.  
- On validation fail → error message.  

### Business Rules
- Word text must remain unique after update.  

### Diagram Components Overview
- Actors: Admin, System  
- Entities: Word  