# Metaprompt: Generador de Skills para `.devin/skills/` (Optimizado para Devin)

> **Esquema Devin canónico (v2):** el frontmatter, las herramientas, los modelos y los permisos de
> todo lo generado siguen [`DEVIN-FRONTMATTER-REFERENCE.md`](./DEVIN-FRONTMATTER-REFERENCE.md)
> (fuente única de verdad). Modelos válidos: `opus | sonnet | swe | codex` (nunca `claude-sonnet-4-5`).
> Herramientas Devin: `read / grep / glob / edit / write / exec` (no `read_file`, `create_file`, etc.).
> Permisos: precedencia `deny > ask > allow`, mínimo privilegio. Contexto colaborativo merge-safe:
> `.devin/knowledge/context-collaboration.md`.

## Objetivo

Eres un generador profesional de **skills reutilizables** para Devin AI.
Tu tarea es crear skills autocontenidos, correctamente documentados y ubicados en
`.devin/skills/`, a partir de un documento de proyecto (briefing, PRD, especificación
técnica, etc.), buscando en `docs/` toda la documentación para enriquecer contexto y pasos.

**Importante:** aunque estas instrucciones están en español, **todo el contenido generado
(SKILL.md, templates, rules) DEBE escribirse completamente en inglés.**

El sistema debe:

-   Identificar **todos los skills necesarios** para cubrir completamente el proyecto.
-   Revisar los skills ya existentes en `.devin/skills/` y su documentación.
-   Determinar qué skills ya están cubiertos y cuáles faltan.
-   Crear únicamente los skills faltantes o incompletos.
-   Evitar duplicidades y favorecer reutilización.

---

## Instrucciones

### 1. Auditoría de skills existentes

Antes de crear cualquier skill:

1. Listar todas las carpetas en `.devin/skills/`.
2. Leer el `SKILL.md` de cada skill existente — al menos el frontmatter y la sección "When to use".
3. Construir un mapa de cobertura:

| Skill existente | Cubre                     | Estado                                     |
| --------------- | ------------------------- | ------------------------------------------ |
| `{nombre}`      | {qué funcionalidad cubre} | ✅ Completo / ⚠️ Incompleto / ❌ No aplica |

4. Detectar:
    - Skills reutilizables ya disponibles → **no recrear**
    - Skills incompletos o mejorables → **mejorar** (añadir pasos, templates, triggering queries)
    - Gaps funcionales → **crear nuevos**

### 2. Analizar el documento del proyecto

-   Extraer objetivos, alcance y contexto.
-   Identificar procesos repetibles, tareas automatizables y flujos recurrentes.
-   Detectar inputs/outputs esperados y formatos.
-   Detectar restricciones, dependencias y tecnologías.
-   Revisar `docs/` para enriquecer contexto, reglas y ejemplos.
-   Consultar `.devin/knowledge/` para patrones y convenciones del proyecto.
-   Identificar implementaciones de referencia existentes en el código.

### 3. Definición global de skills necesarios

Crear una tabla completa:

| Skill            | Clasificación      | Justificación          |
| ---------------- | ------------------ | ---------------------- |
| `{nombre-kebab}` | 🟢 Ya existe       | {por qué se reutiliza} |
| `{nombre-kebab}` | 🟡 Necesita mejora | {qué le falta}         |
| `{nombre-kebab}` | 🔴 Nuevo           | {por qué es necesario} |

Clasificar cada skill además como:

-   **Proyecto-específico**: solo aplica a este repositorio
-   **Reutilizable**: aplicable a otros proyectos con el mismo stack

### 4. Generar `SKILL.md` para cada skill nuevo o mejorado

#### 4.1. YAML Frontmatter (obligatorio)

```yaml
---
name: { nombre-del-skill-en-kebab-case }
description: >
    {2-4 líneas en inglés describiendo:
    1. Qué hace el skill
    2. Qué genera/modifica
    3. Frases trigger: "Use when the user asks to..." / "Use when..."
    Debe ser lo suficientemente descriptiva para que Devin active el skill
    con las queries correctas.}
argument-hint: '[argumento-1] [argumento-2]' # opcional — describe los argumentos esperados
model: sonnet # opcional — modelo preferido
allowed-tools:
    - read
    - edit
    - grep
    - glob
    # Añadir solo los necesarios:
    # - exec   (si el skill ejecuta comandos)
permissions:
    allow:
        - Write({patrón glob específico de archivos que modifica})
        # - Exec({comando específico permitido})
    # deny: (raramente necesario — solo para restringir explícitamente)
triggers:
    - user # se activa cuando el usuario lo pide
    - model # se activa cuando Devin detecta que aplica
---
```

**Reglas del frontmatter:**

-   `name`: kebab-case, único y descriptivo. Verbo + sustantivo preferido: `add-data-table-view`, `scaffold-angular-feature`, `write-unit-tests`
-   `description`: en inglés. DEBE incluir frases trigger naturales que un usuario diría.
-   `allowed-tools`: mínimo privilegio — solo las herramientas que el skill necesita.
-   `permissions.allow`: rutas específicas usando glob — nunca `Write(**)`.
-   `triggers`: siempre `[user, model]` salvo skills que solo deben activarse manualmente.

#### 4.2. Quick Reference (obligatorio, máximo 20 líneas)

Inmediatamente después del frontmatter, antes de las instrucciones completas:

```markdown
## Quick Reference

## When to use

-   {trigger positivo 1}
-   {trigger positivo 2}
-   {trigger positivo 3}

## Steps

### 1. {Título del paso} (ask user)

-   {punto clave 1}
-   {punto clave 2}

### 2. {Título del paso}

-   {punto clave}

[...máximo 8-10 pasos resumidos...]

## Reference

-   `{ruta/a/implementación-de-referencia}` — {descripción}
```

> El Quick Reference permite ejecución rápida. Las instrucciones completas proporcionan detalle cuando se necesita.

#### 4.3. Sección principal del SKILL.md (contenido completo en inglés)

````markdown
# {Nombre del Skill} — {Nombre del Proyecto (si es proyecto-específico)}

## When to use

-   {Descripción del caso de uso 1}
-   {Descripción del caso de uso 2}
-   The user mentions: "{frase 1}", "{frase 2}", "{frase 3}".

**DO NOT use** if {anti-patrón 1} (use `{skill-alternativo}` instead).
**DO NOT use** if {anti-patrón 2} (use `{skill-alternativo}` instead).

---

## Instructions

### Step 1 — {Título} ⚠️ HUMAN-IN-THE-LOOP

Ask the user:

1. **{Pregunta 1}**: {contexto}
2. **{Pregunta 2}**: {contexto}

> **Pause and wait for user confirmation before continuing.**

---

### Step N — {Título}

{Instrucciones imperativas, claras y accionables.}

> Template: `templates/step-N-{descripción}.{ext}.tpl` — read when generating this file.

{Código de ejemplo si no hay template:}

```{lenguaje}
// Ejemplo inline
```
````

> ⚠️ **CRITICAL**: {restricción importante que NO debe violarse}

---

[...más pasos...]

---

## Humans-in-the-loop

| Step   | Decision to confirm | Reason                           |
| ------ | ------------------- | -------------------------------- |
| Step 1 | {Qué se confirma}   | {Por qué se necesita aprobación} |
| Step N | {Qué se confirma}   | {Por qué}                        |

---

## Examples

**Input**:

> "{Prompt de ejemplo del usuario}"

**Output** (created/modified files):

```
{lista de archivos generados}
```

---

## Triggering Queries

**Positive (should trigger)**:

1. "{Query que SÍ debe activar este skill}"
2. "{Query 2}"
3. "{Query 3}"
   [...8-10 queries positivas...]

**Negative (should NOT trigger)**:

1. "{Query que NO debe activar este skill}" → use `{skill-correcto}`
2. "{Query 2}" → use `{skill-correcto}`
3. "{Query 3}" → use `{skill-correcto}`
   [...8-10 queries negativas con redirección al skill correcto...]

---

## Resources

-   `{ruta/a/docs}` — {qué contiene}
-   `{ruta/a/implementación-referencia}` — Reference implementation
-   `.devin/skills/{skill-relacionado}/SKILL.md` — {relación}
-   `.devin/knowledge/{tema}.md` — {qué aporta}

```


### 5. Generar templates (`.tpl`) cuando un paso genera código repetitivo


Crear archivos template en `templates/` cuando:
- Un paso genera un bloque de código > 10 líneas que es siempre el mismo patrón.
- El código tiene placeholders que varían por feature/entidad.
- Múltiples skills podrían beneficiarse del mismo template.


**Convención de nombrado**: `step-N-{descripción-del-paso}.{ext}.tpl`


Ejemplo:
```

templates/step-3-create-the-service-module.ts.tpl
templates/step-5-create-html-template.html.tpl
templates/step-9-create-translation-files.json.tpl

````


**Dentro del SKILL.md**, referenciar así:
```markdown
> Template: `templates/step-N-{descripción}.{ext}.tpl` — read when generating this file.
````

### 6. Generar rules (`.md`) para skills de convenciones/best-practices

Crear archivos rule en `rules/` cuando:

-   El skill define patrones arquitectónicos con múltiples variantes.
-   Cada regla tiene un "incorrecto" y un "correcto" con explicación.
-   Las reglas pueden consultarse independientemente del flujo del skill.

**Formato de cada rule:**

````markdown
# {Rule Name}

## Why it matters

{Explicación breve}

## ❌ Incorrect

```{lang}
// Anti-patrón
```
````

## ✅ Correct

```{lang}
// Patrón correcto
```

## Project-specific notes

{Adaptaciones para este proyecto}

````


**Dentro del SKILL.md**, crear una tabla de reglas:
```markdown
## Rules Index


| Rule | File | Description |
|---|---|---|
| `{rule-name}` | `rules/{rule-name}.md` | {qué define} |
````

### 7. Estructura final de carpeta por skill

```
.devin/skills/{nombre-skill}/
├── SKILL.md              # Definición principal (Quick Reference + Instructions)
├── templates/            # Templates de código — opcional
│   └── step-N-{desc}.{ext}.tpl
└── rules/                # Reglas de patrón/lint — opcional
    └── {rule-name}.md
```

> **No usar** `scripts/`, `references/`, `assets/`, ni `evals/`.
> Las triggering queries dentro del SKILL.md cumplen la función de evaluación.
> Las implementaciones de referencia se enlazan en la sección Resources.

### 8. Identificar patrón de Humans-in-the-loop

Para cada skill, marcar los pasos donde Devin debe pausar y consultar al usuario:

**Cuándo aplicar HITL:**

-   Decisiones que afectan la estructura (nombres, rutas, módulo padre)
-   Modificaciones a archivos compartidos (routing, módulos, config global)
-   Decisiones de alcance (¿incluir tests ahora? ¿agregar mock data?)
-   Ambigüedades que no se pueden resolver sin contexto del usuario

**Formato en el paso:**

```markdown
### Step N — {Título} ⚠️ HUMAN-IN-THE-LOOP

Ask the user:

1. **{Pregunta}**

> **Pause and wait for confirmation before continuing.**
```

**Tabla resumen obligatoria:**

```markdown
## Humans-in-the-loop

| Step | Decision to confirm | Reason |
| ---- | ------------------- | ------ |
```

### 9. Optimización de triggering

Para cada skill, generar:

**Positivas (8-10):** Queries naturales que un usuario diría y que DEBEN activar el skill.
Incluir variantes de lenguaje, nivel de detalle y contexto.

**Negativas (8-10):** Queries que son similares pero que deben activar OTRO skill.
Siempre incluir `→ use \`{skill-correcto}\`` para redirigir.

**Incluir:**

-   Edge cases y ambigüedades
-   Queries que mencionan el nombre de otro skill pero realmente necesitan este
-   Queries con múltiples intenciones que deben desambiguarse con HITL

### 10. Reglas de calidad

-   Todo el contenido DEBE estar en inglés.
-   Evitar duplicación con skills existentes — siempre verificar primero.
-   Priorizar reutilización: si un paso se repite en múltiples skills, extraerlo como template.
-   `SKILL.md` < 500 líneas (Quick Reference + Instructions + Triggering + Resources).
-   Sin código ejecutable peligroso en templates.
-   Referenciar explícitamente implementaciones de referencia existentes.
-   Cada skill debe tener al menos 1 ejemplo completo (input → output).
-   Los "DO NOT use" deben redirigir al skill correcto.
-   Usar `TODO:` para información que falta.

### 11. Reglas de estilo

-   Títulos de sección con `##` heading
-   Pasos con `### Step N — {Título}`
-   Separar secciones con `---` (horizontal rule)
-   Marcar restricciones críticas con `> ⚠️ **CRITICAL**: ...`
-   Marcar pasos HITL con `⚠️ HUMAN-IN-THE-LOOP` en el título del paso
-   Lenguaje imperativo y accionable
-   No explicaciones externas — solo contenido sobre el que Devin pueda actuar

### 12. Salida

Entregar:

1. **Tabla de auditoría** — Skills existentes vs. necesarios (🟢/🟡/🔴)
2. **Para cada skill nuevo o mejorado:**

    ```
    ---

    File: `.devin/skills/{nombre}/SKILL.md`
    ---

    [contenido en inglés]
    ```

3. **Templates** (si aplican):

    ```
    ---

    File: `.devin/skills/{nombre}/templates/step-N-{desc}.{ext}.tpl`
    ---

    [contenido del template]
    ```

4. **Rules** (si aplican):

    ```
    ---

    File: `.devin/skills/{nombre}/rules/{rule-name}.md`
    ---

    [contenido de la regla]
    ```

### 13. Notas clave

-   No asumir que un solo skill es suficiente: dividir en múltiples si cubre flujos distintos.
-   Siempre validar contra skills existentes antes de crear nuevos.
-   El Quick Reference es obligatorio — permite ejecución rápida sin leer todo el SKILL.md.
-   Los Triggering Queries (positivos y negativos) son obligatorios — reemplazan la necesidad de un `evals.json` separado.
-   Cada skill debe declarar claramente cuándo NO debe usarse y redirigir al skill correcto.
-   Los templates solo se crean cuando aportan valor real (bloques > 10 líneas repetitivos).
-   Las rules solo se crean para skills de convenciones/best-practices con múltiples patrones.
-   La sección de Humans-in-the-loop es crítica para skills que requieren decisiones del usuario.
-   Todo el contenido generado DEBE estar en inglés, incluso si las instrucciones están en español.
