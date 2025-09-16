# UC_LP01: View List of Learning Paths

## Use Case Details

### Primary Actors
Admin

### Secondary Actors
None

### Trigger
The Admin clicks on the "Learning Paths" option in the management section.

### Description
As an Admin, I want to view a list of available learning paths, so that I can efficiently browse, search, filter, and manage learning paths in the system.

### Preconditions
- The user must use an Admin account to log into the system.
- The system must have learning paths data available.

### Postconditions
- The Admin can perform search, filter, and sort operations on the learning paths list.
- The Admin can open the "Create new" dialog to create a new learning path or open the "Edit" dialog to edit the selected learning path.
- The Admin can activate/deactivate learning paths based on student progress validation.

### Normal Sequence/Flow

1. **The Admin clicks the Learning Paths management section.**
2. **The system shows a table of learning paths with the following information in each column:**
   - Image (thumbnail)
   - Name
   - Difficulty Level (1-5 stars)
   - Active Status (Active/Inactive badge)
   - Items Count (number of active items in the path)
   - Action column (Edit, Activate/Deactivate buttons)

3. **The Admin can search for learning paths by name.**
4. **The system updates the table to display only learning paths matching the search term.**

5. **The Admin can filter learning paths by:**
   - Difficulty Level (dropdown: 1-5)
   - Status (dropdown: Active/Inactive)

6. **The system updates the table to display only learning paths matching the selected filters.**

7. **The Admin can sort the table by:**
   - Name (A-Z, Z-A)
   - Difficulty Level (Low to High, High to Low)
   - Active Status
   - Items Count

8. **The system immediately refreshes the table with the selected sort order.**

9. **The Admin can navigate through the list using pagination controls at the bottom.**
10. **The system immediately refreshes the table to show learning paths in the selected page.**

11. **The Admin can change the number of learning paths displayed per page (5, 10, 20, 50).**
12. **The system displays the number of learning paths per page matching the selected number.**

### Alternative Sequence/Flow
None

### Exception Sequence/Flow

**Steps 3-12: Error while fetching learning paths:**
- If connection error occurs, the system displays a toast error message (MSG_LP21, MSG_LP22).
- If no learning paths found matching criteria, the system displays an empty state with message (MSG_LP23).

---

## Mockup Design

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                           Learning Paths Management                                  │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│ [+ Create New]                                              🔍 [Search by name...] │
│                                                                                     │
│ Filters: [Difficulty ▼] [Status ▼]                        [🔄 Reset] [🔍 Search]   │
│                                                                                     │
├─────────────────────────────────────────────────────────────────────────────────────┤
│ Image    │ Name                 │ Difficulty │ Status   │ Items │ Last Update │ Action│
├─────────────────────────────────────────────────────────────────────────────────────┤
│ [📖]     │ Basic English Path   │ ⭐⭐        │ [Active] │  12   │ 15/09/2025  │ [✏️][🔄]│
│ [📚]     │ Advanced Math        │ ⭐⭐⭐⭐⭐    │ [Active] │   8   │ 14/09/2025  │ [✏️][🔄]│
│ [🎨]     │ Creative Art         │ ⭐⭐⭐      │[Inactive]│   0   │ 10/09/2025  │ [✏️][🔄]│
│ [🔬]     │ Science Explorer     │ ⭐⭐⭐⭐     │ [Active] │  15   │ 12/09/2025  │ [✏️][🔄]│
│ [🎵]     │ Music Foundation     │ ⭐⭐        │[Inactive]│   3   │ 08/09/2025  │ [✏️][🔄]│
├─────────────────────────────────────────────────────────────────────────────────────┤
│                        Showing 1-5 of 23 records                                   │
│                    [◀️ Previous] [1][2][3][4][5] [Next ▶️]                         │
│                         Records per page: [10 ▼]                                   │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

### UI Elements Description:
- **Search Bar:** Text input for searching by learning path name
- **Filter Dropdowns:** 
  - Difficulty: 1 Star, 2 Stars, 3 Stars, 4 Stars, 5 Stars
  - Status: Active, Inactive, All
- **Sort Controls:** Click column headers to sort (Name, Difficulty, Status, Items Count)
- **Pagination:** Previous/Next buttons + page numbers + records per page selector
- **Action Buttons:** Edit (pencil icon), Activate/Deactivate (toggle icon)

---

## Error Messages & Validation Messages

### Messages

- **MSG_1:** "Learning path created successfully"
- **MSG_2:** "Learning path updated successfully"
- **MSG_3:** "Learning path status changed successfully"
- **MSG_4:** "Learning paths loaded successfully"
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
- **MSG_16:** "No learning paths found matching your criteria"
- **MSG_17:** "Cannot delete learning path. Students have already accessed this content"
- **MSG_18:** "Cannot deactivate learning path. Please check student progress first"
- **MSG_19:** "Learning path must have at least one active item before activation"
- **MSG_20:** "Cannot modify learning path sequence. Category grouping validation failed"
- **MSG_21:** "Cannot remove item. Students have already completed this content"

### When These Messages Occur:

**MSG_14** - Hiển thị khi:
- Connection timeout hoặc network error khi fetch data

**MSG_15** - Hiển thị khi:  
- Server internal error (500) khi xử lý request

**MSG_16** - Hiển thị khi:
- Search/filter không tìm thấy learning path nào matching

**MSG_17** - Hiển thị khi:
- Admin cố gắng xóa hoàn toàn learning path có records trong StudentReading

**MSG_18** - Hiển thị khi:  
- Admin cố gắng deactivate learning path mà chưa kiểm tra student progress

**MSG_19** - Hiển thị khi:
- Admin cố gắng activate learning path không có items nào active

---

## Business Rules


### Business Rules Applied to UC_LP01

| ID | Business Rule | Description |
|----|---------------|-------------|
| **BR_1** | The list displays 10 records per page by default | The number record can be changed via the "Display" dropdown below the table. |
| **BR_2** | The user must have permission or be authorized to access certain functionalities | Ensure the security for the system, only allow users to have the right permission to implement the function. |
| **BR_3** | The system must display toast message | The toast message to notify the result of action |
| **BR_4** | Data must be validated before saving to database | To avoid any incorrect data that leads to an error system. |
| **BR_5** | The device must connect to the internet | Connect to the internet so as to send requests to the server. |
| **BR_6** | Reading and e-book/reading items have an 'active' or 'inactive' status. Only 'active' content should be displayed to users in the mobile app. | Allows administrators to control content visibility without deletion, supporting content management. |
| **BR_7** | Learning paths must display items count from active learning_path_items only (is_active = 1) | Ensures accurate count of available content for students |
| **BR_8** | All search/filter/sort/pagination must be processed in a single query | Optimizes performance and prevents multiple database calls |
| **BR_9** | Learning paths with student progress can only be deactivated, not deleted | Maintains data integrity and student progress history |
| **BR_10** | Learning path name must be unique across the system | Prevents confusion and ensures clear identification |
| **BR_11** | Difficulty level must be between 1-5 | Standardizes difficulty assessment across all learning paths |
| **BR_12** | Image file size cannot exceed 5MB | Ensures optimal system performance and storage management |
| **BR_13** | Only authorized admin users can access learning path management | Maintains system security and content integrity |
| **BR_14** | Learning paths must have at least one active item before activation | Ensures students have content to access when path is active |
| **BR_15** | Category grouping constraint must be maintained | Readings from same category must be grouped together in sequence |
| **BR_16** | Game prerequisite relationships must be preserved | Games must follow their prerequisite readings in sequence |
| **BR_17** | Sequential unlock logic applies to all learning paths | Students must complete items in order, cannot skip ahead |
| **BR_18** | Soft delete policy applies to all learning path operations | No permanent deletion of learning paths or items with student progress |

---

## Technical Implementation Notes

### Required API Endpoint for View Screen
- `GET /admin/learning-paths` - Dùng duy nhất cho màn hình xem danh sách learning path (bao gồm search, filter, sort, pagination)

### Database Query Requirements
- Truy vấn duy nhất với Sequelize `findAndCountAll()`
- Bao gồm liên kết model: LearningPathItems (chỉ đếm item active)
- Hỗ trợ WHERE động theo filter/search
- ORDER BY theo sort
- LIMIT/OFFSET cho phân trang

### Security Considerations
- Yêu cầu xác thực JWT cho endpoint này
- Chỉ admin mới truy cập được
- Validate/sanitize input search/filter
- Chống SQL injection qua query tham số hóa

---

## Diagram Components Overview

### Sequence Diagram Components
**Actors & Objects:**
- Admin (User)
- Frontend UI (Learning Path Management Screen)
- API Gateway/Router
- Auth Middleware
- Learning Path Controller
- Learning Path Repository
- Database (MySQL/PostgreSQL)
- Message Manager

**Key Interactions:**
1. Admin → Frontend: Click "Learning Paths" menu
2. Frontend → API: GET /admin/learning-paths (with JWT token)
3. API → Auth Middleware: Verify JWT & admin role
4. API → Controller: getAllLearningPaths()
5. Controller → Repository: findAllWithPaging(filters, sort, pagination)
6. Repository → Database: SELECT with JOIN LearningPathItems
7. Database → Repository: Return learning paths data
8. Repository → Controller: Formatted results
9. Controller → Frontend: JSON response with pagination
10. Frontend → Admin: Display table with data

### Class Diagram Components
**Main Classes:**
- **LearningPath** (Entity)
  - Properties: id, name, description, difficulty_level, image_url, is_active, created_at, updated_at
  - Methods: validate(), getActiveItemsCount()

- **LearningPathItem** (Entity)
  - Properties: id, learning_path_id, item_id, item_type, sequence_number, is_active
  - Relationship: BelongsTo LearningPath

- **LearningPathController** (Controller Layer)
  - Methods: getAllLearningPaths(req, res)
  - Dependencies: LearningPathRepository, MessageManager

- **LearningPathRepository** (Data Access Layer)
  - Methods: findAllWithPaging(filters, sort, pagination)
  - Dependencies: Sequelize Models

- **AuthMiddleware** (Security Layer)
  - Methods: verifyToken(), checkAdminRole()

- **MessageManager** (Utility Layer)
  - Methods: success(), error(), notFound()

**Relationships:**
- LearningPath hasMany LearningPathItem
- Controller uses Repository
- Controller uses MessageManager
- All requests go through AuthMiddleware

---

Ghi chú: Màn hình này chỉ sử dụng endpoint `GET /admin/learning-paths`. Các thao tác tạo, cập nhật, đổi trạng thái sẽ dùng ở use case khác.