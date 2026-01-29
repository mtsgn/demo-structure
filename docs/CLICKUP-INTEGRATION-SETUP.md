# ClickUp Rules & Integration Configuration

> **Đọc kỹ trước khi làm việc với ClickUp!**

---

## 1. Cấu Trúc Workspace

### Space: Development Team (`90189438827`)

| Folder | ID | Mục đích |
|--------|-----|----------|
| **Planning & Backlog** | `901811729589` | Epics, Product Backlog, Bug Triage |
| **Sprint** | `901811698066` | Sprint Lists |
| **Knowledge Base** | `901811729668` | Tài liệu |

---

## 2. Lists & Statuses

### Epics / Initiatives (`901815396322`)

| Status | Markdown Value |
|--------|----------------|
| `to do` | `to-do` |
| `in progress` | `in-progress` |
| `complete` | `done` |
| `cancelled` | `cancelled` |

### Product Backlog (`901815396340`)

| Status | Markdown Value |
|--------|----------------|
| `Open` | `to-do` |
| `scoping` | `scoping` |
| `in design` | `in-design` |
| `ready for dev` | `ready-for-dev` / `in-progress` |
| `cancelled` | `cancelled` |

### Bug Triage (`901815396345`)

| Status | Markdown Value |
|--------|----------------|
| `new` | `new` |
| `checking` | `checking` |
| `fixing` | `fixing` |
| `verified` | `verified` |
| `won't fix` | `wont-fix` |

### Sprint Lists

| Status | Markdown Value |
|--------|----------------|
| `to do` | `to-do` |
| `in development` | `in-development` |
| `in review` | `in-review` |
| `testing` | `testing` |
| `shipped` | `shipped` |
| `cancelled` | `cancelled` |

---

## 3. Tags (CHỈ DÙNG TAGS CÓ SẴN)

### Technical Domain
`frontend` `backend` `mobile` `devops` `database`

### Functional Module
`auth` `payment` `reporting` `notification` `settings` `integration`

### Maintenance
`hotfix` `bug-prod` `bug-staging` `tech-debt` `refactor` `performance` `security` `accessibility`

### Special
`bmad` `epic` `story` `design` `customer-report`

> ⚠️ **KHÔNG tự tạo tags mới.** Workflow sẽ chỉ sử dụng tags từ frontmatter.

---

## 4. Templates

### Story Template
Đường dẫn: `_bmad-output/templates/story-template.md`

```yaml
---
id: "STORY-X.X"
epic_id: "EPIC-XXX"
title: ""
status: "to-do"
priority: "normal"
assigned_to: null
tags: ["backend", "auth"]   # Chỉ tags có sẵn
story_points: null
sprint: null
start_date: "2026-01-30"
due_date: "2026-02-05"
time_estimate: 6            # hours
clickup_task_id: null
---
```

### Epic Template
Đường dẫn: `_bmad-output/templates/epic-template.md`

---

## 5. PM Workflow

### Gán Task (Natural Language)

PM nói:
> "Gán STORY-3.5 cho minh@company.com, deadline 5/2, priority high, tags backend database"

AI sẽ:
1. Update frontmatter
2. Push → sync to ClickUp
3. Reply: "Đã gán STORY-3.5"

### Review Tiến Độ

PM nói:
> "Tổng hợp tiến độ sprint hiện tại"

AI sẽ scan `_bmad-output/stories/` và generate report.

---

## 6. Dev Workflow

### Update Task (Natural Language)

Dev nói:
> "Update STORY-3.5: Đã xong API endpoint, đang test. Status in-progress"

AI sẽ:
1. Update `status: "in-progress"`
2. Append vào `## Updates` section:
   ```
   **2026-01-29 14:00** - @dev: Đã xong API endpoint, đang test.
   ```
3. Push → sync

### Xem Task

Dev nói:
> "Cho tôi xem các task được gán cho tôi"

AI sẽ filter stories by `assigned_to`.

---

## 7. Assignee Mapping

| Email | ClickUp ID |
|-------|------------|
| `work.huutrung@gmail.com` | `300697285` |
| `mazhnguyen@kwayvina.com` | `300697285` |

Thêm member: Cập nhật `EMAIL_TO_CLICKUP_ID` trong workflow.

---

## 8. Admin Links

- [GitHub Actions](https://github.com/hutune/demo-structure/actions)
- [ClickUp Space](https://app.clickup.com/90182277854/v/s/90189438827)
- [Epics List](https://app.clickup.com/90182277854/v/li/901815396322)
- [Product Backlog](https://app.clickup.com/90182277854/v/li/901815396340)
- [ClickUp Rules Doc](https://app.clickup.com/90182277854/v/dc/2kzmgppy-1258/2kzmgppy-1278)
