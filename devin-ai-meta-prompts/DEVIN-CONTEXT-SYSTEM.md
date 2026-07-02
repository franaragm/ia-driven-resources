# Sistema de Contexto Devin

> Documento de referencia **completo** del sistema de contexto colaborativo `.devin/`: cómo está
> diseñado, sus artefactos, el workflow entre varios devs, qué cubre y qué no.
>
> Referencia operativa concisa (para consulta rápida / sesiones de Devin): `.devin/knowledge/context-collaboration.md`.

---

## Tabla de contenidos

- [Sistema de Contexto Devin](#sistema-de-contexto-devin)
  - [Tabla de contenidos](#tabla-de-contenidos)
  - [1. Propósito y problema que resuelve](#1-propósito-y-problema-que-resuelve)
  - [2. Principios de diseño](#2-principios-de-diseño)
    - [Capa 1 — Crecer añadiendo ficheros, no editando compartidos (núcleo)](#capa-1--crecer-añadiendo-ficheros-no-editando-compartidos-núcleo)
    - [Capa 2 — Congelar lo antiguo sin reescribir historia](#capa-2--congelar-lo-antiguo-sin-reescribir-historia)
    - [Capa 3 — Redes de seguridad de Git](#capa-3--redes-de-seguridad-de-git)
    - [Capa 4 — Gobernanza / proceso](#capa-4--gobernanza--proceso)
  - [3. Estructura de directorios `.devin/`](#3-estructura-de-directorios-devin)
  - [4. Inventario de artefactos](#4-inventario-de-artefactos)
  - [5. Tipos de contexto y dónde va cada cosa](#5-tipos-de-contexto-y-dónde-va-cada-cosa)
  - [6. Convención de decisiones (una decisión = un fichero)](#6-convención-de-decisiones-una-decisión--un-fichero)
  - [7. Redes de seguridad de Git](#7-redes-de-seguridad-de-git)
    - [`.gitattributes` — `merge=union`](#gitattributes--mergeunion)
    - [`.github/CODEOWNERS`](#githubcodeowners)
  - [8. Automatización: harvest-session-knowledge](#8-automatización-harvest-session-knowledge)
  - [9. Curación: knowledge-curator](#9-curación-knowledge-curator)
  - [10. Workflow de dos devs (detallado)](#10-workflow-de-dos-devs-detallado)
  - [11. Matriz de cobertura: qué cubre y qué no](#11-matriz-de-cobertura-qué-cubre-y-qué-no)
  - [12. Cómo contribuir al contexto (guía paso a paso)](#12-cómo-contribuir-al-contexto-guía-paso-a-paso)
  - [13. Resolución de conflictos](#13-resolución-de-conflictos)
  - [14. FAQ y casos límite](#14-faq-y-casos-límite)

---

## 1. Propósito y problema que resuelve

El directorio `.devin/` contiene el **contexto compartido** que guía tanto a los desarrolladores como a
las sesiones de Devin AI:

-   `knowledge/` — conocimiento persistente del proyecto (patrones, decisiones, troubleshooting, etc.).
-   `skills/` — recetas reutilizables paso a paso (una carpeta por skill).
-   `agents/` — perfiles de subagentes de Devin (una carpeta por agente).

Este contexto **se actualiza con mucha frecuencia** (potencialmente tras cada petición) y **por varias
personas en ramas distintas**. El enfoque original —un único fichero append-only `decisions-log.md` al
que todos añadían entradas por arriba— producía, en cada merge:

-   **conflictos casi garantizados** (todos editan la misma zona del mismo fichero),
-   riesgo de **perder o duplicar** entradas al resolver el conflicto,
-   deriva hacia un contexto **caótico o contradictorio**.

**Objetivo del sistema:** un contexto colaborativo **sólido** y **transparente** — que los devs casi nunca
tengan conflictos, que las actualizaciones sean en su mayoría automáticas, y que el conocimiento se
mantenga coherente a lo largo del tiempo.

---

## 2. Principios de diseño

El sistema se apoya en **cuatro capas**, de la más estructural a la de gobernanza.

### Capa 1 — Crecer añadiendo ficheros, no editando compartidos (núcleo)

La idea central: **el contexto crece añadiendo ficheros nuevos con nombres únicos**, no ampliando ficheros
compartidos. Dos ramas que tocan ficheros distintos → git los fusiona sin intervención → **conflicto
imposible por construcción**.

-   Cada **decisión de diseño = un fichero nuevo** en `.devin/knowledge/decisions/`.
-   **Skills** y **agents** ya son "una carpeta por elemento" → heredan la misma propiedad.
-   **Knowledge de dominio**: ficheros **pequeños y monotema** (< 120 líneas). Se prefiere **añadir** un
    fichero nuevo enfocado antes que editar uno grande compartido.

> Es la diferencia clave frente al log único: convierte la operación conflictiva (editar un fichero
> compartido) en una operación sin conflicto (crear un fichero nuevo).

### Capa 2 — Congelar lo antiguo sin reescribir historia

`decisions-log.md` se mantiene como **archivo histórico de solo lectura** (con aviso en cabecera). No se
migran las entradas antiguas (sería un cambio enorme y arriesgado); se congela y la convención cambia
hacia delante.

### Capa 3 — Redes de seguridad de Git

Para lo que **siga siendo** append-only (`decisions-log.md`, `troubleshooting.md`), `.gitattributes`
define `merge=union`: en un merge conserva **las líneas de ambos lados** sin marcadores de conflicto.
Es un **plan B** (puede duplicar/reordenar líneas), no el mecanismo principal.

### Capa 4 — Gobernanza / proceso

-   `CODEOWNERS` sobre `/.devin/` → revisión obligatoria de los cambios de contexto.
-   **Commits de contexto separados** del código → revisión limpia y cherry-pick fácil.
-   **Rebasear `.devin/` desde `development` a menudo** → no dejar divergir las ramas.
-   **Curación periódica** con el agente `knowledge-curator` en un **PR dedicado** (no dentro de features).
-   **Automatización** vía el skill `harvest-session-knowledge` → el dev no tiene que pensar en la mecánica
    (transparencia).

---

## 3. Estructura de directorios `.devin/`

```
.devin/
├── knowledge/
│   ├── context-collaboration.md          ← guía operativa concisa del workflow colaborativo
│   ├── decisions/                        ← ⭐ UNA decisión = UN fichero (merge-safe)
│   │   ├── README.md                     ← convención: naming + plantilla + reglas
│   │   └── YYYY-MM-DD-<rama/ticket>-<slug>.md
│   ├── decisions-log.md                  ← ARCHIVO HISTÓRICO congelado (no añadir aquí)
│   ├── troubleshooting.md                ← log append-only (merge=union)
│   ├── i18n-system.md                    ← knowledge de dominio (monotema, < 120 líneas)
│   ├── ui-components.md
│   └── ...                               ← un fichero por dominio
├── skills/
│   └── <skill>/SKILL.md                  ← una carpeta por skill
└── agents/
    └── <agent>/AGENT.md                  ← una carpeta por agente
```

---

## 4. Inventario de artefactos

| Artefacto                | Ruta                                               | Rol en el sistema                                      |
| ------------------------ | -------------------------------------------------- | ------------------------------------------------------ |
| Guía operativa           | `.devin/knowledge/context-collaboration.md`        | Referencia concisa del workflow (consulta rápida)      |
| Carpeta de decisiones    | `.devin/knowledge/decisions/`                      | Una decisión = un fichero (núcleo anti-conflicto)      |
| Convención de decisiones | `.devin/knowledge/decisions/README.md`             | Naming, plantilla y reglas                             |
| Archivo histórico        | `.devin/knowledge/decisions-log.md`                | Log antiguo congelado (solo lectura)                   |
| Red de seguridad merge   | `.gitattributes`                                   | `merge=union` para logs append-only                    |
| Gobernanza de revisión   | `.github/CODEOWNERS`                               | Regla `/.devin/` → revisión obligatoria                |
| Automatización           | `.devin/skills/harvest-session-knowledge/SKILL.md` | Crea ficheros de decisión/knowledge al final de sesión |
| Curación                 | `.devin/agents/knowledge-curator/AGENT.md`         | Deduplica, consolida y parte ficheros grandes          |
| Estrategia de ramas      | `.devin/skills/git-workflow/SKILL.md`              | Branching + reglas de conflicto (incl. `.devin/`)      |
| Índice/JIT               | `AGENTS.md`                                        | Enlaza el ciclo de vida del conocimiento y esta guía   |
| Este documento           | `docs/agent-prompts/DEVIN-CONTEXT-SYSTEM.md`       | Referencia completa y detallada                        |

---

## 5. Tipos de contexto y dónde va cada cosa

| Aprendizaje                              | Fichero destino                                          | Acción                  | Conflicto en merge      |
| ---------------------------------------- | -------------------------------------------------------- | ----------------------- | ----------------------- | --------------------------------- |
| Decisión de diseño                       | `knowledge/decisions/YYYY-MM-DD-<rama/ticket>-<slug>.md` | **Crear fichero nuevo** | ❌ Nunca (nombre único) |
| Skill nuevo                              | `skills/<name>/`                                         | **Crear carpeta**       | ❌ Nunca                |
| Agente nuevo                             | `agents/<name>/`                                         | **Crear carpeta**       | ❌ Nunca                |
| Patrón / dominio nuevo                   | `knowledge/<topic>.md` (nuevo, enfocado)                 | **Crear fichero**       | ❌ Nunca                |
| Bug fix / workaround                     | `knowledge/troubleshooting.md`                           | Añadir fila             | 🟡 `merge=union`        |
| Editar el **mismo** knowledge de dominio | `knowledge/<topic>.md`                                   | Editar                  | ⚠️ Posible (ver §13)    |
| Editar el **mismo** skill/agente         | `skills                                                  | agents/<name>/...`      | Editar                  | ⚠️ Posible (mantenerlos pequeños) |

---

## 6. Convención de decisiones (una decisión = un fichero)

Definida en `.devin/knowledge/decisions/README.md`.

**Naming:**

```
YYYY-MM-DD-<rama-o-ticket>-<slug>.md
# ejemplos:
2026-07-02-user-details-loader-i18n-common-components.md
2026-07-05-JIRA1234-authorizations-ngrx-slice.md
```

-   `YYYY-MM-DD` — fecha de la decisión.
-   `<rama-o-ticket>` — id corto de rama/ticket (hace único el nombre por autor).
-   `<slug>` — 2-5 palabras kebab-case.

**Plantilla del fichero:**

```markdown
# YYYY-MM-DD — <Título>

-   **Decision**: qué se decidió (1-3 líneas).
-   **Rationale**: por qué.
-   **Alternatives**: opciones consideradas y por qué se descartaron.
-   **Impact**: ficheros/áreas afectadas, follow-ups.
-   **Session/Branch**: `<rama>`.
```

**Reglas:**

-   **Una decisión por fichero.** Nunca añadir una segunda decisión a un fichero existente (reintroduce conflictos).
-   Breve y factual (objetivo < 60 líneas).
-   En tu rama de feature, **no edites los ficheros de decisión de otro** — solo añade los tuyos.
-   `harvest-session-knowledge` los crea automáticamente al final de sesión.
-   La consolidación periódica la hace `knowledge-curator`.

---

## 7. Redes de seguridad de Git

### `.gitattributes` — `merge=union`

```gitattributes
.devin/knowledge/decisions-log.md merge=union
.devin/knowledge/troubleshooting.md merge=union
```

`merge=union` es un driver de merge **integrado en Git** (no requiere configuración local). En un merge
combina las líneas de ambos lados sin marcadores de conflicto. **Limitación:** puede duplicar o reordenar
líneas → por eso es una red de seguridad, no el mecanismo principal (que es "un fichero por entrada").

### `.github/CODEOWNERS`

```
/.devin/ @code_owner
```

Fuerza que cualquier cambio bajo `/.devin/` pase por revisión, evitando contexto contradictorio/caótico.

---

## 8. Automatización: harvest-session-knowledge

El skill `harvest-session-knowledge` se ejecuta al final de una sesión (o cuando el usuario pide "guardar
lo aprendido"):

1. Clasifica cada aprendizaje (decisión / bug / patrón / convención / config).
2. Comprueba que no esté ya documentado (`grep` sobre `.devin/knowledge/`).
3. Persiste: **las decisiones como fichero nuevo** en `decisions/`; el resto en el fichero de dominio
   correspondiente (creando uno nuevo si el aprendizaje es autocontenido).
4. Pide confirmación al usuario antes de finalizar (human-in-the-loop).

Esto hace el sistema **transparente**: el dev no tiene que recordar la mecánica de naming ni de merge; la
parte que más conflicto generaba (las decisiones) se automatiza como creación de fichero.

---

## 9. Curación: knowledge-curator

El agente `knowledge-curator` mantiene la calidad a largo plazo, idealmente en un **PR dedicado** (no
dentro de una feature):

-   Detecta contenido obsoleto.
-   **Consolida duplicados** (incluidos patrones descubiertos en varias ramas a la vez).
-   **Parte ficheros > 120 líneas** en unidades más enfocadas.
-   Verifica cross-references entre agentes/skills y knowledge.
-   Reporta huecos de cobertura.

Así, cuando un mismo patrón se descubre en varias ramas, **se consolida en la curación**, no se pelea en
cada merge.

---

## 10. Workflow de dos devs (detallado)

```
development  (baseline .devin/ compartido)
   │  Dev A ramifica            Dev B ramifica
   ▼                            ▼
feature/A                    feature/B
  · codifica                   · codifica
  · harvest → decisions/          · harvest → decisions/
      2026-07-02-A-xxx.md            2026-07-02-B-yyy.md   (nombre distinto)
  · patrón nuevo → knowledge/       · edita i18n-system.md (mismo dominio ⚠)
      nuevo-topic.md (nuevo)
  · commit de contexto aparte   · commit de contexto aparte
   │                            │
   │ merge/PR a development      │ (antes de mergear) rebase desde development
   ▼                            ▼   → recoge 2026-07-02-A-xxx.md sin conflicto
development ◀──────────────────  merge/PR B
  decisions/…-A.md  ✔
  decisions/…-B.md  ✔   (ambos intactos, sin pelea)
```

**Narrativa:**

1. Dev A y Dev B ramifican desde `development` (mismo `.devin/` base).
2. Cada uno trabaja; al cerrar sesión, `harvest` **crea** sus ficheros de decisión (nombres únicos) y
   cualquier knowledge nuevo enfocado. El contexto va en un **commit separado** del código.
3. Dev A mergea a `development` primero → sus ficheros entran sin fricción.
4. Dev B **rebasa desde `development`** antes de mergear: como los ficheros de A tienen otro nombre,
   coexisten sin conflicto; B mergea.
5. `development` acaba con los ficheros de A **y** de B, intactos. Sin entradas perdidas ni duplicadas.
6. **Único punto de fricción posible:** si A y B editaron **el mismo** fichero de dominio → el segundo en
   mergear rebasa y resuelve un conflicto **pequeño y localizado** (ver §13).

**Comandos típicos de sincronización (Dev B, antes de mergear):**

```bash
git fetch origin
git rebase origin/development     # recoge los ficheros nuevos de A sin conflicto
# (resolver aquí solo si ambos editaron el MISMO fichero de dominio)
```

---

## 11. Matriz de cobertura: qué cubre y qué no

| Caso                                            | ¿Cubierto?      | Mecanismo                                                          |
| ----------------------------------------------- | --------------- | ------------------------------------------------------------------ |
| Decisiones concurrentes (el ~90% del dolor)     | ✅ Total        | Un fichero por decisión (nombre único)                             |
| Skills/agents nuevos en paralelo                | ✅ Total        | Carpeta por elemento                                               |
| Patrón/knowledge nuevo en paralelo              | ✅ Total        | Fichero nuevo enfocado                                             |
| Logs append-only restantes                      | 🟡 Mitigado     | `merge=union`                                                      |
| Dos devs editan el **mismo** fichero de dominio | ⚠️ Coordinación | Regla de oro + preferir fichero nuevo + rebase → conflicto pequeño |
| Dos devs editan el **mismo** skill/agente       | ⚠️ Coordinación | Mantenerlos pequeños/enfocados                                     |
| Contradicciones / caos semántico                | ✅ Gobernanza   | `CODEOWNERS` + curación en PR dedicado                             |
| Deriva a largo plazo                            | ✅ Gobernanza   | `knowledge-curator` periódico                                      |

**Límite honesto:** el sistema **elimina** los conflictos de adición (lo habitual) y **minimiza** los de
edición del mismo fichero (lo raro), pero **no puede eliminar** el conflicto si dos personas editan
literalmente la misma línea del mismo fichero de dominio. Ahí siempre hará falta un rebase con resolución
(ahora trivial y localizada) y la disciplina de "granular + añadir en vez de editar".

---

## 12. Cómo contribuir al contexto (guía paso a paso)

1. **Antes de empezar**: ramifica desde `development` (baseline actual de `.devin/`).
2. **Durante el trabajo**: consulta `.devin/knowledge/` para no reinventar patrones.
3. **Al cerrar sesión / tras un aprendizaje notable**:
    - Decisión de diseño → crea `knowledge/decisions/YYYY-MM-DD-<rama>-<slug>.md` (usa `harvest`).
    - Patrón nuevo autocontenido → crea `knowledge/<topic>.md` nuevo (no engordes uno grande).
    - Bug/workaround → añade fila en `troubleshooting.md`.
    - Skill/agente nuevo → nueva carpeta en `skills/`|`agents/`.
4. **Commitea el contexto en un commit separado** del código.
5. **Rebasea `.devin/` desde `development`** con frecuencia.
6. **No edites** los ficheros de decisión/knowledge de otro en tu rama; solo añade los tuyos.
7. **PR**: `CODEOWNERS` pedirá revisión de `/.devin/`.
8. **Periódicamente**: alguien lanza `knowledge-curator` en un PR dedicado para consolidar.

---

## 13. Resolución de conflictos

| Situación                                        | Cómo resolver                                                                                                                                                      |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Ficheros de decisión distintos (caso normal)     | No hay conflicto — coexisten                                                                                                                                       |
| Ambos añaden filas a `troubleshooting.md`        | `merge=union` mantiene ambas (revisar duplicados en curación)                                                                                                      |
| Ambos editan el **mismo** `knowledge/<topic>.md` | El segundo en mergear **rebasa desde `development`** y resuelve un conflicto pequeño y localizado. Preferir, si es posible, haber creado un fichero nuevo enfocado |
| Ambos editan el **mismo** `SKILL.md`/`AGENT.md`  | Coordinar; mantener skills/agentes pequeños para que sea raro                                                                                                      |

> Regla general del proyecto (ver `git-workflow`): los conflictos se resuelven **en la rama hija**, nunca
> en la rama padre / `development`.

---

## 14. FAQ y casos límite

**¿Puedo añadir una entrada a `decisions-log.md`?** No. Está congelado; crea un fichero en `decisions/`.

**¿Y si mi decisión evoluciona más tarde?** Crea un **fichero nuevo** que la supersede y menciona al
anterior en su `Rationale`/`Impact`. No edites el fichero antiguo de otra sesión.

**¿Dónde van las traducciones de componentes reutilizables?** No es contexto Devin; ver
`.devin/knowledge/i18n-system.md` (scope `common/components/`).

**¿`merge=union` no puede duplicar líneas?** Sí, por eso es red de seguridad. La curación periódica
deduplica. Para lo importante (decisiones) usamos un fichero por entrada, no `union`.

**¿Necesito configurar algo en mi git local para `merge=union`?** No, es un driver integrado en Git; basta
con el `.gitattributes` versionado.

---
