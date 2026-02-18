# Epics and Stories

## MVP

### Epic: Due Date Support

- **Story: Add due date field to task creation form**

  **Acceptance Criteria**
  - The task creation form includes an optional due date input in `YYYY-MM-DD` format.
  - A task can be created without a due date.
  - Invalid date values are ignored and treated as absent (no due date).

  **Technical Requirements**
  - The `TaskForm` component already has a `dueDate` state and a date `<TextField>` input; ensure it validates the value as ISO `YYYY-MM-DD` and passes `null` for invalid values when calling `onSave`.
  - The backend `POST /api/tasks` and `PUT /api/tasks/:id` endpoints in `packages/backend/src/app.js` already accept `due_date`; add server-side validation to reject non-ISO-format strings (store `null` instead).
  - The `tasks` table in the in-memory SQLite database already has a `due_date DATE` column — no schema change needed.

- **Story: Display due date on task list items**

  **Acceptance Criteria**
  - Tasks with a due date display the formatted date in the task list.
  - Tasks without a due date do not display any date indicator.

  **Technical Requirements**
  - The `TaskList` component (`packages/frontend/src/TaskList.js`) already renders a `Chip` with `formatDueDate()` when `task.due_date` is present — verify this works correctly for `YYYY-MM-DD` strings and `null` values.

---

### Epic: Priority Field

- **Story: Add priority field with default P3**

  **Acceptance Criteria**
  - New tasks created without an explicit priority are stored with priority value `"P3"`.
  - The priority field supports only values `"P1"`, `"P2"`, `"P3"`.
  - Invalid priority values are rejected or defaulted to `"P3"`.

  **Technical Requirements**
  - Add a `priority` column (`TEXT NOT NULL DEFAULT 'P3'`) to the `tasks` table in `packages/backend/src/app.js`.
  - Update `POST /api/tasks` to accept an optional `priority` field; validate it against `["P1", "P2", "P3"]` and default to `"P3"`.
  - Update `PUT /api/tasks/:id` to accept and validate the `priority` field.
  - Update `GET /api/tasks` to include `priority` in the response.

- **Story: Add priority selector to task form**

  **Acceptance Criteria**
  - The task creation/edit form includes a priority selector with options P1, P2, and P3.
  - The default selection is P3.
  - The selected priority is submitted with the task data.

  **Technical Requirements**
  - Add a priority `<Select>` (or `<ToggleButtonGroup>`) component to `TaskForm` (`packages/frontend/src/TaskForm.js`) with options `P1`, `P2`, `P3` and default value `"P3"`.
  - Include `priority` in the payload sent to `onSave`.
  - When editing a task (`initialTask`), pre-populate the selector with the task's current priority.

- **Story: Display priority on task list items**

  **Acceptance Criteria**
  - Each task in the list displays its priority level.
  - The priority is visible at a glance alongside the task title.

  **Technical Requirements**
  - In `TaskList` (`packages/frontend/src/TaskList.js`), render the `priority` value as a `Chip` or label next to each task's title.

---

### Epic: Filter Views

- **Story: Add filter tabs (All, Today, Overdue)**

  **Acceptance Criteria**
  - The task list UI provides three filter tabs: All, Today, and Overdue.
  - The default active filter is "All".
  - Clicking a tab switches the displayed tasks immediately.

  **Technical Requirements**
  - Add a `Tabs`/`Tab` component (MUI) above the task list in `App.js` or `TaskList.js` to manage the active filter state.
  - Store the active filter in React state (e.g., `activeFilter` with values `"all"`, `"today"`, `"overdue"`).

- **Story: Implement "All" filter**

  **Acceptance Criteria**
  - The "All" filter shows every task, including completed ones.

  **Technical Requirements**
  - When `activeFilter === "all"`, display the unfiltered task list returned by `GET /api/tasks`.

- **Story: Implement "Today" filter**

  **Acceptance Criteria**
  - The "Today" filter shows only incomplete tasks whose due date matches today's date.
  - Completed tasks are excluded from this view.
  - Tasks without a due date are excluded from this view.

  **Technical Requirements**
  - Filter tasks client-side: include only tasks where `completed === 0` and `due_date === <today in YYYY-MM-DD>`.
  - Compare dates as strings in `YYYY-MM-DD` format to avoid timezone issues.

- **Story: Implement "Overdue" filter**

  **Acceptance Criteria**
  - The "Overdue" filter shows only incomplete tasks whose due date is before today.
  - Completed tasks are excluded from this view.
  - Tasks without a due date are excluded from this view.

  **Technical Requirements**
  - Filter tasks client-side: include only tasks where `completed === 0` and `due_date < <today in YYYY-MM-DD>`.
  - Compare dates as strings in `YYYY-MM-DD` format to avoid timezone issues.

---

## Post-MVP

### Epic: Visual Enhancements

- **Story: Highlight overdue tasks in red**

  **Acceptance Criteria**
  - Incomplete tasks whose due date is in the past are visually highlighted with a red indicator (e.g., red background, red border, or red text).
  - Completed tasks are not highlighted regardless of due date.

  **Technical Requirements**
  - In `TaskList` (`packages/frontend/src/TaskList.js`), add conditional styling to `ListItem`: if `task.completed === 0` and `task.due_date < today`, apply a red-tinted background or border color.

- **Story: Add color-coded priority badges**

  **Acceptance Criteria**
  - P1 tasks display a red badge.
  - P2 tasks display an orange badge.
  - P3 tasks display a gray badge.

  **Technical Requirements**
  - In `TaskList`, style the priority `Chip` with conditional `sx` or `color` prop: red (`#f44336`) for P1, orange (`#ff9800`) for P2, gray (`#9e9e9e`) for P3.

---

### Epic: Task Sorting

- **Story: Sort tasks by overdue status, priority, and due date**

  **Acceptance Criteria**
  - Overdue incomplete tasks appear first.
  - Within each group, tasks are sorted by priority: P1, then P2, then P3.
  - Within the same priority, tasks are sorted by due date ascending.
  - Tasks without a due date appear last.

  **Technical Requirements**
  - Implement a client-side sort function applied after fetching tasks in `TaskList` (or in `App.js` before passing to `TaskList`).
  - Sort comparator logic: (1) overdue & incomplete first, (2) priority rank (`P1=1, P2=2, P3=3`), (3) `due_date` ascending with nulls last.
