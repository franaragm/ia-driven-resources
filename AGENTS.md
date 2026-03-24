# AGENTS.md

## CONTEXTO

**Proyecto:** TODO
**Propósito:** TODO
**Ámbito:** TODO

**Componentes principales:**

* TODO: listar dominios o servicios clave

**Restricciones:**

* No asumir requisitos no definidos
* No modificar comportamiento fuera del scope

---

## ESTRUCTURA GLOBAL

```text id="gpldfh"
.
├── AGENTS.md
├── .agents/              
│   ├── agents/           # Worker subagents y orchestrators
│   ├── skills/           # Skills reutilizables
│   │   ├── TODO: skill1.agent.md
│   │   ├── TODO: skill2.agent.md
│   ├── prompts/          # Prompts base y plantillas
│   │   ├── TODO: prompt1.md
│   │   ├── TODO: prompt2.md
├── apps/        # TODO
├── services/    # TODO
├── packages/    # TODO
├── libs/        # TODO
├── infra/       # TODO
├── src/         # TODO (si aplica)
├── tests/
└── docs/
```

---

## JIT INDEX (NAVEGACIÓN)

```text
- apps/            → TODO: descripción
- services/        → TODO: descripción
- packages/        → TODO: descripción
- libs/            → TODO: descripción
```

* Consultar `AGENTS.md` locales si existen
* No explorar el repo completo innecesariamente

---

## SUBAGENT FILES

| Role / File                   | Read when...                                                         |
| ----------------------------- | -------------------------------------------------------------------- |
| `1-analyst.agent.md`          | Analizar requisitos, generar o refinar PRD y clarificar scope        |
| `2-architect.agent.md`        | Diseñar arquitectura, generar ADD, definir reglas de agente          |
| `3-product-owner.agent.md`    | Priorizar features, definir roadmap, actualizar objetivos            |
| `4-engineer.agent.md`         | Planificación técnica detallada, preparar entorno de desarrollo      |
| `5-coder.agent.md`            | Generar o modificar código fuente                                    |
| `6-tester.agent.md`           | Crear y ejecutar tests, validar resultados                           |
| `7-cleaner.agent.md`          | Limpiar artefactos temporales, refactor ligero                       |
| `8-dev-ops.agent.md`          | Configuración CI/CD, deployments, scripts de infraestructura         |
| `<rol>-orchestrator.agent.md` | Coordinar múltiples subagents, gestionar handoffs y flujos paralelos |

---

## ALCANCE (SCOPING)

Trabajar **SOLO** en:

* TODO: rutas o módulos permitidos

NO modificar:

* TODO: áreas críticas (ej. infra, auth, core libs)
* TODO: código fuera del scope de la tarea

---

## DEPENDENCIAS CLAVE

* TODO: relaciones entre módulos
* TODO: contratos importantes (API, tipos, eventos)

Reglas:

* No romper contratos existentes
* Mantener compatibilidad

---

## SKILLS REUTILIZABLES (`.agents/skills/`)

* Cada skill debe tener un archivo `.agent.md` con la siguiente estructura:

```markdown
---
name: <skill_name>
description: <Qué hace la skill>
argument-hint: <Información que necesita el agente>
model: Auto
tools: [<herramientas permitidas>]
user-invocable: false
disable-model-invocation: true
---
# Skill: <skill_name>

## Uso
TODO: descripción de cómo usar la skill

## Contexto
TODO: información necesaria para ejecutar la skill
```

* Ejemplo de skills:

  * `generación-de-prd.agent.md`
  * `diseño-arquitectónico.agent.md`
  * `planificación-de-implementación.agent.md`

---

## PROMPTS REUTILIZABLES (`.agents/prompts/`)

* Cada prompt base se almacena en `.agents/prompts/` y puede ser invocado por cualquier subagent/orchestrator.
* Ejemplo de plantilla:

```text
# prompt1.md
Objetivo: Analizar un PRD incompleto y generar un resumen de alcance.
Instrucciones:
1. Leer el PRD proporcionado
2. Detectar vacíos y riesgos
3. Sugerir pasos de refinamiento
4. Devolver un resumen estructurado con secciones claras
```

---

## STACK

* Lenguaje(s): TODO
* Framework(s): TODO
* Build/Test: TODO
* Storage: TODO

---

## COMANDOS

```bash
# setup
TODO

# run
TODO

# test
TODO

# build
TODO
```

---

## FLUJO DEL AGENTE

1. Identificar módulo afectado (usar JIT INDEX)
2. Leer solo archivos relevantes
3. Validar dependencias
4. Aplicar cambio mínimo
5. Ejecutar tests del módulo
6. Verificar que no hay efectos colaterales

Si hay bloqueo:

* Hacer una única pregunta clara y específica

---

## REUSO Y OPTIMIZACION

* Priorizar reutilización sobre creación
* Evitar duplicación
* Seguir patrones existentes
* Consultar `.agents/skills/` y `.agents/prompts/` antes de crear nueva lógica

---

## ENTORNO

* OS: TODO
* Branch principal: TODO
* Repo: TODO

---

## OUTPUT ESPERADO

* Código funcional y consistente
* Sin efectos colaterales
* Cambios mínimos
* Tests pasando (si existen)
* Explicación breve (solo si aporta valor)

---

## NOTAS

* Usar `TODO:` para información faltante
* Mantener este archivo < 200 líneas
* Priorizar claridad y accionabilidad

---
