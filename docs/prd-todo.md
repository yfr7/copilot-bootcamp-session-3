# Product Requirements Document (PRD) - Todo App Upgrade

## 1. Overview

We are upgrading the basic TODO app to support due dates, priority levels, and task filters so users can better organize and triage their tasks. The current app only supports a title and completed status. This upgrade will make the app more practical for day-to-day task management without adding unnecessary complexity.

---

## 2. MVP Scope

- **Due Date field**: Each task can have an optional due date in ISO `YYYY-MM-DD` format. Invalid values should be ignored (treated as absent).
- **Priority field**: Each task has a priority level — `P1`, `P2`, or `P3`. Defaults to `P3` if not specified.
- **Filter views**: Users can switch between three filter tabs:
  - **All** — Shows all tasks, including completed ones.
  - **Today** — Shows only incomplete tasks with a due date of today.
  - **Overdue** — Shows only incomplete tasks whose due date is in the past.
- **Data model**:
  - `title`: required
  - `priority`: `"P1" | "P2" | "P3"`, default `"P3"`
  - `dueDate`: optional, ISO `YYYY-MM-DD`
- **Storage**: Local only (no backend or external storage changes).

---

## 3. Post-MVP Scope

- **Overdue visual highlighting**: Overdue tasks are visually highlighted (e.g., red) so they stand out at a glance.
- **Priority color badges**: Color-coded badges for priority levels — red for P1, orange for P2, gray for P3.
- **Sorting rules**: Tasks are sorted in the following order:
  1. Overdue tasks first
  2. Then by priority (P1 → P2 → P3)
  3. Then by due date ascending
  4. Tasks without a due date appear last

---

## 4. Out of Scope

- Notifications
- Recurring tasks
- Multi-user support
- Keyboard navigation / accessibility enhancements
- External storage or backend changes
