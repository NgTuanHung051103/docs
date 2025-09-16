# Quy Tắc Nghiệp Vụ Chung - AppKid Project

## 1. Validation và Error Handling

### 1.1 Validation Rules
- **Mọi dữ liệu từ CMS phải được validate tại Controller**
- **Nguyên tắc "Fail Fast":** Chỉ cần 1 lỗi validation → trả về message lỗi ngay lập tức
- **Format response lỗi thống nhất:**
  ```javascript
  return messageManager.validationFailed("resource_name", res, "Error message");
  ```

### 1.2 Transaction Management
- **Bắt buộc sử dụng Transaction cho:**
  - Tất cả action CREATE
  - Tất cả action UPDATE
  - Bất kỳ operation nào có nhiều action tạo/sửa cùng lúc
- **Pattern thực hiện:**
  ```javascript
  let transaction;
  try {
    transaction = await db.sequelize.transaction();
    // Perform operations with { transaction }
    await transaction.commit();
    return success_response;
  } catch (error) {
    if (transaction) await transaction.rollback();
    throw error;
  }
  ```

## 2. File Upload Rules

### 2.1 File Size Limits
- **Images:** Tối đa 5MB
- **Videos:** Tối đa 100MB

### 2.2 Allowed File Types
```javascript
const allowedMimeTypes = {
  image: [
    "image/jpeg", 
    "image/jpg", 
    "image/png", 
    "image/gif", 
    "image/webp"
  ],
  video: [
    "video/mp4",
    "video/avi", 
    "video/mov",
    "video/wmv",
    "video/flv",
    "video/webm",
    "video/mkv",
    "video/quicktime"
  ]
};
```

### 2.3 Storage
- **Tất cả image/file được upload lên MinIO**
- **Sử dụng helper:** `uploadToMinIO(file, folder_name)`
- **Validate file trước khi upload:** `validateKidReadingFiles(req.files)`

## 3. CMS Data Listing Rules

### 3.1 Bắt Buộc Có Các Tính Năng
Mọi API list data trong CMS phải implement đầy đủ:

1. **Search theo text**
   ```javascript
   const { searchTerm = "" } = req.body;
   // Apply to main fields (title, name, description)
   ```

2. **Filter**
   ```javascript
   const { is_active = null, category_id = null, grade_id = null } = req.body;
   // Filter theo status, category, grade, etc.
   ```

3. **Sort**
   ```javascript
   const { sorts = null } = req.body;
   // Sort theo bất kỳ field nào hiển thị
   ```

4. **Pagination**
   ```javascript
   const { pageNumb = 1, pageSize = 10 } = req.body;
   const page = parseInt(pageNumb);
   const limit = parseInt(pageSize);
   const offset = (page - 1) * limit;
   ```

### 3.2 Single Query Principle
- **Tất cả search/filter/sort/pagination phải chung 1 query duy nhất**
- **Sử dụng Sequelize findAndCountAll() hoặc repository pattern**
- **Không được gọi nhiều query riêng lẻ**

### 3.3 Response Format
```javascript
return messageManager.fetchSuccess("resource_name", {
  records: transformedRecords,
  total_record: total_record,
  total_page: total_page
}, res);
```

## 4. Học Liệu Management Rules

### 4.1 Soft Delete Policy
- **Học liệu KHÔNG ĐƯỢC XÓA VĨNH VIỄN**
- **Chỉ được Active/Deactive thông qua `is_active` field**
- **Mục đích:** Giữ lại dữ liệu để tracking student progress

### 4.2 Delete Validation
- **Trước khi deactive học liệu, phải check:**
  ```javascript
  // Check xem có student nào đã làm bài này chưa
  const hasStudentProgress = await StudentReading.findOne({
    where: { 
      kid_reading_id: reading_id,
      // hoặc learning_path_id cho learning path
    }
  });
  
  if (hasStudentProgress) {
    // Chỉ cho phép deactive, không cho xóa
    return messageManager.validationFailed("resource", res, 
      "Cannot delete: Students have already accessed this content");
  }
  ```

### 4.3 Learning Path Special Rules
- **Learning Path với student progress:** Chỉ có thể deactive
- **Learning Path Items:** Check cả reading và game dependencies
- **Game prerequisite update:** Tự động cập nhật khi xóa reading

### 4.4 Learning Path Item Management Rules

#### 4.4.1 Category Grouping Constraint
**Ràng buộc:** Các reading cùng category phải được nhóm liền kề nhau theo sequence_order.

**Ví dụ hợp lệ:**
```
✅ ĐÚNG:
- Reading 1: "Con mèo" (category: Animals, sequence_order: 1)
- Reading 2: "Con chó" (category: Animals, sequence_order: 2)  
- Reading 3: "Con gà" (category: Animals, sequence_order: 3)
- Reading 4: "Gia đình tôi" (category: Family, sequence_order: 4)
- Reading 5: "Bố mẹ tôi" (category: Family, sequence_order: 5)

❌ SAI:
- Reading 1: "Con mèo" (category: Animals, sequence_order: 1)
- Reading 2: "Gia đình tôi" (category: Family, sequence_order: 2)
- Reading 3: "Con chó" (category: Animals, sequence_order: 3) ❌ - Animals bị tách rời
```

#### 4.4.2 Reading và Game Movement Logic

**Logic 1: Game luôn đi kèm với Reading tiên quyết**
```
Quy tắc: Khi di chuyển reading, game có prerequisite_reading_id = reading đó sẽ tự động di chuyển theo
```

**Ví dụ:**
```
TRƯỚC khi di chuyển:
- Reading 6: "Màu đỏ" (sequence_order: 6)
- Game ABC: prerequisite_reading_id = 6 (sequence_order: 7)

SAU khi di chuyển Reading 6 lên vị trí 4:
- Reading 6: "Màu đỏ" (sequence_order: 4)  
- Game ABC: (sequence_order: 5) ← tự động di chuyển theo
```

**Logic 2: Di chuyển cả nhóm category**
```
Quy tắc: Khi di chuyển reading đứng đầu một category lên trước reading đầu tiên của category khác,
thì toàn bộ category sẽ được di chuyển lên trước category đích.
```

**Ví dụ:**
```
TRƯỚC khi di chuyển:
Category Family:
- Reading 4: "Gia đình tôi" (sequence_order: 4) ← đầu tiên của Family
- Reading 5: "Bố mẹ tôi" (sequence_order: 5)

Category Colors:  
- Reading 6: "Màu đỏ" (sequence_order: 6) ← đầu tiên của Colors
- Game ABC: (sequence_order: 7) ← game của Reading 6
- Reading 8: "Màu xanh" (sequence_order: 8)

THAO TÁC: Di chuyển Reading 6 lên vị trí 4 (trước Reading 4)

SAU khi di chuyển:
Category Colors (di chuyển cả nhóm):
- Reading 6: "Màu đỏ" (sequence_order: 4)
- Game ABC: (sequence_order: 5)  
- Reading 8: "Màu xanh" (sequence_order: 6)

Category Family (shift xuống):
- Reading 4: "Gia đình tôi" (sequence_order: 7)
- Reading 5: "Bố mẹ tôi" (sequence_order: 8)
```

#### 4.4.3 Movement Algorithm
**Step 1: Xác định loại di chuyển**
- Kiểm tra reading có phải đầu tiên trong category không
- Kiểm tra vị trí đích có phải đầu tiên của category khác không
- Quyết định: `MOVE_SINGLE_READING` hoặc `MOVE_ENTIRE_CATEGORY`

**Step 2: Thực hiện di chuyển**
- `MOVE_SINGLE_READING`: Chỉ di chuyển reading + game đi kèm
- `MOVE_ENTIRE_CATEGORY`: Di chuyển toàn bộ category với thứ tự nội bộ giữ nguyên

**Step 3: Validation sau di chuyển**
- Đảm bảo category grouping vẫn hợp lệ
- Kiểm tra sequence_order không bị trùng lặp
- Verify game prerequisite relationships

## 5. API Response Standards

### 5.1 Success Response
```javascript
// Single item
return messageManager.fetchSuccess("resource_name", data, res);

// List with pagination
return messageManager.fetchSuccess("resource_name", {
  records: data,
  total_record: count,
  total_page: totalPages
}, res);
```

### 5.2 Error Response
```javascript
// Validation error
return messageManager.validationFailed("resource_name", res, "Error message");

// Not found
return messageManager.notFound("resource_name", res);

// General error
return messageManager.fetchFailed("resource_name", res);
```

## 6. Database Relationships

### 6.1 Foreign Key Rules
- **Tất cả FK phải có constraint CASCADE hoặc SET NULL**
- **Soft delete:** Sử dụng `is_active` thay vì DELETE
- **Learning path dependencies:** Maintain data integrity khi deactive

### 6.2 Association Loading
- **Luôn load related data khi cần thiết**
- **Sử dụng Sequelize include để optimize query**
- **Tránh N+1 query problem**

## 7. Security Rules

### 7.1 Input Sanitization
- **Sanitize tất cả input data trước validation**
- **Handle object/array input properly**
- **Convert data types correctly**

### 7.2 File Security
- **Validate file type và size trước khi upload**
- **Không trust client-side validation**
- **Generate unique filename khi upload MinIO**

---

**Lưu ý:** Tất cả quy tắc trên là BẮT BUỘC và phải được tuân thủ nghiêm ngặt trong toàn bộ dự án để đảm bảo tính nhất quán và ổn định của hệ thống.