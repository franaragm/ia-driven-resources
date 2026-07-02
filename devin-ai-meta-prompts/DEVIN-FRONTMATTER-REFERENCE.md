# Devin Frontmatter & Tools — Canonical Reference

> Fuente única de verdad para el **frontmatter YAML** y las **herramientas** que usan skills
> (`.devin/skills/<name>/SKILL.md`) y subagentes personalizados (`.devin/agents/<name>/AGENT.md`).
> Todos los metaprompts de esta carpeta deben ceñirse a este esquema. Verificado contra la
> documentación oficial de Devin CLI (extensibility/skills/creating-skills, subagents, reference/permissions).

Aunque este documento está en español, **el contenido que generan los metaprompts (SKILL.md, AGENT.md,
knowledge) va en inglés.**

---

## 1. Ubicación de ficheros

| Tipo      | Proyecto (versionado)                                                | Global (no versionado)                                         |
| --------- | -------------------------------------------------------------------- | -------------------------------------------------------------- |
| Skill     | `.devin/skills/<name>/SKILL.md`                                      | `~/.config/devin/skills/` · Windows: `%APPDATA%\devin\skills\` |
| Subagente | `.devin/agents/<name>/AGENT.md` (o `.agents/agents/<name>/AGENT.md`) | `~/.config/devin/agents/` · Windows: `%APPDATA%\devin\agents\` |

El **nombre del directorio** es el identificador (skill: `/my-skill`; subagente: perfil `my-agent`).

---

## 2. Frontmatter (skills y subagentes usan el MISMO frontmatter)

```yaml
---
name: my-skill # display name / identificador
description: What it does and when to use it (shown in completions)
argument-hint: '[file] [options]' # opcional (solo skills)
model: sonnet # opcional — ver §4
subagent: true # opcional (solo skills) — correr el skill como subagente
agent: reviewer # opcional (solo skills) — correr como subagente con este perfil
max-nesting: 2 # opcional (solo subagentes) — permitir spawns anidados
allowed-tools: # opcional — restringe las tools (default: todas)
    - read
    - grep
    - glob
    - exec
permissions: # opcional — overrides de permisos (ver §5)
    allow:
        - Read(src/**)
        - Exec(npm run test)
    ask:
        - Write(**)
    deny:
        - exec
triggers: # opcional (solo skills) — default [user, model]
    - user
    - model
---
Prompt / system prompt content (en inglés)...
```

### Campos

| Campo           | Ámbito    | Notas                                                                                     |
| --------------- | --------- | ----------------------------------------------------------------------------------------- |
| `name`          | ambos     | kebab-case, único, verbo+sustantivo (`add-data-table-view`).                              |
| `description`   | ambos     | En inglés; incluir "Invoke when: ..." / frases trigger naturales.                         |
| `argument-hint` | skill     | Hint tras el comando; se interpola con `$1`, `$2`, `$ARGUMENTS`.                          |
| `model`         | ambos     | Ver §4. **NO** usar `claude-sonnet-4-5` (ese es un id de API, no un modelo de Devin CLI). |
| `subagent`      | skill     | `true` → corre el skill como subagente (`subagent_general`). Experimental.                |
| `agent`         | skill     | Nombre de un perfil de subagente (built-in o custom). Tiene prioridad sobre `subagent`.   |
| `max-nesting`   | subagente | Profundidad de anidamiento permitida (por defecto los subagentes no crean subagentes).    |
| `allowed-tools` | ambos     | Mínimo privilegio (ver §3).                                                               |
| `permissions`   | ambos     | `allow` / `ask` / `deny` (ver §5).                                                        |
| `triggers`      | skill     | `[user, model]` salvo skills solo-manuales (`[user]`).                                    |

---

## 3. Herramientas (nombres CANÓNICOS de Devin CLI)

Usar SIEMPRE estos nombres (no los de IDE/Windsurf como `read_file`, `grep_search`, `create_file`,
`edit_file`, `str_replace_editor`, `semantic_search`, `get_errors`, `run_command`):

| Tool                         | Uso                                                            |
| ---------------------------- | -------------------------------------------------------------- |
| `read`                       | Leer ficheros                                                  |
| `grep`                       | Buscar por contenido (regex)                                   |
| `glob` / `find_file_by_name` | Buscar por nombre/patrón                                       |
| `edit`                       | Editar ficheros existentes                                     |
| `write`                      | Crear/sobrescribir ficheros                                    |
| `exec`                       | Ejecutar comandos de shell (build, test, lint, git, typecheck) |
| `web_search`, `webfetch`     | Búsqueda/lectura web (solo si se necesita investigación)       |

Equivalencias frecuentes al migrar prompts antiguos: `read_file→read`, `grep_search→grep`,
`file_search→glob`, `create_file→write`, `edit_file→edit`, `str_replace_editor→edit`,
`get_errors→exec` (ejecutar typecheck/build), `semantic_search→grep`/`glob`.

### Perfiles de subagente por defecto

-   `subagent_explore` — solo lectura (research). Tools: read/grep/glob + web_search; no edita ni fetch de URLs arbitrarias.
-   `subagent_general` — propósito general con cambios de código (full tools en foreground; solo pre-aprobadas en background).

---

## 4. Modelos (valores válidos de `model`)

Mismos valores que el flag `--model`: **`opus`**, **`sonnet`**, **`swe`**, **`codex`**.

| Elegir          | Cuándo                                                  |
| --------------- | ------------------------------------------------------- |
| `opus`          | Tareas complejas / arquitectura / razonamiento profundo |
| `sonnet`        | Default equilibrado para la mayoría de skills/agents    |
| `swe` / `codex` | Tareas rápidas y acotadas (lint fix, formateo)          |

Tras ejecutar un skill con `model`, la sesión vuelve al modelo previo.

---

## 5. Permisos (orden de precedencia: deny > ask > allow)

Cuando el agente llama a una tool, se evalúa: **deny** (bloquea) → **ask** (siempre pregunta) →
**allow** (procede sin preguntar) → si nada coincide, pregunta.

-   **Scopes**: `Read(<glob>)`, `Write(<glob>)`, `Exec(<comando>)` (p. ej. `Exec(npm run test)`).
-   **Tokens de tool desnudos** en `deny`/`allow`: `write`, `edit`, `exec`, `read`, ...
-   **Mínimo privilegio**: nunca `Write(**)` en un skill; usar globs específicos de lo que toca.
-   **Subagente de solo lectura** (analyst, reviewer, explorer): `permissions.deny: [write, edit, exec]`.
-   **Subagente de escritura** (coder, tester): `allow: [Exec(<comandos concretos>), Write(<glob>)]`.

---

## 6. Subagentes personalizados (`AGENT.md`)

-   Mismo frontmatter que un skill; **el cuerpo ES el system prompt del subagente** (no una plantilla de
    documentación). Redáctalo en segunda persona: "You are a ... subagent. Your job is to ...".
-   Un subagente comparte tools y contexto de código con el padre, pero **no** hereda su historial.
-   Por defecto un subagente **no** crea subagentes (usar `max-nesting` para permitirlo, con cuidado por coste).

---

## 7. Orquestación con skills-como-subagentes (patrón nativo)

Además de los `AGENT.md` orquestadores, Devin permite orquestar con **skills**: define skills con
`subagent: true` (tareas acotadas) y un skill "orquestador" normal que los invoca en secuencia. La
anidación es siempre **de un nivel**: el orquestador crea subagentes, y esos ejecutan todo lo demás inline.

---

## 8. Contexto colaborativo (merge-safe)

Todo lo que estos metaprompts generen bajo `.devin/` debe seguir el sistema de contexto colaborativo:
una decisión = un fichero en `.devin/knowledge/decisions/`, knowledge granular (< 120 líneas), commits de
contexto separados. Ver `.devin/knowledge/context-collaboration.md` y [`DEVIN-CONTEXT-SYSTEM.md`](./DEVIN-CONTEXT-SYSTEM.md).

---

## 9. Checklist de conformidad (todo generado debe cumplirlo)

-   [ ] `model` es uno de `opus|sonnet|swe|codex` (nunca `claude-sonnet-4-5`).
-   [ ] `allowed-tools` usa nombres Devin (`read/grep/glob/edit/write/exec/...`), no nombres de IDE.
-   [ ] `permissions` usa `allow`/`ask`/`deny` con scopes `Read/Write/Exec` y mínimo privilegio.
-   [ ] Subagentes de solo lectura: `deny: [write, edit, exec]`.
-   [ ] Skill/subagente en la carpeta correcta (`.devin/skills|agents/<name>/`).
-   [ ] `AGENT.md` = frontmatter + **system prompt** en inglés.
-   [ ] Contenido generado en inglés; ficheros de knowledge < 120 líneas.
