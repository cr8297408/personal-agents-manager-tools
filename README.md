# 🧠 Personal Agents & Skills Manager

> A curated collection of **AI agent definitions** and **reusable skills** for agentic coding assistants.

## 📁 Repository Structure

```text
personal-agents-manager-tools/
├── agents/                        # 🤖 Agent definitions (high-level, compose skills)
│   └── architecture/              # System design, ADRs, project auditing
│       ├── AGENT.md
│       ├── examples/
│       ├── resources/
│       └── scripts/
│
├── skills/                        # 🔧 Standalone, reusable skills
│   ├── skill_creator/             # Meta-skill: scaffolds new skills
│   ├── nextjs-structure/          # Next.js project scaffolding & modular architecture
│   ├── nextjs-auth-security/      # Authentication, authorization & security
│   ├── nextjs-data-flow/          # Server Actions, data fetching, DAL patterns
│   ├── nextjs-optimization/       # Core Web Vitals, SEO, performance
│   ├── nextjs-scope-rule/         # Scope Rule for component placement
│   ├── react-component-patterns/  # Advanced React patterns & composition
│   └── react-testing-qa/          # Testing with RTL, Vitest, accessibility
│
└── .gitignore
```

## 🤖 Agents vs 🔧 Skills

| Concept | Description | File |
| :--- | :--- | :--- |
| **Skill** | A focused, single-concern capability (e.g., "Set up Next.js structure") | `SKILL.md` |
| **Agent** | A high-level orchestrator that composes skills and handles cross-cutting concerns (e.g., "Architecture decisions") | `AGENT.md` |

### Key Differences

- **Skills** are **atomic** — they do one thing well.
- **Agents** are **composable** — they orchestrate multiple skills and add higher-level reasoning.
- Skills live in `skills/`, agents live in `agents/`.
- An agent can reference skills via `composed_skills` in its frontmatter.

## 🚀 Quick Start

### Using a Skill
Reference the skill path in your agentic coding assistant configuration:
```
skills/nextjs-structure/SKILL.md
```

### Using an Agent
Reference the agent path:
```
agents/architecture/AGENT.md
```

### Creating a New Skill
Use the `skill_creator` meta-skill:
```
> Create a new skill for [topic]
```
This will scaffold a complete skill following the Gold Standard template.

## 📝 Standards

### Skill Structure
```
my-skill/
├── SKILL.md       # Main instructions (required)
├── scripts/       # Helper scripts (optional)
├── examples/      # Reference implementations (optional)
└── resources/     # Templates and assets (optional)
```

### Agent Structure
```
my-agent/
├── AGENT.md       # Main agent definition (required)
├── scripts/       # Helper scripts (optional)
├── examples/      # Reference implementations (optional)
└── resources/     # Templates and assets (optional)
```

## 📄 License
Apache-2.0
