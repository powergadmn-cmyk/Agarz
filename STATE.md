# STATE del proyecto Agarz Bot

## TASK-001

Status:
completed

Objective:
Crear el bootstrap completo del proyecto para un bot autónomo local de AgarZ con arquitectura modular en Python 3.11+, dejando contratos/stubs sólidos para protocolo, automatización de navegador, policy, state machine, observabilidad y recuperación.

Changes Made:
- Se creó el esqueleto base del proyecto Python con empaquetado y CLI (`pyproject.toml`, `app/main.py`).
- Se implementó composición de dependencias y runtime modular con contratos explícitos (`app/bootstrap.py`, `app/core/contracts.py`, `app/core/runtime.py`).
- Se añadieron modelos de configuración tipados y carga YAML (`app/config/models.py`, `app/config/loader.py`, `configs/default.yaml`).
- Se añadieron modelos de dominio y snapshots (`app/domain/*`).
- Se añadieron stubs de protocolo WebSocket + parser (`app/protocol/*`).
- Se añadieron stubs de automatización de navegador (`app/browser/*`).
- Se añadieron policy y máquina de estados base (`app/policy/*`, `app/state_machine/*`).
- Se añadió observabilidad base: logging centralizado y métricas en memoria (`app/utils/logging.py`, `app/telemetry/metrics.py`).
- Se añadió capa de safety/recovery (`app/safety/recovery.py`).
- Se preparó extensión futura de visión OpenCV/Qwen-VL (`app/vision/service.py`).
- Se añadieron documentación inicial y test de máquina de estados (`README.md`, `docs/architecture.md`, `tests/test_state_machine.py`).

Files Affected:
- created:
  - pyproject.toml
  - .gitignore
  - README.md
  - app/__init__.py
  - app/bootstrap.py
  - app/main.py
  - app/config/__init__.py
  - app/config/models.py
  - app/config/loader.py
  - app/core/__init__.py
  - app/core/contracts.py
  - app/core/runtime.py
  - app/domain/__init__.py
  - app/domain/actions.py
  - app/domain/entities.py
  - app/domain/state.py
  - app/protocol/__init__.py
  - app/protocol/parser.py
  - app/protocol/websocket.py
  - app/browser/__init__.py
  - app/browser/controller.py
  - app/policy/__init__.py
  - app/policy/default_policy.py
  - app/state_machine/__init__.py
  - app/state_machine/models.py
  - app/state_machine/machine.py
  - app/telemetry/__init__.py
  - app/telemetry/metrics.py
  - app/vision/__init__.py
  - app/vision/service.py
  - app/safety/__init__.py
  - app/safety/recovery.py
  - app/utils/__init__.py
  - app/utils/logging.py
  - configs/default.yaml
  - docs/architecture.md
  - tests/test_state_machine.py
- modified:
  - STATE.md
- removed:
  - none

Dependencies:
- added:
  - PyYAML>=6.0
  - pytest>=8.0 (dev)
  - mypy>=1.10 (dev)
  - ruff>=0.6 (dev)
- removed:
  - none
- unchanged

Architecture Impact:
major

Technical Notes:
- La implementación actual es intencionalmente stub/placeholder en infraestructura para evitar acoplamiento prematuro.
- La arquitectura ya separa dominio, contratos, orquestación, adaptadores y observabilidad.
- `StateProvider` y `InputExecutor` están listos para sustitución por integraciones reales sin romper policy/runtime.
- Se priorizó tipado, docstrings y estructura de paquetes para escalabilidad y mantenibilidad.

Next Step:
Implementar TASK-002 para integrar cliente WebSocket real + parser de protocolo AgarZ y adaptar `WebSocketStateProvider` a frames reales.

## TASK-002

Status:
completed

Objective:
Implementar la capa de protocolo/WebSocket orientada a mensajes binarios para AgarZ con arquitectura desacoplada, incluyendo parser base por opcodes, dispatcher, normalización a eventos internos y almacenamiento incremental en `GameStateStore`.

Changes Made:
- Se implementó infraestructura binaria base con `BinaryReader` y manejo robusto de offsets/lecturas (`app/protocol/binary_reader.py`).
- Se definieron opcodes y versión de protocolo en estructura extensible (`app/protocol/opcodes.py`, `ProtocolSpec`).
- Se crearon modelos de payload y evento de protocolo (`app/protocol/models.py`, `app/protocol/events.py`).
- Se implementó `OpcodeRegistry` y `MessageDispatcher` para enrutado desacoplado por opcode (`app/protocol/registry.py`, `app/protocol/dispatcher.py`).
- Se implementó `PacketParser` con handlers base para:
  - ready/start/version
  - player_id/spectate_id
  - position update
  - node add/update/remove
  - leaderboard
  - board size
  - chat/top message
- Se implementó normalización protocolo→dominio (`ProtocolEventNormalizer`) (`app/protocol/domain_events.py`).
- Se implementó `GameStateStore` para aplicar eventos de dominio y mantener estado incremental (`app/protocol/state_store.py`).
- Se introdujo abstracción de stream binario para futura conexión real (`BinaryPacketStream`) con implementación in-memory (`app/protocol/stream.py`).
- Se integró el flujo parser/normalizer/store en `WebSocketStateProvider` sin mezclar browser automation (`app/protocol/websocket.py`).
- Se actualizó capa de compatibilidad de parser (`app/protocol/parser.py`) y exports de módulo (`app/protocol/__init__.py`).
- Se añadieron tests unitarios de parser/store con paquetes binarios simulados (`tests/test_protocol_parser.py`).
- Verificación técnica realizada con compilación estática: `python -m compileall app tests`.

Files Affected:
- created:
  - app/protocol/binary_reader.py
  - app/protocol/opcodes.py
  - app/protocol/events.py
  - app/protocol/models.py
  - app/protocol/registry.py
  - app/protocol/dispatcher.py
  - app/protocol/packet_parser.py
  - app/protocol/domain_events.py
  - app/protocol/state_store.py
  - app/protocol/stream.py
  - tests/test_protocol_parser.py
- modified:
  - app/protocol/__init__.py
  - app/protocol/parser.py
  - app/protocol/websocket.py
  - STATE.md
- removed:
  - none

Dependencies:
- added:
  - none
- removed:
  - none
- unchanged

Architecture Impact:
major

Technical Notes:
- Los valores de opcode se dejan como placeholders explícitos y documentados para validación con tráfico real en una fase posterior.
- El diseño separa claramente parsing binario, normalización de eventos y estado de juego para minimizar impacto ante cambios de protocolo.
- `PacketParser` está preparado para versionado mediante `ProtocolSpec` + `OpcodeRegistry`.
- `pytest` no estaba instalado en el entorno al intentar ejecución (`No module named pytest`), por lo que se validó sintaxis/compilación con `compileall`.

Next Step:
Implementar TASK-003 para conectar `BinaryPacketStream` a una fuente real de tráfico WebSocket (proxy/sniffer local), validar opcodes con captura real y ajustar parsers de payload a formato exacto de AgarZ.

## TASK-003

Status:
completed

Objective:
Implementar la capa de automatización de navegador para inyección de inputs con arquitectura desacoplada, incluyendo control de sesión, executor de acciones, adaptación dominio→input, extracción ligera de UI y demo controlada sin policy.

Changes Made:
- Se rediseñó `browser/controller.py` como módulo de automatización con separación de responsabilidades:
  - `BrowserController` (abstracción de backend de navegador)
  - `InputExecutor` (interfaz browser específica y compatible con runtime)
  - `GameSessionController` (apertura de sesión, carga de URL, espera de canvas, start/spectate)
  - `DomProbe` (lectura ligera de DOM/UI)
  - `BrowserActionAdapter` (traducción de `BotAction` a inputs)
  - `BrowserInputExecutor` (ejecutor concreto)
  - `DummyBrowserController` (backend stub para pruebas/desarrollo)
- Se añadieron acciones explícitas en `InputExecutor`:
  - `move_mouse(x, y)`
  - `split()`
  - `eject()`
  - `macro_z()`
  - `macro_x()`
  - `focus_game()`
- Se añadió manejo básico de errores con `BrowserAutomationError` y logging operativo.
- Se añadió demo controlada aislada en `app/browser/demo.py`.
- Se añadió soporte CLI para ejecutar demo sin runtime completo (`--browser-demo`) en `app/main.py`.
- Se ampliaron parámetros de configuración browser en `app/config/models.py` y `configs/default.yaml`:
  - resolución (`viewport_width`, `viewport_height`)
  - timings/delays (`startup_delay_ms`, `action_delay_ms`, `key_press_delay_ms`, `wait_canvas_timeout_ms`)
  - selectores UI (`canvas_selector`, `focus_selector`, `start_button_selector`, `spectate_button_selector`)
  - `headless` se mantiene `false` por defecto
- Se actualizaron exports de módulo en `app/browser/__init__.py`.
- Se añadieron tests unitarios de capa browser en `tests/test_browser_layer.py`.
- Verificación técnica realizada con compilación estática: `python -m compileall app tests`.

Files Affected:
- created:
  - app/browser/demo.py
  - tests/test_browser_layer.py
- modified:
  - app/browser/controller.py
  - app/browser/__init__.py
  - app/config/models.py
  - configs/default.yaml
  - app/main.py
  - STATE.md
- removed:
  - none

Dependencies:
- added:
  - none
- removed:
  - none
- unchanged

Architecture Impact:
major

Technical Notes:
- La integración real con Playwright/Selenium queda preparada detrás de `BrowserController`, evitando acoplar infraestructura en policy/state machine.
- La demo de navegador no depende de la policy, cumpliendo la fase solicitada de ejecución controlada.
- La capa browser mantiene compatibilidad con el contrato de ejecución del runtime (`execute(BotAction)`).
- No se ejecutó `pytest` por ausencia de módulo en entorno; se validó compilación/sintaxis con `compileall`.

Next Step:
Implementar TASK-004 para conectar `BrowserController` a backend real (Playwright recomendado), incluir detección robusta de canvas/UI real de AgarZ y validar secuencias de input sobre sesión real.

## TASK-004

Status:
completed

Objective:
Implementar la primera Policy MVP funcional y la máquina de estados táctica del bot para supervivencia y crecimiento conservador, con decisiones heurísticas explicables (sin ML), scoring de objetivos y split únicamente en escenarios simples de alta confianza y bajo riesgo.

Changes Made:
- Se añadió configuración tipada de policy y carga desde YAML con umbrales heurísticos:
  - `safe_distance`
  - `threat_ratio`
  - `chase_ratio`
  - `split_confidence_threshold`
- Se implementó módulo de scoring heurístico en `app/policy/scoring.py`:
  - evaluación de comida cercana
  - detección de amenazas cercanas por ratio de masa
  - selección de presas comestibles
  - cálculo de confianza de split con bloqueo por riesgo
- Se refactorizó `DefaultPolicy` a un motor MVP funcional en `app/policy/default_policy.py` con reglas:
  - amenaza grande cercana -> evadir
  - sin amenaza y con presa clara -> perseguir
  - split solo con confianza alta y riesgo bajo
  - sin amenaza y con comida -> buscar comida
  - sin señales útiles -> idle/no-op
- Se añadieron logs de decisión con causa y contexto de score/distancia para depuración.
- Se actualizó la máquina de estados con los estados solicitados:
  - `IDLE`
  - `SEEK_FOOD`
  - `EVADE_THREAT`
  - `CHASE_TARGET`
  - `RECOVER`
- Se amplió `BotStateMachine` con `set_state()` para permitir transición táctica desde metadata de acción.
- Se integró la salida de policy en el runtime manteniendo acciones abstractas, leyendo `next_state` desde metadata en `BotRuntime`.
- Se mantuvo la integración con browser mediante acciones abstractas `BotAction` que siguen siendo traducidas por `BrowserActionAdapter`.
- Se añadieron tests unitarios MVP:
  - `tests/test_policy_mvp.py` para decisiones por snapshots simulados
  - actualización de `tests/test_state_machine.py` para los nuevos estados
- Validación técnica ejecutada: `python -m compileall app tests` (exit code 0).

Files Affected:
- created:
  - app/policy/scoring.py
  - tests/test_policy_mvp.py
- modified:
  - app/config/models.py
  - app/config/loader.py
  - app/policy/default_policy.py
  - app/policy/__init__.py
  - app/state_machine/models.py
  - app/state_machine/machine.py
  - app/state_machine/__init__.py
  - app/bootstrap.py
  - app/core/runtime.py
  - configs/default.yaml
  - tests/test_state_machine.py
  - STATE.md
- removed:
  - none

Dependencies:
- added:
  - none
- removed:
  - none
- unchanged

Architecture Impact:
major

Technical Notes:
- La policy sigue siendo totalmente heurística y auditable; no hay acoplamiento con inputs físicos ni ML.
- El canal de decisión permanece desacoplado: `PolicyEngine` produce `BotAction` abstracta y `BrowserActionAdapter` traduce a inputs.
- La transición táctica se comunica con `action.metadata["next_state"]`, evitando acoplar política y runtime por tipos concretos adicionales.
- El criterio de split se diseñó conservador: se inhibe si existe amenaza en radio de riesgo ampliado.

Next Step:
Implementar TASK-005 para conectar esta policy MVP al modelo de mundo consolidado (`GameStateStore` central multi-fuente), incorporar validaciones de consistencia espacial y calibrar umbrales con telemetría en sesiones reales.
