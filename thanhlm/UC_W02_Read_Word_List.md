# UC_W02: Read Word List

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
The Admin clicks the **"Word List"** option in the management section.

### Description
As an Admin, I want to view a list of words, so that I can manage vocabulary more efficiently.

### Preconditions
- The user must use an Admin account to log into the system.

### Postconditions
- Word list is displayed with pagination.

### Normal Sequence/Flow
1. Admin selects **Word List**.  
2. System queries repository with pagination.  
3. System displays the list of words with actions: view, edit, delete.  

### Alternative Sequence/Flow
- None.  

### Exception Sequence/Flow
- If system error occurs → `"Failed to load words"`.  

### Mockup Design

### Error Messages & Validation Messages
- `"word.read.fail"` = "Failed to load words."  

### Messages
- None additional.  

### When These Messages Occur
- On failed read → error message.  

### Business Rules
- The system must support pagination.  
- Admin can search and filter words.  

### Diagram Components Overview
- Actors: Admin, System  
- Entities: Word  
