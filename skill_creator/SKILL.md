---
name: skill_creator
description: >
  Specialized agentic skill designed to scaffold and generate new skills following the "Gold Standard" template.
  Trigger: "Create a new skill", "Generate skill", "Scaffold skill", "New skill template".
version: 1.0.0
author: smartcoderlabs
license: Apache-2.0
tags:
  - meta-skill
  - scaffolding
  - productivity
---

# Skill Creator

## 1. Overview
The **Skill Creator** is a meta-skill responsible for expanding the agent's capabilities by generating new, standardized skill files (`SKILL.md`). It ensures that all new skills adhere to a strict, professional structure (Frontmatter, Overview, Prerequisites, Workflow, Rules, Examples), enabling consistent behavior across the agentic system. It eliminates valid inconsistencies and enforces best practices in prompt engineering configurations.

## 2. Prerequisites & Context
*   **Required Tools**: 
    *   `write_to_file`: To save the generated skill file.
    *   `read_file`: To read the master template if available (optional, can use internal knowledge).
*   **Environment**: Any standard file system with write access.
*   **Input**: 
    *   Name of the skill to be created.
    *   Description of the skill's purpose.
    *   (Optional) Specific tools or rules the skill should include.

## 3. Workflow
1.  **Analyze Requirements**: Identify the proposed skill's name, purpose, and key tools from the user's request.
2.  **Load/Reference Template**: Use the standard "Gold Standard" skill template structure (Frontmatter -> Overview -> Prerequisites -> Workflow -> Rules -> Examples).
3.  **Draft Content**:
    *   **Frontmatter**: Populate `name`, `description`, `trigger`, and `tags`.
    *   **Overview**: Write a clear, high-level summary.
    *   **Prerequisites**: List likely tools needed for the described task.
    *   **Workflow**: outline a logical 3-5 step process for the skill.
    *   **Rules**: Define critical constraints (e.g., "Always verify...", "Use absolute paths").
    *   **Examples**: create at least one realistic "User Input" -> "Reasoning" -> "Action" interaction to guide the model.
4.  **Write File**: Save the content to the appropriate directory (e.g., `<skill_name>/SKILL.md`). Ensure a `resources/` folder is created if templates are needed.

## 4. Detailed Instructions & Rules

### Critical Rules
-   [ ] **Rule 1**: **Always** follow the strict template structure: Frontmatter, 1. Overview, 2. Prerequisites, 3. Workflow, 4. Rules, 5. Examples.
-   [ ] **Rule 2**: **Never** leave sections empty. If information is missing, infer reasonable defaults or use placeholders like `{Needs specific tool}`.
-   [ ] **Rule 3**: Ensure the `Trigger` in the frontmatter is specific and distinct to avoid overlapping with other skills.
-   [ ] **Rule 4**: The `Examples` section MUST include a realistic user prompt and the expected agent behavior/tool usage. This is crucial for few-shot learning.
-   [ ] **Rule 5**: Always use `SKILL.md` (singular, all caps) as the standard filename for the skill entry point. Structure should be: `skill_name/SKILL.md` and `skill_name/resources/` (if needed).

### Formatting Guidelines
-   **Markdown**: Use standard GFM (GitHub Flavored Markdown).
-   **Frontmatter**: YAML format between triple dashes `---`.
-   **Style**: Professional, technical, and imperative (e.g., "Run the command", not "You should run...").

## 5. Examples

### Example 1: Creating a "Git Commit" Skill
**User Input**:
> "Create a skill called 'git-expert' that handles smart git commits and branch management."

**Reasoning**:
The user wants a git management skill. I need to define the workflow (check status, add files, commit with convention) and rules (conventional commits).

**Action/Output**:
```markdown
---
name: git-expert
description: >
  Start git operations, enforcing conventional commits and clean history.
  Trigger: "commit changes", "git push", "new branch".
version: 1.0.0
...
---

# Git Expert

## 1. Overview
Handles version control operations with a focus on clean, conventional commit messages...

## 3. Workflow
1. **Check Status**: Run `git status` to see changes.
2. **Stage Files**: Use `git add` for relevant files...
```

### Example 2: Handling Ambiguous Requests
**User Input**:
> "Make a skill for checking stuff."

**Reasoning**:
The request is too vague. I cannot generate a specific workflow without knowing *what* to check (URLs? Files? APIs?).

**Action/Output**:
> "Could you specify what kind of 'checking' this skill should perform? For example, is it for checking website uptime, validating code syntax, or verifying file permissions?"