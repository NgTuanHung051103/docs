# UC_LP02: Create Learning Path

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
The Admin clicks on the "Create New" button in the Learning Paths Management screen.

### Description
As an Admin, I want to create a new learning path with basic information (name, description, difficulty, image), so that I can establish a new structured learning sequence for students to follow.

### Preconditions
- The user must be authenticated with a valid JWT token
- The user must have Admin role permissions (verified by Auth & Role middleware)
- MinIO service must be accessible for image upload
- Database must be available and accessible
- The Admin must be on the Learning Paths Management screen

### Postconditions
- A new learning path record is created in the database 
- The learning path gets an auto-generated sequence number
- The uploaded image is stored in MinIO with proper URL
- Success message (MSG_1) is displayed to the Admin
- The Admin is redirected back to the Learning Paths list or to the items management screen
- The new learning path appears in the learning paths list

### Normal Sequence/Flow

1. **The Admin clicks the "Create New" button on the Learning Paths Management screen.**
2. **The system opens a Create Learning Path dialog/form with the following fields:**
   - Name (text input, required)
   - Description (textarea, optional)
   - Difficulty Level (dropdown: 1-5 stars, required)
   - Image Upload (file input, required)

3. **The Admin enters the learning path name in the Name field.**
4. **The system validates the name input in real-time (not empty, character limit) - shows (MSG_5) if empty, (MSG_7) if > 255 characters.**

5. **The Admin enters a description for the learning path (optional).**
6. **The system validates description length (max 1000 characters) - shows (MSG_8) if exceeded.**

7. **The Admin selects difficulty level from dropdown (1-5).**
8. **The system validates the difficulty selection - shows (MSG_9) if not selected, (MSG_10) if invalid.**

9. **The Admin uploads an image file for the learning path.**
10. **The system validates the uploaded file - shows (MSG_11) if missing, (MSG_12) if > 5MB, (MSG_13) if invalid format:**
    - File format (JPEG, PNG, GIF, WebP)
    - File size (max 5MB)
    - File integrity

11. **The Admin clicks "Save" button to create the learning path.**
12. **The system performs comprehensive validation - shows respective error messages if validation fails:**
    - Name is not empty and unique in the system (MSG_5, MSG_6, MSG_7)
    - Difficulty is between 1-5 (MSG_9, MSG_10)
    - Image file meets requirements (MSG_11, MSG_12, MSG_13)
    - Description length validation (MSG_8)

13. **The system creates a database transaction and performs the following operations - shows (MSG_15) if any step fails:**
    - Upload image to MinIO storage
    - Create learning path record with default values (is_active = 1, auto sequence)
    - Generate unique learning path ID

14. **The system commits the transaction and displays success message (MSG_1).**
15. **The system close modal and refresh new list.**

### Alternative Sequence/Flow

**Alternative 1 - Cancel Operation:**
- At any step: Admin clicks "Cancel" button
- System discards all input data
- System returns to Learning Paths Management screen
- No database changes are made
- No success or error message displayed

### Exception Sequence/Flow

**Steps 3-4: Name Validation Errors:**
- If name is empty during real-time validation or submit: Display (MSG_5)
- If name exceeds 255 characters during typing or submit: Display (MSG_7)

**Steps 5-6: Description Validation Errors:**
- If description exceeds 1000 characters during typing or submit: Display (MSG_8)

**Steps 7-8: Difficulty Validation Errors:**
- If no difficulty selected when clicking Save: Display (MSG_9)
- If invalid difficulty value received by server: Display (MSG_10)

**Steps 9-10: Image Upload Errors:**
- If no image uploaded when clicking Save: Display (MSG_11)
- If file size > 5MB during file selection or upload: Display (MSG_12)
- If invalid format during file selection: Display (MSG_13)

**Steps 11-14: System-level Errors:**
- If network connection fails during form submit: Display (MSG_14)
- If name already exists during server validation: Display (MSG_6)
- If MinIO upload fails during step 13: Display (MSG_15)
- If database error occurs during step 13: Display (MSG_15)
- If transaction fails during step 13: Rollback all changes and display (MSG_15)

---

## Mockup Design

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                            Create Learning Path                                     │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  Name: *                                                                            │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │ [Enter learning path name...]                                               │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
│  Description:                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │ [Enter description... (optional)]                                           │   │
│  │                                                                             │   │
│  │                                                                             │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
│  Difficulty Level: *                                                                │
│  ┌─────────────────────────────────────┐                                           │
│  │ Select Difficulty ▼                 │                                           │
│  └─────────────────────────────────────┘                                           │
│     ⭐ 1 Star - Very Easy                                                           │
│     ⭐⭐ 2 Stars - Easy                                                              │
│     ⭐⭐⭐ 3 Stars - Medium                                                           │
│     ⭐⭐⭐⭐ 4 Stars - Hard                                                            │
│     ⭐⭐⭐⭐⭐ 5 Stars - Very Hard                                                       │
│                                                                                     │
│  Image: *                                                                           │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                          📁 Choose File                                     │   │
│  │                                                                             │   │
│  │                    [Drag & Drop or Click to Upload]                        │   │
│  │                     Supported: JPEG, PNG, GIF, WebP                        │   │
│  │                          Max size: 5MB                                     │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │ 📷 [learning_path_image.jpg] (2.3 MB) ✅                                   │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│                    [Cancel]                              [Save]                     │
│                                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

### UI Elements Description:
- **Name Field:** Required text input with validation, max 255 characters
- **Description Field:** Optional textarea, max 1000 characters with character counter
- **Difficulty Dropdown:** Required selection with visual star representation
- **Image Upload Area:** Drag & drop file upload with preview, validation indicators
- **Cancel Button:** Discards all changes and returns to learning paths list
- **Save Button:** Creates learning path and returns to list

### Form Validation Behavior:
- **Real-time validation:** Show validation errors immediately when user leaves field
- **Submit validation:** Prevent form submission if any required field is invalid
- **Visual indicators:** Red border for invalid fields, green checkmark for valid fields
- **Error messages:** Display specific error messages below each field
- **Progress indicator:** Show upload progress for image files

---

## Error Messages & Validation Messages

### Messages

- **MSG_1:** "Learning path created successfully"
- **MSG_5:** "Learning path name is required"
- **MSG_6:** "Learning path name must be unique"
- **MSG_7:** "Learning path name cannot exceed 255 characters"
- **MSG_8:** "Description cannot exceed 1000 characters"
- **MSG_9:** "Difficulty level is required"
- **MSG_10:** "Difficulty level must be between 1 and 5"
- **MSG_11:** "Image is required"
- **MSG_12:** "Image file size cannot exceed 5MB"
- **MSG_13:** "Invalid image file format. Only JPEG, PNG, GIF, WebP are allowed"
- **MSG_14:** "Connection error. Please try again later"
- **MSG_15:** "Server error occurred while processing request"

### When These Messages Occur:

**MSG_1** - Hiển thị khi:
- Learning path được tạo thành công và commit vào database
- Image được upload thành công lên MinIO
- Tất cả validation đều pass

**MSG_5** - Hiển thị khi:
- User để trống trường Name và click Save

**MSG_6** - Hiển thị khi:
- Tên learning path đã tồn tại trong database (case-insensitive check)
- Server validation trước khi insert database

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

**MSG_11** - Hiển thị khi:
- User không upload image và click Save
- Required field validation

**MSG_12** - Hiển thị khi:
- File size vượt quá 5MB limit
- Client-side validation ngay khi select file

**MSG_13** - Hiển thị khi:
- File format không phải JPEG, PNG, GIF, WebP
- MIME type validation

**MSG_14** - Hiển thị khi:
- Network connection error khi submit form

**MSG_15** - Hiển thị khi:
- Database connection error
- MinIO service unavailable
- Transaction rollback due to system error
- Unexpected server errors

---

## Business Rules Applied to UC_LP02

| ID | Business Rule | Description | Implementation |
|----|---------------|-------------|----------------|
| **BR_1** | Transaction required for CREATE operations | MULTIPLE action include CREATE operations must use database transaction | Sequelize transaction wrapper around create operation |
| **BR_2** | Admin authorization required | Only authenticated admin users can create learning paths | Auth.middleware.js + Role.middleware.js |
| **BR_3** | Fail-fast validation principle | Stop on first validation error and return immediately | Controller validation before database operations |
| **BR_4** | File upload size restrictions | Images max 5MB, stored in MinIO with validation | FileValidation.helper.js + UploadToMinIO.helper.js |
| **BR_5** | Soft delete policy | New learning paths created with is_active = 1 by default | Default value in model definition |
| **BR_6** | Unique name constraint | Learning path names must be unique across system | Database unique constraint + validation check |
| **BR_7** | Difficulty standardization | Difficulty level must be between 1-5 | Input validation + database constraint |
| **BR_8** | Auto sequence assignment | New learning paths get auto-generated sequence number | Database auto-increment or application logic |
| **BR_9** | Required field validation | Name, difficulty, and image are mandatory fields | Form validation + server-side validation |
| **BR_10** | Character limit enforcement | Name max 255 chars, description max 1000 chars | Input validation + database column limits |
| **BR_11** | File format validation | Only JPEG, PNG, GIF, WebP images allowed | MIME type validation in FileValidation helper |
| **BR_12** | Response format consistency | Use MessageManager for all API responses | messageManager.success() / messageManager.validationFailed() |

---

## Technical Implementation Notes

### Required API Endpoint
- `POST /admin/learning-paths` - Create new learning path with image upload

### API Request Contract
```javascript
POST /admin/learning-paths
Content-Type: multipart/form-data
Authorization: Bearer <JWT_TOKEN>

Form Data:
- name (string, required) - Learning path name, max 255 characters
- description (string, optional) - Description, max 1000 characters  
- difficulty (integer, required) - Difficulty level 1-5
- image (file, required) - Image file max 5MB, formats: JPEG/PNG/GIF/WebP
```

### API Response Contract
```javascript
// Success Response (201)
{
  "statusCode": 201,
  "message": "Learning path created successfully",
  "data": {
    "id": 123,
    "name": "Basic English Path",
    "description": "Learning path for beginners",
    "difficulty_level": 2,
    "image_url": "https://minio-url/learning-paths/path_123_image.jpg",
    "is_active": true,
    "sequence": 1,
    "created_at": "2025-09-17T10:30:00.000Z",
    "updated_at": "2025-09-17T10:30:00.000Z"
  }
}

// Validation Error Response (400)
{
  "statusCode": 400,
  "message": "Learning path name is required",
  "data": null
}

// Duplicate Name Error (409)
{
  "statusCode": 409,
  "message": "Learning path name must be unique",
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
const { sequelize } = require('../models');

const createLearningPath = async (req, res) => {
  let transaction;
  try {
    transaction = await sequelize.transaction();
    
    // 1. Validate input data
    const { name, description, difficulty } = req.body;
    const imageFile = req.files?.image;
    
    // 2. Check name uniqueness
    const existingPath = await LearningPath.findOne({
      where: { name: { [Op.iLike]: name } },
      transaction
    });
    
    if (existingPath) {
      await transaction.rollback();
      return messageManager.validationFailed('learning_paths', res, 'Learning path name must be unique');
    }
    
    // 3. Upload image to MinIO
    const imageUrl = await uploadToMinIO(imageFile, 'learning-paths');
    
    // 4. Create learning path record
    const learningPath = await LearningPath.create({
      name,
      description,
      difficulty_level: parseInt(difficulty),
      image_url: imageUrl,
      is_active: true,
      sequence: await getNextSequence()
    }, { transaction });
    
    await transaction.commit();
    
    return messageManager.success('learning_paths', res, 'Learning path created successfully', learningPath);
    
  } catch (error) {
    if (transaction) await transaction.rollback();
    console.error('Error creating learning path:', error);
    return messageManager.fetchFailed('learning_paths', res);
  }
};
```

#### Validation Implementation
```javascript
const validateCreateLearningPath = (req, res, next) => {
  const { name, difficulty } = req.body;
  const imageFile = req.files?.image;
  
  // Name validation
  if (!name || name.trim() === '') {
    return messageManager.validationFailed('learning_paths', res, 'Learning path name is required');
  }
  
  if (name.length > 255) {
    return messageManager.validationFailed('learning_paths', res, 'Learning path name cannot exceed 255 characters');
  }
  
  // Description validation
  if (req.body.description && req.body.description.length > 1000) {
    return messageManager.validationFailed('learning_paths', res, 'Description cannot exceed 1000 characters');
  }
  
  // Difficulty validation
  if (!difficulty) {
    return messageManager.validationFailed('learning_paths', res, 'Difficulty level is required');
  }
  
  const difficultyNum = parseInt(difficulty);
  if (isNaN(difficultyNum) || difficultyNum < 1 || difficultyNum > 5) {
    return messageManager.validationFailed('learning_paths', res, 'Difficulty level must be between 1 and 5');
  }
  
  // Image validation
  if (!imageFile) {
    return messageManager.validationFailed('learning_paths', res, 'Image is required');
  }
  
  // Use existing FileValidation helper
  const imageValidation = validateKidReadingFiles({ image: imageFile });
  if (!imageValidation.isValid) {
    return messageManager.validationFailed('learning_paths', res, imageValidation.message);
  }
  
  next();
};
```

### Security & Performance Considerations
- **File Upload Security**: Use FileValidation.helper.js to prevent malicious uploads
- **Input Sanitization**: Sanitize all string inputs to prevent XSS
- **Transaction Management**: Ensure data consistency with proper rollback on errors
- **MinIO Integration**: Handle MinIO service failures gracefully
- **Unique Constraint**: Use database-level unique constraint + application validation
- **File Size Optimization**: Consider image compression before storing in MinIO

---

## Diagram Components Overview

### Sequence Diagram Components
**Actors & Objects:**
- Admin (User)
- Frontend UI (Create Learning Path Form)
- API Gateway/Router
- Auth Middleware
- File Validation Middleware
- Learning Path Controller
- Learning Path Model
- MinIO Service
- Database (MySQL/PostgreSQL)
- Message Manager

**Key Interactions:**
1. Admin → Frontend: Click "Create New" button
2. Frontend → API: POST /admin/learning-paths (multipart form data)
3. API → Auth Middleware: Verify JWT & admin role
4. API → File Middleware: Validate image file
5. API → Controller: createLearningPath()
6. Controller → Database: Begin transaction
7. Controller → Model: Check name uniqueness
8. Controller → MinIO: Upload image file
9. Controller → Model: Create learning path record
10. Controller → Database: Commit transaction
11. Controller → Frontend: Success response with data
12. Frontend → Admin: Display success message & redirect

### Class Diagram Components
**Main Classes:**
- **LearningPath** (Entity Model)
  - Properties: id, name, description, difficulty_level, image_url, is_active, sequence, created_at, updated_at
  - Methods: validate(), create(), findByName()
  - Constraints: unique name, difficulty 1-5

- **LearningPathController** (Controller Layer)
  - Methods: createLearningPath(req, res)
  - Dependencies: LearningPath Model, MinIO Service, MessageManager
  - Validation: Input validation, business rule enforcement

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
- Controller uses FileValidation Helper
- Controller uses UploadToMinIO Helper
- Controller uses MessageManager
- All requests go through AuthMiddleware
- FileValidation validates before upload

---
---

Ghi chú: Use case này tập trung vào việc tạo learning path với thông tin cơ bản. Quản lý items trong learning path sẽ được thực hiện ở các use case riêng biệt.