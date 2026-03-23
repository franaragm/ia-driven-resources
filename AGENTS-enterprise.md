# AGENTS INSTRUCTIONS

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

```text
.
├── AGENTS.md
├── .agents/
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

Usar este índice para localizar rápidamente el área de trabajo.

```text
- apps/            → TODO: descripción
- services/        → TODO: descripción
- packages/        → TODO: descripción
- libs/            → TODO: descripción
```

* Consultar `AGENTS.md` locales si existen
* No explorar el repo completo innecesariamente

---

## ALCANCE (SCOPING)

Trabajar SOLO en:

* TODO: rutas o módulos permitidos

NO modificar:

* TODO: áreas críticas (ej. infra, auth, core libs)
* TODO: código no relacionado con la tarea

---

## DEPENDENCIAS CLAVE

* TODO: relaciones entre módulos
* TODO: contratos importantes (API, tipos, eventos)

Reglas:

* No romper contratos existentes
* Mantener compatibilidad

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

## REGLAS DE CAMBIO

* Cambios mínimos y enfocados
* Reutilizar código existente antes de crear nuevo
* No duplicar lógica
* Seguir patrones del repositorio
* Mantener consistencia con el código existente

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
* TODO: reglas específicas del proyecto

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
