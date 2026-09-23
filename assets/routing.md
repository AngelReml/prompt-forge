# Routing — making the skill fire when it should

Three levers, ordered by how deterministic they are. Use as many as your surface
allows; they compose.

## 1. Direct invocation (deterministic, documented)

```
/prompt-forge
usa prompt-forge para escribir el prompt de ...
```

The slash command comes from the **directory name** for personal/project skills
(`~/.claude/skills/prompt-forge/` → `/prompt-forge`). Nothing to configure.
This is the path to use when it must not miss.

## 2. `CLAUDE.md` routing line (for invariants)

Skills are on-demand procedures; rules that must *always* apply belong in
`CLAUDE.md` / `AGENTS.md`. Two independent evals found rules in the docs index
firing at or near 100% versus 53% and 6% for skill-ambient matching
([Vercel](https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals),
[Codeminer42](https://blog.codeminer42.com/stop-putting-best-practices-in-skills/)).

Paste into your project `CLAUDE.md` (or `~/.claude/CLAUDE.md`):

```markdown
## Prompt work

When a request asks me to write, refactor, improve or audit a prompt or system
prompt, or names an external product/tool/API I would have to describe from
memory ("usa <producto> para hacer X"), invoke the `prompt-forge` skill before
answering. Do not draft the prompt from parametric knowledge and do not skip the
research phase; if research is unnecessary, say so explicitly and continue.
```

Angle brackets are fine here — the XML-tag restriction applies only to a skill's
frontmatter `name` and `description`.

## 3. `when_to_use:` frontmatter (Claude Code only — opt in)

Claude Code reads an extra frontmatter field for trigger phrases, appended to the
description and counted against the same listing budget
([docs](https://code.claude.com/docs/en/skills)).

**Trade-off, stated plainly:** `when_to_use` is not in the open Agent Skills
spec. The reference validator enforces a closed field allowlist, so adding it
makes `skills-ref validate` fail, and it is not portable to claude.ai/Cowork
upload. That is why the shipped SKILL.md does **not** include it.

If this install is Claude-Code-only, add it right after `description:` — one
line, single scalar, no angle brackets:

```yaml
when_to_use: Trigger phrases - usa prompt-forge para, necesito un prompt para, escribe el prompt de, refactoriza este prompt, mejora este system prompt, audita este prompt, make me a prompt for, write a system prompt, usa X para hacer Y where X is an external product the model would otherwise describe from memory.
```

Keep it a **single line**. Multi-line, folded (`>`), literal (`|`) and
Prettier-wrapped descriptions silently disable discovery: the slash command keeps
working while ambient matching dies, with no error shown.

## 4. What does not work

- **Hooks as a substitute for a weak description.** One 650-trial study measured
  activation *dropping* to 37% when a hook was stacked on a passive description
  ([study](https://medium.com/@ivan.seleznov1/why-claude-code-skills-dont-activate-and-how-to-fix-it-86f679409af1)).
  There is no documented hook that forces a named skill to run.
- **Assuming an edit took effect.** Skills do not hot-reload; restart the session
  after editing SKILL.md, then re-measure.
- **Ignoring the listing budget.** The skill listing has a character budget
  (~1% of the context window); when it overflows, descriptions are dropped
  starting with the least-used skills, silently. Fewer, sharper skills trigger
  better than many vague ones.

## Install matrix

| Surface | Install | Description limit | `when_to_use` |
|---|---|---|---|
| Claude Code, personal | `~/.claude/skills/prompt-forge/` | 1024 | supported |
| Claude Code, project | `<repo>/.claude/skills/prompt-forge/` | 1024 | supported |
| claude.ai / Cowork | upload the `.skill` / zip — the skill **directory** must be the single top-level entry | **200** | not portable |
| API | `/v1/skills`, referenced by `skill_id` | 1024 | not portable |

The shipped description is ≤200 characters precisely so one package works
everywhere. If you only target Claude Code, you have 1024 to spend — but spend it
on literal user phrasings, not on prose.
