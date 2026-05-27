# IA Driven Development Resources

Este repositorio reúne **plantillas, recursos y ejemplos para el desarrollo impulsado por IA**. Está diseñado para desarrolladores, investigadores y entusiastas que quieran experimentar con **agents, prompts, skills, meta-prompts y flujos de creación de agentes**, facilitando un trabajo más rápido y replicable.

## Contenido del repositorio

* **.agents/** – Estructura y ejemplos internos de agentes, prompts y skills.
* **AGENTS.md** – Guía de estructura, reglas y buenas prácticas para crear y mantener agentes.
* **Prompts** – Plantillas y ejemplos de prompts optimizados para distintos casos de uso.
* **Skills** – Habilidades predefinidas que se pueden integrar en agentes para extender su funcionalidad.
* **Meta-prompts** – Archivos `*.meta.prompt.md` para generar agentes, skills y subagents automáticamente.
* **devin-ai-meta-prompts/** – Variantes de prompts meta orientadas a workflows y diseño de arquitecturas.
  * Incluye meta-prompts de `devin` para crear agentes, skills y subagents con enfoque en diseño arquitectónico, análisis de requisitos y planificación de soluciones.
* **res/** – Recursos adicionales y archivos de apoyo.

## Referencia de archivos

| Archivo / carpeta | Descripción |
| --- | --- |
| `.agents/` | Estructura de agentes internos, ejemplos de prompts y skills para aprendizaje y reutilización. |
| `AGENTS.md` | Guía de estructura, reglas y buenas prácticas para crear y mantener agentes. |
| `create-agents.meta.prompt.md` | Meta-prompt para generar agentes nuevos a partir de requisitos. |
| `create-skills.meta.prompt.md` | Meta-prompt para crear skills reutilizables para agentes. |
| `create-subagents.meta.prompt.md` | Meta-prompt para generar subagents especializados dentro de un flujo. |
| `devin-ai-meta-prompts/` | Colección de meta-prompts específicos de Devin orientados a diseño, arquitectura y workflows. |
| `devin-ai-meta-prompts/devin-create-agents.meta.prompt.md` | Meta-prompt Devin para crear agentes con enfoque en solución y arquitectura. |
| `devin-ai-meta-prompts/devin-create-skills.meta.prompt.md` | Meta-prompt Devin para definir skills alineadas al diseño de agentes. |
| `devin-ai-meta-prompts/devin-create-subagents.meta.prompt.md` | Meta-prompt Devin para generar subagents y delegar tareas específicas. |
| `devin-ai-meta-prompts/devin-knowledge-system.meta.prompt.md` | Meta-prompt Devin para extraer y estructurar conocimiento del sistema. |
| `devin-ai-meta-prompts/devin-system-architect.meta.prompt.md` | Meta-prompt Devin para diseñar arquitecturas y definir componentes. |

## Cómo usarlo

1. Clona el repositorio:

```bash
git clone https://github.com/franaragm/ia-driven-resources.git
```

2. Revisa `AGENTS.md` para entender la estructura del repositorio y cómo utilizar los agentes.

3. Explora los archivos de meta-prompts (`*.meta.prompt.md`) para generar nuevos agentes, skills y subagents.

4. Personaliza los prompts y las skills según tus necesidades de desarrollo.

5. Usa la carpeta `.agents/` para ver ejemplos de configuración interna y aprender cómo se organizan los componentes.

