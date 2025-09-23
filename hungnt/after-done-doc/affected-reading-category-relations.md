# Affected files when removing `reading_category_relations`

This document lists all places in the project that reference the `reading_category_relations` model/table and therefore will be affected if that model/table is removed. All entries are read directly from the repository (no assumptions).

Format: file path — (context) — function / route — line number (approx exact position where the reference or function is defined)

---

## Routes

- `routes/ReadingCategoryRelations.route.js` — routes exposing the relations API
  - `POST /all` -> handler: `controller.getAll` — route declared at line 6
  - `GET /:reading_id/:category_id` -> handler: `controller.getById` — route declared at line 7

- `index.js` — routes registration
  - `app.use("/api/reading-category-relations", readingCategoryRelationsRoute);` — line 38


## Controllers

- `controllers/ReadingCategoryRelations.controller.js`
  - `async function getAll(req, res)` — line 4
  - `async function getById(req, res)` — line 16

- `controllers/KidReading.controller.js` — uses `db.ReadingCategoryRelations` directly
  - `async function createKidReading(req, res)` — function declared at line 275
    - `await db.ReadingCategoryRelations.bulkCreate(categoryRelations, { transaction });` — call at line 336
  - `async function getKidReadingByCategory(req, res)` — function declared at line 459
    - `const categoryRelations = await db.ReadingCategoryRelations.findAll({ where: { category_id: category_id }, attributes: ["reading_id"] });` — call at line 479


## Repositories

- `repositories/ReadingCategoryRelations.repository.js` — repository for the model
  - `class ReadingCategoryRelationsRepository` — declaration at line 3
  - methods:
    - `async findAll()` — line 5
    - `async findById(reading_id, category_id)` — line 9
    - `async create(data)` — line 15
    - `async update(reading_id, category_id, data)` — line 18
    - `async delete(reading_id, category_id)` — line 24
    - `async findAllPaging(offset = 0, limit = 10)` — subsequent lines (~30+)

- `repositories/KidReading.repository.js` — heavily uses ReadingCategoryRelations
  - Top import: destructuring includes `ReadingCategoryRelations` — top of file (line 1-4)
  - `async findByCategory(categoryId, queryParams)` — contains `db.ReadingCategoryRelations.findAll(...)` — found at line 69
  - `async update(id, data)` — contains several interactions (function begins earlier in file)
    - `const currentRelations = await ReadingCategoryRelations.findAll({ where: { reading_id: id }, attributes: ["category_id"] });` — line 208
    - `await ReadingCategoryRelations.destroy({ where: { reading_id: id, category_id: categoriesToRemove, }, });` — line 224
    - `await ReadingCategoryRelations.bulkCreate(relations);` — line 237
  - `async delete(id)` — calls `ReadingCategoryRelations.destroy({ where: { reading_id: id } });` — line 247

- `repositories/ReadingCategory.repository.js` — references table name directly in raw SQL
  - `async findAllWithStatsAndPagination(offset, limit, searchTerm)` — contains multiple `Sequelize.literal` subqueries referencing `reading_category_relations` table (see function body in file). These appear starting around line ~46 in that file.


## Migrations

- `migrations/20250528151621-create-reading-category-relations.js` — original create migration (creates `reading_category_relations`)
- `migrations/20250719032029-update_created_at_and_updated_at.js` — alters `created_at`/`updated_at` columns for `reading_category_relations`
- `migrations/20250923120000-delete-reading-category-relations.js` — (new) migration created to drop the table. If applied, the code locations above will fail at runtime unless updated.


## Notes & Recommendations

- Removing the model/table will cause runtime errors in the controller and repository code that reference it directly (see `KidReading.controller.js` and `KidReading.repository.js`).
- You must decide one of:
  1. Remove and update all code that references this model (remove routes/controllers/repository methods, or rewrite them to use a new structure).
  2. Keep the model but mark it deprecated and avoid dropping the table until code is refactored.

If you'd like, I can prepare a patch to either:
- Remove the `ReadingCategoryRelations` API and references (safe approach but may change behavior), or
- Replace those usages with a new approach (e.g., store `category_id` on `kid_readings` or use a different relation table) — requires specification.

---

Document generated from repository source on 2025-09-23.
