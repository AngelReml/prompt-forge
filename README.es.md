# prompt-forge — guía rápida (ES)

Convierte una petición vaga en el prompt más completo que la tarea **justifica**
(que no es el más largo). El manual operativo incluido es la única autoridad.

## Qué hace, en una frase

`usa claude design para hacer X` →
investigación profunda multi-fuente sobre "claude design" (docs oficiales,
changelogs, repos, X, LinkedIn, YouTube, foros) →
matriz §7 → nivel L1/L2/L3 → prompt redactado y citado contra el manual →
checklist §13 honesta → golden set → autocrítica.

## Instalación en Claude Code estándar

Descomprime la carpeta `prompt-forge/` en cualquiera de estas rutas:

```
~/.claude/skills/prompt-forge/          # personal, todos tus proyectos
<proyecto>/.claude/skills/prompt-forge/ # solo ese proyecto, versionable en git
```

Comprueba que carga con `/skills` o pidiendo `usa prompt-forge para...`.
En Claude/Cowork, sube el archivo `.skill` y guárdalo desde la tarjeta.

## Uso

```
usa prompt-forge para escribir el prompt de <lo que sea>
usa <producto que el modelo quizá no conoce bien> para hacer X
refactoriza este prompt: <pega el prompt>
audita este prompt antes de producción: <pega el prompt>
```

Tres modos: **forge** (por defecto), **refactor** (prompt existente roto),
**audit** (solo checklist §13).

Sobre el disparo automático, con los números delante: la invocación ambiental de
skills se ha medido entre 0% y 66% en evaluaciones independientes
([Vercel](https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals),
[Codeminer42](https://blog.codeminer42.com/stop-putting-best-practices-in-skills/)).
Por eso la skill trae tres palancas en `assets/routing.md`, de más a menos
determinista: `/prompt-forge`, una línea de enrutado en tu `CLAUDE.md`, y
`when_to_use:` (solo Claude Code). Y trae el instrumento para medirlo:
`evals/trigger-golden-set.md`, 20 utterances etiquetadas.

## Qué te devuelve

En `./prompt-forge-out/<slug>/`:

| Archivo | Contenido |
|---|---|
| `prompt.md` | El prompt solo, listo para pegar |
| `report.md` | Informe de 8 secciones (intake, dossier, matriz, prompt, schema, justificación, checklist, autocrítica) |
| `tool-schema.json` | Solo si la salida se consume por código |
| `golden-set.md` | ≥10 casos de evaluación |
| `promptfoo.yaml` | Solo si vas a medir |

## Contenido del paquete

```
prompt-forge/
├── SKILL.md                        motor: 5 fases, reglas duras, modos
├── README.es.md                    esto
├── references/
│   ├── manual.md                   el Manual Operativo (§0-§15). Autoridad única
│   ├── research-protocol.md        6 clases de fuente, tiers, tabla de evidencia
│   ├── output-contract.md          las 8 secciones del informe
│   └── worked-example.md           ejemplo completo de "usa claude design para..."
├── assets/
│   ├── templates.md                L1/L2/L3, tool use, caching, anti-injection, golden set
│   └── routing.md                  disparo: slash, CLAUDE.md, when_to_use, matriz de instalación
├── evals/
│   └── trigger-golden-set.md       20 utterances etiquetadas para medir el disparo
└── scripts/
    ├── refactor.py                 opcional: refactor por API, manual cacheado
    └── test_smoke.py               valida el script sin gastar saldo
```

## El acoplamiento es deliberado

El comportamiento vive en `references/manual.md`, no en el código ni en el
SKILL.md. Si mejoras el manual, la skill cambia sin tocar nada más. Única
condición: mantener estables los identificadores de sección (§N, FN, AN, LN),
porque SKILL.md, el contrato de salida y `refactor.py` los citan.

## Reglas de la frontmatter que rompen la skill si las tocas

Verificadas contra documentación oficial el 2026-07-28:

| Regla | Fuente |
|---|---|
| `name` y `description` **no pueden contener XML tags** — ni `<` ni `>` | [platform docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) |
| `description` ≤ **200 caracteres** para subirla a claude.ai/Cowork (la spec permite 1024) | [claude.com/docs/skills/how-to](https://claude.com/docs/skills/how-to) |
| `description` en **una sola línea**: `\|`, `>` o líneas envueltas por Prettier rompen el descubrimiento sin mostrar error | issues 9817 / 11322 / 12971 de anthropics/claude-code |
| `name` en minúsculas, números y guiones, ≤64, igual al nombre del directorio, sin "claude" ni "anthropic" | [platform docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) |
| El zip debe llevar la **carpeta de la skill como única entrada raíz** | [how-to](https://claude.com/docs/skills/how-to) |

Síntoma típico: `/prompt-forge` funciona pero la skill nunca se dispara sola →
la frontmatter no parsea. `claude --debug` enseña el error.

## Lo que esta skill NO hace (§13, honestidad operativa)

- **No mide.** Propone golden set; no lo ejecuta contra el prompt. Hasta que lo
  ejecutes, el informe es un argumento de diseño, no una medición (C1, C2).
- **No lee X ni LinkedIn.** Están cerrados en origen a cualquier fetcher (host
  entero `ROBOTS_DISALLOWED`, verificado). En vez de advertirlo y parar, el
  protocolo escala: HN API, foros Discourse del propio fabricante, Bluesky
  público, GitHub, prensa que cite el hilo; luego APIs con credencial; luego tú,
  pegando el contenido o mirándolo en tu navegador; y solo entonces declara el
  hueco. Ver `references/research-protocol.md` §6.
- **No blinda contra prompt injection.** Aplica las capas §9.1 que caben en un
  prompt y dice explícitamente cuáles requieren arquitectura (§9.3).
- **No sustituye tu criterio de dominio.** El etiquetado del golden set lo tienes
  que validar tú; si lo valida el propio modelo, hay circularidad (§10.1).

## Uso opcional del script

```bash
export ANTHROPIC_API_KEY=sk-ant-...
python scripts/test_smoke.py                    # valida sin gastar saldo
python scripts/refactor.py -f prompt_roto.txt --save
```

Sirve para volumen (refactorizar muchos prompts en batch con el manual cacheado).
Para un solo prompt, la skill dentro de Claude Code es mejor: investiga, y el
script no.
