# Metaprompt: Generador de Sistema de Contexto Devin — Colaborativo y Super Robusto (v2)

> **Esquema Devin canónico (v2):** el frontmatter, las herramientas, los modelos y los permisos de
> todo lo generado siguen [`DEVIN-FRONTMATTER-REFERENCE.md`](./DEVIN-FRONTMATTER-REFERENCE.md)
> (fuente única de verdad). Modelos válidos: `opus | sonnet | swe | codex` (nunca `claude-sonnet-4-5`).
> Herramientas Devin: `read / grep / glob / edit / write / exec` (no `read_file`, `create_file`, etc.).
> Permisos: precedencia `deny > ask > allow`, mínimo privilegio. Contexto colaborativo merge-safe:
> `.devin/knowledge/context-collaboration.md`.

## Objetivo

Eres un generador profesional de **sistemas de contexto persistente** para Devin AI. El sistema que
generas debe ser **super robusto para trabajo colaborativo**: varios devs, en ramas distintas, deben poder
hacer crecer el contexto `.devin/` **sin conflictos de merge y sin corromperlo**, y cada sesión debe dejar
el proyecto más inteligente que la anterior.

Tu tarea tiene tres dimensiones:

1. **Crear knowledge files** — referencia en `.devin/knowledge/` que Devin consulta ANTES de trabajar.
2. **Diseñar la captura de conocimiento de sesiones** — persistir aprendizajes al final de cada sesión
   **de forma merge-safe**.
3. **Instalar las 4 capas de robustez colaborativa** — estructura, congelado, redes de seguridad de git y
   gobernanza.

**Importante:** las instrucciones están en español, pero **TODO el contenido generado (knowledge, skills,
agents, docs) DEBE escribirse en inglés.**

---

## Principio rector (el que hace robusto al sistema)

> **El contexto crece AÑADIENDO ficheros nuevos con nombres únicos, no editando/append a ficheros
> compartidos.** Dos ramas que crean ficheros distintos se fusionan sin intervención → **conflicto
> imposible por construcción**. Toda decisión de diseño del generador debe respetar este principio.

El problema que se resuelve: un único fichero append-only (p. ej. `decisions-log.md` con entradas por
arriba) editado por todos → conflictos en cada merge, entradas perdidas/duplicadas, contexto caótico o
contradictorio.

---

## PARTE 0 — Las 4 capas de robustez (OBLIGATORIAS)

El generador DEBE instalar estas cuatro capas, de la más estructural a la de gobernanza.

### Capa 1 — Estructura: "una entrada = un fichero"

-   **Cada decisión de diseño = un fichero nuevo** en `.devin/knowledge/decisions/`.
-   **Skills** y **agents** = una carpeta por elemento (ya son merge-safe).
-   **Knowledge de dominio**: ficheros pequeños y monotema (< 120 líneas). Preferir crear un fichero nuevo
    enfocado antes que engordar uno compartido.

### Capa 2 — Congelar lo antiguo sin reescribir historia

-   Si existe un log append-only, **congélalo como archivo histórico de solo lectura** (cabecera de aviso).
    No migrar entradas antiguas (cambio grande y arriesgado); la convención cambia hacia delante.

### Capa 3 — Redes de seguridad de git

-   `.gitattributes` con `merge=union` para los logs que sigan siendo append-only. Es un driver integrado en
    git (sin config local). Es **plan B** (puede duplicar/reordenar), no el mecanismo principal.

### Capa 4 — Gobernanza / proceso

-   `CODEOWNERS` con regla explícita para `/.devin/` → revisión obligatoria del contexto.
-   **Commits de contexto separados** del código (revisión limpia, cherry-pick fácil).
-   **Rebasear `.devin/` desde la rama de integración a menudo**.
-   **Curación periódica** vía el agente `knowledge-curator`, en un **PR dedicado** (nunca dentro de una feature).
-   **Automatización** vía el skill `harvest-session-knowledge` → la mecánica (naming, merge) es transparente
    para el dev.

---

## PARTE 1 — Knowledge Files

### 1.1 Auditoría previa

Listar `.devin/knowledge/`, leer cada fichero, mapear cobertura, y revisar `docs/`, `skills/`, `agents/`,
`README`, config. Detectar: dominios sin knowledge → crear; obsoletos → actualizar; disperso → extraer;
duplicados → consolidar.

### 1.2 Dominios (conjunto mínimo)

Patrones arquitectónicos, testing, git/CI, config, UI/design, i18n, dev local, linting, code review,
datos/API, estado/reactividad, troubleshooting, decisiones. **Mínimo obligatorio:**

1. `{framework}-patterns.md`
2. `testing-conventions.md`
3. `git-workflow.md`
4. **`context-collaboration.md`** — reglas del contexto colaborativo merge-safe (NUEVO, obligatorio).

### 1.3 Formato

Markdown plano, sin YAML frontmatter, título `# {Topic} — {Project}`, **máx 120 líneas**, `##` sections,
tablas de referencia, bloques CORRECT/WRONG, anti-patrones "NEVER DO", referencias a rutas (no snippets
largos), `TODO:` para pendientes.

### 1.4 Knowledge vs Skill vs Docs

| Tipo      | Ubicación           | Propósito                        | Tamaño       |
| --------- | ------------------- | -------------------------------- | ------------ |
| Knowledge | `.devin/knowledge/` | Qué saber (patrones, referencia) | < 120 líneas |
| Skill     | `.devin/skills/`    | Cómo hacer (pasos)               | < 500 líneas |
| Docs      | `docs/`             | Referencia humana extensa        | sin límite   |

Sistema/proceso complejo → **Docs detallado + un knowledge conciso que lo resuma** (patrón "dos niveles").

---

## PARTE 2 — Captura de conocimiento (merge-safe)

### 2.1 Convención de decisiones (sustituye al log append-only)

Generar `.devin/knowledge/decisions/README.md` (inglés):

-   **Naming**: `YYYY-MM-DD-<branch-or-ticket>-<slug>.md` (el `<branch-or-ticket>` hace único el nombre por autor).
-   **Plantilla por fichero**:

    ```markdown
    # YYYY-MM-DD — <Title>

    -   **Decision**: what was decided.
    -   **Rationale**: why.
    -   **Alternatives**: options considered and why rejected.
    -   **Impact**: files/areas affected, follow-ups.
    -   **Session/Branch**: `<branch>`.
    ```

-   **Reglas**: una decisión por fichero; breve (< 60 líneas); en tu rama **no edites ficheros de decisión de
    otro** (solo añade los tuyos); si una decisión evoluciona, crea un fichero nuevo que la supersede (no
    edites el antiguo).

Si existe `decisions-log.md`: **congélalo** con cabecera `FROZEN ARCHIVE — do NOT add new entries; new
decisions go under ./decisions/`.

### 2.2 Skill `harvest-session-knowledge` (merge-safe)

Frontmatter:

```yaml
---
name: harvest-session-knowledge
description: >
    Extracts learnings from the current Devin session and persists them into .devin/knowledge/.
    Design decisions are saved as ONE FILE PER DECISION under .devin/knowledge/decisions/ (merge-safe),
    never appended to a shared log. Use at end of session or when asked to "save what we learned".
allowed-tools: [read, edit, grep, glob]
permissions:
    allow: [Write(.devin/knowledge/**)]
triggers: [user, model]
---
```

Secciones (inglés):

1. **Quick Reference** — con nota de merge-safety (decisiones = un fichero; preferir fichero nuevo antes que editar uno grande; enlaza `context-collaboration.md`).
2. **When to use** — fin de sesión, bug no trivial, patrón/anti-patrón, convención, petición explícita; NO triviales.
3. **Instructions**:
    - **Step 1 — Classify learnings:**
      | Category | Target | Action |
      |---|---|---|
      | Design decision | `decisions/YYYY-MM-DD-<branch/ticket>-<slug>.md` | **Create a NEW file** (never touch the frozen log) |
      | Bug/workaround | `troubleshooting.md` | Append row (`merge=union` safety net) |
      | New pattern | `{domain}-patterns.md` | Prefer a NEW focused file |
      | Anti-pattern | `{domain}-patterns.md` | WRONG / NEVER DO |
      | Config | `environment-config.md` / `local-dev-setup.md` | Append |
      | Convention | `{relevant}-conventions.md` | Append/update |
    - **Step 2 — Check existing (HITL):** grep para no duplicar; contradicción o dominio nuevo → preguntar.
    - **Step 3 — Persist:** decisión = fichero nuevo; resto en su dominio (crear si autocontenido); respetar 120 líneas.
    - **Step 4 — Verify cross-references:** reportar (no modificar) agentes cuyo `## Context` debería enlazar el knowledge nuevo.
    - **Step 5 — Summary (HITL):** presentar cambios; **recomendar commit de contexto SEPARADO del código**; confirmar antes de commitear.
4. **Humans-in-the-loop** — contradicción, dominio nuevo, resumen final.
5. **Triggering Queries** — positivas ("save what we learned", "harvest session knowledge", ...) y negativas.
6. **Resources** — `context-collaboration.md`, `decisions/README.md`, `knowledge-curator`.

### 2.3 Agente `knowledge-curator`

Frontmatter con `permissions.allow: [Write(.devin/knowledge/**)]`. Rol: **no crea knowledge desde cero; cura, consolida y mejora**.
Tareas: (1) auditar completitud/formato; (2) detectar obsolescencia vs código real; (3) **consolidar duplicados** (incluidos patrones descubiertos en varias ramas); (4) **dividir ficheros > 120 líneas**; (5) verificar cross-refs; (6) reportar gaps.

-   Se ejecuta **en un PR dedicado**, NUNCA dentro de una feature.
-   HITL antes de borrar/mergear/dividir.
-   Output: informe de curación (tabla File | Issue | Severity | Proposed fix + acciones + gaps).

### 2.4 Guía de contexto colaborativo (NUEVO — obligatorio)

Generar `.devin/knowledge/context-collaboration.md` (< 120 líneas, inglés) con:

-   Principio "crecer por ficheros nuevos".
-   Tabla "quién toca qué" + superficie de conflicto.
-   Reglas: una decisión = un fichero; regla de oro (no editar ficheros de otro); knowledge/skills pequeños;
    commits de contexto separados; rebasear a menudo.
-   **Flujo de 2 devs con diagrama** (ver 2.6).
-   Curación en PR dedicado.
-   Redes de seguridad (`.gitattributes`, `CODEOWNERS`).

Si el sistema es complejo, generar además un **doc humano detallado** (p. ej.
`docs/.../DEVIN-CONTEXT-SYSTEM.md`) que amplíe diseño, artefactos, workflow, cobertura y FAQ; enlazarlo
desde el índice de docs y desde `context-collaboration.md` (patrón "dos niveles").

### 2.5 Redes de seguridad de git (NUEVO)

-   `.gitattributes` (raíz):
    ```gitattributes
    .devin/knowledge/decisions-log.md merge=union
    .devin/knowledge/troubleshooting.md merge=union
    ```
-   `CODEOWNERS` (o `.github/CODEOWNERS`), regla explícita:
    ```
    /.devin/ @<equipo-o-responsable>
    ```

### 2.6 Flujo de 2 devs (debe quedar documentado)

```
development (baseline .devin/)
   │  Dev A ramifica            Dev B ramifica
   ▼                            ▼
feature/A                    feature/B
  harvest → decisions/…-A.md     harvest → decisions/…-B.md   (nombre distinto)
  commit de contexto aparte      commit de contexto aparte
   │ merge a development          │ rebase desde development → recoge …-A.md sin conflicto
   ▼                             ▼  merge
development: decisions/…-A.md ✔ y …-B.md ✔ (ambos intactos)
Único conflicto posible: ambos editan el MISMO fichero de dominio → rebase + resolución pequeña y localizada.
```

### 2.7 Integración en orchestrators

Fase final opcional: invocar `harvest-session-knowledge` si hubo aprendizajes notables.

### 2.8 Diagrama del sistema completo

```
Devin Session
  └── Phase final (optional) — harvest-session-knowledge
        ▼
  .devin/knowledge/
    ├── decisions/YYYY-..-<branch>-<slug>.md   (NEW file — merge-safe)
    ├── decisions-log.md                        (FROZEN archive)
    ├── troubleshooting.md                      (append; merge=union)
    ├── context-collaboration.md                (collab rules)
    └── {domain}.md                             (new/small)
Safety nets: .gitattributes(merge=union) + CODEOWNERS(/.devin/)
Periodic: knowledge-curator (dedicated PR) → consolidate/split/dedupe
```

### 2.9 Archivo especial `troubleshooting.md`

Tabla `Symptom | Root Cause | Solution | Prevention`. Append-only, cubierto por `merge=union`.

---

## PARTE 3 — Reglas generales

### 3.1 Calidad

Inglés; sin frontmatter en knowledge; título `# {Topic} — {Project}`; máx 120 líneas; tablas; CORRECT/WRONG;
"NEVER DO"; rutas no snippets; sin narrativa; `TODO:`.

### 3.2 Estilo

`##` headings; fences con lenguaje; tablas a la izquierda; listas con `-`; línea vacía entre secciones; sin
emojis (salvo checklists `- [ ]`).

### 3.3 Crear vs actualizar vs ignorar (merge-safe)

| Situación                               | Acción                                                         |
| --------------------------------------- | -------------------------------------------------------------- |
| **Decisión de diseño (cualquiera)**     | **Crear fichero nuevo** en `decisions/` (nunca append al log)  |
| Descubrimiento nuevo, dominio existente | Preferir **fichero nuevo enfocado**; si encaja, append pequeño |
| Descubrimiento nuevo, dominio nuevo     | **Crear** (con aprobación del usuario)                         |
| Ya documentado y completo               | **Ignorar**                                                    |
| Ya documentado, incompleto              | **Enriquecer**                                                 |
| Información contradictoria              | **HITL** — preguntar                                           |
| Archivo > 120 líneas                    | **Dividir**                                                    |

### 3.4 Salida esperada

Con cabecera `--- File: <ruta> ---` y contenido **en inglés**:

-   Knowledge/auditoría → `.devin/knowledge/{nombre}.md`
-   Guía colaborativa → `.devin/knowledge/context-collaboration.md`
-   Convención de decisiones → `.devin/knowledge/decisions/README.md`
-   Skill de captura → `.devin/skills/harvest-session-knowledge/SKILL.md`
-   Agente curador → `.devin/agents/knowledge-curator/AGENT.md`
-   Redes de seguridad → `.gitattributes` + regla en `CODEOWNERS`
-   (Opcional) doc detallado → `docs/.../DEVIN-CONTEXT-SYSTEM.md`
-   Congelado → cabecera FROZEN en `decisions-log.md` (si existía)
-   Integración en orchestrators → qué añadir al workflow
-   `AGENTS.md` → filas para `## SUBAGENT FILES` y `## REUSABLE SKILLS`, y nota "Knowledge lifecycle" con la
    regla merge-safe (una decisión = un fichero) + enlace a `context-collaboration.md`.

---

## Criterios de aceptación (el sistema NO es robusto si falta alguno)

-   [ ] Las decisiones se guardan como **un fichero por decisión** en `decisions/` (no append a un log).
-   [ ] Existe `decisions/README.md` con naming (`YYYY-MM-DD-<branch/ticket>-<slug>`) y plantilla.
-   [ ] El log antiguo (si existía) está **congelado** con aviso.
-   [ ] Existe `context-collaboration.md` con la tabla "quién toca qué" y el flujo de 2 devs.
-   [ ] `.gitattributes` define `merge=union` para los logs append-only.
-   [ ] `CODEOWNERS` tiene regla explícita para `/.devin/`.
-   [ ] `harvest-session-knowledge` crea fichero de decisión (no edita el log) y recomienda commit de contexto separado.
-   [ ] `knowledge-curator` consolida/divide/deduplica en un PR dedicado.
-   [ ] `AGENTS.md` documenta la regla merge-safe y enlaza la guía.
-   [ ] Todos los ficheros de knowledge < 120 líneas y en inglés.

## Regla final

El conocimiento que no se persiste se pierde; el que se persiste mal, corrompe el contexto. El objetivo es
doble: que la **sesión N+1 sea mejor que la N**, y que **varios devs en ramas distintas** hagan crecer el
contexto **sin conflictos ni caos**, porque el sistema crece **añadiendo ficheros nuevos con nombres
únicos**, con redes de seguridad de git y gobernanza que lo mantienen coherente.
