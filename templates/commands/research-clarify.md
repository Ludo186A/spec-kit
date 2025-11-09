---
description: Sesión de aclaraciones y research previas a la implementación (PRPs TestAgent/VoiceAgent/Orchestrator)
---

## User Input

```text
$ARGUMENTS  
Primera línea (obligatoria): Ruta del PRP objetivo a actualizar (por ejemplo: specs/001-voice-agent-testing/prps/orchestrator-prp.md)
Líneas siguientes (opcionales): Contexto adicional del usuario. El asistente generará las preguntas de aclaración automáticamente.
```

You **MUST** consider the user input before proceeding (if not empty).

## Setup

**Setup**: Run `.specify/scripts/powershell/check-prerequisites.ps1 -Json` from repo root **once** and parse minimal JSON payload fields:
- `FEATURE_DIR`
- `AVAILABLE_DOCS` (list of available documents in feature directory)
- If JSON parsing fails, abort and instruct user to re-run `/speckit.specify` or verify feature branch environment.
- For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

## Objetivo
Detectar y reducir ambigüedades o decisiones faltantes que impacten arquitectura, contratos, pruebas y evaluación. La sesión produce respuestas concretas que se **integran directamente en el PRP indicado en `$ARGUMENTS`** (agregando/actualizando secciones mínimas y testables).

## Alcance y Reglas (basado en speckit.clarify)
- El asistente (yo) **genera proactivamente las dudas/preguntas** necesarias para crear/ajustar el PRP objetivo, a partir del spec actual y research con Anchor MCP. El input del usuario es opcional.
- Máximo 5 preguntas por sesión, una por vez.
- Cada pregunta debe resolverse con:
  - Opción múltiple (2–5 opciones mutuamente exclusivas), o
  - Respuesta corta (<= 5 palabras).
- Siempre mostrar una recomendación explícita antes de listar opciones.
- Registrar cada respuesta aceptada en el **PRP objetivo** bajo `## Clarifications > ### Session YYYY-MM-DD` (si no existe, crearla) y actualizar la sección afectada del PRP de forma mínima y testable.
- No paralelizar: resolver en orden de mayor impacto (Impacto × Incertidumbre).

## Modo de Operación
- Inicio con una pregunta de alto impacto (prioridad T019–T023) siguiendo el formato del workflow.
- Tras tu respuesta, **integro de inmediato en el PRP objetivo** (archivo provisto en `$ARGUMENTS`) y ajusto secciones del PRP si aplica.
- Repetimos hasta 5 preguntas o hasta que indiques que es suficiente.

## Taxonomía de Cobertura (guía de escaneo)
- Alcance funcional y criterios de éxito
- Dominio y modelo de datos
- Flujo de interacción y UX (turnos, etapas)
- Atributos no funcionales (latencia, confiabilidad, observabilidad)
- Integraciones y dependencias externas (LiveKit, Langfuse, MCP)
- Edge cases y manejo de fallos (retiros, timeouts, mind change)
- Restricciones y trade-offs
- Terminología y consistencia
- Señales de finalización y DoD

## Cola de Preguntas (candidatas)
Las preguntas se activan de a una. A continuación se listan candidatas iniciales por área de research pendiente (T019–T023).

### T019 — Langfuse evaluators y scoring
Pregunta C1: ¿Nivel de evaluación principal?

**Recommended:** Opción B — Evaluación por conversación con eventos por turno  
Razón: Equilibra granularidad con simplicidad; permite KPIs globales y anexar excerpts/flags por turno.

| Option | Description |
|--------|-------------|
| A | Solo por turno (cada intercambio produce score independiente) |
| B | Por conversación + eventos por turno (KPIs globales y anotaciones turno a turno) |
| C | Mixto con pesos configurables (complejidad mayor al MVP) |
| Short | Personaliza (<=5 palabras) |

Formato de respuesta: "A", "B", "C" o short.

### T020 — Coordinación multi‑agente LiveKit
Pregunta C2: ¿Detección de turnos en MVP?

**Recommended:** Opción B — Manual (orchestrator controla)  
Razón: Reduce incertidumbre inicial; luego podemos pasar a detector semántico/multilingüe.

| Option | Description |
|--------|-------------|
| A | VAD/turn detection automática (Multilingual/Semantic) |
| B | Manual (VoiceAgent saluda; orchestrator avanza etapas) |
| C | Híbrido (manual con fallback VAD) |
| Short | Otra (<=5 palabras) |

### T022 — Pytest + secuencial + retry/backoff
Pregunta C3: ¿Alcance del retry en MVP?

**Recommended:** Opción B — Retries en operaciones I/O críticas (LiveKit room, trace finalize)  
Razón: Mayor beneficio con menor ruido; evita ocultar bugs lógicos.

| Option | Description |
|--------|-------------|
| A | Retries amplios (casi todas las operaciones) |
| B | Solo I/O críticas (crear sala, unirse, finalize trace) |
| C | Sin retries (fallar rápido) |
| Short | Personaliza (<=5 palabras) |

### T023 — Evaluación híbrida + LLM‑as‑a‑Judge
Pregunta C4: ¿Métrica general mínima del MVP?

**Recommended:** Opción B — 3 métricas: task_success (binaria), professionalism (1–5), latency_ms (numérica)  
Razón: Cubre outcome, calidad y rendimiento; simple de reportar/visualizar.

| Option | Description |
|--------|-------------|
| A | Solo task_success (binaria) |
| B | task_success + professionalism + latency_ms |
| C | Paquete extendido (7+ métricas) |
| Short | Otra (<=5 palabras) |

## Plan de Integración tras cada respuesta
1. Añadir una línea en el **PRP objetivo** bajo `## Clarifications > ### Session YYYY-MM-DD`:
   - `- Q: <pregunta> → A: <respuesta>`
2. Actualizar en el **PRP objetivo** la sección afectada (especificación técnica, criterios de éxito, blueprint, ejemplos) manteniendo el cambio mínimo y testable.
3. Si corresponde, reflejar notas en otros artefactos (p. ej., `Specs/Readme_langfuse_specs.md` o contratos), pero la fuente de verdad de esta sesión es el **PRP indicado**.
4. Guardar y marcar T019/T020/T022/T023 como ✅ cuando corresponda.

## Anchor MCP — Plan de Queries
- Langfuse evaluators: "langfuse evaluator api dataset scoring", "langfuse score config examples"
- LiveKit coordinación: "AgentSession multi participant turn management", "RoomIO examples"
- Pytest async: "pytest asyncio fixtures sequential", "exponential backoff pattern"
- LLM‑as‑a‑Judge: "langfuse llm judge rubric", "hybrid evaluation metrics"

## Resultado esperado de la sesión
- 3–5 respuestas aceptadas que reduzcan incertidumbre de MVP.
- Actualización de `research.md` y notas en PRPs.
- Señal clara para iniciar implementación (o si falta una ronda más de clarificación).
