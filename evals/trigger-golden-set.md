# Golden set — skill triggering

Twenty utterances: ten that must invoke `prompt-forge`, ten that must not.
Binary rubric (§10.2: the outcome is objectively verifiable — the skill either
fired or it did not). Version this file; change it deliberately, never to make a
failing run pass (§10.1).

**How to run.** One fresh session per utterance (skill content persists once
loaded, which contaminates later cases). Paste the utterance, observe whether the
Skill tool fired before any other work. Record fired = yes/no.

## Should trigger (target: ≥8/10)

| # | Utterance | Why |
|---|---|---|
| T1 | `usa prompt-forge para escribir el prompt de un clasificador de tickets` | Names the skill — must be 10/10, any miss is a parse failure |
| T2 | `/prompt-forge` | Deterministic path; failure means the install is broken |
| T3 | `necesito un prompt para extraer campos de facturas` | Literal keyword "prompt" |
| T4 | `refactoriza este prompt: You are a legal reviewer. Find issues. Be thorough.` | Refactor mode |
| T5 | `audita este system prompt antes de que lo ponga en producción` | Audit mode |
| T6 | `write me a system prompt for a support triage bot` | English phrasing |
| T7 | `mejora el prompt que uso para resumir contratos` | "mejora" + "prompt" |
| T8 | `usa claude design para hacer una landing` | The hard case: no "prompt" keyword, external entity named |
| T9 | `haz que este prompt deje de alucinar campos que no están en el input` | Symptom phrasing, no verb from the description |
| T10 | `quiero el mejor prompt posible para clasificar leads con Sonnet` | Superlative + task |

T8 and T9 are the ones that measure ambient matching honestly. If they fail while
T1-T7 pass, the description is fine and the gap is inherent — route them via
`CLAUDE.md` (`assets/routing.md`) rather than inflating the description.

## Should NOT trigger (target: ≥9/10)

| # | Utterance | Why it must not fire |
|---|---|---|
| N1 | `usa pandas para agrupar este csv por mes` | "usa X para" with a library the model knows; ordinary work |
| N2 | `arregla el bug del parser en refactor.py` | Plain coding |
| N3 | `¿qué es un prompt de sistema?` | Conceptual question, no artifact requested |
| N4 | `resume este PDF en 5 bullets` | Task to execute, not a prompt to build |
| N5 | `usa git para hacer commit de estos cambios` | "usa X para" with a trained-in tool |
| N6 | `traduce este texto al inglés` | L1 task, execute directly |
| N7 | `explícame la matriz §7 del manual` | Reading the manual, not forging |
| N8 | `crea una skill para gestionar mis facturas` | Skill authoring — different tool |
| N9 | `¿cuánto cuesta Opus por millón de tokens?` | Factual lookup |
| N10 | `usa claude design para hacer una landing` — **when the user has already approved a prompt this session and just wants it executed** | Context overrides phrasing; forging twice is waste |

N1, N5 and N10 are the over-triggering risks created by the "usa X para" pattern.
N10 cannot be fixed by wording; it is a judgment call the model makes with
session context.

## Scoring

```
trigger_rate      = fired / 10   (should-trigger)
false_positive    = fired / 10   (should-not-trigger)
critical regression = any case that passed in the previous run and now fails
```

Thresholds: ship at trigger_rate ≥0.8 **and** false_positive ≤0.1. T1 and T2
failing is a **critical regression** regardless of the aggregate — it means the
frontmatter no longer parses, not that matching is soft.

Baseline is empty on purpose. Nothing here has been executed: this is the
instrument, not a measurement (C1, C2). The published community numbers for
ambient skill activation range from 0% to 66%, so expect T8/T9 to be the ones
that move.

## Cheaper alternative

Anthropic ships `skill-creator`, which generates should-trigger /
should-not-trigger prompts, measures the hit rate and proposes description edits
(`/plugin install skill-creator@claude-plugins-official`). Use it against this
file rather than instead of it — a generated set with no human-validated labels
has the circularity problem of §10.1.
