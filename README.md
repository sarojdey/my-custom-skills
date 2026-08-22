# Custom Skills

Custom skills for AI coding agents (Antigravity, Claude Code, etc.).

## Install

Run this in your project root to copy/update skills into `.agents/skills`:

```bash
npx -y degit sarojdey/my-custom-skills#master .agents/skills --force
```

## Skills

| Skill | Command | Description |
| :--- | :--- | :--- |
| **[autopsy](./autopsy)** | `autopsy`, `/autopsy` | Audit and diagnose unfamiliar/legacy code before refactoring |
| **[lets-talk](./lets-talk)** | `/lets-talk` | Read-only codebase investigation and architectural discussion |
| **[my-work-report](./my-work-report)** | `/my-work-report` | Generate daily work report for your commits |
| **[team-work-report](./team-work-report)** | `/team-work-report` | Generate branch-wide team progress and commit report |
