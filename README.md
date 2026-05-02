# CPE494-agent-coding-team

This project implements a **multi-agent AI coding team** that collaborates to design, generate, and validate a real-world software application.

The system simulates a software engineering team composed of specialized AI agents, working together to build an **ASP.NET Core ERP Invoice application**.

---

## 🎯 Objective

This repository is part of the CPE494 course and is designed to:

- Teach students how **AI agents collaborate in software development**
- Demonstrate **agent orchestration workflows**
- Show how AI can assist in building a **multi-sprint, production-style application**
- Prepare students to discuss **agent-based development systems in interviews**

---

## 🧠 Multi-Agent Roles

The system is composed of the following agents:

- **Architect**
  - Analyzes requirements
  - Designs file structure
  - Produces a technical plan and task list (JSON)

- **Coder**
  - Generates source code (C#, Razor Pages, CSS)
  - Follows strict conventions (English-only UI, naming rules)

- **Logic Tester**
  - Validates code correctness
  - Checks database rules (PKs, relationships, concurrency)
  - Ensures safe transaction behavior

- **UI Auditor**
  - Validates UI against design rules
  - Enforces "Zen Green" theme
  - Checks layout consistency and usability rules

---

## ⚙️ Workflow

The system follows a structured, human-in-the-loop workflow:

1. Human provides a **Sprint Goal**
2. Architect generates:
   - `manifest.json`
   - `tasks.json`
3. Human reviews and approves the plan
4. For each task:
   - Coder generates code
   - Logic Tester reviews correctness
   - UI Auditor reviews UI/UX
5. Only code that **passes all checks** is accepted
6. Changes are applied to the target application
7. Application is built and verified (`dotnet build`)
8. Human reviews changes via Git and commits

---

## 🏗️ Target Application

This agent system generates code into a separate repository:

👉 **CPE494-erp-invoice-app**

The target app is an ASP.NET Core Razor Pages application that will be developed across multiple sprints:

- App shell and layout
- Authentication and authorization
- Product and customer management
- Invoice processing (header + line items)
- Reporting and UI polish

---

## 🧪 Technology Stack

- **Language:** Python (agent orchestration)
- **LLM:** Gemini API (Google)
- **Backend App:** ASP.NET Core (C# Razor Pages)
- **Database:** Entity Framework Core
- **Frontend:** CSS (Zen Green theme), Tabulator, Font Awesome
- **Environment:** VS Code + Dev Container
- **Version Control:** Git + GitHub

---

## 📁 Project Structure

```text
erp-agent-coding-team/
  notebooks/        # Teaching notebooks (orchestrator demo)
  prompts/          # Agent role definitions
  src/              # Core orchestration logic
  outputs/          # Generated plans, logs, artifacts
