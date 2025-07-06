## Functional Requirements

### Habit Management

- **Data Model:**
  - **ID:** Unique identifier for each habit.
  - **User ID:** Foreign key reference to a registered user (see User Management).
  - **Title:** Name of the habit (e.g., “Drink Water”).
  - **Description:** Optional longer text describing the habit.
  - **Frequency:** Enum-like value representing how often the habit should be done (e.g., DAILY, WEEKLY).
  - **Target Days:** Array of days of the week (e.g., [Mon, Wed, Fri]) for the habit.
  - **Created_at:** Timestamp for habit creation.
  - **Updated_at:** Timestamp for last modification.

- **API Endpoints:**
  - **Create Habit:** Endpoint to add a new habit.
  - **Retrieve Habits:** Endpoint to list one or more habits.
    - Support filters (e.g., by user, frequency).
  - **Update Habit:** Modify title, description, frequency, etc.
  - **Delete Habit:** Remove a habit entry.
  - **Retrieve Single Habit:** Get details of a specific habit by ID.

- **Additional Considerations:**
  - Input validation (e.g., required fields, valid frequency/target days).
  - Error handling (e.g., invalid enum value, habit not found).

---

### Habit Tracking (Daily Check-ins)

- **Data Model:**
  - **ID:** Unique identifier for each habit check-in.
  - **Habit ID:** Foreign key to the associated habit.
  - **Date:** The date the habit was completed.
  - **Status:** Boolean (or enum) indicating whether the habit was completed.

- **API Endpoints:**
  - **Create Check-in:** Mark a habit as done for a specific day.
  - **Retrieve Check-ins:** List habit completions for a date or range.
  - **Update Check-in:** Modify check-in status (optional).
  - **Delete Check-in:** (Optional) Remove a check-in.

- **Additional Considerations:**
  - Prevent duplicate check-ins for the same habit and date.
  - Support statistics calculation (e.g., streaks, success rate) via service layer.

---

### User Management

- **Data Model:**
  - **ID:** Unique identifier for each user.
  - **Name:** The user's display name.
  - **Created_at:** Timestamp for user registration.

- **API Endpoints:**
  - **Create User:** Register a new user.
  - **Retrieve Users:** List all users.
  - **Update User:** Update name or profile info (if needed).
  - **Retrieve Single User:** Get user details by ID.

- **Additional Considerations:**
  - Input validation for user data.
  - Error handling (e.g., user not found, duplicate name).

---

### REST API Interface

- **CRUD Operations:**
  - All operations for habits, users, and check-ins must use RESTful endpoints.
  - Use HTTP verbs:
    - `POST` for create,
    - `GET` for retrieve,
    - `PUT`/`PATCH` for update,
    - `DELETE` for removal.

- **Data Relationships:**
  - Habits belong to users.
  - Check-ins belong to habits (and indirectly to users).

- **API Versioning & Documentation:**
  - Prefix endpoints with `/api/v1/` or similar.
  - Document all endpoints using OpenAPI/Swagger.

---

## Additional Guidelines

### 1. REST API Function Naming Conventions

- **In `HabitController`:**
  - `create`: Add a new habit.
  - `get`: Get details of a habit by ID.
  - `list`: Get a list of habits for a user.
  - `update`: Edit an existing habit.
  - `delete`: Delete a habit.

- **In `CheckinController`:**
  - `create`: Mark a habit as done.
  - `list`: List check-ins for a date range.
  - `update`: Update a check-in status.
  - `delete`: Delete a check-in.

- **In `UserController`:**
  - `create`, `get`, `list`, `update`, `delete`（必要に応じて）

---

### 2. Application Code Structure

#### a. **Models (Domain Objects)**

- `Habit`, `User`, `Checkin`
- Located in `models/`

#### b. **Controllers (Endpoint Handlers)**

- `HabitController`, `UserController`, `CheckinController`
- Located in `controllers/`

#### c. **Services (Business Logic)**

- `HabitService`, `UserService`, `CheckinService`
- Includes logic like "streak counting", "duplicate prevention"
- Located in `services/`

#### d. **Repositories (Data Access Layer)**

- `HabitRepository`, `UserRepository`, `CheckinRepository`
- Located in `repositories/` or `dao/`

#### e. **Configuration & Infrastructure**

- DB settings, DI setup, middleware, etc.
- Located in `config/`

#### f. **Utilities & Helpers**

- Date calculations, error formatter, validation helpers
- Located in `utils/` or `helpers/`

#### g. **Tests**

- Unit tests for models, services, and controllers
- Located in `tests/`

---

## Non-Functional Requirements

- **Performance & Scalability:**
  - Support concurrent usage (e.g., multiple users tracking at once).
  - Efficient querying by date ranges and user IDs.

- **Security:**
  - Even認証が未実装でも、パラメータ検証と正当性チェックを強化。
  - 将来的なログイン機能追加を見据えた設計にしておく。

- **Maintainability:**
  - モジュール分割とテスト設計により変更・追加しやすい構造に。
  - 例：新機能「通知」などを無理なく追加できるように。

- **Data Persistence:**
  - RDBMS 使用（PostgreSQL, MySQL など）、habit_id や date にインデックス付与。
  - Unitテスト中心（DB統合テストはスコープ外）
