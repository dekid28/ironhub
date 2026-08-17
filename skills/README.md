# skills

SKILL.md prompt extensions. Each skill is a directory containing a single `SKILL.md` with YAML frontmatter that the agent loads to extend its prompt.

## Skills

| Skill | Status | Description |
|---|---|---|
| `gas-fee-watchdog` | in PR (#1) | Audits a NEAR wallet's recent transactions and explains unusually high gas payments against that wallet's own baseline. Read-only; orchestrates `near-rpc` + `pikespeak`, no new tool. |

Conventions and the lifecycle for adding a new skill live in [CONTRIBUTING.md](../CONTRIBUTING.md).