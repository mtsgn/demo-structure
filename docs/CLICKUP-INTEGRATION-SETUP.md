# ClickUp Integration - Complete Configuration

## Quick Start (Developers)

```bash
git clone https://github.com/hutune/demo-structure.git
cd demo-structure
git pull origin main
# Edit files in _bmad-output/
git add . && git commit -m "Update story" && git push
# → ClickUp auto-syncs!
```

---

## ClickUp Space Architecture

### Space: Development Team
**ID**: `90189438827`

### Folders Structure

| Folder | ID | Purpose |
|--------|-----|---------|
| **Planning & Backlog** | `901811729589` | Epics và Product Backlog |
| **Sprint** | `901811698066` | Sprint lists |
| **Knowledge Base** | `901811729668` | Documentation |

### Lists (Sync Target)

| List | ID | Synced From |
|------|-----|-------------|
| **Epics / Initiatives** | `901815396322` | `_bmad-output/epics/*.md` |
| **Product Backlog** | `901815396340` | `_bmad-output/stories/*.md` |
| **Bug Triage** | `901815396345` | *(not synced)* |

### Sprint Lists

| Sprint | ID | Dates |
|--------|-----|-------|
| Sprint 1 | `901815360889` | 1/26 - 2/8 |
| Sprint 2 | `901815360910` | 2/9 - 2/22 |

---

## Status Mappings

### Epics / Initiatives List

| Order | ClickUp Status | Type | Markdown Value |
|-------|----------------|------|----------------|
| 0 | `to do` | open | `to-do` |
| 1 | `in progress` | custom | `in-progress` |
| 2 | `complete` | done | `done` |
| 3 | `cancelled` | closed | `cancelled` |

### Product Backlog List

| Order | ClickUp Status | Type | Markdown Value |
|-------|----------------|------|----------------|
| 0 | `Open` | open | `to-do` |
| 1 | `scoping` | custom | `scoping` |
| 2 | `in design` | custom | `in-design` |
| 3 | `ready for dev` | custom | `in-progress` |
| 4 | `cancelled` | closed | `done` |

### Space-Level Statuses (Reference)

Full list of statuses available at space level:
- `backlog` → `scoping` → `in design` → `in development` → `in review` → `testing` → `ready for development` → `shipped` → `cancelled`

---

## Markdown Frontmatter Format

### Epic

```yaml
---
id: "EPIC-001"
title: "User Authentication"
status: "to-do"           # to-do | in-progress | done | cancelled
priority: "high"
assigned_to: null
created_at: "2026-01-29"
updated_at: "2026-01-29"
clickup_task_id: null     # Auto-filled by workflow
---
```

### Story

```yaml
---
id: "STORY-1.1"
epic_id: "EPIC-001"       # Links to parent epic
title: "Login Page"
status: "to-do"           # to-do | scoping | in-design | in-progress | done
assigned_to: null
story_points: 5
sprint: "Sprint 1"
created_at: "2026-01-29"
updated_at: "2026-01-29"
clickup_task_id: null     # Auto-filled by workflow
---
```

---

## GitHub Workflow Configuration

### Required Secret

**Name**: `CLICKUP_API_KEY`  
**Value**: Your ClickUp API token (`pk_xxx...`)

Location: Repository → Settings → Secrets → Actions

### List IDs (in `.github/workflows/sync-clickup.yml`)

```yaml
env:
  CLICKUP_EPICS_LIST_ID: "901815396322"
  CLICKUP_STORIES_LIST_ID: "901815396340"
```

---

## How It Works

```
Developer edits _bmad-output/*.md
        ↓
git push to main
        ↓
GitHub Action triggers (path filter)
        ↓
Workflow parses frontmatter
        ↓
IF clickup_task_id is null:
   → CREATE new task
   → Commit ID back to repo
ELSE:
   → UPDATE existing task
        ↓
ClickUp task synced!
```

---

## Troubleshooting

### "refusing to allow OAuth App to create workflow"

**Fix**: Use Personal Access Token with `repo` + `workflow` scopes.

### "exit code 128"

**Fix**: Already handled with:
```yaml
permissions:
  contents: write
```

### Task not creating

**Check**:
1. File is in `_bmad-output/epics/` or `_bmad-output/stories/`
2. File ends with `.md`
3. Frontmatter has `clickup_task_id: null`

### Status not updating

**Check**: Status value matches mappings above (case-sensitive).

---

## Admin Links

- **GitHub Actions**: https://github.com/hutune/demo-structure/actions
- **ClickUp Space**: https://app.clickup.com/90182277854/v/s/90189438827
- **Epics List**: https://app.clickup.com/90182277854/v/li/901815396322
- **Product Backlog**: https://app.clickup.com/90182277854/v/li/901815396340
