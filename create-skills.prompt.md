Objetivo:
Crear un nuevo skill autocontenido, reutilizable y correctamente documentado en `.agents/skills/`, a partir de un documento detallado de proyecto (briefing, PRD, especificación técnica, etc.), buscando en `docs/` toda la documentación para enriquecer contexto y pasos. Identificar claramente qué skills son **propios del proyecto** y cuáles pueden ser **reutilizables en otros proyectos**.

Instrucciones:

1. Analizar el documento
   - Extraer objetivos, alcance y contexto del proyecto.
   - Identificar procesos repetibles, tareas automatizables o flujos de información.
   - Detectar inputs/outputs esperados y formatos requeridos.
   - Detectar restricciones, dependencias y tecnologías mencionadas.
   - Revisar `docs/` para obtener reglas, ejemplos o documentación relevante.

2. Definir la intención del skill
   - Determinar qué hace el skill y en qué contexto debe activarse.
   - Redactar el nombre del skill en inglés (`kebab-case`), descriptivo y único.
   - Especificar la descripción de activación: cuándo el skill debe dispararse y qué tarea resuelve.
   - Marcar explícitamente si el skill es **reusable en otros proyectos** o es **propio del proyecto**.

3. Plan de test y validación
   - Diseñar 2-3 prompts de ejemplo que simulen situaciones reales de uso.
   - Determinar criterios de éxito objetivos (para outputs verificables) o cualitativos (para outputs subjetivos).

4. Redactar el SKILL.md
   - Frontmatter YAML obligatorio:
     ---
     name: <skill_name>
     description: <Qué hace y cuándo usar>
     reusable: <true|false>  # true si puede usarse en cualquier proyecto, false si es propio
     ---
   - Markdown con:
     - **When to use**: contextos de activación y aplicabilidad.
     - **Instructions**: pasos detallados, en forma imperativa, para realizar la tarea.
     - **Examples**: inputs/outputs si aplica.
     - **Resources (optional)**: scripts, referencias, plantillas, assets.

5. Reglas de estilo
   - Mantener lenguaje claro, conciso y profesional.
   - Preferir la voz imperativa en instrucciones.
   - Mantener SKILL.md <500 líneas.
   - Asegurarse que la skill no contiene código malicioso ni sorpresas.
   - Referenciar explícitamente cualquier archivo o recurso que el skill necesite.

6. Optimización de triggering
   - Crear 8-10 queries que deberían disparar la skill y 8-10 que no (edge cases, ambigüedades).
   - Revisar con el usuario para ajustar descripciones y cobertura de triggering.

7. Entrega final
   - Carpeta del skill: `.agents/skills/<skill_name>/SKILL.md`
   - Lista de prompts de test: `evals/evals.json`
   - Recursos opcionales: `scripts/`, `references/`, `assets/`

Plantilla de carpeta:

.agents/skills/<skill_name>/
├── SKILL.md
├── scripts/      # opcional, código para tareas repetitivas
├── references/   # opcional, documentación o ejemplos
└── assets/       # opcional, plantillas, iconos, fonts

Ejemplo SKILL.md:

---
name: <skill_name>
description: <Qué hace la skill y cuándo usarla>
reusable: <true|false>
---

# <Skill Name>

## When to use
- TODO: describir contextos de activación

## Instructions
- Step 1: TODO
- Step 2: TODO
- Step 3: TODO

## Examples
Input: TODO
Output: TODO

## Resources (optional)
- scripts/
- references/
- assets/