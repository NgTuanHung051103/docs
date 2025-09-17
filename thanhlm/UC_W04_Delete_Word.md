# UC_W04: Delete Word

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
The Admin clicks the **"Delete"** option of a Word in the list.

### Description
As an Admin, I want to delete a word, so that I can remove unused or incorrect vocabulary.

### Preconditions
- The word must exist in the system.

### Postconditions
- The Word is deleted.  
- All related `GameWord` records are also deleted.  

### Normal Sequence/Flow
1. Admin clicks **Delete** on a Word.  
2. System shows confirmation dialog.  
3. Admin confirms deletion.  
4. System deletes related `GameWord` records.  
5. System deletes the Word.  
6. System shows a success message.  

### Alternative Sequence/Flow
- None.  

### Exception Sequence/Flow
- If Word not found → `"Word not found"`.  
- If system error occurs → `"Failed to delete word"`.  

### Mockup Design

### Error Messages & Validation Messages
- `"word.delete.fail"` = "Failed to delete word."  
- `"word.notfound"` = "Word not found."  

### Messages
- `"word.delete.success"` = "Word deleted successfully."  

### When These Messages Occur
- On successful delete → success message.  
- On failure → error message.  

### Business Rules
- When a Word is deleted, all `GameWord` relations must also be deleted.  

### Diagram Components Overview
- Actors: Admin, System  
- Entities: Word, GameWord  
