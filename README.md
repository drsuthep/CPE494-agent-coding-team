# CPE494-agent-coding-team

This repository implements a **multi-agent AI coding team** that collaborates to design, generate, and validate a real-world software application.

The agents work together to build an **ASP.NET Core ERP Invoice application** in the companion repository:

👉 **CPE494-erp-invoice-app-by-ai**

---

## Objective

This project is part of the **CPE494 course** and is designed to:

- Teach how **AI agents collaborate as a development team**
- Demonstrate **agent orchestration workflows**
- Show how AI can generate and evolve a **multi-sprint application**
- Prepare students to discuss **agent-based development systems in interviews**

---

## Multi-Agent Roles

The system simulates a software engineering team composed of:

- **Architect**
  - Analyzes requirements
  - Defines structure and design
  - Produces a task plan (JSON)

- **Coder**
  - Generates source code (C#, Razor Pages, CSS)
  - Follows defined conventions and constraints

- **Logic Tester**
  - Validates correctness of code
  - Checks database rules and integrity
  - Ensures safe transaction behavior

- **UI Auditor**
  - Validates UI against design rules
  - Enforces the "Zen Green" theme
  - Checks layout and usability constraints

---

## Workflow

The system follows a structured, human-in-the-loop workflow:

1. Human provides a **Sprint Goal**
2. Architect generates:
   - `manifest.json`
   - `tasks.json`
3. Human reviews and approves the plan
4. For each task:
   - Coder generates code
   - Logic Tester validates correctness
   - UI Auditor validates UI/UX
5. Only outputs that **pass all checks** are accepted
6. Approved changes are applied to the target application
7. Application is built and verified
8. Human reviews changes via Git and commits

---

## Target Application

This agent system generates code into:

👉 **CPE494-erp-invoice-app-by-ai**

The application is developed across multiple sprints, including:

- App shell and layout
- Authentication and authorization
- Product and customer management
- Invoice processing
- Reporting and UI refinement

---

## Technology Stack

- **Language**: Python (agent orchestration)
- **LLM**: Gemini API
- **Backend App**: ASP.NET Core (Razor Pages, C#)
- **Database**: Entity Framework Core
- **Frontend**: CSS, Tabulator, Font Awesome
- **Environment**: VS Code
- **Version Control**: Git and GitHub

---

## Project Structure

```text
notebooks/      Demonstration notebooks (agent workflow)
prompts/        Agent role definitions
src/            Core orchestration logic
outputs/        Generated plans, logs, and artifacts
```

---

## Running the Agent

From the project root:

```bash
python src/orchestrator.py
```

Or run the notebook:

```text
notebooks/01_agent_demo.ipynb
```

---

## Notes for Students

- This system is intentionally implemented in **pure Python** for transparency
- You are expected to understand:
  - how agents collaborate
  - how tasks are decomposed
  - how outputs are validated before acceptance
- AI output is not guaranteed to be correct — **verification is required**

---

## Key Idea

AI can function as a structured software engineering team when properly orchestrated, constrained, and reviewed.

---

## License

For educational use in CPE494.
