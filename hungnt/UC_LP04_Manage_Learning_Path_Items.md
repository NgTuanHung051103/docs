# UC_LP04: Manage Learning Path Items

## Table of Contents

- [Use Case Details](#use-case-details)
- [Normal Sequence/Flow](#normal-sequenceflow)
- [Mockup Design](#mockup-design)
- [UI Elements Description](#ui-elements-description)
- [Error Messages & Validation Messages](#error-messages--validation-messages)
- [Business Rules Applied to UC_LP04](#business-rules-applied-to-uc_lp04)
- [Technical Implementation Notes](#technical-implementation-notes)
- [Diagram Components Overview](#diagram-components-overview)
- [Notes](#notes)

## Use Case Details

### 1. USE CASE DETAILS

**Primary Actors**
Teacher

**Secondary Actors**
System, Database

**Trigger**
The Teacher clicks on "Edit" or "Manage Items" button from a specific learning path on the Learning Paths Management screen.

**Description**
As an Teacher, I want to view, add, remove, and reorder readings and games within a specific learning path using a table interface with search/filter capabilities and modal selection, so that I can create structured learning sequences with proper category grouping and difficulty progression.

**Preconditions**
- Teacher is authenticated and has teacher role permissions
- Teacher is on Learning Paths Management screen  
- A specific learning path has been selected for item management
- Learning path exists and is accessible in the database
- Reading categories and readings are available in the system
- Games and readings have been created in the system

**Postconditions**
- Learning path items table is displayed with current items grouped by category
- Teacher can successfully add readings via general or category-specific modal
- Items are added, removed, or reordered with proper validation
- Category grouping constraints are maintained (same category readings are adjacent)
- Sequence order is automatically calculated and managed
- Game prerequisites are maintained when readings are moved or removed
- Student progress protection is enforced (items with progress only deactivated)
- Database transactions are committed successfully
- Teacher receives appropriate success/error messages for all actions

### 2. NORMAL SEQUENCE/FLOW

1. **The Teacher clicks the "Edit" or "Manage Items" button for a specific learning path.**

2. **The system navigates to "Edit Learning Path Items" screen with the following components:**
   - Page title showing learning path name and difficulty level
   - Search box and Filter dropdown at the top
   - General "Add Reading" button next to search/filter area
   - Table with headers: Order number, Image, Name, Type, Difficult, Status, Action
   - Items grouped by category with category headers containing category names and "Add Reading" buttons
  - Reading rows include a collapse/expand toggle (▶ / ▼) to hide/show their child Games; default expanded
  - Games are visually indented under their parent Reading to indicate hierarchy and have a drag handle (≡)
  - Each Reading displays a child-game counter (e.g., "Games (2)") visible even when collapsed


3. **The system automatically loads and displays current learning path items in table format:**
   - Shows all readings and games ordered by sequence_order
   - Groups items by reading categories with visual category headers (e.g., "📁 Animals Category [+ Add Reading]")
   - Displays order number, item image, name, type (Reading/Game), difficulty stars, and status
   - Shows appropriate action buttons for each item type
   - Applies drag handles (≡) for games within same prerequisite reading group

4. **The system loads search and filter functionality:**
  - Search box for searching by item name/title with real-time results
  - Filter dropdown with options: Difficulty (All, 1-5), Status (All, Active, Inactive), Type (All, Reading, Game), Category (All + category list)
  - New Filter control: Show = [ All | Active Only ]
    - `All`: display all items regardless of `is_active` state.
    - `Active Only`: display only items where `is_active = 1`. Important: when `Active Only` is selected, any Game whose parent Reading has `is_active = 0` must NOT be displayed even if the Game itself is `is_active = 1` (visibility inherits from the Reading in Active-only mode).
  - Auto-applies difficulty filter matching learning path's difficulty_level by default
  - Shows (MSG_1) with filtered results count or (MSG_2) if no items match filters

5. **The Teacher uses search/filter functionality to find specific items:**
   - Types in search box to filter by name - system shows matching items with (MSG_1)
   - Changes filter dropdown selections - system updates table display immediately
   - Can override difficulty filter to see items from other difficulty levels with (MSG_11) warning

6. **The Teacher performs item management actions by clicking action buttons:**
   - **For Reading items:** View Reading, View Student Stats, Remove, Add Game
   - **For Game items:** Edit Game, Remove Game
   - **Drag & drop:** Games can be reordered within same prerequisite reading group only,  
    -  Dragging a Reading moves the whole Reading+its Games as a block; dragging a Game is restricted to its prerequisite group


7. **When Teacher clicks general "Add Reading" button:**
   - System opens "Add Reading to Learning Path" modal (see Alternative Flow 1)
   - Modal shows all categories on left, readings on right based on selected category

8. **When Teacher clicks category-specific "Add Reading" button:**
   - System opens "Add Reading to Learning Path" modal pre-filtered for that category (see Alternative Flow 2)
   - Modal shows only selected category on left (highlighted, non-clickable), readings on right

9. **When Teacher clicks "View Reading" action:**
   - System redirects to existing reading detail screen
   - Preserves context to return to learning path management

10. **When Teacher clicks "View Student Stats" action:**
    - System displays modal/popup showing number of students who have completed this reading
    - Shows statistics: total attempts, completion rate, average scores
    - Displays (MSG_12) with student statistics

11. **When Teacher clicks "Add Game" action for a reading:**
    - System creates new game with prerequisite_reading_id set to selected reading
    - Game is positioned after last game with same prerequisite reading
    - Sequence order auto-calculated to maintain proper positioning
    - Shows (MSG_3) for successful game creation and redirects to game editing screen

12. **When Teacher clicks "Remove" action:**
    - System checks if item has student progress via StudentReading table
    - **If has student progress:** Deactivates item (is_active = 0) and shows (MSG_7)
    - **If no student progress:** Removes item completely from learning path and shows (MSG_8)
    - **If removing reading with dependent games:** Updates game prerequisite_reading_id and shows (MSG_9)
    - Updates sequence_order of remaining items to close gaps

13. **When Teacher drags and drops games within prerequisite reading group:**
    - System validates movement is within same prerequisite reading group only
    - Updates sequence_order for all affected games in that group
    - Shows (MSG_5) for successful reorder or (MSG_6) for invalid movement
    - Maintains category grouping and reading-game relationships

14. **The Teacher can continue managing items until satisfied with learning path structure.**

### 3. ALTERNATIVE SEQUENCE/FLOW

**Alternative 1 - General "Add Reading" Modal Flow:**
- **Condition:** At step 7, Teacher clicks general "Add Reading" button
- **Steps:**
  1. System opens "Add Reading to Learning Path" modal with 30%-70% left-right layout
  2. Left panel shows all available categories as vertical clickable list
  3. Right panel shows readings from first category by default
  4. Teacher clicks on different category in left panel - category highlights and right panel updates with readings
  5. Teacher can search readings in right panel search box
  6. Teacher can filter readings by difficulty and status in right panel
  7. Teacher selects multiple readings by checking checkboxes
  8. Teacher clicks "Select" button to add chosen readings to learning path
  9. System validates readings not already in path and adds them with proper sequence_order
  10. Modal closes and main table refreshes with new readings added
- **Result:** Selected readings added to learning path in appropriate category positions

**Alternative 2 - Category-Specific "Add Reading" Modal Flow:**
- **Condition:** At step 8, Teacher clicks category-specific "Add Reading" button
- **Steps:**
  1. System opens "Add Reading to Learning Path" modal with same 30%-70% layout
  2. Left panel shows only the selected category (highlighted, non-interactive)
  3. Right panel shows only readings from that specific category
  4. Teacher can search and filter readings in right panel (same as Alternative 1)
  5. Teacher selects readings and clicks "Select" button
  6. System adds readings to learning path within that category group
  7. Modal closes and table refreshes showing new readings in category
- **Result:** Selected readings added to learning path within specific category grouping

**Alternative 3 - Cancel Modal Operation:**
- **Condition:** During any modal operation, Teacher clicks "Cancel" button
- **Steps:** System closes modal without making any changes
- **Result:** Teacher returns to main items management screen with no modifications

**Alternative 4 - Override Difficulty Filter:**
- **Condition:** At step 5, Teacher wants to see items with different difficulty levels  
- **Steps:** Teacher changes difficulty filter dropdown to different level (1-5)
- **Result:** System shows items matching selected difficulty level with warning (MSG_11)

### 4. EXCEPTION SEQUENCE/FLOW

**Steps 4-5: Search/Filter Loading Errors:**
- If no items match current search/filter criteria: Display (MSG_2)
- If loading items fails due to database error: Display (MSG_17)
- If learning path has no items at all: Display (MSG_13) indicating empty learning path

**Steps 7-8: Modal Loading Errors:**
- If no categories available in system: Display (MSG_14) and disable Add Reading functionality
- If no readings available for selected category and difficulty: Display (MSG_2) in modal
- If modal fails to load due to system error: Display (MSG_17)

**Alternative Flow 1-2: Reading Addition Errors:**
- If reading already exists in current learning path: Display (MSG_15) and prevent selection
- If adding reading would violate category grouping constraints: Display (MSG_4)
- If maximum items limit per learning path exceeded: Display (MSG_16)
- If database transaction fails during addition: Display (MSG_17) and rollback

**Steps 9-10: Navigation/Action Errors:**
- If reading detail screen unavailable: Display (MSG_18) and prevent navigation
- If student statistics cannot be loaded: Display (MSG_19) but allow other operations
- If network connection lost during navigation: Display (MSG_20)

**Steps 11-13: Item Management Errors:**
- If game creation fails due to system error: Display (MSG_21)
- If trying to remove last active item from learning path: Display (MSG_22) and prevent removal
- If removal operation fails due to database constraint: Display (MSG_17)
- If drag & drop attempted outside valid zones: Display (MSG_6) and revert position

**Deletion & Deactivation Rules (finalized):**

When a Teacher attempts to delete or deactivate a Reading that has dependent Games, apply the following mandatory logic:

1) Case: Reading has NO student progress (no students ever started/completed the Reading)
  - Requirement: All dependent Games must be deleted first. Deletion flow:
    a) System will present a preview modal listing the dependent Games and require explicit Teacher confirmation to delete those Games.
    b) If Teacher confirms, system deletes the dependent Games, then deletes the Reading, then re-calculates sequence_order and validates category grouping.
    c) If Teacher cancels, no destructive action is taken.

2) Case: Reading HAS student progress (at least one student has activity on the Reading)
  - For each dependent Game:
    - TH1: If the Game has NO student progress → it may be deleted (system can perform deletion after Teacher confirmation).
    - TH2: If the Game HAS student progress → it MUST NOT be deleted; instead the Game is set to deactive (`is_active = 0`).
  - The Reading itself cannot be hard-deleted if any dependent Game remains active with student progress. In such situations the Teacher may choose to DEACTIVATE the Reading instead of deleting it.

Deactivation behavior (explicit):
  - If a Reading is DEACTIVATED (`is_active = 0`), the system will also DEACTIVATE all dependent Games automatically.
  - If a Reading is later RE-ACTIVATED (`is_active = 1`), the system will RE-ACTIVATE those dependent Games that were deactivated because of the Reading's deactivation (teacher may override individual game active states after reactivation).

Audit & Safety:
  - All deletion/deactivation operations must run inside a DB transaction and be recorded to an audit log with actor, timestamp, and a list of affected Games and final actions (deleted/deactivated/reassigned).
  - The UI must show a preview (dry-run) of resultant sequence_order changes and which games will be deleted or deactivated.

**System-level Errors:**
- If JWT token expires during session: Display (MSG_23) and redirect to login
- If teacher loses permissions during session: Display (MSG_24) and restrict access
- If concurrent modification detected (another teacher editing): Display (MSG_25) and reload current state
- If server becomes unavailable: Display (MSG_26) and provide retry option

## Mockup Design

### 5. MOCKUP DESIGN

#### Main Screen - Edit Learning Path Items (draggable blocks)

```
┌───────────────────────────────────────────────────────────────────────────────────────────────────────┐
│          Edit Learning Path Items - "Basic English Reading Path" (Difficulty: ⭐⭐⭐)                 │
├───────────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                       │
│  Filter by: [Difficulty ▼]   [+ Add Reading]   Search: [Search items by name...          ] [🔍]       │
│                                                                                                       │
├───────────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                       │
│  ➤ [DRAG]  Name of category  (Category header is a draggable block)           [+ Add Reading]         │
│  ┌─────────────────────────────────────────────────────────────────────────────────────────────────┐
│  │ [DRAG] 1. ▶ Name of reading              (Reading)    ⭐⭐    Active    [View] [Remove] [Add game]   │
│  │         Games (2)                                                                            │
│  └─────────────────────────────────────────────────────────────────────────────────────────────────┘
│  ┌─────────────────────────────────────────────────────────────────────────────────────────────────┐
│  │   [DRAG] 2. ≡   Name of game                (Game)       ⭐⭐    Deactive  [Edit] [Remove]           │
│  └─────────────────────────────────────────────────────────────────────────────────────────────────┘
│  ┌─────────────────────────────────────────────────────────────────────────────────────────────────┐
│  │   [DRAG] 3. ≡   Name of game 2              (Game)       ⭐⭐    Deactive  [Edit] [Remove]           │
│  └─────────────────────────────────────────────────────────────────────────────────────────────────┘
│                                                                                                       │
│  ➤ [DRAG]  Name of category 2 (draggable category header)                 [+ Add Reading]            │
│  ┌─────────────────────────────────────────────────────────────────────────────────────────────────┐
│  │ [DRAG] 4. ▶ Name of reading 2            (Reading)    ⭐⭐⭐   Active    [View] [Remove] [Add game]     │
│  │         Games (0)                                                                            │
│  └─────────────────────────────────────────────────────────────────────────────────────────────────┘
│                                                                                                       │
│  * Each row is a draggable div block. Category headers are also draggable to reorder category groups.  │
│  * Games are visually indented relative to their parent Reading; they show a drag handle (≡) and can  │
│    be dragged within their prerequisite reading group only.                                          │
│  * Reading rows include a collapse/expand icon (▶ collapsed / ▼ expanded) to hide/show child games.   │
│  * Reading rows show a child-game counter (e.g., "Games (2)") next to the title so teachers can see │
│    the number of child games even when the group is collapsed.                                       │
│                                                                                                       │
└───────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

#### Modal - Add Reading to Learning Path

```
┌─────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                              Add Reading to Learning Path                                               │
├─────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                         │
│ Categories (30%)            │                        Readings (Animal) (70%)                             │
│                             │                                                                            │
│ ┌───────────────────────┐   │ ┌─────────────────────────────────────────────────────────────────────┐ │
│ │ 📁 Number            │   │ │ Search: [Search readings...    ] [🔍]                              │ │
│ │ 📁 Alphabet          │   │ │ Filter: [Difficulty ▼] [Status ▼]                                 │ │
│ │ ▶ 📁 Animal (selected)│   │ │                                                                     │ │
│ │ 📁 Family            │   │ │ ┌─────────────────────────────────────────────────────────────┐   │ │
│ │                       │   │ │ │ ☐ [📖] Dog                  ⭐⭐⭐        Active    [Available] │   │ │
│ │                       │   │ │ │ ☐ [📖] Cat                  ⭐⭐⭐        Active    [Available] │   │ │
│ │                       │   │ │ │ ☐ [📖] Tiger                ⭐⭐⭐        Active    [Available] │   │ │
│ │                       │   │ │ │ ☐ [📖] Elephant             ⭐⭐⭐        Active    [Available] │   │ │
│ │                       │   │ │ │ ☐ [📖] Rabbit               ⭐⭐⭐        Active    [Available] │   │ │
│ └───────────────────────┘   │ │ └─────────────────────────────────────────────────────────────┘   │ │
│                             │ │                                                                     │ │
│                             │ │ 0 readings selected                                                     │ │
│                             │ └─────────────────────────────────────────────────────────────────────┘ │
│                                                                                                         │
├─────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                    [Cancel]        [Select]                                             │
└─────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

### 6. UI ELEMENTS DESCRIPTION

#### Main Screen Elements

**Input Fields:**
- **Search Box:** Text input for searching items by name/title with real-time filtering
- **Filter Dropdown:** Multi-option filter by Difficulty (All, 1-5), Status (All, Active, Inactive), Type (All, Reading, Game), Category (All + list)

**Buttons & Controls:**
- **Add Reading (General):** Opens modal to add readings from any category
- **Add Reading (Per Category):** Opens modal pre-filtered for specific category
- **View Reading:** Navigates to existing reading detail screen
- **View Student Stats:** Shows popup with student completion statistics  
- **Remove/Remove Game:** Removes or deactivates item from learning path
- **Add Game:** Creates new game with prerequisite reading and redirects to game editor
- **Edit Game:** Opens game editing screen for selected game
- **Drag Handles (≡):** Allows reordering games within same prerequisite reading group only

**Row Controls & Interaction:**
- **Reading Collapse/Expand Toggle (▶ / ▼):** Each Reading row includes a toggle to collapse or expand its child games. Default state: expanded. When collapsed, child Game rows are hidden but the Reading still shows a child-game counter.
- **Child-Game Counter:** Displayed next to the Reading title (e.g., "Games (2)") to indicate the number of direct child games. Counter is visible even when the Reading is collapsed.
- **Game Indentation:** Game rows are visually indented relative to their parent Reading row to indicate hierarchy. Indentation applies to layout and keyboard focus order.
- **Drag Handles for Games (≡):** Games have a dedicated drag handle and can only be dragged within their prerequisite reading group. Reading rows have a separate drag affordance that moves the whole reading+its games as a block (category movement rules still apply).

**Display Elements:**
- **Table Headers:** Order number, Image, Name, Type, Difficult (stars), Status, Action
- **Category Headers:** Expandable/collapsible sections with category name and item count
- **Order Numbers:** Sequential numbering showing item position in learning path
- **Item Images:** Thumbnails for readings and games (📖 for reading, 🎮 for game icons)
- **Type Indicators:** Clear labels showing "Reading" or "Game"
- **Difficulty Stars:** Visual representation of difficulty level (1-5 stars)
- **Status Badges:** "Active" or "Deactive" status with appropriate colors

**Visual & Accessibility Notes:**
- Ensure collapse/expand toggles are keyboard accessible (Enter/Space to toggle) and have ARIA labels (e.g., `aria-expanded`).
- Child-game counters should update immediately after add/remove/reorder actions and be returned by API as `children_count` to avoid extra queries.
- Indentation should be implemented via CSS (margin or padding) without changing DOM order to preserve sequence numbering and keyboard navigation.
- Provide a small animation when expanding/collapsing groups to improve discoverability.

#### Modal Elements

**Layout Structure:**
- **Categories Panel (30%):** Left side showing available categories as vertical clickable list
- **Readings Panel (70%):** Right side showing readings for selected category

**Categories Panel:**
- **Category List:** Vertical list of all categories with folder icons (📁)
- **Highlight State:** Selected category has different background color
- **Expand Arrows:** Visual indicators (▶) for category selection state

**Readings Panel:**
- **Search Box:** Text input for filtering readings within selected category
- **Filter Controls:** Dropdowns for Difficulty and Status filtering
- **Reading List:** Grid/list view of available readings with checkboxes for multi-selection
- **Reading Cards:** Show image, title, difficulty stars, status, and availability indicator
- **Selection Counter:** Shows "X readings selected" at bottom
- **Pagination:** Page navigation if readings list is long

**Modal Actions:**
- **Cancel Button:** Closes modal without changes
- **Select Button:** Adds selected readings to learning path and closes modal

**Status Indicators in Modal:**
- **Available:** Reading can be added to current learning path
- **In This Path:** Reading already exists in current learning path (checkbox disabled)
- **In Other Path:** Reading exists in different learning path (shows warning, checkbox enabled)

## Error Messages & Validation Messages

### 7. ERROR MESSAGES & VALIDATION MESSAGES

#### Messages
- **MSG_1:** "Showing [number] items matching your search criteria"
- **MSG_2:** "No items found matching current filters"
- **MSG_3:** "Game successfully created and added to learning path"
- **MSG_4:** "Cannot add reading: would violate category grouping constraint"
- **MSG_5:** "Games reordered successfully"
- **MSG_6:** "Cannot move game: must stay within same prerequisite reading group"
- **MSG_7:** "Item has student progress - deactivated instead of removed"
- **MSG_8:** "Item successfully removed from learning path"
- **MSG_9:** "Reading removed - dependent game prerequisites updated automatically"
- **MSG_10:** "Learning path items updated successfully"
- **MSG_11:** "Warning: Showing items with different difficulty level than learning path"
- **MSG_12:** "Student Statistics: [number] students completed this reading with [percentage]% success rate"
- **MSG_13:** "Learning path is empty - add some readings to get started"
- **MSG_14:** "No reading categories available in the system"
- **MSG_15:** "Reading already exists in this learning path"
- **MSG_16:** "Maximum items limit (100) reached for this learning path"
- **MSG_17:** "Operation failed due to system error - please try again"
- **MSG_18:** "Reading details screen is currently unavailable"
- **MSG_19:** "Could not load student statistics at this time"
- **MSG_20:** "Connection lost - please check your network and try again"
- **MSG_21:** "Failed to create game - please try again"
- **MSG_22:** "Cannot remove last active item from learning path"
- **MSG_23:** "Session expired - redirecting to login"
- **MSG_24:** "Access denied - insufficient permissions"
- **MSG_25:** "Learning path was modified by another teacher - page will refresh"
- **MSG_26:** "Server temporarily unavailable - please try again later"

#### When These Messages Occur

**MSG_1** - Displays when:
- Search/filter returns results in main table view
- Real-time feedback during search operations
- Step 4 and 5 in Normal Flow

**MSG_2** - Displays when:
- Current search/filter criteria return no items
- Empty state after applying filters
- Steps 4-5 Exception Flow

**MSG_3** - Displays when:
- Game successfully created from "Add Game" action
- Game added to learning path with proper sequence order
- Step 11 in Normal Flow

**MSG_4** - Displays when:
- Adding reading would violate category grouping constraints
- Validation fails during reading addition from modal
- Alternative Flow 1-2 Exception Flow

**MSG_5** - Displays when:
- Game drag & drop reordering completes successfully
- Games reordered within same prerequisite reading group
- Step 13 in Normal Flow

**MSG_6** - Displays when:
- Attempting to drag game outside its prerequisite reading group
- Invalid drag & drop operation blocked
- Step 13 Exception Flow

**MSG_7** - Displays when:
- Attempting to remove item that has student progress
- System deactivates instead of removing completely
- Step 12 Exception Flow

**MSG_8** - Displays when:
- Item without student progress successfully removed
- Complete removal from learning path
- Step 12 in Normal Flow

**MSG_9** - Displays when:
- Removing reading that has dependent games
- Game prerequisite_reading_id automatically updated
- Step 12 in Normal Flow

**MSG_11** - Displays when:
- Teacher changes difficulty filter to different level than learning path
- Alternative Flow 4 - override difficulty filter

**MSG_12** - Displays when:
- "View Student Stats" action executed successfully
- Student statistics loaded and displayed
- Step 10 in Normal Flow

**MSG_13** - Displays when:
- Learning path has no items to display
- Empty state on first load
- Steps 4-5 Exception Flow

**MSG_15** - Displays when:
- Attempting to select reading already in current learning path in modal
- Duplicate prevention during modal selection
- Alternative Flow 1-2 Exception Flow

**MSG_17** - Displays when:
- Database operations fail during any action
- Server errors prevent operation completion
- Throughout all flows for system errors

**MSG_20** - Displays when:
- Network connection lost during operations
- Connection timeout during API calls
- Steps 9-10 Exception Flow

**MSG_23** - Displays when:
- JWT token expires during session
- Authentication middleware blocks access
- System-level Exception Flow

**MSG_25** - Displays when:
- Another teacher modifies same learning path concurrently
- Optimistic locking detects concurrent changes
- System-level Exception Flow

## Business Rules Applied to UC_LP04

### 8. BUSINESS RULES APPLIED TO UC_LP04

| ID | Business Rule | Description | Implementation |
|----|---------------|-------------|----------------|
| **BR_1** | Teacher authorization required | Only authenticated teacher users can manage learning path items | JWT + Role middleware verification |
| **BR_2** | Transaction required for modifications | All add/remove/reorder operations must use database transaction | Sequelize transaction wrapper around save operations |
| **BR_3** | Category grouping constraint | Readings of same category must be adjacent in sequence_order | Real-time validation during add/move operations |
| **BR_4** | Auto-filtering by difficulty level | Default filter shows only items matching learning path difficulty_level | API auto-applies difficulty filter with override option |
| **BR_5** | Student progress protection | Items with student progress can only be deactivated, not removed | Check StudentReading table before removal operations |
| **BR_6** | Single query principle | All search/filter/sort/pagination in one database query | Repository pattern with comprehensive where clause |
| **BR_7** | Game prerequisite maintenance | Games must update prerequisite_reading_id when reading removed | Automatic prerequisite update logic during removal |
| **BR_8** | Sequence order integrity | No duplicate or invalid sequence_order values allowed | Validation and auto-assignment of sequence numbers |
| **BR_9** | File upload validation | Images max 5MB, proper file type validation | MinIO upload with size and type checks |
| **BR_10** | Reading uniqueness per path | No duplicate readings allowed in same learning path | Database constraint and validation check |
| **BR_11** | Movement logic for games | Games automatically follow their prerequisite readings | Intelligent movement algorithm implementation |
| **BR_12** | Category movement intelligence | Moving category head moves entire category group | Category detection and bulk movement logic |
| **BR_13** | Response message standardization | All API responses use MessageManager format | Consistent response format across all endpoints |
| **BR_14** | Input sanitization required | All user input must be sanitized and validated | Input validation middleware and sanitization |
| **BR_15** | Soft delete policy | Use is_active flag instead of permanent deletion | Database operations preserve data integrity |
| **BR_16** | Active-only visibility inheritance | When 'Show = Active Only' is selected, Games whose parent Reading is deactivated MUST be hidden even if the Game itself is active | Apply visibility rule in query layer (join condition) |

## Technical Implementation Notes

### 9. TECHNICAL IMPLEMENTATION NOTES

#### Database Operations Required

**Transaction Pattern:**
```javascript
// All modification operations wrapped in transaction
const transaction = await db.sequelize.transaction();
try {
  // 1. Validate category grouping constraints
  // 2. Check student progress for removal operations  
  // 3. Update sequence_order for affected items
  // 4. Add/remove LearningPathItem records
  // 5. Update game prerequisites if needed
  await transaction.commit();
} catch (error) {
  await transaction.rollback();
  throw error;
}
```

**Models Involved:**
- `LearningPath` - Get learning path info and difficulty_level
- `LearningPathItem` - Main table for path-reading/game relationships
- `KidReading` - Reading details for table display and modal selection  
- `ReadingCategory` - Category information for grouping and modal categories
- `StudentReading` - Check student progress before removal operations
- `Game` - Game details and prerequisite_reading_id management

**Validation Implementation:**
- **Category Grouping:** Query existing items, validate adjacency after modifications
- **Student Progress:** Join with StudentReading to check has_student_progress flag
- **Difficulty Auto-Filter:** Default where clause with difficulty_level = path.difficulty_level
- **Sequence Order:** Auto-increment logic maintaining gaps for insertions and proper game positioning
- **Show Filter (All / Active Only):** When building the query for items, apply additional join/where logic so that in `Active Only` mode the query excludes any Game whose parent Reading is deactivated (i.e., include condition: Reading.is_active = 1 AND Game.is_active = 1).

#### API Request Contracts



#### Database Operations Required

**Transaction Pattern:**
```javascript
// All modification operations wrapped in transaction
const transaction = await db.sequelize.transaction();
try {
  // 1. Validate category grouping constraints
  // 2. Check student progress for removal operations  
  // 3. Update sequence_order for affected items
  // 4. Add/remove LearningPathItem records
  // 5. Update game prerequisite_reading_id if needed
  await transaction.commit();
} catch (error) {
  await transaction.rollback();
  throw error;
}
```

**Models Involved:**
- `LearningPath` - Get learning path info and difficulty_level
- `LearningPathItem` - Main table for path-reading/game relationships
- `KidReading` - Reading details for available items selection  
- `ReadingCategory` - Category information for grouping
- `StudentReading` - Check student progress before removal
- `Game` - Game details and prerequisite_reading_id updates

**Validation Implementation:**
- **Category Grouping:** Query existing items, validate adjacency after modifications
- **Student Progress:** Join with StudentReading to check has_student_progress
- **Difficulty Auto-Filter:** Default where clause with difficulty_level = path.difficulty_level
- **Sequence Order:** Auto-increment logic maintaining gaps for insertions

#### Security & Performance Considerations

**Authentication:**
- JWT token validation on all operations
- Teacher role verification via Role middleware  
- Session timeout handling with graceful error messages

**Input Validation:**
- Sanitize all search terms and filter parameters
- Validate numeric inputs (IDs, positions, difficulty levels)
- XSS prevention for text inputs
- File type and size validation for images

**Database Performance:**
- Single query for search/filter/sort/pagination on items table
- Indexed queries on sequence_order, learning_path_id, category_id
- Batch operations for sequence_order updates during reordering
- Optimized joins for category grouping validation
- Efficient drag & drop validation without full table scans

## Diagram Components Overview

### 10. DIAGRAM COMPONENTS OVERVIEW

#### Sequence Diagram Components

**Actors & Objects:**
- Teacher (Primary user managing learning path items)
- Frontend UI (Learning path management interface)
- Auth Middleware (JWT and role validation)
- LearningPathController (Main controller handling requests)
- LearningPathRepository (Data access layer)
- Database (Sequelize models and transactions)
- MinIO Service (File storage for reading images)

**Key Interactions:**
1. Teacher → Frontend UI: Select learning path and open items management
2. Frontend UI → Auth Middleware: Validate teacher permissions for path management
3. Auth Middleware → LearningPathController: Forward authenticated request
4. LearningPathController → LearningPathRepository: Query current items and available readings
5. LearningPathRepository → Database: Execute complex queries with joins and filters
6. Database → LearningPathRepository: Return items data with category grouping info
7. LearningPathController → Frontend UI: Send formatted response with items and metadata
8. Teacher → Frontend UI: Perform add/remove/reorder operations
9. Frontend UI → LearningPathController: Send modification requests with validation
10. LearningPathController → Database: Execute transaction with constraint validation
11. Database → LearningPathController: Confirm successful modifications
12. LearningPathController → Frontend UI: Return success confirmation with updated data

#### Class Diagram Components

**Main Classes:**
- **LearningPathController** (Controller Layer)
  - Properties: Request handlers, validation logic
  - Methods: getItems(), getAvailableReadings(), addItems(), reorderItems(), removeItems()
  - Dependencies: LearningPathRepository, Auth middleware, MessageManager

- **LearningPathRepository** (Data Access Layer)  
  - Properties: Database connection, query builders
  - Methods: findItemsWithSequence(), findAvailableReadings(), validateCategoryGrouping()
  - Dependencies: Sequelize models, transaction management

- **LearningPathItem** (Entity Model)
  - Properties: id, learning_path_id, reading_id, game_id, sequence_order, is_active
  - Methods: Sequelize model methods, associations
  - Dependencies: LearningPath, KidReading, Game models

- **CategoryGroupingValidator** (Business Logic)
  - Properties: Validation rules, constraint definitions
  - Methods: validateAdjacency(), checkGroupingIntegrity(), validateMovement()
  - Dependencies: LearningPathItem queries

- **SequenceOrderManager** (Utility Class)
  - Properties: Order calculation logic
  - Methods: calculateNewPositions(), updateSequenceOrder(), shiftItems()
  - Dependencies: Transaction management

**Relationships:**
- LearningPathController uses LearningPathRepository
- LearningPathRepository manages LearningPathItem entities
- CategoryGroupingValidator validates LearningPathItem relationships
- SequenceOrderManager handles LearningPathItem ordering
- Auth Middleware protects LearningPathController endpoints
- MessageManager formats all controller responses

## Notes

### 11. NOTES

**Dependencies:**
- This use case depends on UC_LP01 (View Learning Paths) for path selection
- Prerequisites UC_LP02 (Create Learning Path) for existing paths
- Integrates with future UC_LP05 (Manage Games in Learning Path) for game operations

**Future Enhancements:**
- Bulk import of readings from Excel files with category auto-assignment
- Advanced drag & drop with visual preview of category grouping constraints
- Template-based learning path creation with predefined item structures
- AI-powered reading recommendations based on difficulty progression
- Real-time collaboration for multiple admins managing same learning path

**Known Limitations:**
  - Category movement only works for readings (games follow automatically)
- No undo functionality - all changes committed immediately on save
- Concurrent editing protection through optimistic locking only

**Integration Requirements:**
- MinIO service must be available for reading image display
- StudentReading table integration for progress protection
- Game management system integration for prerequisite updates
- Reading category management system for dropdown population

**Special Considerations:**
- Complex business logic requires extensive validation at multiple layers
- Category grouping constraint enforcement needs careful testing with edge cases
- Student progress protection is critical for data integrity and user experience
- Performance optimization needed for large item lists with search/filter functionality
- Drag & drop interface must provide clear visual feedback for valid/invalid actions
- Modal selection supports multi-select with clear status indicators for each reading
- Real-time validation prevents invalid category grouping during item addition

---

**Note:** This use case focuses on comprehensive item management within learning paths using a table-based interface with advanced modal selection. The implementation emphasizes user experience with real-time search/filter, intelligent category grouping, and robust drag & drop functionality while maintaining strict data integrity through student progress protection and category grouping constraints.