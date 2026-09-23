# Output contract — `report.md`

Eight sections, in this order, always. The schema is fixed so that two runs on
the same request are comparable (§3.4: the model does not choose the format, the
contract fixes it). A section with nothing to say says so explicitly; it is never
dropped.

---

## 1. Intake and interpretation

The Phase 0 block verbatim, plus:

- **Interpretation:** one paragraph stating what you understood the user to want.
- **Assumptions:** numbered list. Each assumption states what changes if it is
  wrong. Assumptions the user can cheaply confirm are flagged `ASK`.

## 2. Research dossier

- Gate decision: deep protocol or explicit skip, with the reason.
- The evidence table (claim · source · tier · date · changes-the-prompt).
- `UNVERIFIED`: what could not be reached and what was therefore assumed.
- Contradictions found, unresolved ones left visible.
- Research budget: rounds, sources read, facts that changed the design.

If Phase 1 was skipped, this section contains one line stating the skip and its
justification. It is never absent.

## 3. Decision matrix (§7) and spec

The seven questions with their answers, as a table, then the four-line spec:

```
Nivel:               ...
Razonamiento:        ...
Salida:              ...
Defensa adversarial: ...
Caching:             ...
```

Plus one paragraph: why this level and not the one above or below (§6 reglas
duras). "L3 because it feels important" is not a justification; a level chosen
without matrix evidence is superstition.

## 4. The prompt

The complete prompt, in a fenced block, paste-ready, with nothing interleaved.
Identical to `prompt.md`. If the design has a system/user split, show both,
labelled, with the cache boundary marked:

```
[SYSTEM — cacheable]
...
[SYSTEM — cacheable: examples]
...
[USER — not cached]
...
```

## 5. Tool / schema definition

The JSON tool definition if the output is consumed programmatically (§3.4), or
the literal line `null — output is read by a human, structure via prompt (§3.4)`.

## 6. Justification, block by block

| Block | Manual section | Why it is there |
|---|---|---|
| Role line | §6 L2 | ... |
| Anti-injection warning | §9.1 Capa 1 | ... |
| Examples with edge cases | §12 A7 | ... |

Every architectural block of the prompt appears exactly once. A block that cannot
name its section is a block that should be deleted — delete it and say so here.

## 7. §13 checklist, applied to your own prompt

All seven categories — Diseño, Estructura, Razonamiento, Stack, Evaluación,
Seguridad, Economía — with per-item status:

| Item | Status | Note |
|---|---|---|
| Level justified by §7 matrix | checked | ... |
| Golden set ≥10 cases exists | unchecked | Shipped as proposal, not yet run against the prompt — assumed risk, not oversight |
| Cost per request measured | n/a | One-off use, no volume |

Closing line, mandatory: *"Unchecked items are consciously assumed risks, not
oversights."* — followed by the list of them, so the user can decide.

## 8. Residual attack surface, adversarial tests and autocritique

**Adversarial tests** — the three minimum cases and the predicted behaviour:

| Input | Prompt's predicted behaviour | Acceptable? |
|---|---|---|
| Injection inside the data block | ... | ... |
| Empty / degenerate input | ... | ... |
| Out-of-domain input | ... | ... |

**Residual attack surface** — each risk with severity and whether the prompt
alone can close it. Most cannot; the honest answer is usually "no — needs
external verification in the pipeline" (§9.3, §14 L3).

**Autocritique** — concrete, not decorative. Name at least:

- one bias in this analysis (templatization, over-correction, familiarity with
  one domain over another),
- one untested assumption,
- one thing that would change the design if measured and found false.

Predictions of quality are not evidence of quality (§0 C2, §14 L1). This report
is a design argument; the golden set is what turns it into a measurement.

---

## Companion files

- `prompt.md` — section 4, alone.
- `tool-schema.json` — section 5, if any.
- `golden-set.md` — the ≥10 cases: typical, edge, ambiguous, adversarial, empty,
  each with expected output or ordinal rubric anchor (§10.1, §10.2).
- `promptfoo.yaml` — only when the user will actually run evals (§10.5).
