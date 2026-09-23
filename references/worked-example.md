# Worked example — `"usa claude design para hacer una landing"`

Illustrative walkthrough of the pipeline. **The evidence rows below are
placeholders showing the shape of the output, not researched facts.** Never copy
a claim from this file into a real report.

---

## Phase 0 — Intake

```
REQUEST (verbatim): "usa claude design para hacer una landing"
GOAL:               Produce a prompt that drives <entity> to generate a landing page
EXTERNAL ENTITIES:  "claude design" — ambiguous: product? feature? mode? third-party tool?
CONSUMER:           unknown — likely a human reviewing the generated page
ERROR COST:         low-medium — output is reviewable before use
LATENCY BUDGET:     normal
VOLUME:             one-off (assumed)
UNTRUSTED INPUT:    no, unless brand copy is pasted in later
UNKNOWNS:           (1) what "claude design" actually is, (2) what the landing is for,
                    (3) does the user want the page as code, as a design file, or as a spec
```

Unknown (1) is not asked — it is researched. Unknowns (2) and (3) flip design
forks and cost the user ten seconds, so they are the questions worth asking. If
nobody is available to answer, assume the cheapest useful reading (a code
landing page for an unspecified product), declare it, continue.

## Phase 1 — Research (gate: OPEN, named external entity)

Question set: what is it, current status, inputs/outputs, limitations, how it is
invoked, what practitioners report, what changed recently, name disambiguation.

Parallel sweep, one subagent per class:

| # | Claim *(placeholder)* | Source | Tier | Date | Changes the prompt? |
|---|---|---|---|---|---|
| 1 | `<entity>` is <what it actually is, per vendor docs> | docs URL | A | YYYY-MM-DD | Yes — determines whether the prompt targets a UI, a CLI or an API |
| 2 | Accepted input format is `<...>` | docs URL | A | YYYY-MM-DD | Yes — fixes the output contract of the prompt |
| 3 | Documented limitation: `<...>` | changelog | A | YYYY-MM-DD | Yes — prompt must avoid it or handle the failure |
| 4 | Practitioners report `<...>` breaks with long specs | X thread | C | YYYY-MM-DD | Yes — forces chunked instructions |
| 5 | Video walkthrough shows workflow `<...>` | YouTube | B/C | YYYY-MM-DD | No — context only |

```
UNVERIFIED
- LinkedIn practitioner posts: login wall, snippets only.
- Behaviour beyond the last documented release: no tier-A source.
CONTRADICTION
- Docs (tier A, dated) say <X>; two community reports (tier C) say <X> fails for
  <case>. Prompt is designed to tolerate the failure rather than assume the docs.
Research budget: 2 rounds, 11 sources read, 4 facts changed the design.
```

This is the whole point of the skill: the prompt is written against what the tool
**is**, not against what the model remembers it was.

## Phase 2 — Spec

| # | §7 question | Answer |
|---|---|---|
| 1 | Single objectively correct answer? | No → ordinal rubric (§10.2) |
| 2 | Tolerable error cost | 1-5% → L2 |
| 3 | Latency budget | Normal → Self-Consistency/ToT not excluded but not needed |
| 4 | External knowledge? | Yes, dynamic → resolved by research, folded into context |
| 5 | Consumed programmatically? | No → prose/code output, no tool use |
| 6 | Adversarial input? | No, unless third-party copy is pasted later |
| 7 | Executions per day | One-off → caching does not pay |

```
Nivel:               L2
Razonamiento:        CoT implícita vía formato (sin "step by step")
Salida:              código + notas, sin tool use
Defensa adversarial: no aplica hoy; añadir §9.1 Capa 1 si se pega copy externo
Caching:             no aplica (uso único, §3.2 umbral no alcanzado)
```

Why not L3: no irreversible action, output is reviewed by a human, error is cheap
and visible. L3 here is the textbook over-engineering case (§6, §12 A4).

Why not L1: the task has real quality criteria the model will not infer, plus
verified constraints from research that must be stated.

## Phase 3 — Draft

L2 template, with the research-derived constraints written as **verifiable**
requirements ("above the fold: headline ≤ 12 words, one primary CTA"), the
documented limitation from row 3 encoded as an explicit avoidance rule, and one
edge-case example (missing product information → the prompt must ask or emit a
`TODO`, not invent features).

## Phase 4 — Self-audit

§13 checklist applied honestly. Realistic outcome for a one-off:

- `Golden set ≥10 cases` → **unchecked**: shipped as a proposal, never run.
  Consciously assumed risk.
- `Cost per request measured` → **n/a**: one-off.
- `Tested with 3 adversarial inputs` → **checked**: injection in pasted copy,
  empty brief, out-of-domain brief.

Autocritique: the design leans on tier-C reports for row 4; if those are wrong,
the chunking constraint is unnecessary overhead. Nothing here is measured — it is
a design argument until the golden set is run (§0 C2, §14 L1).

## Phase 5 — Deliver

`prompt-forge-out/landing-<entity>/` with `prompt.md`, `report.md`,
`golden-set.md`. In chat: the four-line spec, the level and why, the three
findings that changed the design, and the `UNVERIFIED` list.
