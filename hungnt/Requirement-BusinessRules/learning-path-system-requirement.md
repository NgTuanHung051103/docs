# Learning Path System - Hệ thống Quản lý Lộ trình Học tập AppKid

## TỔNG QUAN HỆ THỐNG

Hệ thống quản lý lộ trình học tập tích hợp cho phép teacher tạo và quản lý các lộ trình học bao gồm readings và games, với hệ thống từ vựng hỗ trợ, đồng thời cung cấp trải nghiệm học tập có tổ chức cho học sinh.

---

## 1. QUẢN LÝ DANH SÁCH LỘ TRÌNH (TEACHER)

### Mục đích
Hiển thị tất cả lộ trình học tập với khả năng CRU (Create, Read, Update - không có Delete)

### Thông tin hiển thị
- image, name, difficulty level, active status
- Số lượng items active có trong lộ trình (đếm từ learning_path_items với is_active = 1)

### Chức năng chi tiết

#### CREATE - Tạo mới lộ trình
- **Input:** name, description, difficulty (1-5), image upload
- **Validation:** 
  - Name không trống, unique trong hệ thống
  - Difficulty phải từ 1-5
  - Image max 5MB, upload lên MinIO
- **Default:** is_active = 1, sequence tự động

#### READ - Danh sách và tìm kiếm
- **Hiển thị:** image, name, difficulty_level, status active, items_count
- **Search:** Theo name (tìm kiếm text)
- **Filter:**
  - Độ khó (difficulty_level): 1-5
  - Status (is_active): Active/Inactive
- **Sort:** Theo name, difficulty_level, active status, items_count
- **Pagination:** pageNumb, pageSize chuẩn
- **Single Query:** Tất cả tham số search/filter/sort/pagination chung 1 query

#### UPDATE - Cập nhật lộ trình
- **Active/Deactive:** Soft delete thông qua is_active
- **Kiểm tra Student Progress:** 
  - Nếu có record trong StudentReading với learning_path_id: chỉ deactive
  - Không có student progress: có thể xóa hoàn toàn
- **Cập nhật thông tin:** name, description, difficulty, image

---

## 2. QUẢN LÝ ITEMS TRONG LỘ TRÌNH (TEACHER)

### Mục đích
Hiển thị, chỉnh sửa và quản lý items (readings và games) trong lộ trình cụ thể với giao diện table có tìm kiếm và filter

### Giao diện chính
Màn hình "Edit learning path" hiển thị table items theo category grouping với:
- Header: Order number, Image, Name, Type, Difficult, Status, Action
- Items được nhóm theo category với header category rõ ràng
- Search box và Filter dropdown ở phía trên table
- **Button "Add Reading" tổng quát** ở phía trên table để thêm reading từ bất kỳ category nào
- **Button "Add Reading"** ở cạnh tên mỗi category để thêm reading chỉ từ category cụ thể đó

### Hiển thị thông tin items trong table
- **Order number:** sequence_order của item trong lộ trình
- **Image:** Hình đại diện của reading/game
- **Name:** Tên của reading hoặc game
- **Type:** "Reading" hoặc "Game" 
- **Difficult:** difficulty_level (1-5) hoặc cấp độ khó
- **Status:** "Active" hoặc "Deactive"
- **Action:** Các button hành động tùy theo loại item

### Chức năng tìm kiếm và lọc
- **Search:** Tìm kiếm theo name (title) của readings và games
- **Filter By dropdown:**
  - Độ khó (difficulty_level): "All", "1", "2", "3", "4", "5"
  - Trạng thái (is_active): "All", "Active", "Inactive" 
  - Loại item: "All", "Reading", "Game"
  - Category: "All" + dropdown danh sách categories
- **Sort:** Theo name, difficulty_level, status, sequence_order
- **Single Query:** Tất cả search/filter/sort parameters trong 1 API call

### Action Buttons cho từng loại item

#### Actions cho Reading items:
- **View Reading:** Redirect sang màn hình chi tiết reading (sử dụng screen có sẵn)
- **View Student Stats:** Hiển thị số lượt học sinh đã làm bài reading này
- **Remove:** Xóa reading khỏi lộ trình (với validation)
- **Add Game:** Tạo game mới với prerequisite_reading_id là reading này

#### Actions cho Game items:
- **Edit Game:** Redirect sang màn hình chỉnh sửa game
- **Remove Game:** Xóa game khỏi lộ trình (với validation)

### Logic thêm game mới từ reading
- **Vị trí:** Game mới được thêm ở cuối cùng trong nhóm games cùng prerequisite_reading_id
- **Sequence order:** Tự động tính toán để đặt sau game cuối cùng của cùng reading
- **Ví dụ:**
  ```
  Reading A (seq: 1) 
  ├── Game A1 (seq: 2, prerequisite: Reading A)
  ├── Game A2 (seq: 3, prerequisite: Reading A) 
  └── Game A3 (seq: 4, prerequisite: Reading A) ← Game mới thêm vào đây
  Reading B (seq: 5)
  ```

### Drag & Drop Logic cho Games
- **Ràng buộc:** Games chỉ có thể di chuyển trong nhóm cùng prerequisite_reading_id
- **Không được:** Kéo game sang reading khác hoặc ra ngoài nhóm
- **Tự động update:** sequence_order của tất cả games trong nhóm sau khi di chuyển
- **Visual feedback:** Hiển thị drop zones hợp lệ khi đang kéo game

### Chức năng Add Reading với Modal Selection

#### 1. Button "Add Reading" Tổng Quát
- **Vị trí:** Đặt ở phía trên table, cạnh Search box và Filter dropdown
- **Chức năng:** Mở modal để thêm reading từ bất kỳ category nào vào lộ trình
- **Modal Layout:** Chia 2 phần trái-phải
  - **Bên trái:** Danh sách tất cả categories trong hệ thống
  - **Bên phải:** Danh sách readings thuộc category đang được chọn
- **Behavior:** Khi chọn category bên trái, button category đổi màu highlight và bên phải cập nhật danh sách readings tương ứng

#### 2. Button "Add Reading" Per Category  
- **Vị trí:** Đặt cạnh tên category trong category header (ví dụ: "Animals Category [+ Add Reading]")
- **Chức năng:** Mở modal để thêm reading chỉ từ category cụ thể đó
- **Modal Layout:** Tương tự modal tổng quát nhưng có sự khác biệt:
  - **Bên trái:** Chỉ hiển thị category được chọn sẵn (đã highlight, không thể chọn category khác)
  - **Bên phải:** Danh sách readings thuộc category đó
- **Behavior:** Category đã được pre-selected và không thể thay đổi

#### Giao diện Modal Add Reading
- **Structure:** Modal chia 2 cột với tỷ lệ 30%-70%
- **Bên trái - Categories List:**
  - Hiển thị danh sách categories dạng vertical list
  - Category được chọn có background highlight (màu khác)
  - Có thể scroll nếu danh sách categories dài
  - Click vào category để chọn (chỉ áp dụng với modal tổng quát)
- **Bên phải - Readings List:**
  - Hiển thị readings của category đang chọn dạng grid hoặc list
  - Mỗi reading hiển thị: image, title, difficulty_level, status
  - Có search box riêng để tìm kiếm trong readings
  - Có filter riêng cho readings (difficulty, status)
  - Pagination cho danh sách readings
- **Modal Actions:**
  - Button "Cancel": Đóng modal không làm gì
  - Button "Select": Thêm readings đã chọn vào lộ trình

### Hiển thị Category Grouping trong Table
- **Category headers:** Hiển thị tên category như "Name of category" với arrow icon
- **Items grouping:** Tất cả readings và games thuộc category được nhóm lại
- **Visual separation:** Có đường phân cách hoặc background khác nhau giữa categories
- **Add Reading button per category:** Mỗi category header có button "Add Reading" riêng

### Ràng buộc Category Grouping
- **Quy tắc chính:** Các reading cùng category phải nhóm liền kề theo sequence_order
- **Cho phép:** Nhiều category khác nhau trong cùng 1 lộ trình
- **Validation:** Không được có reading cùng category bị tách rời

**Ví dụ cấu trúc hiển thị:**
```
📁 Animals Category
├── 1. Reading: "Con mèo" (Reading, difficulty: 2, Active)
├── 2. Game: "Animal Game 1" (Game, difficulty: 2, Active) 
├── 3. Game: "Animal Game 2" (Game, difficulty: 2, Deactive)
└── 4. Reading: "Con chó" (Reading, difficulty: 2, Active)

📁 Family Category  
├── 5. Reading: "Gia đình tôi" (Reading, difficulty: 3, Active)
└── 6. Reading: "Bố mẹ tôi" (Reading, difficulty: 3, Active)
```

### Quản lý Sequence Order và Movement Logic

#### Drag & Drop với Logic Đặc biệt

**Logic 1 - Game đi kèm Reading:**
- Khi di chuyển reading, game có prerequisite_reading_id tương ứng tự động di chuyển theo
- Game luôn ở vị trí ngay sau reading tiên quyết (sequence_order + 1)

**Logic 2 - Di chuyển cả Category:**
- Khi di chuyển reading đứng đầu category lên trước reading đầu tiên của category khác
- Toàn bộ category (readings + games) di chuyển với thứ tự nội bộ giữ nguyên
- Các category khác tự động shift để duy trì grouping

**Validation Movement:**
- Sau mỗi lần di chuyển, system tự động validate category grouping
- Không cho phép di chuyển nếu vi phạm ràng buộc liền kề
- Hiển thị preview movement trước khi confirm

### Logic xóa/deactive items
- **Item đã được student làm:** Chỉ active/deactive, không xóa hoàn toàn
- **Item chưa có student:** Có thể xóa hoàn toàn
- **Khi xóa reading có game phụ thuộc:**
  - Có reading khác trước game: Set prerequisite_reading_id = reading gần nhất
  - Không có reading nào: Set prerequisite_reading_id = null

### Logic Add Reading vào Lộ Trình

#### Auto-positioning Logic
- **Reading mới:** Tự động đặt ở cuối cùng trong nhóm category tương ứng
- **Nếu category chưa có trong lộ trình:** Đặt reading ở cuối cùng của toàn bộ lộ trình
- **Sequence order calculation:** Tính toán tự động để duy trì category grouping

#### Multi-selection Support
- **Cho phép:** Chọn multiple readings cùng lúc từ modal
- **Validation:** Kiểm tra readings đã có trong lộ trình hay chưa
- **Bulk insert:** Thêm tất cả readings được chọn với sequence order phù hợp

### Logic bổ sung  
- Query LearningPathItem join các bảng liên quan để có đầy đủ thông tin display
- Count StudentReading để có student_count cho từng item
- Validate category grouping khi thực hiện các thao tác CRUD
- Check readings availability và existing paths khi hiển thị modal

---

## 3. TẠO GAME TRONG LỘ TRÌNH (TEACHER)

### Mục đích
Thêm game vào lộ trình học với quản lý từ vựng tích hợp

### Chức năng
- Tạo game mới (trạng thái deactive)
- Redirect tới trang edit game
- Tự động set sequence order khi thêm vào lộ trình
- Gán từ vựng cho game thông qua hệ thống Words

---

## 4. HỆ THỐNG QUẢN LÝ TỪ VỰNG

### Table WORDS (Kho từ vựng dùng chung)
```sql
WORDS:
- id (PK, AUTO_INCREMENT)
- word (VARCHAR(100), NOT NULL, UNIQUE) - Từ vựng
- image (VARCHAR(255), NULL) - Hình ảnh minh họa
- level (INT, NOT NULL) - Độ khó (1-5)
- definition (TEXT, NULL) - Định nghĩa
- pronunciation (VARCHAR(255), NULL) - Phiên âm
- note (TEXT, NULL) - Ghi chú bổ sung
- type (TINYINT, NOT NULL) - Loại từ: 0=noun, 1=verb, 2=adjective
- is_active (BOOLEAN, DEFAULT 1)
- created_at, updated_at (TIMESTAMP)
```

### Table GAME_WORDS (Quan hệ Many-to-Many)
```sql
GAME_WORDS:
- id (PK, AUTO_INCREMENT)
- game_id (FK -> Games.id, NOT NULL)
- word_id (FK -> Words.id, NOT NULL)
- sequence_order (INT, NOT NULL) - Thứ tự từ trong game
- created_at, updated_at (TIMESTAMP)

UNIQUE INDEX: (game_id, word_id) - Tránh từ trùng lặp
INDEX: (game_id, sequence_order) - Tối ưu query
```

### Nghiệp vụ quản lý từ vựng

#### 1. Quản lý kho từ vựng (Teacher)
- **Create:** Thêm từ với đầy đủ thông tin (word, image, level, definition, pronunciation, note, type)
- **Read:** Search/filter/sort/pagination
  - Search: word, definition
  - Filter: level (1-5), type (noun/verb/adjective), is_active
  - Sort: word, level, type, created_at, updated_at
- **Update:** Cập nhật tất cả thông tin từ
- **Soft Delete:** Deactive từ (kiểm tra usage trong games)

#### 2. Import từ vựng từ Excel (Teacher)
**Mục đích:** Nhập nhiều từ vựng cùng lúc từ file Excel

**File Excel mẫu (words_template.xlsx):**
```
| word    | level | definition           | pronunciation | note                | type |
|---------|-------|----------------------|---------------|---------------------|------|
| cat     | 1     | A small animal       | /kæt/         | Example: My cat     | 0    |
| run     | 2     | To move quickly      | /rʌn/         | Action verb         | 1    |
| beautiful| 3    | Very pretty          | /ˈbjuːtɪfəl/  | Descriptive word    | 2    |
```

**Logic Import:**
1. **Upload file Excel:** Validate format và required columns
2. **Parse data:** Đọc và validate từng row
3. **Check duplicates:** So sánh field `word` với database (case-insensitive)
4. **Response preview:**
   ```json
   {
     "total_words": 100,
     "new_words": 85,
     "duplicate_words": 15,
     "duplicates": [
       {"word": "cat", "existing_id": 123},
       {"word": "run", "existing_id": 456}
     ],
     "preview_new": [
       {"word": "beautiful", "level": 3, "type": 2}
     ]
   }
   ```
5. **Teacher action:** 
  - **Cancel:** Hủy import
  - **Proceed:** Import chỉ những từ không trùng

**Validation Rules:**
- File max 10MB
- Required columns: word, level, type
- Optional columns: definition, pronunciation, note  
- Level phải từ 1-5
- Type phải 0,1,2
- Word không được trống
- Maximum 1000 words per file

#### 3. Gán từ vào game (Teacher)
- Hiển thị danh sách từ với search/filter
- **Auto-filter theo difficulty:** Tự động filter từ vựng có `level <= learning_path.difficulty_level` của lộ trình chứa game
- Thêm/xóa từ khỏi game với sequence_order
- Hiển thị từ vựng trong game theo thứ tự
- Drag & drop sắp xếp thứ tự từ
- **Logic filter từ vựng:**
  - Lấy `difficulty_level` từ learning path chứa game hiện tại
  - Chỉ hiển thị words có `level = difficulty_level`
  - Teacher có thể override filter nếu cần thiết

#### 4. Validation rules
- Word không trống, unique
- Level từ 1-5
- Type phải 0, 1, hoặc 2
- Image max 5MB
- Không xóa word đang dùng trong game active

---

## 5. DANH SÁCH LỘ TRÌNH CHO STUDENT

### Mục đích
Student xem và chọn lộ trình học

### Chức năng
- Hiển thị lộ trình available (is_active = 1)
- Redirect tới chi tiết lộ trình khi chọn
- Hiển thị progress tổng quan của student với từng lộ trình

---

## 6. CHI TIẾT LỘ TRÌNH CHO STUDENT

### Mục đích
Hiển thị progress và items trong lộ trình với điều kiện tiên quyết tự động

### Thông tin cần có
- Danh sách items (readings + games) theo sequence
- Trạng thái đã học/chưa học của từng item
- Thông tin category cho readings
- **Logic điều kiện tiên quyết:** Bài học phía trước (bao gồm cả game nếu có) phải hoàn thành hoàn toàn mới được làm bài tiếp theo
- **Logic:** Join với StudentReading xác định progress theo lộ trình

### Điều kiện Unlock Logic (Tự động)
- **Item đầu tiên (sequence_order = 1):** Luôn unlock, có thể làm ngay
- **Các item tiếp theo:** Chỉ unlock khi TẤT CẢ items trước đó đã hoàn thành
- **Hoàn thành:** is_completed = 1 AND is_passed = 1 (đối với cả reading và game)
- **Không cần trường unlock_condition:** Logic tự động dựa trên sequence_order và progress

### Ví dụ Logic:
```
Sequence 1: Reading A (completed) → Unlock
Sequence 2: Game A (completed) → Unlock  
Sequence 3: Reading B (not completed) → Locked (vì phải chờ Game A hoàn thành)
Sequence 4: Reading C → Locked (vì phải chờ Reading B hoàn thành)
```

---

## 7. LẤY THÔNG TIN GAME

### Mục đích
Student click vào game → trả về chi tiết game với từ vựng

### Chức năng
- Query game theo ID và response
- **Check prerequisite:** Verify student đã hoàn thành TẤT CẢ items trước đó trong lộ trình (theo sequence_order)
- Trả về danh sách từ vựng trong game theo sequence_order
- **Logic:** Game chỉ accessible khi tất cả readings và games có sequence_order nhỏ hơn đều đã completed

---

## 8. CẬP NHẬT PROGRESS KHI HOÀN THÀNH ITEM

### Mục đích
Đánh dấu đã hoàn thành reading/game

### Chức năng
- **Sử dụng endpoint có sẵn:** POST /create với jwtMiddleware
- **Cập nhật:** Lưu game_id vào StudentReading, set 5 sao cho game
- **Transaction:** Sử dụng transaction cho data consistency

---

## YÊU CẦU KỸ THUẬT

### Tuân thủ Business Rules
- **Validation:** Fail fast principle, validate tất cả input tại controller
- **Transaction:** Bắt buộc cho CREATE/UPDATE operations
- **File Upload:** Image max 5MB, video max 100MB, upload lên MinIO
- **Soft Delete:** Không xóa vĩnh viễn, chỉ deactive thông qua is_active
- **Single Query:** Tất cả search/filter/sort/pagination chung 1 query

### API Standards
- **Response format:** Thống nhất sử dụng messageManager
- **Error handling:** Chuẩn với proper HTTP status codes
- **Pagination:** Với total_record, total_page, records
- **Security:** Input sanitization và file validation

### Logic Filtering Tự Động Theo Difficulty Level

#### 1. Endpoint: POST /teacher/learning-paths/:pathId/available-readings
```javascript
// Tự động áp dụng filter difficulty khi lấy readings cho lộ trình
async function getAvailableReadings(req, res) {
  const learningPath = await LearningPath.findByPk(pathId);
  const maxDifficulty = learningPath.difficulty_level;
  
  // Auto-apply difficulty filter
  const whereClause = {
    // Đổi toán tử so sánh thành bằng (=) maxDifficulty
    difficulty_level: maxDifficulty
  };
  
  // Admin vẫn có thể override bằng cách pass difficulty_level trong request
  if (req.body.difficulty_level !== undefined) {
    whereClause.difficulty_level = req.body.difficulty_level;
  }
}
```

#### 2. Endpoint: GET /teacher/games/:gameId/available-words
```javascript
// Tự động filter từ vựng theo difficulty của lộ trình chứa game
async function getAvailableWords(req, res) {
  const game = await Game.findByPk(gameId, {
    include: [{
      model: LearningPathItem,
      include: [{ model: LearningPath }]
    }]
  });
  
  const maxLevel = game.learningPathItem.learningPath.difficulty_level;
  
  const whereClause = {
    difficulty_level: maxDifficulty
    is_active: 1
  };
}
```

#### 3. Business Rules cho Difficulty Filtering
- **Default behavior:** Luôn áp dụng auto-filter theo difficulty của lộ trình
- **Override capability:** Admin có thể override bằng cách specify difficulty_level/level trong request
- **Warning system:** Hiển thị cảnh báo nếu admin cố gắng thêm item khó hơn lộ trình

---

## MOVEMENT ALGORITHM IMPLEMENTATION

### Step 1: Xác định loại di chuyển
```javascript
function determineMoveType(readingToMove, targetPosition, allItems) {
  const sourceCategory = readingToMove.category_id;
  const sourceCategoryItems = allItems.filter(item => 
    item.reading?.category_id === sourceCategory
  ).sort((a, b) => a.sequence_order - b.sequence_order);
  
  const isFirstInCategory = sourceCategoryItems[0].id === readingToMove.id;
  const targetItem = allItems.find(item => item.sequence_order === targetPosition);
  const targetCategory = targetItem?.reading?.category_id;
  
  if (isFirstInCategory && targetCategory && targetCategory !== sourceCategory) {
    const targetCategoryItems = allItems.filter(item => 
      item.reading?.category_id === targetCategory
    ).sort((a, b) => a.sequence_order - b.sequence_order);
    
    const isTargetFirstInCategory = targetCategoryItems[0].id === targetItem.id;
    
    if (isTargetFirstInCategory) {
      return 'MOVE_ENTIRE_CATEGORY';
    }
  }
  
  return 'MOVE_SINGLE_READING';
}
```

### Step 2: Thực hiện di chuyển theo loại

#### Case 1: MOVE_SINGLE_READING
```javascript
function moveSingleReading(readingId, newPosition) {
  // 1. Di chuyển reading
  updateSequenceOrder(readingId, newPosition);
  
  // 2. Tìm game có prerequisite là reading này
  const relatedGame = findGameByPrerequisite(readingId);
  if (relatedGame) {
    updateSequenceOrder(relatedGame.id, newPosition + 1);
  }
  
  // 3. Shift các items khác
  shiftOtherItemsSequence(readingId, newPosition);
}
```

#### Case 2: MOVE_ENTIRE_CATEGORY
```javascript
function moveEntireCategory(categoryId, targetPosition) {
  // 1. Lấy tất cả items của category (reading + game)
  const categoryItems = getCategoryItemsWithGames(categoryId);
  
  // 2. Di chuyển cả nhóm đến vị trí mới
  let currentPos = targetPosition;
  for (const item of categoryItems) {
    updateSequenceOrder(item.id, currentPos);
    currentPos++;
  }
  
  // 3. Shift các items khác
  shiftCategoriesSequence(categoryId, targetPosition);
}
```

### Step 3: Validation sau di chuyển
- Đảm bảo category grouping vẫn hợp lệ
- Kiểm tra sequence_order không bị trùng lặp
- Verify game prerequisite relationships

---

## DATABASE MODELS CREATED

### Models
- ✅ `Words.model.js` - Quản lý từ vựng
- ✅ `GameWords.model.js` - Quan hệ game-từ vựng
- ✅ Relationships đã được thiết lập với Game model

### Migrations
- ✅ `20250916000002-create-words.js` - Tạo table words
- ✅ `20250916000003-create-game-words.js` - Tạo table game_words

### Relationships Updated
- ✅ Game ↔ Words (Many-to-Many through GameWords)
- ✅ Indexes và constraints đã được thiết lập
- ✅ Models đã được import vào index.js

---

## MOCKUP DATA - CHI TIẾT LỘ TRÌNH CHO STUDENT

### GET /student/learning-paths/:id

#### Request
```http
GET /student/learning-paths/1
Authorization: Bearer <JWT_TOKEN>
```

#### Response Success (200)
```json
{
  "statusCode": 200,
  "message": "Learning path fetched successfully",
  "data": {
    "id": 1,
    "name": "Basic English Reading Path",
    "description": "Lộ trình học đọc tiếng Anh cơ bản dành cho trẻ em từ 6-8 tuổi",
    "image": "https://minio-url/learning_paths/path_basic_english_001.jpg",
    "difficulty_level": 2,
    "is_active": 1,
    "created_at": "2025-09-15",
    "updated_at": "2025-09-16",
    "items": [
      {
        "id": 1,
        "learning_path_id": 1,
        "reading_id": 15,
        "game_id": null,
        "sequence_order": 1,
        "is_active": 1,
        "reading": {
          "id": 15,
          "title": "The Little Cat",
          "description": "Câu chuyện về chú mèo nhỏ đáng yêu",
          "image": "https://minio-url/kid_reading/cat_story_001.jpg",
          "file": "https://minio-url/kid_reading/cat_story_001.mp4",
          "difficulty_level": 1,
          "category": { 
            "id": 3,
            "title": "Animals",
            "description": "Stories about animals",
            "image": "https://minio-url/categories/animals.jpg"
          }
        },
        "game": null,
        "student_progress": {
          "is_completed": 1,
          "score": 85,
          "star": 4.5,
          "is_passed": 1
        },
        "is_unlocked": true
      },
      {
        "id": 2,
        "learning_path_id": 1,
        "reading_id": null,
        "game_id": 1,
        "sequence_order": 2,
        "is_active": 1,
        "reading": null,
        "game": {
          "id": 1,
          "name": "Animal Matching Game",
          "description": "Ghép các con vật với tên của chúng",
          "type": 1,
          "prerequisite_reading_id": 15
        },
        "student_progress": {
          "is_completed": 1,
          "score": null,
          "star": 5.0,
          "is_passed": 1
        },
        "is_unlocked": true
      },
      {
        "id": 3,
        "learning_path_id": 1,
        "reading_id": 18,
        "game_id": null,
        "sequence_order": 3,
        "is_active": 1,
        "reading": {
          "id": 18,
          "title": "My Family",
          "description": "Bài đọc về gia đình",
          "image": "https://minio-url/kid_reading/family_story_001.jpg",
          "file": "https://minio-url/kid_reading/family_story_001.mp4",
          "difficulty_level": 2,
          "category": {
            "id": 5,
            "title": "Family",
            "description": "Stories about family",
            "image": "https://minio-url/categories/family.jpg"
          }
        },
        "game": null,
        "student_progress": {
          "is_completed": 0,
          "score": null,
          "star": null,
          "is_passed": 0
        },
        "is_unlocked": true
      },
      {
        "id": 4,
        "learning_path_id": 1,
        "reading_id": 22,
        "game_id": null,
        "sequence_order": 4,
        "is_active": 1,
        "reading": {
          "id": 22,
          "title": "Colors and Shapes",
          "description": "Học về màu sắc và hình khối",
          "image": "https://minio-url/kid_reading/colors_shapes_001.jpg",
          "file": "https://minio-url/kid_reading/colors_shapes_001.mp4",
          "difficulty_level": 2,
          "category": {
            "id": 7,
            "title": "Basic Learning", 
            "description": "Basic concepts for children",
            "image": "https://minio-url/categories/basic_learning.jpg"
          }
        },
        "game": null,
        "student_progress": {
          "is_completed": 0,
          "score": null,
          "star": null,
          "is_passed": 0
        },
        "is_unlocked": false
      },
      {
        "id": 5,
        "learning_path_id": 1,
        "reading_id": null,
        "game_id": 2,
        "sequence_order": 5,
        "is_active": 1,
        "reading": null,
        "game": {
          "id": 2,
          "name": "Color Quiz Game",
          "description": "Trả lời câu hỏi về màu sắc",
          "type": 2,
          "prerequisite_reading_id": 22
        },
        "student_progress": {
          "is_completed": 0,
          "score": null,
          "star": null,
          "is_passed": 0
        },
        "is_unlocked": false
      }
    ]
  }
}
```

### Giải thích Logic trong Mockup:

#### Trạng thái Unlock:
- **Item 1 (Reading A):** `is_unlocked: true` - Luôn unlock (đầu tiên)
- **Item 2 (Game A):** `is_unlocked: true` - Unlock vì Item 1 đã completed
- **Item 3 (Reading B):** `is_unlocked: true` - Unlock vì Item 1,2 đều completed
- **Item 4 (Reading C):** `is_unlocked: false` - Locked vì Item 3 chưa completed
- **Item 5 (Game B):** `is_unlocked: false` - Locked vì Item 3,4 chưa completed

#### Student Progress:
- **is_completed: 1** - Đã hoàn thành
- **is_passed: 1** - Đã vượt qua (đạt điều kiện)
- **score**: Điểm số 0-100 (chỉ reading)
- **star**: Số sao 1-5 (reading có thể <5, game luôn 5 nếu hoàn thành)

#### Business Rules trong Response:
1. **Unlock Logic:** Tự động dựa trên sequence_order và completion status
2. **No unlock_condition field:** Không cần lưu trữ, tính toán runtime
3. **Sequential Learning:** Phải hoàn thành tuần tự, không thể skip
4. **Both Reading + Game:** Cả reading và game đều phải completed mới unlock item tiếp

---

## DATABASE CHANGES REQUIRED

### Models và Migrations cần bỏ:
- ❌ **Bỏ trường `unlock_condition`** khỏi LearningPathItem model
- ❌ **Bỏ migration** tạo trường unlock_condition (nếu có)

### Logic thay thế:
- ✅ **Runtime calculation:** Tính toán is_unlocked dựa trên sequence_order
- ✅ **Progressive unlock:** Item chỉ unlock khi tất cả items trước completed
- ✅ **Automatic prerequisite:** Không cần setup manual, tự động theo thứ tự
---

Hệ thống Learning Path đã sẵn sàng để implement đầy đủ các API endpoints theo business requirements đã định nghĩa, với kiến trúc database hoàn chỉnh và logic nghiệp vụ rõ ràng.