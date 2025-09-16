# Mockup Data - GET /student/learning-paths/:id

## Request
```http
GET /student/learning-paths/1
Authorization: Bearer <JWT_TOKEN>
Content-Type: application/json

{
  "student_id": 123
}
```

## Response Success (200)
```json
{
  "statusCode": 200,
  "message": "learningpath fetched successfully",
  "data": {
    "id": 1,
    "name": "Basic English Reading Path",
    "description": "Lộ trình học đọc tiếng Anh cơ bản dành cho trẻ em từ 6-8 tuổi. Bao gồm các bài đọc đơn giản và game tương tác.",
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
        "unlock_condition": 0,
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
        }
      },
      {
        "id": 2,
        "learning_path_id": 1,
        "reading_id": null,
        "game_id": 1,
        "sequence_order": 2,
        "unlock_condition": 1,
        "is_active": 1,
        "reading": null,
        "game": {
          "id": 1,
          "name": "Animal Matching Game",
          "description": "Ghép các con vật với tên của chúng",
          "type": 1,
          "prerequisiteReading": {
            "id": 15,
            "title": "The Little Cat"
          }
        },
        "student_progress": {
          "is_completed": 1,
          "score": null,
          "star": 5.0,
          "is_passed": 1
        }
      },
      {
        "id": 3,
        "learning_path_id": 1,
        "reading_id": 18,
        "game_id": null,
        "sequence_order": 3,
        "unlock_condition": 1,
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
        }
      },
      {
        "id": 4,
        "learning_path_id": 1,
        "reading_id": 22,
        "game_id": null,
        "sequence_order": 4,
        "unlock_condition": 1,
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
        }
      },
      {
        "id": 5,
        "learning_path_id": 1,
        "reading_id": null,
        "game_id": 2,
        "sequence_order": 5,
        "unlock_condition": 1,
        "is_active": 1,
        "reading": null,
        "game": {
          "id": 2,
          "name": "Color Quiz Game",
          "description": "Trả lời câu hỏi về màu sắc",
          "type": 2,
          "prerequisiteReading": {
            "id": 22,
            "title": "Colors and Shapes"
          }
        },
        "student_progress": {
          "is_completed": 0,
          "score": null,
          "star": null,
          "is_passed": 0
        }
      }
    ]
  }
}
```

## Response Error - Not Found (404)
```json
{
  "statusCode": 404,
  "message": "learningpath not found",
  "data": null
}
```

## Response Error - Validation Failed (400)
```json
{
  "statusCode": 400,
  "message": "Student ID is required",
  "data": null
}
```

## Response Error - Unauthorized (401)
```json
{
  "statusCode": 401,
  "message": "Unauthorized access",
  "data": null
}
```

## Giải thích Student Progress:

### Trạng thái học tập:
- **is_completed: 1** - Đã hoàn thành
- **is_completed: 0** - Chưa hoàn thành
- **is_passed: 1** - Đã vượt qua (đạt điều kiện)
- **is_passed: 0** - Chưa vượt qua (chưa đạt điều kiện)

### Điểm số và sao:
- **score**: Điểm số từ 0-100 (chỉ có cho reading)
- **star**: Số sao từ 1-5 (reading có thể < 5, game luôn 5 sao nếu hoàn thành)
- **null**: Chưa có dữ liệu (chưa làm bài)

### Unlock Logic:
- **unlock_condition: 0** - Không khóa, có thể làm ngay
- **unlock_condition: 1** - Có khóa, cần hoàn thành bài trước đó
- Items được sắp xếp theo **sequence_order**

### Business Rules trong Response:
1. Student chỉ thấy learning path **is_active = 1**
2. Student chỉ thấy items **is_active = 1**
3. Progress được tính theo **student_id** và **learning_path_id**
4. Game có **prerequisite_reading_id** sẽ hiển thị thông tin reading tiên quyết
5. Items được sort theo **sequence_order** ASC