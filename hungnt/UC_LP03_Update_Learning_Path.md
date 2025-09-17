# UC_LP03: Update Learning Path

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
The Admin clicks on the "Edit Information" button in the column Action of Learning Paths Management screen for a specific learning path.

### Description
As an Admin, I want to update an existing learning path's information (name, description, difficulty, image), so that I can maintain and improve the learning content while ensuring student progress is preserved.

### Preconditions
- The user must be authenticated with a valid JWT token
- The user must have Admin role permissions (verified by Auth & Role middleware)
- MinIO service must be accessible for image upload (if image is updated)
- Database must be available and accessible
- The learning path must exist in the system
- The Admin must be on the Learning Paths Management screen

### Postconditions
- The learning path record is updated in the database with new information
- If image is updated, the new image is stored in MinIO with proper URL
- Success message (MSG_1) is displayed to the Admin
- The Admin is redirected back to the Learning Paths list
- The updated learning path appears with new information in the list
- If learning path is deactivated, check student progress constraints

## Normal Sequence/Flow

1. **The Admin clicks the "Edit Information" button (pencil icon) for a specific learning path in the Learning Paths Management table.**

2. **The system retrieves the current learning path data and opens an Update Learning Path dialog/form pre-filled with existing values:**
   - Name (text input, required, current value displayed)
   - Description (textarea, optional, current value displayed)
   - Difficulty Level (dropdown: 1-5 stars, required, current value selected)
   - Image Upload (file input, optional, current image thumbnail displayed)
   - Active Status (toggle/dropdown, current status selected)

3. **The Admin modifies the learning path name in the Name field.**
4. **The system validates the name input in real-time (not empty, character limit, uniqueness except current record) - shows (MSG_5) if empty, (MSG_7) if > 255 characters, (MSG_6) if name exists in other records.**

5. **The Admin updates the description for the learning path (optional).**
6. **The system validates description length (max 1000 characters) - shows (MSG_8) if exceeded.**

7. **The Admin changes difficulty level from dropdown (1-5).**
8. **The system validates the difficulty selection - shows (MSG_9) if not selected, (MSG_10) if invalid.**

9. **The Admin uploads a new image file for the learning path (optional).**
10. **The system validates the uploaded file if provided - shows (MSG_11) if invalid format, (MSG_12) if > 5MB, (MSG_13) if invalid format:**
    - File format (JPEG, PNG, GIF, WebP)
    - File size (max 5MB)
    - File integrity

11. **The Admin changes the active status (Active/Inactive).**
12. **If Admin selects Inactive status, the system checks student progress - shows (MSG_16) if students have accessed this learning path and prevents deactivation.**

13. **The Admin clicks "Save" button to update the learning path.**
14. **The system performs comprehensive validation - shows respective error messages if validation fails:**
    - Name is not empty and unique except current record (MSG_5, MSG_6, MSG_7)
    - Difficulty is between 1-5 (MSG_9, MSG_10)
    - Image file meets requirements if provided (MSG_11, MSG_12, MSG_13)
    - Description length validation (MSG_8)
    - Student progress validation for status change (MSG_16)

15. **The system creates a database transaction and performs the following operations - shows (MSG_15) if any step fails:**
    - Upload new image to MinIO storage (if provided)
    - Update learning path record with modified values
    - Keep existing image URL if no new image provided

16. **The system commits the transaction and displays success message (MSG_1).**
17. **The system closes modal and refreshes the learning paths list with updated information.**

## Alternative Sequence/Flow

**Alternative 1 - Cancel Operation:**
- At any step: Admin clicks "Cancel" button
- System discards all input changes
- System returns to Learning Paths Management screen with original data
- No database changes are made
- No success or error message displayed

**Alternative 2 - Update Without Image Change:**
- Steps 1-8: Normal flow until image upload
- Step 9: Admin does not upload new image
- Step 10: System skips image validation
- Steps 11-17: Continue with normal flow using existing image URL

## Exception Sequence/Flow

**Steps 3-4: Name Validation Errors:**
- If name is empty during real-time validation or submit: Display (MSG_5)
- If name exceeds 255 characters during typing or submit: Display (MSG_7)
- If name already exists in another learning path: Display (MSG_6)

**Steps 5-6: Description Validation Errors:**
- If description exceeds 1000 characters during typing or submit: Display (MSG_8)

**Steps 7-8: Difficulty Validation Errors:**
- If no difficulty selected when clicking Save: Display (MSG_9)
- If invalid difficulty value received by server: Display (MSG_10)

**Steps 9-10: Image Upload Errors:**
- If file size > 5MB during file selection or upload: Display (MSG_12)
- If invalid format during file selection: Display (MSG_13)

**Steps 11-12: Status Change Validation Errors:**
- If trying to deactivate learning path with student progress: Display (MSG_16)

**Steps 13-16: System-level Errors:**
- If network connection fails during form submit: Display (MSG_14)
- If learning path not found during update: Display (MSG_17)
- If MinIO upload fails during step 15: Display (MSG_15)
- If database error occurs during step 15: Display (MSG_15)
- If transaction fails during step 15: Rollback all changes and display (MSG_15)

## Mockup Design

### MOCKUP DESIGN

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                            Update Learning Path                                     │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Name: *                                                                            │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │ Basic English Reading Path                                                  │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
│  Description:                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │ Lộ trình học đọc tiếng Anh cơ bản dành cho trẻ em từ 6-8 tuổi              │   │
│  │                                                                             │   │
│  │                                                                             │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
│  Difficulty Level: *                                                                │
│  ┌─────────────────────────────────────┐                                           │
│  │ ⭐⭐ 2 Stars - Easy ▼               │                                           │
│  └─────────────────────────────────────┘                                           │
│                                                                                     │
│  Current Image:                                                                     │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │ [📷 basic_english_path.jpg] (1.2 MB) ✅                                     │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
│  Upload New Image: (Optional)                                                       │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                          📁 Choose File                                     │   │
│  │                    [Drag & Drop or Click to Upload]                        │   │
│  │                     Supported: JPEG, PNG, GIF, WebP                        │   │
│  │                          Max size: 5MB                                     │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
│  Status: *                                                                          │
│  ┌─────────────────────────────────────┐                                           │
│  │ ✅ Active ▼                         │                                           │
│  └─────────────────────────────────────┘                                           │
│                                                                                     │
│  ⚠️ Note: Deactivating will hide this path from students                           │
│                                                                                     │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│                    [Cancel]                              [Save Changes]             │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

### UI ELEMENTS DESCRIPTION

**Input Fields:**
- **Name Field:** Required text input with validation, max 255 characters, pre-filled with current value
- **Description Field:** Optional textarea, max 1000 characters with character counter, pre-filled with current value
- **Difficulty Dropdown:** Required selection with visual star representation, current difficulty pre-selected
- **Current Image Display:** Shows thumbnail of existing image with filename and size
- **New Image Upload Area:** Optional drag & drop file upload with preview, validation indicators
- **Status Dropdown:** Required selection between Active/Inactive, current status pre-selected

**Buttons & Controls:**
- **Cancel Button:** Discards all changes and returns to learning paths list
- **Save Changes Button:** Updates learning path and returns to list

**Display Elements:**
- **Current Image Thumbnail:** Shows existing image with filename and size information
- **Warning Note:** Displays information about deactivation consequences

**Form Validation Behavior:**
- **Real-time validation:** Show validation errors immediately when user leaves field
- **Submit validation:** Prevent form submission if any required field is invalid
- **Visual indicators:** Red border for invalid fields, green checkmark for valid fields
- **Error messages:** Display specific error messages below each field
- **Progress indicator:** Show upload progress for new image files
- **Student progress check:** Validate before allowing status change to inactive

## Error Messages & Validation Messages

### Messages

- **MSG_1:** "Learning path updated successfully"
- **MSG_5:** "Learning path name is required"
- **MSG_6:** "Learning path name must be unique"
- **MSG_7:** "Learning path name cannot exceed 255 characters"
- **MSG_8:** "Description cannot exceed 1000 characters"
- **MSG_9:** "Difficulty level is required"
- **MSG_10:** "Difficulty level must be between 1 and 5"
- **MSG_12:** "Image file size cannot exceed 5MB"
- **MSG_13:** "Invalid image file format. Only JPEG, PNG, GIF, WebP are allowed"
- **MSG_14:** "Connection error. Please try again later"
- **MSG_15:** "Server error occurred while processing request"
- **MSG_16:** "Cannot deactivate: Students have already accessed this learning path"
- **MSG_17:** "Learning path not found"

### When These Messages Occur

**MSG_1** - Hiển thị khi:
- Learning path được cập nhật thành công và commit vào database
- Tất cả validation đều pass
- Image được upload thành công lên MinIO (nếu có)

**MSG_5** - Hiển thị khi:
- User xóa hết nội dung trường Name và click Save

**MSG_6** - Hiển thị khi:
- Tên learning path đã tồn tại trong database ở record khác (case-insensitive check)
- Server validation trước khi update database

**MSG_7** - Hiển thị khi:
- User nhập tên vượt quá 255 ký tự

**MSG_8** - Hiển thị khi:
- User nhập description vượt quá 1000 ký tự
- Real-time validation với character counter

**MSG_9** - Hiển thị khi:
- User không chọn difficulty level và click Save
- Required field validation

**MSG_10** - Hiển thị khi:
- Server receive invalid difficulty value (not 1-5)
- Client-side manipulation protection

**MSG_12** - Hiển thị khi:
- File size vượt quá 5MB limit khi upload image mới
- Client-side validation ngay khi select file

**MSG_13** - Hiển thị khi:
- File format không phải JPEG, PNG, GIF, WebP khi upload image mới
- MIME type validation

**MSG_14** - Hiển thị khi:
- Network connection error khi submit form

**MSG_15** - Hiển thị khi:
- Database connection error
- MinIO service unavailable khi upload image mới
- Transaction rollback due to system error
- Unexpected server errors

**MSG_16** - Hiển thị khi:
- Admin cố gắng deactive learning path đã có student progress
- Business rule validation để protect student data

**MSG_17** - Hiển thị khi:
- Learning path ID không tồn tại trong database
- Record đã bị xóa hoặc corrupted

## Business Rules Applied to UC_LP03

| ID | Business Rule | Description | Implementation |
|----|---------------|-------------|----------------|
| **BR_1** | Transaction required for UPDATE operations | All UPDATE operations must use database transaction | Sequelize transaction wrapper around update operation |
| **BR_2** | Admin authorization required | Only authenticated admin users can update learning paths | Auth.middleware.js + Role.middleware.js |
| **BR_3** | Fail-fast validation principle | Stop on first validation error and return immediately | Controller validation before database operations |
| **BR_4** | File upload size restrictions | Images max 5MB, stored in MinIO with validation | FileValidation.helper.js + UploadToMinIO.helper.js |
| **BR_5** | Soft delete policy | Cannot deactivate learning paths with student progress | Check StudentReading records before status change |
| **BR_6** | Unique name constraint | Learning path names must be unique except current record | Database unique constraint + validation check excluding current ID |
| **BR_7** | Difficulty standardization | Difficulty level must be between 1-5 | Input validation + database constraint |
| **BR_8** | Student progress protection | Preserve student data when updating learning paths | Validate student progress before deactivation |
| **BR_9** | Required field validation | Name and difficulty are mandatory fields | Form validation + server-side validation |
| **BR_10** | Character limit enforcement | Name max 255 chars, description max 1000 chars | Input validation + database column limits |
| **BR_11** | File format validation | Only JPEG, PNG, GIF, WebP images allowed | MIME type validation in FileValidation helper |
| **BR_12** | Response format consistency | Use MessageManager for all API responses | messageManager.success() / messageManager.validationFailed() |

## Technical Implementation Notes

### Required API Endpoint
- `PUT /admin/learning-paths/:id` - Update existing learning path with optional image upload

### API Request Contract
```javascript
PUT /admin/learning-paths/:id
Content-Type: multipart/form-data
Authorization: Bearer <JWT_TOKEN>

Form Data:
- name (string, required) - Learning path name, max 255 characters
- description (string, optional) - Description, max 1000 characters  
- difficulty_level (integer, required) - Difficulty level 1-5
- is_active (integer, required) - Active status 1 or 0
- image (file, optional) - New image file max 5MB, formats: JPEG/PNG/GIF/WebP
```

### API Response Contract
```javascript
// Success Response (200)
{
  "statusCode": 200,
  "message": "Learning path updated successfully",
  "data": {
    "id": 123,
    "name": "Updated Learning Path Name",
    "description": "Updated description",
    "difficulty_level": 3,
    "is_active": 1,
    "image": "https://minio-url/learning_paths/updated_image.jpg",
    "updated_at": "2025-09-17T10:30:00.000Z"
  }
}

// Validation Error Response (400)
{
  "statusCode": 400,
  "message": "Learning path name must be unique",
  "data": null
}

// Student Progress Error (400)
{
  "statusCode": 400,
  "message": "Cannot deactivate: Students have already accessed this learning path",
  "data": null
}

// Not Found Error (404)
{
  "statusCode": 404,
  "message": "Learning path not found",
  "data": null
}

// Server Error Response (500)
{
  "statusCode": 500,
  "message": "Server error occurred while processing request",
  "data": null
}
```

### Database Operations Required

#### Transaction Pattern
```javascript
const updateLearningPath = async (req, res) => {
  let transaction;
  try {
    const { id } = req.params;
    
    // Check if learning path exists
    const existingPath = await db.LearningPath.findByPk(id);
    if (!existingPath) {
      return messageManager.notFound('learningpath', res);
    }

    // Check student progress if deactivating
    if (req.body.is_active === 0 && existingPath.is_active === 1) {
      const hasProgress = await checkStudentProgress(id);
      if (hasProgress) {
        return messageManager.validationFailed('learningpath', res, 
          'Cannot deactivate: Students have already accessed this learning path');
      }
    }

    transaction = await db.sequelize.transaction();
    
    // Handle image upload if provided
    let imageUrl = existingPath.image;
    if (req.files?.image) {
      imageUrl = await uploadToMinIO(req.files.image[0], "learning_paths");
    }

    // Update learning path
    await db.LearningPath.update({
      name: req.body.name,
      description: req.body.description,
      difficulty_level: req.body.difficulty_level,
      is_active: req.body.is_active,
      image: imageUrl
    }, {
      where: { id },
      transaction
    });

    await transaction.commit();
    
    const updatedPath = await db.LearningPath.findByPk(id);
    return messageManager.updateSuccess('learningpath', updatedPath, res);
    
  } catch (error) {
    if (transaction) await transaction.rollback();
    console.error('Update learning path error:', error);
    return messageManager.updateFailed('learningpath', res);
  }
};
```

#### Student Progress Check
```javascript
const checkStudentProgress = async (learningPathId) => {
  const progress = await db.StudentReading.findOne({
    where: { learning_path_id: learningPathId }
  });
  return !!progress;
};
```

### Security & Performance Considerations
- **File Upload Security**: Use FileValidation.helper.js to prevent malicious uploads
- **Input Sanitization**: Sanitize all string inputs to prevent XSS
- **Transaction Management**: Ensure data consistency with proper rollback on errors
- **MinIO Integration**: Handle MinIO service failures gracefully, keep existing image on upload failure
- **Unique Constraint**: Use database-level unique constraint + application validation excluding current record
- **Student Data Protection**: Always check student progress before allowing deactivation
- **File Size Optimization**: Consider image compression before storing in MinIO

## Diagram Components Overview

### Sequence Diagram Components
**Actors & Objects:**
- Admin (User)
- Frontend UI (Update Learning Path Form)
- API Gateway/Router
- Auth Middleware
- File Validation Middleware
- Learning Path Controller
- Learning Path Repository
- Student Reading Model (for progress check)
- MinIO Service
- Database (MySQL/PostgreSQL)
- Message Manager

**Key Interactions:**
1. Admin → Frontend: Click "Edit Information" button
2. Frontend → API: GET learning path data for pre-filling form
3. Frontend → API: PUT /admin/learning-paths/:id (multipart form data)
4. API → Auth Middleware: Verify JWT & admin role
5. API → File Middleware: Validate image file (if provided)
6. API → Controller: updateLearningPath()
7. Controller → Repository: Check if learning path exists
8. Controller → Student Reading: Check student progress (if deactivating)
9. Controller → Database: Begin transaction
10. Controller → MinIO: Upload new image (if provided)
11. Controller → Repository: Update learning path record
12. Controller → Database: Commit transaction
13. Controller → Frontend: Success response with updated data
14. Frontend → Admin: Display success message & refresh list

### Class Diagram Components
**Main Classes:**
- **LearningPath** (Entity Model)
  - Properties: id, name, description, difficulty_level, image_url, is_active, created_at, updated_at
  - Methods: findByPk(), update(), validate()
  - Constraints: unique name, difficulty 1-5

- **LearningPathController** (Controller Layer)
  - Methods: updateLearningPath(req, res), checkStudentProgress()
  - Dependencies: LearningPath Model, StudentReading Model, MinIO Service, MessageManager
  - Validation: Input validation, business rule enforcement, student progress check

- **StudentReading** (Entity Model)
  - Properties: id, kid_student_id, learning_path_id, is_completed
  - Methods: findOne()
  - Purpose: Check student progress before deactivation

- **FileValidation** (Helper Layer)
  - Methods: validateKidReadingFiles(), checkFileSize(), checkMimeType()
  - Rules: Max 5MB, allowed formats

- **UploadToMinIO** (Helper Layer)
  - Methods: uploadToMinIO(file, folder)
  - Integration: MinIO service connection

- **AuthMiddleware** (Security Layer)
  - Methods: verifyToken(), checkAdminRole()
  - Security: JWT validation, role authorization

**Relationships:**
- Controller uses LearningPath Model
- Controller uses StudentReading Model for progress check
- Controller uses FileValidation Helper
- Controller uses UploadToMinIO Helper
- Controller uses MessageManager
- All requests go through AuthMiddleware
- FileValidation validates before upload

---

## Notes

Ghi chú: Use case này tập trung vào việc cập nhật thông tin cơ bản của learning path với bảo vệ dữ liệu học sinh. Quản lý items trong learning path được thực hiện ở các use case riêng biệt (UC_LP04, UC_LP05).