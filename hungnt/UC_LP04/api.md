# UC_LP04: Manage Learning Path Items - API Documentation

## Table of Contents

- [API Overview](#api-overview)
- [Authentication & Authorization](#authentication--authorization)
- [API Endpoints](#api-endpoints)
- [Business Rules Implementation](#business-rules-implementation)
- [Error Handling](#error-handling)
- [Database Operations Summary](#database-operations-summary)

## API Overview

Tài liệu này mô tả tất cả các API endpoints cần thiết để implement UC_LP04: Manage Learning Path Items. Các APIs được thiết kế để hỗ trợ đầy đủ chức năng quản lý items trong learning path bao gồm view, add, remove, reorder và manage games.

## Authentication & Authorization

**Authentication Required**: JWT Token  
**Authorization Required**: Teacher Role  
**Middleware**: Auth.middleware.js + Role.middleware.js

## API Endpoints

### 1. GET /api/learning-paths/:pathId/items
**Mục đích**: Load và hiển thị danh sách items hiện tại trong learning path

#### Input Parameters
```json
{
  "pathParams": {
    "pathId": "number - ID của learning path"
  },
  "queryParams": {
    "search": "string (optional) - Tìm kiếm theo tên item",
    "difficultyFilter": "number 1-5 (optional) - Lọc theo độ khó", 
    "statusFilter": "string: All|Active|Inactive (optional) - Lọc theo trạng thái",
    "typeFilter": "string: All|Reading|Game (optional) - Lọc theo loại",
    "categoryFilter": "number categoryId (optional) - Lọc theo category",
    "showFilter": "string: All|Active Only (optional, default: All) - Hiển thị tất cả hoặc chỉ active"
  }
}
```

#### Business Logic
1. Validate JWT token và teacher role permissions
2. Auto-apply difficulty filter = learning path's difficulty_level nếu không có override
3. Query items với joins: LearningPathItem -> KidReading/Game -> ReadingCategory
4. Group items theo category với proper sequence_order
5. Apply search/filter với single query principle (BR_6)
6. Xử lý "Active Only" filter: exclude Games có parent Reading inactive
7. Calculate children_count cho mỗi reading

#### Output Response
```json
{
  "success": true,
  "message": "MSG_1: Showing [number] items matching your search criteria | MSG_2: No items found matching current filters",
  "data": {
    "learningPath": {
      "id": "number",
      "name": "string", 
      "difficulty_level": "number"
    },
    "categories": [
      {
        "category_id": "number",
        "category_name": "string",
        "items": [
          {
            "id": "number",
            "sequence_order": "number",
            "type": "Reading|Game",
            "name": "string",
            "image_url": "string",
            "difficulty": "number",
            "is_active": "boolean",
            "children_count": "number - số games con cho reading",
            "prerequisite_reading_id": "number|null - ID reading cha cho game"
          }
        ]
      }
    ],
    "totalItems": "number",
    "filteredItems": "number"
  }
}
```

#### Database Actions
- **VIEW**: LearningPath, LearningPathItem, KidReading, Game, ReadingCategory

---

### 2. GET /api/learning-paths/:pathId/available-readings
**Mục đích**: Load readings có thể thêm vào learning path (cho modal selection)

#### Input Parameters
```json
{
  "pathParams": {
    "pathId": "number - ID của learning path"
  },
  "queryParams": {
    "categoryId": "number (optional) - Lọc theo category cụ thể cho category-specific modal",
    "search": "string (optional) - Tìm kiếm readings",
    "difficultyFilter": "number 1-5 (optional) - Lọc theo độ khó",
    "statusFilter": "string: All|Active|Inactive (optional) - Lọc theo trạng thái"
  }
}
```

#### Business Logic
1. Validate teacher permissions cho learning path
2. Load tất cả categories nếu không có categoryId (general modal)
3. Query readings NOT already in current learning path  
4. Auto-apply difficulty filter = path's difficulty_level với override option
5. Mark readings availability status: Available, In This Path, In Other Path
6. Support both general modal (all categories) và category-specific modal

#### Output Response
```json
{
  "success": true,
  "message": "Readings loaded successfully",
  "data": {
    "categories": [
      {
        "id": "number",
        "name": "string",
        "is_selected": "boolean - true nếu đây là category được chọn cho category-specific modal",
        "readings": [
          {
            "id": "number",
            "name": "string",
            "image_url": "string", 
            "difficulty": "number",
            "is_active": "boolean",
            "availability_status": "Available|In This Path|In Other Path"
          }
        ]
      }
    ],
    "totalReadings": "number"
  }
}
```

#### Database Actions
- **VIEW**: ReadingCategory, KidReading, LearningPathItem

---

### 3. POST /api/learning-paths/:pathId/items
**Mục đích**: Thêm readings vào learning path

#### Input Parameters
```json
{
  "pathParams": {
    "pathId": "number - ID của learning path"
  },
  "body": {
    "readingIds": ["number[] - Array IDs của readings cần thêm"],
    "insertPosition": "string: end|after_category|specific_position (optional) - Vị trí insert",
    "targetCategoryId": "number (optional) - Category để insert readings vào"
  }
}
```

#### Business Logic
1. Validate readings không duplicate trong path hiện tại (BR_10)
2. Check category grouping constraints (BR_3) 
3. Check maximum items limit per learning path (100 items)
4. Calculate sequence_order cho new items (BR_8)
5. Maintain category adjacency requirement
6. Show MSG_11 warning nếu difficulty khác với path difficulty_level
7. Handle transaction để đảm bảo consistency
8. Update sequence_order của existing items nếu cần

#### Output Response
```json
{
  "success": true,
  "message": "MSG_10: Learning path items updated successfully | MSG_4: Cannot add reading: would violate category grouping constraint | MSG_15: Reading already exists in this learning path | MSG_16: Maximum items limit (100) reached",
  "data": {
    "added_items": [
      {
        "reading_id": "number",
        "sequence_order": "number", 
        "category_id": "number"
      }
    ],
    "updated_sequence_orders": [
      {
        "item_id": "number",
        "new_sequence_order": "number"
      }
    ]
  }
}
```

#### Database Actions
- **CREATE**: LearningPathItem records cho new readings
- **UPDATE**: sequence_order của existing items nếu cần shift positions
- **VIEW**: Validation queries cho duplicates và category grouping constraints

---

### 4. DELETE /api/learning-paths/:pathId/items/:itemId
**Mục đích**: Remove item khỏi learning path

#### Input Parameters
```json
{
  "pathParams": {
    "pathId": "number - ID của learning path",
    "itemId": "number - ID của item cần remove"
  }
}
```

#### Business Logic
1. Check nếu item có student progress data
   - Nếu có: deactivate item (is_active = false) thay vì delete
   - Nếu không: delete hoàn toàn
2. Nếu remove reading có dependent games:
   - Update game prerequisite_reading_id (BR_7)
   - Hoặc remove games nếu không có student progress
3. Recalculate sequence_order cho remaining items (BR_8)
4. Maintain category grouping integrity (BR_3)
5. Handle transaction để đảm bảo data consistency

#### Output Response
```json
{
  "success": true,
  "message": "MSG_8: Item successfully removed from learning path | MSG_9: Reading removed - dependent game prerequisites updated automatically",
  "data": {
    "removed_item_id": "number",
    "action_taken": "deleted|deactivated",
    "affected_games": [
      {
        "game_id": "number",
        "action": "updated_prerequisite|removed|deactivated"
      }
    ],
    "updated_sequence_orders": [
      {
        "item_id": "number", 
        "new_sequence_order": "number"
      }
    ]
  }
}
```

#### Database Actions
- **DELETE**: LearningPathItem record (nếu không có student progress)
- **UPDATE**: is_active = false (nếu có student progress)
- **UPDATE**: Game prerequisite_reading_id nếu cần
- **UPDATE**: sequence_order của remaining items
- **VIEW**: StudentReading để check progress data

---

### 5. PUT /api/learning-paths/:pathId/items/reorder
**Mục đích**: Reorder items thông qua drag & drop operations

#### Input Parameters
```json
{
  "pathParams": {
    "pathId": "number - ID của learning path"
  },
  "body": {
    "draggedItemId": "number - ID của item được drag",
    "targetPosition": "number - Vị trí mục tiêu (sequence_order)",
    "dragType": "reading|game|category - Loại drag operation",
    "dragContext": {
      "sourceCategory": "number - Category ID nguồn",
      "targetCategory": "number - Category ID đích",
      "prerequisiteReadingId": "number|null - Cho game movement"
    }
  }
}
```

#### Business Logic
1. Validate movement constraints (BR_11):
   - Games chỉ move trong cùng prerequisite reading group
   - Readings có thể move giữa categories với proper grouping
   - Category movement moves toàn bộ category block
2. Check category grouping rules cho reading movement (BR_3)
3. Calculate new sequence_order cho all affected items (BR_8)
4. Handle category block movement (reading + tất cả games của nó)
5. Validate không vi phạm prerequisite relationships
6. Transaction-based update để đảm bảo consistency

#### Output Response
```json
{
  "success": true,
  "message": "MSG_5: Games reordered successfully | MSG_6: Cannot move game: must stay within same prerequisite reading group",
  "data": {
    "operation": "game_reorder|reading_move|category_move",
    "updated_sequence_orders": [
      {
        "item_id": "number",
        "new_sequence_order": "number",
        "item_type": "reading|game"
      }
    ],
    "category_changes": [
      {
        "item_id": "number",
        "old_category": "number",
        "new_category": "number"
      }
    ]
  }
}
```

#### Database Actions
- **UPDATE**: sequence_order cho multiple LearningPathItem records
- **UPDATE**: category assignments nếu có reading movement across categories

---

### 6. POST /api/learning-paths/:pathId/readings/:readingId/games
**Mục đích**: Tạo game mới với prerequisite reading specified

#### Input Parameters
```json
{
  "pathParams": {
    "pathId": "number - ID của learning path",
    "readingId": "number - ID của reading làm prerequisite"
  },
  "body": {
    "gameTemplate": "object (optional) - Template data cho game mới"
  }
}
```

#### Business Logic
1. Validate reading exists trong learning path
2. Create new game với prerequisite_reading_id = readingId
3. Calculate sequence_order để position after last game of same reading
4. Add game to learning path items với proper positioning
5. Maintain sequence order integrity (BR_8)
6. Auto-generate game template nếu không provided

#### Output Response
```json
{
  "success": true,
  "message": "MSG_3: Game successfully created and added to learning path",
  "data": {
    "game_id": "number",
    "sequence_order": "number",
    "prerequisite_reading_id": "number",
    "redirect_url": "string - URL để edit game mới tạo"
  }
}
```

#### Database Actions
- **CREATE**: Game record
- **CREATE**: LearningPathItem record cho game
- **UPDATE**: sequence_order của items sau position mới

---

### 7. GET /api/readings/:readingId/student-stats
**Mục đích**: Hiển thị thống kê học sinh cho reading specific

#### Input Parameters
```json
{
  "pathParams": {
    "readingId": "number - ID của reading cần xem stats"
  }
}
```

#### Business Logic
1. Query StudentReading table cho completion statistics
2. Calculate metrics:
   - Total students attempted
   - Completed students count
   - Completion rate percentage
   - Average scores
   - Total attempts across all students
3. Group data by learning paths nếu reading xuất hiện trong multiple paths

#### Output Response
```json
{
  "success": true,
  "message": "MSG_12: Student Statistics: [number] students completed this reading with [percentage]% success rate",
  "data": {
    "reading_info": {
      "id": "number",
      "name": "string",
      "difficulty": "number"
    },
    "statistics": {
      "total_students": "number",
      "completed_students": "number",
      "completion_rate": "number - percentage",
      "average_score": "number",
      "total_attempts": "number"
    },
    "learning_paths": [
      {
        "path_id": "number",
        "path_name": "string",
        "students_in_path": "number"
      }
    ]
  }
}
```

#### Database Actions
- **VIEW**: StudentReading, KidStudent, LearningPath tables

---

## Business Rules Implementation

### Các Business Rules được implement trong APIs:

| Business Rule ID | Rule Description | Implementation trong APIs |
|------------------|------------------|---------------------------|
| **BR_1** | Teacher authorization required | JWT + Role middleware cho tất cả endpoints |
| **BR_3** | Category grouping constraint | POST /items và PUT /reorder - validate adjacency |
| **BR_4** | Auto-filtering by difficulty level | GET /items - default filter = path difficulty |
| **BR_6** | Single query principle | GET /items - all search/filter trong 1 query |
| **BR_7** | Game prerequisite maintenance | DELETE /items - auto update prerequisite_reading_id |
| **BR_8** | Sequence order integrity | Tất cả modification APIs - auto-calculate sequence |
| **BR_10** | Reading uniqueness per path | POST /items - validate duplicates với warning |
| **BR_11** | Movement logic for games | PUT /reorder - restrict game movement rules |
| **BR_13** | Response standardization | Tất cả APIs - use MessageManager format |
| **BR_14** | Input sanitization | Middleware validation cho all inputs |

## Error Handling

### Standard Error Response Format:
```json
{
  "success": false,
  "message": "Error message theo MessageManager format",
  "error_code": "string - Mã lỗi specific",
  "details": "object (optional) - Chi tiết lỗi nếu cần"
}
```

### Common Error Scenarios:

1. **Authentication Errors**:
   - `MSG_23`: "Session expired - redirecting to login"
   - `MSG_24`: "Access denied - insufficient permissions"

2. **Validation Errors**:
   - `MSG_4`: "Cannot add reading: would violate category grouping constraint"
   - `MSG_6`: "Cannot move game: must stay within same prerequisite reading group"
   - `MSG_15`: "Reading already exists in this learning path"
   - `MSG_16`: "Maximum items limit (100) reached for this learning path"

3. **System Errors**:
   - `MSG_17`: "Operation failed due to system error - please try again"
   - `MSG_25`: "Learning path was modified by another teacher - page will refresh"
   - `MSG_26`: "Server temporarily unavailable - please try again later"

### Transaction Management:
Tất cả modification APIs (POST, PUT, DELETE) được wrapped trong database transactions:

```javascript
const transaction = await db.sequelize.transaction();
try {
  // 1. Validate business constraints
  // 2. Execute modifications
  // 3. Update sequence orders
  // 4. Update related records
  await transaction.commit();
} catch (error) {
  await transaction.rollback();
  throw error;
}
```

## Database Operations Summary

| API Endpoint | Tables Involved | Primary CRUD Operations |
|-------------|----------------|------------------------|
| `GET /items` | LearningPath, LearningPathItem, KidReading, Game, ReadingCategory | **VIEW** all |
| `GET /available-readings` | ReadingCategory, KidReading, LearningPathItem | **VIEW** all |
| `POST /items` | LearningPathItem | **CREATE** items, **UPDATE** sequences |
| `DELETE /items/:id` | LearningPathItem, Game, StudentReading | **DELETE**/deactivate, **UPDATE** prerequisites |
| `PUT /reorder` | LearningPathItem | **UPDATE** sequence orders |
| `POST /games` | Game, LearningPathItem | **CREATE** both records |
| `GET /student-stats` | StudentReading, KidStudent, LearningPath | **VIEW** all |

### Performance Considerations:
1. **Indexing**: sequence_order, learning_path_id, category_id được index
2. **Batch Operations**: Sequence order updates được batch để optimize performance
3. **Query Optimization**: Single query principle cho search/filter operations
4. **Caching**: Category data có thể cache để giảm queries
5. **Pagination**: Implement cho large item lists nếu cần

---

**Note**: Tài liệu này cung cấp specification đầy đủ cho việc implement UC_LP04 APIs. Tất cả endpoints đều tuân thủ business rules và error handling requirements được định nghĩa trong use case documentation.
