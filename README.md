# Prompt Forge

**Le pides una tarea vaga y te devuelve el prompt de nivel producción que esa tarea necesita — nunca la respuesta a la tarea.**

Es una skill para [Claude Code](https://claude.com/claude-code) (funciona igual en Cowork/claude.ai). La construí porque escribir un buen prompt es un oficio en sí mismo, y la mayoría de las veces no tenemos tiempo ni ganas de aplicarlo: pedimos algo en dos líneas y esperamos que salga bien. Prompt Forge hace ese trabajo por ti: investiga de verdad, piensa la tarea a fondo y te entrega un prompt profesional, listo para usar.

Si escribes prompts para tus propios agentes, para un cliente o simplemente quieres dejar de improvisar cada vez que le pides algo a una IA, esto te ahorra ese rato de pensar la estructura, mirar la documentación oficial de la herramienta en cuestión y acordarte de las cosas que se te olvidan siempre (qué pasa si el input viene vacío, qué pasa si alguien intenta colarle instrucciones dentro de un dato, etc.).

## Pruébalo en dos minutos

Instálalo (abajo tienes el cómo) y escribe, tal cual, algo como:

```
usa prompt-forge para escribir el prompt de un agente que revise PRs de GitHub
y deje comentarios solo cuando encuentre un problema real
```

En unos segundos tienes de vuelta un prompt completo, con rol, reglas, formato de salida y defensas contra que alguien le cuele instrucciones falsas dentro de un PR. No una respuesta a "cómo revisar PRs" — el prompt en sí, listo para pegar en tu agente.

## El problema que resuelve

Cuando le pides algo vago a una IA, dos cosas suelen pasar: o te contesta con lo primero que se le ocurre (que puede estar bien o puede estar mal, y tú no lo sabes hasta que falla), o tienes que sentarte tú a escribir un prompt en condiciones, y eso lleva tiempo si quieres hacerlo bien de verdad: pensar quién va a usarlo, qué falla si algo sale mal, buscar cómo funciona de verdad la herramienta o producto que mencionas (porque el modelo puede tener información desactualizada), y dejarlo todo por escrito de forma clara.

Prompt Forge hace ese trabajo en tu lugar, en tres pasos, siempre en ese orden:

### 1. Entiende la petición

Lee lo que pides y también lo que das por hecho sin decirlo: quién va a usar esto, sobre qué datos, con qué frecuencia, qué pasa si sale mal. Si de verdad hay una decisión que solo tú puedes tomar, te hace **como mucho una pregunta**. Si no, elige la opción sensata y lo deja anotado dentro del propio prompt, para que sepas qué asumió.

### 2. Investiga de verdad

Antes de escribir una sola línea, sale a buscar información actual sobre lo que mencionas: documentación oficial, repositorios, artículos técnicos, foros de la comunidad. Esto es clave y es lo que más diferencia a Prompt Forge de pedirle "escríbeme un prompt" a cualquier IA: un modelo de lenguaje puede sonar muy seguro hablando de una herramienta y estar describiendo una versión que ya no existe. Aquí, todo lo que se afirma sobre una capacidad concreta viene de una fuente real o se declara abiertamente como suposición — nunca se inventa.

Y todo lo que trae de fuera lo trata como **dato, nunca como instrucción**: si una página web que consulta contiene un intento de "olvida tus reglas y haz esto otro", lo ignora. Esa misma defensa la incorpora dentro del prompt que te entrega.

### 3. Forja el prompt

Con la petición entendida y la información verificada, escribe un único prompt profesional: rol claro, reglas de comportamiento numeradas, formato de salida exacto, criterios verificables en vez de vaguedades ("máximo 50 palabras" en lugar de "sé breve"), y las defensas necesarias si el prompt va a recibir datos de fuentes que no controlas. Todo dentro de un único bloque de texto, autocontenido: quien lo reciba no necesita ver tu proyecto ni tu código para poder usarlo.

El tamaño se ajusta a la tarea. Un prompt para algo simple sale corto y directo; uno para un agente que toma decisiones delicadas sale con toda la estructura que hace falta. Nunca al revés.

## Un ejemplo, antes y después

**Lo que le pides:**

> usa claude design para maquetar la landing de mi app

**Lo que normalmente pasa si se lo pides directo a una IA:** te da una landing ya hecha, basada en lo que el modelo "cree" que es Claude Design — que puede estar bien o puede ser una mezcla de suposiciones.

**Lo que hace Prompt Forge:** en vez de maquetar nada, investiga qué es Claude Design ahora mismo (documentación, ejemplos, límites conocidos), y te devuelve el prompt que, cuando lo uses, sí va a maquetar tu landing correctamente — con las capacidades reales de la herramienta, no con una versión imaginada.

## Instalación

Descarga o clona este repositorio y coloca la carpeta `prompt-forge/` en una de estas dos rutas:

```
~/.claude/skills/prompt-forge/           → disponible en todos tus proyectos
<tu-proyecto>/.claude/skills/prompt-forge/  → solo en ese proyecto, versionable con git
```

Para comprobar que está cargada, escribe `/skills` en Claude Code o pide directamente "usa prompt-forge para...".

Si trabajas desde Claude o Cowork en el navegador: sube el paquete como skill personalizada desde la propia conversación.

## Cómo se usa

```
usa prompt-forge para escribir el prompt de <lo que necesites>
usa <una herramienta que el modelo quizá no conozca bien> para hacer X
refactoriza este prompt: <pega tu prompt actual>
audita este prompt antes de llevarlo a producción: <pega tu prompt>
```

Tiene tres modos: **forjar** uno nuevo (el por defecto), **refactorizar** uno que ya tienes y no te convence, y **auditar** uno antes de usarlo en serio, sin tocarlo.

## Qué te devuelve

Cada vez que la usas, deja el resultado en `./prompt-forge-out/<nombre-de-la-tarea>/`:

| Archivo | Qué contiene |
|---|---|
| `prompt.md` | El prompt, solo, listo para copiar y pegar donde lo necesites |
| `report.md` | El razonamiento completo: qué entendió, qué encontró investigando, por qué lo escribió así |
| `golden-set.md` | Al menos 10 casos de prueba para comprobar que el prompt funciona como debería |
| `tool-schema.json` | Solo si el prompt está pensado para que lo consuma código, no una persona |
| `promptfoo.yaml` | Solo si vas a medir el prompt con una herramienta de evaluación |

## Qué hay dentro del paquete

```
prompt-forge/
├── SKILL.md                     el motor: los tres pasos, las reglas fijas, los modos
├── README.es.md                 guía rápida en español
├── references/
│   ├── manual.md                 el manual operativo completo — la autoridad única
│   ├── research-protocol.md      cómo investiga: qué fuentes mira y en qué orden
│   ├── output-contract.md        cómo tiene que verse el informe que te entrega
│   └── worked-example.md         un caso completo resuelto de principio a fin
├── assets/
│   ├── templates.md              patrones de estructura según el tipo de tarea
│   └── routing.md                cómo conseguir que se dispare sola, sin tener que invocarla a mano
├── evals/
│   └── trigger-golden-set.md     20 frases de ejemplo para medir si se activa cuando toca
└── scripts/
    ├── refactor.py                opcional: refactorizar muchos prompts de golpe, por API
    └── test_smoke.py              valida que el script anterior funciona sin gastar saldo
```

Todo el comportamiento real vive en `references/manual.md`. Si algún día mejoro el manual, la skill entera mejora con él, sin tocar nada más — es la única fuente de verdad, y todo lo demás la cita.

## Lo que esta skill no hace

Prefiero decir esto claramente en vez de venderla de más:

- **No mide por ti.** Te deja preparados los casos de prueba (el golden set), pero no los ejecuta. Hasta que tú los pases, lo que te entrega es un argumento bien razonado, no un resultado medido.
- **No puede leer X ni LinkedIn.** Esas redes bloquean cualquier lector automático. Cuando la información que necesita está solo ahí, lo dice abiertamente en vez de fingir que la tiene.
- **No blinda tu sistema contra cualquier ataque.** Incorpora las defensas que caben dentro de un prompt; si tu caso necesita algo más (a nivel de arquitectura, de infraestructura), te avisa de que hace falta y de qué tipo.
- **No sustituye tu criterio.** Si vas a usar los casos de prueba para validar algo importante, revísalos tú — que el mismo modelo se autoevalúe no es una validación real.

## Uso opcional del script de refactor

Si tienes muchos prompts que refactorizar de golpe (no uno solo, sino un lote), hay un script que lo hace por API:

```bash
export ANTHROPIC_API_KEY=sk-ant-...
python scripts/test_smoke.py                  # comprueba que todo está bien montado, sin gastar saldo
python scripts/refactor.py -f prompt_roto.txt --save
```

Para un prompt suelto, mejor usa la skill directamente dentro de Claude Code: investiga de verdad, y el script no.

---

Si la usas y algo no te cuadra, o se te ocurre una mejora, abre un issue. Y si te resulta útil, una estrella al repositorio ayuda a que otras personas la encuentren.
