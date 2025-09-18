Hãy tạo use case [UC_CODE]: [Use Case Name] cho nghiệp vụ [Business Function] theo cấu trúc chuẩn, bao gồm:

## Use Case Details

### 1. USE CASE DETAILS
**Bắt buộc có đầy đủ các mục sau:**

**Primary Actors**
[Actor chính thực hiện use case - ví dụ: Admin, Student, Teacher]

**Secondary Actors**
[Actor phụ nếu có - ví dụ: System, External Service, hoặc ghi "None"]

**Trigger**
[Hành động cụ thể kích hoạt use case - ví dụ: "The Admin clicks on the 'Create New' button", "The user selects menu item"]

**Description**
[Mô tả theo format: "As a [Actor], I want to [specific action/goal], so that I can [business value/outcome]"]

**Preconditions**
[Điều kiện bắt buộc trước khi thực hiện - bao gồm:
- Authentication/authorization requirements
- System state requirements  
- Data availability requirements
- Screen/context requirements]

**Postconditions**
[Kết quả sau khi hoàn thành thành công - bao gồm:
- Database changes
- UI state changes
- User notifications
- Navigation/redirection
- System state changes]

### 2. NORMAL SEQUENCE/FLOW
**Liệt kê các bước tuần tự từ 1-N, mỗi bước phải có cấu trúc:**

[Số thứ tự]. **[Mô tả hành động actor hoặc system response]**

**Yêu cầu cho từng bước:**
- **Actor actions:** Bắt đầu với actor thực hiện (ví dụ: "The Admin clicks...", "The system opens...")
- **System responses:** Mô tả chi tiết system xử lý gì (validation, UI changes, data processing)
- **Input/Output:** Rõ ràng về data được nhập/hiển thị
- **Validation:** Ghi rõ validation nào được thực hiện và message codes (MSG_X)
- **Business logic:** Logic nghiệp vụ được áp dụng
- **State changes:** Thay đổi trạng thái UI hoặc database

**Ví dụ format bước:**
1. **The Admin clicks the "Create New" button on the Learning Paths Management screen.**
2. **The system opens a Create Learning Path dialog/form with the following fields:**
   - Name
   - Description
   - Difficulty Level
3. **The Admin fill the fields and click on Save button.**
4. **The system validates the form, if the error is found, it shows the respective error message.**
  - If Name is empty, show (MSG_5)
  - If Name exceeds 255 characters, show (MSG_7)
  - If Difficulty Level is not selected, show (MSG_6)
5. **If validation passes, the system attempts to save the new record to the database. **
  - If action save is successful, show (MSG_1) and close the dialog
  - If action error occurs, show (MSG_15)


### 3. ALTERNATIVE SEQUENCE/FLOW (nếu có)
**Chỉ viết nếu thực sự có alternative flows. Format:**

**Alternative [Number] - [Tên alternative flow]:**
- **Điều kiện kích hoạt:** [Khi nào alternative này xảy ra]
- **Các bước thực hiện:** [Liệt kê steps cụ thể]
- **Kết quả:** [Khác biệt với normal flow như thế nào]

**Ví dụ:**
**Alternative 1 - Cancel Operation:**
- At any step: Admin clicks "Cancel" button
- System discards all input data
- System returns to Learning Paths Management screen

### 4. EXCEPTION SEQUENCE/FLOW
**Bắt buộc liệt kê tất cả exception scenarios theo nhóm. Format:**

**Steps [X-Y]: [Tên nhóm lỗi]:**
- **Điều kiện lỗi:** [Khi nào xảy ra]
- **Message hiển thị:** [MSG_X code và nội dung]
- **Hành động system:** [System xử lý như thế nào]

**Các nhóm exception bắt buộc:**
1. **Field Validation Errors** (cho từng field input)
2. **Business Logic Errors** (duplicate, constraint violations)
3. **System-level Errors** (network, database, file upload)
4. **Authentication/Authorization Errors** (nếu có)

**Ví dụ:**
**Steps 3-4: Name Validation Errors:**
- If name is empty during real-time validation: Display (MSG5)

**Steps 11-14: System-level Errors:**
- If network connection fails: Display (MSG14)
- If the action save fails: Failed to add record. Display (MSG16)

## Mockup Design

### 5. MOCKUP DESIGN (bắt buộc nếu có UI)
**Vẽ ASCII mockup chi tiết với format chuẩn:**

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                            [Screen Title]                                           │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                     │
│  [Field Labels và Input Elements]                                                  │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │ [Input placeholders và validation indicators]                               │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                                     │
│  [Buttons và Controls]                                                             │
│                                                                                     │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                    [Action Buttons]                                                 │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

**Yêu cầu cho mockup:**
- Sử dụng ký tự box drawing (┌┐└┘├┤┬┴┼─│)
- Hiển thị chính xác layout của form/table/list
- Bao gồm tất cả input fields, buttons, và controls
- Ghi rõ required fields (dấu *)
- Hiển thị validation indicators và file upload areas
- Responsive width phù hợp (khoảng 85 ký tự)

### 6. UI ELEMENTS DESCRIPTION (bắt buộc nếu có UI)
**Mô tả chi tiết từng UI element:**

**Input Fields:**
- **[Field Name]:** [Type] input, [required/optional]

**Buttons & Controls:**  
- **[Button Name]:** [Behavior description], [what happens when clicked]

**Display Elements:**
- **[Element Name]:** [Format], [data source], [interaction behavior]


**Ví dụ:**
- **Name Field:** Required text input with validation
- **Save Button:** Creates learning path and returns to list

## Error Messages & Validation Messages

### 7. ERROR MESSAGES & VALIDATION MESSAGES
**Bắt buộc có 2 phần:**

#### Messages
**Liệt kê tất cả message codes với nội dung:**
- **MSG_1:** "[Success message text]"
- **MSG_5:** "[Validation error message]"  
- **MSG_X:** "[Other messages...]"

#### When These Messages Occur
**Giải thích chi tiết khi nào message xuất hiện:**

**MSG_X** - Hiển thị khi:
- [Điều kiện cụ thể trigger message]
- [Context hoặc step nào trong flow]
- [Validation rule nào bị vi phạm]

**Yêu cầu:**
- Phải có ít nhất MSG_1 (success), trong trường hợp fetch data thì không cần message, ngoài ra hành động thêm, sửa, thì có message thành công hoặc thất bại, MSG_14 (connection error), MSG16 (Failed to add record.), MSG15 (Record added successfully.),
- Ghi rõ step nào trong Normal/Alternative/Exception flow message xuất hiện
- Message text phải rõ ràng, user-friendly

## Business Rules Applied to [UC_CODE]

### 8. BUSINESS RULES APPLIED TO [UC_CODE]
**Format bảng bắt buộc:**

| ID | Business Rule | Description | Implementation |
|----|---------------|-------------|----------------|
| **BR_X** | [Tên rule] | [Mô tả chi tiết] | [Cách implement] |

**Các loại Business Rules bắt buộc phải có:**
- **Authentication/Authorization:** JWT, role-based access
- **Transaction Management:** Cho CREATE/UPDATE operations  
- **Validation Rules:** Field validation, format checks
- **File Upload Rules:** Size limits, format restrictions
- **Data Integrity:** Unique constraints, referential integrity
- **Performance:** Single query principle, pagination
- **Security:** Input sanitization, SQL injection prevention
- **Response Standards:** MessageManager usage

**Ví dụ:**
| **BR_1** | Transaction required for CREATE operations | All CREATE operations must use database transaction | Sequelize transaction wrapper |
| **BR_2** | Admin authorization required | Only authenticated admin users can create | Auth + Role middleware |

## Technical Implementation Notes

### 9. TECHNICAL IMPLEMENTATION NOTES
**Bắt buộc bao gồm các phần:**

#### Database Operations Required
**Liệt kê operations:**
- **Transaction pattern:** [Sequelize transaction usage]
- **Models involved:** [Which models are used]
- **Validation implementation:** [How validation is done]

#### Security & Performance Considerations
- **Authentication:** [JWT, role middleware]
- **Input validation:** [Sanitization, XSS prevention] 
- **File handling:** [Size limits, type validation]
- **Database:** [Query optimization, indexing]

## Diagram Components Overview

### 10. DIAGRAM COMPONENTS OVERVIEW
**Bắt buộc có 2 phần:**

#### Sequence Diagram Components
**Actors & Objects:**
- [Actor Name] ([Role description])
- [System Component] ([Function description])
- [Service/Database] ([Purpose])

**Key Interactions:**
1. [Actor] → [Target]: [Action description]
2. [Source] → [Target]: [Message/Data description] 
3. [Component] → [Database]: [Operation description]

#### Class Diagram Components  
**Main Classes:**
- **[ClassName]** ([Layer Type])
  - Properties: [List key properties]
  - Methods: [List key methods]
  - Dependencies: [What it depends on]

**Relationships:**
- [Class A] [relationship type] [Class B]
- [Controller] uses [Repository]
- [Middleware] protects [Controller]

**Ví dụ:**
**Actors:** Admin, Frontend UI, API Gateway, Auth Middleware, Controller, Database
**Classes:** LearningPath (Entity), LearningPathController (Controller), AuthMiddleware (Security)

---

## Notes

### 11. NOTES (tùy chọn)
**Chỉ viết khi có thông tin bổ sung quan trọng:**

- **Dependencies:** [Phụ thuộc vào use case nào khác]
- **Future enhancements:** [Tính năng sẽ mở rộng]  
- **Known limitations:** [Hạn chế đã biết]
- **Integration requirements:** [Yêu cầu tích hợp]
- **Special considerations:** [Lưu ý đặc biệt]

**Ví dụ:**
Ghi chú: Use case này tập trung vào việc tạo learning path với thông tin cơ bản. Quản lý items trong learning path sẽ được thực hiện ở các use case riêng biệt.

---

## CHẤT LƯỢNG VÀ KIỂM TRA

### Tiêu chí đánh giá Use Case hoàn chỉnh:
✅ **Completeness:** Đầy đủ 11 phần bắt buộc  
✅ **Clarity:** Mỗi bước flow rõ ràng, có message codes  
✅ **Technical Detail:** API contracts và database operations chi tiết  
✅ **UI Mockup:** ASCII mockup chính xác nếu có UI  
✅ **Error Handling:** Exception flows đầy đủ với MSG codes  
✅ **Business Rules:** Áp dụng đúng BR với implementation  
✅ **Consistency:** Format và style nhất quán theo mẫu UC_LP01, UC_LP02
