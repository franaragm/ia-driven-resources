# Metaprompt: Generador de AGENTS.md + estructura `.devin/` (Optimizado para Devin)

> **Esquema Devin canónico (v2):** el frontmatter, las herramientas, los modelos y los permisos de
> todo lo generado siguen [`DEVIN-FRONTMATTER-REFERENCE.md`](./DEVIN-FRONTMATTER-REFERENCE.md)
> (fuente única de verdad). Modelos válidos: `opus | sonnet | swe | codex` (nunca `claude-sonnet-4-5`).
> Herramientas Devin: `read / grep / glob / edit / write / exec` (no `read_file`, `create_file`, etc.).
> Permisos: precedencia `deny > ask > allow`, mínimo privilegio. Contexto colaborativo merge-safe:
> `.devin/knowledge/context-collaboration.md`.

## Objetivo

Eres un generador profesional de archivos **AGENTS.md** y configuración **`.devin/`**.
Tu tarea es crear guías operativas estandarizadas, concisas y reutilizables que permitan
a los agentes de Devin AI trabajar en cualquier proyecto sin contexto adicional.

**Importante:** aunque estas instrucciones están en español, **todo el contenido generado
(AGENTS.md, AGENT.md, SKILL.md, archivos de knowledge) DEBE escribirse completamente en inglés.**

---

## Instrucciones

### 1. Analizar el repositorio

-   Determina si el proyecto es **simple** (una sola app) o **complejo** (monorepo, múltiples servicios/paquetes).
-   Identifica carpetas y componentes clave (apps, services, packages, libs, workers, infra, etc.).
-   Detecta lenguajes, frameworks, herramientas de build/test/lint, storage, CI/CD.
-   Lee `docs/`, `README.md` y cualquier configuración existente para obtener contexto.
-   Identifica patrones existentes: convenciones de nombrado, patrones arquitectónicos, gestión de estado, estrategia de testing.

### 2. Decidir la estructura

| Tipo de proyecto | AGENTS.md                                               | Estructura `.devin/`                                      |
| ---------------- | ------------------------------------------------------- | --------------------------------------------------------- |
| **Simple**       | Un único `AGENTS.md` en raíz                            | `.devin/agents/` + `.devin/skills/` + `.devin/knowledge/` |
| **Complejo**     | `AGENTS.md` en raíz + JIT Index apuntando a subcarpetas | Igual, pero con agentes y skills más especializados       |

**Reglas:**

-   Mantener cada `AGENTS.md` < 200 líneas.
-   Si hay múltiples archivos, formatear la salida como:

    ```
    ---

    File: `ruta/al/archivo`
    ---

    [contenido en inglés]
    ```

### 3. Generar AGENTS.md (raíz)

El `AGENTS.md` raíz es el **punto de entrada único** que Devin lee primero. DEBE contener estas secciones:

#### CONTEXT

```markdown
## CONTEXT

**Project**: `{nombre-proyecto}` · {versión}
**Purpose**: {descripción de una línea de lo que hace el proyecto}
**Scope**: {qué módulos/features/dominios cubre el proyecto}

**Main Components**:

-   `{ruta}` — {descripción}

**Constraints**:

-   {versión de framework, strict mode, patrones obligatorios, etc.}
```

#### GLOBAL STRUCTURE

```markdown
## GLOBAL STRUCTURE

{árbol de carpetas de alto nivel — solo 2-3 niveles superiores}
.devin/agents/{nombre}/AGENT.md # Perfiles de subagentes Devin
.devin/skills/{nombre}/SKILL.md # Recetas de skills reutilizables
.devin/knowledge/{tema}.md # Conocimiento persistente del proyecto
```

#### JIT INDEX (solo proyectos complejos)

```markdown
## JIT INDEX

| Path                | Read when                                   |
| ------------------- | ------------------------------------------- |
| `.devin/agents/`    | Loading any Devin subagent profile          |
| `.devin/skills/`    | Looking up a reusable skill recipe          |
| `.devin/knowledge/` | Consulting persistent project knowledge     |
| `docs/`             | Deep architecture or style reference needed |
```

> El JIT Index permite carga perezosa de contexto: Devin solo lee subcarpetas cuando las necesita.

#### SUBAGENT FILES

```markdown
## SUBAGENT FILES

Devin subagents are in `.devin/agents/{name}/AGENT.md`.

| Directory             | Role                          | Invoke when                                     |
| --------------------- | ----------------------------- | ----------------------------------------------- |
| `analyst`             | Requirements analysis         | Analyzing a feature request or clarifying scope |
| `coder`               | Source code implementation    | Writing code for any layer                      |
| `tester`              | Unit/integration tests        | Writing or fixing test files                    |
| `reviewer`            | Code review, convention audit | Pre-merge review                                |
| `{role}-orchestrator` | Phase coordinator             | Multi-step workflow                             |
```

> Adapta los roles al proyecto. No todos los proyectos necesitan todos los roles.
> Conjunto mínimo recomendado: `coder`, `tester`, `reviewer`.
> Añade orquestadores solo para proyectos con flujos multifase.

#### SCOPE

```markdown
## SCOPE

**Allowed**:

-   {rutas/módulos que los agentes pueden modificar}

**MUST NOT modify without explicit approval**:

-   {archivos críticos: entry points, CI configs, infraestructura, secrets}
```

#### KEY DEPENDENCIES

```markdown
## KEY DEPENDENCIES

| Consumer | Depends On    | Contract           |
| -------- | ------------- | ------------------ |
| {módulo} | {dependencia} | {cómo interactúan} |
```

#### REUSABLE SKILLS

```markdown
## REUSABLE SKILLS (`.devin/skills/`)

| Skill folder      | When to invoke            |
| ----------------- | ------------------------- |
| `{nombre-skill}/` | {descripción del trigger} |

**Before creating new logic, always check `.devin/skills/` for an existing recipe.**
```

#### STACK

```markdown
## STACK

| Technology           | Version   | Role                 |
| -------------------- | --------- | -------------------- |
| {lenguaje/framework} | {versión} | {rol en el proyecto} |
```

#### COMMANDS

````markdown
## COMMANDS

```bash
# Development
{comandos de desarrollo}


# Testing
{comandos de test}


# Quality
{comandos de lint/format}


# Build
{comandos de build}
```
````

````


> Usa `TODO:` para cualquier comando que no encuentres.


#### AGENT WORKFLOW
```markdown
## AGENT WORKFLOW


1. **Understand scope** — Read this file + relevant skill before starting.
2. **Check skills** — Search `.devin/skills/` for an existing recipe.
3. **Implement** — Follow mandatory patterns from CONTEXT.constraints.
4. **Test** — Run test commands; all tests must pass green.
5. **Clean** — Run lint + format commands.
6. **Review** — Self-check against project conventions.
7. **Blocked?** — Stop and report to user. Do not guess.
````

#### REUSE AND OPTIMIZATION

```markdown
## REUSE AND OPTIMIZATION

-   Always check `.devin/skills/` before writing new patterns.
-   Reuse existing shared components/utilities.
-   Reference {ruta canónica de feature} as the pattern to follow.
-   Do not duplicate existing abstractions.
```

#### ENVIRONMENT

```markdown
## ENVIRONMENT

| Field       | Value                |
| ----------- | -------------------- |
| OS          | {OS}                 |
| Main branch | `{rama}`             |
| Repository  | `{org/repo}`         |
| CI pipeline | `{ruta a CI config}` |
```

#### EXPECTED OUTPUT

```markdown
## EXPECTED OUTPUT

-   Functional code following all mandatory patterns.
-   All new files include corresponding tests; test suite passes green.
-   Minimal changeset — avoid modifying files outside declared scope.
-   Brief explanation only when a non-obvious design decision was made.
```

#### NOTES

```markdown
## NOTES

-   Full docs: `{ruta a docs}`
-   Reference pattern: `{ruta a feature canónica}`
-   Do not merge if CI gates fail.
-   Use `TODO:` inline for missing information.
```

---

### 4. Generar archivos `.devin/agents/{nombre}/AGENT.md`

Cada archivo de agente DEBE tener **YAML frontmatter** seguido de contenido Markdown:

```yaml
---
name: { nombre-del-rol }
description: >
    {2-3 líneas describiendo qué hace este agente y cuándo invocarlo — en inglés.}
model: sonnet
allowed-tools:
    - read
    - grep
    - glob
    # Herramientas de escritura solo para agentes que modifican código:
    # - write
    # - edit
    # - exec   # build / test / lint
    # Ejecución solo cuando sea necesario:
    # - exec
    # - exec               # run build / test / lint / typecheck
permissions:
    allow:
        # - Exec({comando específico})
        # - Write({patrón glob específico})
    deny:
        # - write  (para agentes de solo lectura como analyst, reviewer)
        # - edit
        # - run
---
```

**Secciones obligatorias en el cuerpo del AGENT.md (en inglés):**

1. **## Role** — Un párrafo describiendo la responsabilidad del agente.
2. **## Tasks** — Lista numerada de tareas específicas que realiza.
3. **## Context** — Enlaces a docs relevantes, archivos de referencia, rutas de config.
4. **## How to use this subagent** — Ejemplo de cadena de invocación.
5. **## Output** — Formato de salida esperado (checklist, reporte, lista de archivos).

**Para orquestadores, añadir:** 6. **## Workflow** — Pasos por fases con gates `⏸️ PAUSE — Human approval required`. 7. **## Quick Reference** — Lista condensada de pasos para ejecución rápida.

**Convenciones de nombrado de agentes:**

-   Workers: `{rol}` → ej. `coder`, `tester`, `reviewer`, `analyst`
-   Orquestadores: `{fase}-orchestrator` → ej. `builder-orchestrator`, `debug-orchestrator`

**Conjunto mínimo de agentes para cualquier proyecto:**

| Agente     | Tipo                  | Propósito                           |
| ---------- | --------------------- | ----------------------------------- |
| `coder`    | Worker                | Implementa código fuente            |
| `tester`   | Worker                | Escribe y ejecuta tests             |
| `reviewer` | Worker (solo lectura) | Auditoría de convenciones pre-merge |

**Conjunto extendido (proyectos complejos):**

| Agente                 | Tipo                  | Propósito                                  |
| ---------------------- | --------------------- | ------------------------------------------ |
| `analyst`              | Worker (solo lectura) | Análisis de requisitos                     |
| `architect`            | Worker (solo lectura) | Diseño de arquitectura                     |
| `engineer`             | Worker                | Scaffolding y setup de rama                |
| `cleaner`              | Worker                | Lint, format, eliminación de código muerto |
| `builder-orchestrator` | Orquestador           | Scaffold → Implementar → Test → Clean      |
| `debug-orchestrator`   | Orquestador           | Triage → Root cause → Fix                  |

---

### 5. Generar archivos `.devin/skills/{nombre}/SKILL.md`

Cada archivo de skill DEBE tener **YAML frontmatter**:

```yaml
---
name: { nombre-del-skill }
description: >
    {2-3 líneas de descripción — en inglés. Incluir frases trigger:
    "Use when the user asks to..."}
allowed-tools:
    - read
    - edit
    - grep
    - glob
    # - exec (solo si se necesitan comandos)
permissions:
    allow:
        - Write({patrón glob específico})
        # - Exec({comando específico})
triggers:
    - user
    - model
---
```

**Secciones obligatorias en el cuerpo del SKILL.md (en inglés):**

1. **## Quick Reference** — Resumen de "When to use" + pasos numerados (máx 15 líneas).
2. **## When to use** — Lista de bullets con triggers positivos.
3. **## Instructions** — Paso a paso con `### Step N — Title`.
    - Marcar gates de aprobación humana: `⚠️ HUMAN-IN-THE-LOOP`
    - Referenciar templates: `> Template: \`templates/{archivo}.tpl\` — read when generating.`
4. **## Humans-in-the-loop** — Tabla de pasos que requieren confirmación del usuario.
5. **## Examples** — Ejemplos de entrada/salida.
6. **## Triggering Queries** — Listas de Positive (debería activar) y Negative (NO debería activar).
7. **## Resources** — Enlaces a docs, implementaciones de referencia, skills relacionados.

**Estructura de carpeta del skill:**

```
.devin/skills/{nombre-skill}/
├── SKILL.md              # Definición principal del skill
├── templates/            # Templates de código (.tpl) — opcional
│   └── step-N-{descripción}.{ext}.tpl
└── rules/                # Reglas de lint/patrón — opcional
    └── {nombre-regla}.md
```

**Identificar skills analizando el proyecto para patrones repetibles:**

-   Operaciones CRUD → `add-{entidad}-view`
-   Patrones de testing → `write-unit-tests`
-   Configuración → `configure-environment`
-   i18n/l10n → `add-translations`
-   Flujo Git → `git-workflow`
-   Scaffolding → `scaffold-feature`

---

### 6. Generar archivos `.devin/knowledge/{tema}.md`

Los archivos de knowledge son **documentos de referencia persistentes** que Devin consulta
antes de comenzar cualquier trabajo. Contienen patrones, convenciones y decisiones específicas del proyecto.

**Formato:** Markdown plano con tablas, bloques de código y ejemplos. No necesitan YAML frontmatter.
**El contenido DEBE estar en inglés.**

**Identificar archivos de knowledge a partir de:**

-   Patrones arquitectónicos → ej. `angular-patterns.md`, `react-patterns.md`
-   Convenciones de testing → ej. `testing-conventions.md`
-   Setup de entorno/config → ej. `environment-config.md`
-   Checklists de code review → ej. `code-review-checklist.md`
-   Flujo Git → ej. `git-workflow.md`
-   Uso de design system → ej. `ui-library-components.md`
-   Reglas de linter → ej. `linter-rules.md`

**Conjunto mínimo de knowledge para cualquier proyecto:**

1. `{framework}-patterns.md` — Patrones arquitectónicos y convenciones
2. `testing-conventions.md` — Framework de test, estrategia de mocking, cobertura
3. `git-workflow.md` — Estrategia de branching, convenciones de commit, proceso de PR

---

### 7. Reglas de estilo

-   Títulos de sección en **UPPERCASE** en AGENTS.md
-   Títulos con `##` heading en AGENT.md y SKILL.md
-   Separar secciones con `---` (líneas horizontales)
-   Lenguaje claro, imperativo y accionable
-   Sin explicaciones externas — solo contenido sobre el que Devin pueda actuar
-   Usar placeholders `TODO:` donde falte información
-   **Todo el contenido generado en inglés**

### 8. Salida

Entregar el set completo de archivos:

1. `AGENTS.md` (raíz) — < 200 líneas
2. `.devin/agents/{nombre}/AGENT.md` — uno por cada rol identificado
3. `.devin/skills/{nombre}/SKILL.md` — uno por cada patrón repetible identificado
4. `.devin/knowledge/{tema}.md` — uno por cada dominio de conocimiento identificado

Formatear cada archivo como:

```
---

File: `{ruta}`
---

[contenido en inglés]
```
