---
clickup_workspace_id: "90182277854"
clickup_doc_id: "2kzmgppy-1898"
type: "doc"
title: "RMN Platform Overview"
---

# RMN Platform - Project Overview

> **Modern Platform Development with AI-Powered Workflows**

---

## 🎯 Project Purpose

RMN Platform is designed for efficient, AI-assisted development using the **BMAD Method** (Breakthrough Method of Agile AI-Driven Development) with integrated project management via ClickUp.

---

## 📊 Tech Stack

| Category | Technologies |
|----------|-------------|
| **Frontend** | Flutter Web |
| **Backend** | Golang, Kubernetes, OpenAPI |
| **AI Development** | BMAD Method (47 workflows), Claude Code |
| **Project Management** | ClickUp (automated sync) |
| **CI/CD** | GitHub Actions |
| **Collaboration** | Google Drive, GitHub |

---

## 🚀 Quick Start

### For New Team Members
1. **Setup**: Read [Developer Setup Guide](docs/team-processes/developer-setup-guide.md)
2. **Workflows**: Review [ClickUp Workflow Guide](docs/team-processes/clickup-workflow-guide.md)
3. **Architecture**: Explore [Tech Specs](docs/tech-specs/)

### For Quick Development
```bash
# Clone repository
git clone https://github.com/hutune/demo-structure.git
cd demo-structure

# Configure Git
git config user.name "Your Name"
git config user.email "your.email@kwayvina.com"

# Start working with BMAD
# See docs for available workflows
```

---

## 📂 Repository Structure

```
RMN/
├── .agent/                    # BMAD workflows (47 commands)
├── .claude/                   # Claude Code agents & skills
├── .github/workflows/         # GitHub Actions (ClickUp sync)
├── _bmad/                     # BMAD framework core
├── _bmad-output/              # Generated artifacts
│   ├── epics/                 # Product epics
│   ├── stories/               # User stories  
│   └── templates/             # Templates for epics, stories, reports
├── docs/                      # Documentation
│   ├── team-processes/        # Operational guides & workflows
│   ├── tech-specs/            # Architecture & technical specs
│   └── bmad_onboarding/       # BMAD framework onboarding
├── src/                       # Source code (TBD)
└── README.md                  # This file
```

---

## 🔄 Development Workflow

### 1. Planning Phase
```bash
# Create Product Requirements
@bmad-bmm-create-prd

# Design Architecture
@bmad-bmm-create-architecture

# Break down into Epics & Stories
@bmad-bmm-create-epics-and-stories
```

### 2. Implementation Phase
```bash
# Implement a story
@bmad-bmm-dev-story

# Code review
@bmad-bmm-code-review
```

### 3. Sync to ClickUp (Automatic)
```bash
# Just commit and push
git add .
git commit -m "feat: Add user authentication epic"
git push

# GitHub Actions automatically:
# - Creates/updates tasks in ClickUp
# - Syncs status, assignees, tags, dates
# - Updates comments to Activity panel
```

---

## 📖 Documentation

| Document | Purpose | Location |
|----------|---------|----------|
| **Developer Setup** | Onboarding guide for new team members | [docs/team-processes/](docs/team-processes/developer-setup-guide.md) |
| **ClickUp Workflow** | How to work with ClickUp integration | [docs/team-processes/](docs/team-processes/clickup-workflow-guide.md) |
| **Tech Specs** | Architecture & design decisions | [docs/tech-specs/](docs/tech-specs/) |
| **BMAD Onboarding** | BMAD framework deep dive | [docs/bmad_onboarding/](docs/bmad_onboarding/) |

---

## 🤝 Team

| Name | Role | Email | ClickUp ID |
|------|------|-------|------------|
| Mazh Nguyen | Developer | work.huutrung@gmail.com | 300697285 |
| Leo | Developer | leonkenzo1997@gmail.com | 107577458 |
| Eric Bae | Owner/Manager | eric@devcrane.com | 55771542 |

---

## � Key Features

### Automated ClickUp Integration
- ✅ **Auto-sync** epics & stories from Git to ClickUp
- ✅ **Full field mapping**: status, priority, dates, tags, assignees, links
- ✅ **Comments** sync to Activity panel (not description)
- ✅ **Checklists & Attachments** support
- ✅ **No manual intervention** required

### AI-Powered Development
- 47 BMAD workflows (from brainstorming to deployment)
- 10 specialized agents (PM, Architect, Dev, UX, QA, etc.)
- Continuous learning system
- Custom commands for rapid development

---

## �️ Tools & Technologies

### Development Tools
- **VS Code** with Claude Code extension
- **Git & GitHub CLI**
- **Node.js >= 18**
- **Python >= 3.9**

### Project Management
- **ClickUp** - Task management (auto-synced)
- **GitHub Actions** - CI/CD automation
- **Google Drive** - Document collaboration

---

## 📞 Support

- **Team Chat**: [Your communication channel]
- **Issues**: [GitHub Issues](https://github.com/hutune/demo-structure/issues)
- **Admin Contact**: work.huutrung@gmail.com

---

## � License

[Your License] - All Rights Reserved

---

**Last Updated**: January 29, 2026  
**Version**: 1.0  
**Maintainer**: work.huutrung@gmail.com
