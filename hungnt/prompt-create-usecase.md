Hãy tạo use case [UC_CODE]: [Use Case Name] cho nghiệp vụ [Business Function] theo cấu trúc chuẩn, bao gồm:

### 1. USE CASE DETAILS
- Primary Actors: [Actor Role]
- Secondary Actors: [If any]  
- Trigger: [Event/Action that starts the use case]
- Description: "As a [Actor], I want to [Goal/Action], so that I can [Business Value/Outcome]"
- Preconditions: [What must be true before starting]
- Postconditions: [What will be true after successful completion]

### 2. NORMAL SEQUENCE/FLOW
[Liệt kê các bước tuần tự từ 1-N, mỗi bước mô tả:
- Actor action hoặc System response
- Input/Output data
- Business logic được thực hiện
- Validation checks
- State changes]

### 3. ALTERNATIVE SEQUENCE/FLOW (nếu có)
[Các flow thay thế hoặc optional paths:
- Điều kiện trigger alternative flow
- Các bước trong alternative flow  
- Kết quả khác với normal flow]

### 4. EXCEPTION SEQUENCE/FLOW  
[Xử lý lỗi và edge cases:
- Validation failures
- System errors
- Business rule violations
- Network/connectivity issues
- User error scenarios]

### 5. MOCKUP DESIGN (nếu có UI)
[ASCII mockup hoặc mô tả UI layout:
- Screen elements
- Input fields
- Buttons and controls
- Data display areas
- Navigation elements]

### 6. UI ELEMENTS DESCRIPTION (nếu có UI)
[Chi tiết từng element:
- Input validation rules
- Button behaviors  
- Display formats
- Interactive elements
- Accessibility features]

### 7. ERROR MESSAGES & VALIDATION MESSAGES
[Danh sách messages:
- Validation error messages
- System error messages
- Success messages
- Warning messages
- When each message occurs]
- các message này sẽ xuất hiện trong step nào của normal/alternative/exception flow

### 8. BUSINESS RULES APPLIED TO [UC_CODE]
| ID | Business Rule | Description |
|----|---------------|-------------|
[Liệt kê các business rules áp dụng:
- Authentication/Authorization rules
- Data validation rules  
- Business logic constraints
- Performance requirements
- Security requirements]

### 9. TECHNICAL IMPLEMENTATION NOTES
[Technical specifications:
- API endpoints required (GET/POST/PUT/DELETE)
- Request/Response formats
- Database operations needed
- External service integrations
- Security considerations
- Performance requirements]

### 10. DIAGRAM COMPONENTS OVERVIEW
[System interaction overview:
- Sequence diagram components
- Class diagram elements  
- Data flow patterns
- Integration points]

### 11. NOTES
[Additional considerations:
- Dependencies on other use cases
- Future enhancements planned
- Known limitations
- Integration requirements]