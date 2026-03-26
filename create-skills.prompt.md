Objetivo:
Crear uno o varios skills autocontenidos, reutilizables y correctamente documentados en `.agents/skills/`, a partir de un documento detallado de proyecto (briefing, PRD, especificación técnica, etc.), buscando en `docs/` toda la documentación para enriquecer contexto y pasos.

El sistema debe:
- Identificar **todos los skills necesarios** para cubrir completamente el proyecto.
- Revisar los skills ya existentes en `.agents/skills/` y su documentación.
- Determinar qué skills ya están cubiertos y cuáles faltan.
- Crear únicamente los skills faltantes o incompletos.
- Evitar duplicidades y favorecer reutilización.
- Generar **todos los skills en inglés** (nombre, descripción, contenido completo del SKILL.md).

Además, debe identificar claramente qué skills son:
- **Propios del proyecto**
- **Reutilizables en otros proyectos**

Incluir puntos de **Humans-in-the-loop** para que la skill consulte al usuario cuando haya decisiones críticas o ambiguas.

---

Instrucciones:

1. Auditoría de skills existentes
   - Revisar `.agents/skills/` para identificar skills ya creados.
   - Analizar su documentación (`SKILL.md`) y capacidades.
   - Determinar cobertura actual respecto al proyecto.
   - Detectar:
     - Skills reutilizables ya disponibles
     - Skills incompletos o mejorables
     - Gaps funcionales (skills faltantes)

2. Analizar el documento del proyecto
   - Extraer objetivos, alcance y contexto.
   - Identificar procesos repetibles, tareas automatizables y flujos.
   - Detectar inputs/outputs esperados y formatos.
   - Detectar restricciones, dependencias y tecnologías.
   - Revisar `docs/` para enriquecer contexto, reglas y ejemplos.

3. Definición global de skills necesarios
   - Crear una lista completa de todos los skills requeridos.
   - Clasificarlos en:
     - Ya existentes (reutilizar)
     - Necesitan mejora
     - Nuevos (a crear)
   - Justificar brevemente cada decisión.

4. Definir cada skill (solo nuevos o a mejorar)
   - Determinar qué hace y cuándo se activa.
   - Nombre en inglés (`kebab-case`), único y descriptivo.
   - Descripción en inglés clara y orientada a triggering.
   - Indicar:
     - `reusable: true` o `false`
   - Identificar pasos críticos donde aplicar **Humans-in-the-loop**.

5. Plan de test y validación
   - Diseñar 2-3 prompts por skill.
   - Definir criterios de éxito (objetivos o cualitativos).
   - Incluir casos con interacción Humans-in-the-loop.

6. Redactar el SKILL.md (EN INGLÉS OBLIGATORIAMENTE)
   - Frontmatter YAML:
     ---
     name: <skill_name>
     description: <what it does and when to use it>
     reusable: <true|false>
     ---
   - Contenido en inglés:
     - **When to use**
     - **Instructions** (imperative, paso a paso)
       - Marcar claramente pasos con pausa: **Ask user before proceeding**
     - **Humans-in-the-loop**
     - **Examples**
     - **Resources (optional)**

7. Reglas de calidad
   - Todo el contenido debe estar en inglés.
   - Evitar duplicación de skills existentes.
   - Priorizar reutilización.
   - Mantener claridad, concisión y estructura.
   - SKILL.md < 500 líneas.
   - Sin código malicioso.
   - Referenciar explícitamente recursos necesarios.

8. Optimización de triggering
   - Para cada skill:
     - 8-10 queries que lo disparan
     - 8-10 que NO lo disparan
   - Incluir edge cases y ambigüedades.
   - Incluir casos con Humans-in-the-loop.

9. Entrega final
   - Crear solo los skills faltantes en:
     `.agents/skills/<skill_name>/SKILL.md`
   - Generar:
     - `evals/evals.json` con prompts de test
   - Incluir opcionales:
     - `scripts/`
     - `references/`
     - `assets/`

---

Plantilla de carpeta:

.agents/skills/<skill_name>/
├── SKILL.md
├── scripts/
├── references/
└── assets/

---

Notas clave:
- No asumir que un solo skill es suficiente: dividir en múltiples si es necesario.
- Siempre validar contra skills existentes antes de crear nuevos.
- Todos los outputs finales deben estar en inglés.