# claude-skills

> Reverse-engineering open-source AI tools into Claude-compatible SKILL.md files.
> Created and maintained by [Abdelhamid Fahim](https://github.com/AboMalek1986)

---

## The idea

Most open-source AI tools have great functionality buried inside their code. This repo extracts that functionality into simple Markdown instruction files — called SKILL.md files — that any Claude agent can read and use immediately. No installation, no dependencies, just drop the file in and go.

This started when I noticed that [Feynman](https://github.com/getcompanion-ai/feynman) — an open-source AI research agent — ships its capabilities as Pi skill files. I reverse-engineered those skills into a Claude-compatible format. That felt useful enough to turn into a pattern.

---

## What is a SKILL.md?

A SKILL.md is a Markdown instruction file that tells a Claude agent how to use a specific tool or run a specific workflow. Think of it as a plugin — no code required. Claude Code, opencode, and other Pi-compatible agents pick them up automatically from your skills folder.

---

## Skills

| Skill | Source project | Description |
|---|---|---|
| [feynman](skills/feynman/SKILL.md) | [getcompanion-ai/feynman](https://github.com/getcompanion-ai/feynman) | AI research agent — deep research, lit reviews, paper-code audits, replications, peer review |

More coming. PRs welcome.

---

## How to install a skill

```bash
# Claude Code
mkdir -p ~/.codex/skills/feynman
cp skills/feynman/SKILL.md ~/.codex/skills/feynman/SKILL.md

# opencode
mkdir -p .agents/skills/feynman
cp skills/feynman/SKILL.md .agents/skills/feynman/SKILL.md
```

---

## Contributing

If you know an open-source tool worth converting, open a PR with a new `skills/<toolname>/SKILL.md` file. Good candidates: n8n, LangChain, AutoGen, Cursor rules, Mem0, any tool with a public GitHub repo.

---

## Author

**Abdelhamid Fahim** — GCC Marketing Manager, specialty pharma | AI tools enthusiast
GitHub: [@AboMalek1986](https://github.com/AboMalek1986)

---

## License

Skills are reverse-engineered from their respective open-source projects and carry their original licenses. Attribution is included in each SKILL.md header. This repo itself is MIT licensed.
