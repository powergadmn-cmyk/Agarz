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

## TASK-005

Status:
completed

Objective:
Implementar el orquestador principal del bot (runtime) para un loop robusto y entendible que coordine percepción, decisión, ejecución de acciones, telemetría y recuperación, incluyendo rate limiting, separación de ticks, dry-run y shutdown limpio.

Changes Made:
- Se reforzó el runtime principal para ejecutar ciclo coordinado por ticks separados:
  - perception tick
  - decision tick
  - action tick
- Se añadió rate limiting central con `tick_interval_ms` y control por divisores `every_n_ticks`.
- Se implementó modo `dry-run` para depuración sin enviar inputs reales al navegador.
- Se añadió summary periódico de estado en logs de consola (`status_summary_every_n_ticks`).
- Se añadió cancelación limpia mediante señal interna y método explícito `request_shutdown()`.
- Se añadió shutdown seguro en `finally`, cerrando provider y executor aun cuando haya errores.
- Se extendieron contratos de infraestructura con `close()` para cierre ordenado:
  - `StateProvider.close()`
  - `InputExecutor.close()`
- Se adaptaron implementaciones concretas para cumplir cierre limpio:
  - `WebSocketStateProvider.close()`
  - `BrowserInputExecutor.close()`
- Se amplió configuración runtime en modelos y YAML:
  - `perception_every_n_ticks`
  - `decision_every_n_ticks`
  - `action_every_n_ticks`
  - `status_summary_every_n_ticks`
  - `dry_run`
- Se amplió CLI con flag `--dry-run` para activar ejecución segura de depuración.
- Se añadieron tests unitarios del orquestador cubriendo:
  - separación de ticks
  - dry-run sin ejecución de inputs
  - shutdown limpio con `close()`
- Verificación técnica realizada con compilación estática: `python -m compileall app tests`.

Files Affected:
- created:
  - tests/test_runtime_orchestrator.py
- modified:
  - app/config/models.py
  - configs/default.yaml
  - app/core/contracts.py
  - app/core/runtime.py
  - app/protocol/websocket.py
  - app/browser/controller.py
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
- Se mantuvo diseño síncrono para máxima claridad operativa en fase MVP, con separación explícita de responsabilidades.
- El modo dry-run evita apertura/uso real del ejecutor de inputs y registra acciones omitidas para auditoría.
- El summary periódico permite observar comportamiento del loop (ticks activos, estado y modo) sin instrumentación externa adicional.
- El cierre en `finally` protege contra fugas de recursos y deja la base preparada para evolucionar a runtime async en fases posteriores.

Next Step:
Implementar TASK-006 para conectar el orquestador con un `GameStateStore` de mundo consolidado multi-fuente (protocolo/visión/UI), añadir métricas de latencia por etapa y evaluar migración progresiva a ejecución async con colas de eventos.

## TASK-006

Status:
completed

Objective:
Implementar observabilidad básica production-oriented para el bot de AgarZ, incorporando logging estructurado, métricas operativas clave, snapshots ligeros para depuración/replay y persistencia de eventos de fallo para acelerar iteración y diagnóstico.

Changes Made:
- Se extendió configuración tipada de telemetría y safety con controles para logging estructurado, reporter periódico, buffer de snapshots, replay JSONL y persistencia de fallos en disco.
- Se implementó formateador JSON para logging centralizado y activación por configuración sin romper el flujo de logs existente.
- Se ampliaron métricas runtime con señales clave:
  - duración de tick
  - latencia decisión→acción
  - supervivencia
  - masa máxima
  - acciones por minuto
  - transiciones de estado
- Se creó la capa de debug/replay (`SnapshotStore`, `SnapshotRecord`, `DebugReporter`, `ReplaySink`, `JsonlReplaySink`) con almacenamiento ligero y serialización JSONL.
- Se integró observabilidad en runtime para recolectar assessment, construir snapshots por tick, reportar estado táctico y emitir métricas avanzadas durante la ejecución.
- Se reforzó recovery para persistir eventos de error/fallo en disco cuando la configuración lo habilita.
- Se añadieron tests unitarios de observabilidad para store, métricas, snapshots y replay sink.
- Validación técnica ejecutada con compilación estática: `python -m compileall app tests` (exit code 0).

Files Affected:
- created:
  - app/telemetry/debug.py
  - tests/test_observability.py
- modified:
  - app/config/models.py
  - app/config/__init__.py
  - configs/default.yaml
  - app/utils/logging.py
  - app/telemetry/metrics.py
  - app/telemetry/__init__.py
  - app/core/runtime.py
  - app/safety/recovery.py
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
- La activación de logging estructurado es configurable (`structured_logging`) para alternar entre salida humana y JSON sin cambios de código en llamadas de logging.
- Se preservó compatibilidad en runtime con collectors más básicos mediante observación defensiva de capacidades.
- El modelo snapshot/replay se diseñó de bajo overhead para no bloquear el loop principal en fase MVP.
- La persistencia JSONL de fallos y replay habilita análisis post-mortem y base para visor/replayer en fases siguientes.

Next Step:
Implementar TASK-007 para consolidar un `GameStateStore` de mundo multi-fuente, conectar observabilidad con trazas por etapa (percepción/decisión/acción) y preparar herramienta de replay visual offline.

## TASK-007

Status:
completed

Objective:
Implementar una capa seria de seguridad operacional y recuperación para ejecución autónoma prolongada del bot, cubriendo detección de estado stale, loops sin progreso, control de ritmo de acciones, circuit breaker, refocus y recuperación con reset de emergencia.

Changes Made:
- Se extendió `SafetyConfig` con parámetros operativos para detectores, guards, circuit breaker y políticas de recuperación (`stale_state_seconds`, thresholds de loop, cooldowns de acción, timeout por fase, límites de recovery, persistencia de incidentes).
- Se actualizó configuración YAML base con defaults seguros y explícitos para seguridad/recovery.
- Se implementó capa `safety/guards.py` con:
  - `StaleStateDetector`
  - `LoopDetector`
  - `ActionCooldownGuard`
  - `TimeoutGuard`
- Se implementó `safety/incidents.py` para registro estructurado de incidentes con severidad, tipo, causa raíz estimada y persistencia JSONL mediante `IncidentJournal`.
- Se reforzó `RecoveryCoordinator` con:
  - incident logging unificado
  - reconexión y reset parcial (`recover_stale`)
  - recuperación por loop (`recover_loop`)
  - recuperación de foco (`recover_focus`)
  - `emergency_reset` (close + reconnect + reopen)
  - circuit breaker temporal con cooldown
  - control de reintentos de recovery
- Se amplió el contrato `RecoveryManager` con hooks opcionales para incidentes, estado del circuit breaker y estrategias de recuperación.
- Se amplió capa browser para soporte de foco operacional (`is_focused`, `ensure_focus`) en controlador dummy y ejecutor.
- Se integró en `BotRuntime`:
  - chequeo de circuit breaker antes de procesar tick
  - detección de stale state y recuperación automática
  - detección de pérdida de foco y refocus/recovery
  - detección de loops de acción sin progreso y recuperación
  - guard de cooldown de acciones
  - timeout por fase (percepción/decisión/acción) con incidentes
  - fallback a `emergency_reset` cuando recovery no resuelve
- Se añadieron tests de fallos simulados para guards/recovery/runtime safety:
  - detección stale
  - detección de loop sin progreso
  - bloqueo por cooldown
  - apertura de circuit breaker
  - activación de recuperación por stale/focus/loop
- Se validó compilación completa: `python -m compileall app tests` (exit code 0).

Files Affected:
- created:
  - app/safety/guards.py
  - app/safety/incidents.py
  - tests/test_safety_recovery.py
- modified:
  - app/config/models.py
  - configs/default.yaml
  - app/core/contracts.py
  - app/browser/controller.py
  - app/safety/recovery.py
  - app/core/runtime.py
  - app/safety/__init__.py
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
- Se priorizó diseño defensivo y desacoplado: detectores/guards separados de runtime y recovery para facilitar calibración y pruebas.
- Los incidentes se modelan con causa raíz estimada para acortar diagnóstico post-mortem en operación prolongada.
- El circuito de recuperación usa estrategia progresiva (recover específico → emergency reset) para minimizar downtime y evitar loops de error.
- Se mantuvo compatibilidad hacia atrás en contratos mediante métodos no abstractos con comportamiento por defecto en `RecoveryManager`.
- Timeouts y cooldowns se dejaron desactivados por defecto (`0`) en modelo para no romper fases previas; los valores operativos recomendados se publican en YAML.

Next Step:
Implementar TASK-008 para consolidar `GameStateStore` multi-fuente (protocolo + señales browser + visión opcional), propagar indicadores de calidad de estado al runtime safety y añadir replay/diagnóstico visual de incidentes.

## TASK-008

Status:
completed

Objective:
Implementar una capa opcional de visión con OpenCV como complemento del estado por protocolo, añadiendo captura/preprocesado/detección heurística y fusión protocol-first sin reemplazar la fuente primaria WebSocket.

Changes Made:
- Se extendió configuración de visión para activar/desactivar pipeline y calibrar umbrales heurísticos, incluyendo modo debug de frames.
- Se implementaron modelos visuales (`VisualRegion`, `VisualObservations`) y utilidades para clasificar señales de comida, blobs grandes y candidatos de virus.
- Se implementó `ScreenCaptureAdapter` con implementación dummy para entorno local de desarrollo/test.
- Se implementó `FramePreprocessor` (blur + normalización HSV) y detectores heurísticos en `BasicVisionDetector`.
- Se implementó `ProtocolFirstFusion` para enriquecer metadata manteniendo prioridad absoluta del snapshot protocolar.
- Se integró `VisionService` en bootstrap/runtime de forma opcional por configuración, con cierre seguro y métricas básicas de observación.
- Se amplió `GameStateStore` para aceptar observaciones visuales complementarias sin alterar estado de protocolo.
- Se añadieron tests unitarios de capa visual/fusión y se validó compilación completa con `python -m compileall app tests`.

Files Affected:
- created:
  - app/vision/models.py
  - app/vision/capture.py
  - app/vision/preprocessing.py
  - app/vision/detectors.py
  - app/vision/fusion.py
  - tests/test_vision_layer.py
- modified:
  - app/config/models.py
  - configs/default.yaml
  - app/vision/service.py
  - app/vision/__init__.py
  - app/protocol/state_store.py
  - app/core/runtime.py
  - app/bootstrap.py
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
- Se mantiene el principio protocol-first: visión solo complementa metadata y no sobrescribe entidades/probabilidades de protocolo.
- La detección visual es deliberadamente heurística y conservadora para evitar sobre-automatización frágil en esta fase.
- El modo debug deja preparada la evolución a persistencia de frames anotados/replay visual avanzado en próximas tareas.
- El diseño actual deja lista la extensión a integración real de captura del canvas y a Qwen-VL en etapa posterior.

Next Step:
Implementar TASK-009 para consolidar world model multi-fuente con score de calidad de estado, calibrar umbrales de visión con sesiones reales y añadir pipeline de replay visual de incidentes.

## TASK-009

Status:
completed

Objective:
Implementar la base de un manager multi-instancia para el bot de AgarZ, permitiendo lanzar y supervisar múltiples perfiles con lifecycle independiente, configuración aislada por instancia y preparación para coordinación futura.

Changes Made:
- Se extendió el modelo de configuración para soportar manager y perfiles múltiples (`ManagerConfig`, `BotProfileConfig`) incluyendo límites de recursos, separación de sesión e overrides por perfil.
- Se actualizó la carga YAML para parsear `manager` y `profiles`, con defaults robustos y normalización de overrides.
- Se añadió el paquete `app/manager` con:
  - modelos de health (`HealthStatus`, `InstanceHealth`)
  - contrato de instancia (`BotInstance`) y factory de runtime por perfil (`InstanceRuntimeFactory`)
  - implementación `ThreadedBotInstance` con lifecycle independiente (start/stop/restart), estado de salud y resumen de métricas por instancia
  - `BotManager` con operaciones de supervisión básica (`start`, `stop`, `restart`, `start_all`, `stop_all`, `restart_all`, `health_summary`)
- Se amplió bootstrap con `DefaultInstanceRuntimeFactory` y `build_manager()` para composición DI del modo multi-instancia.
- Se amplió la CLI para operaciones de manager:
  - `--manager`
  - `--manager-command {start|stop|restart|summary}`
  - `--profile`
- Se añadió aislamiento por perfil para logs y sesión (`log_file_path`, `session_id`, `session_data_dir`) sin coordinación estratégica entre bots.
- Se añadió configuración ejemplo de perfiles y límites de recursos en `configs/default.yaml`.
- Se añadieron tests unitarios de manager para lifecycle básico y respeto del límite máximo de instancias.
- Se actualizó README con documentación breve del modo manager.
- Verificación técnica ejecutada con compilación estática: `python -m compileall app tests` (exit code 0).

Files Affected:
- created:
  - app/manager/__init__.py
  - app/manager/health.py
  - app/manager/instance.py
  - app/manager/manager.py
  - tests/test_bot_manager.py
- modified:
  - app/config/models.py
  - app/config/loader.py
  - app/config/__init__.py
  - app/bootstrap.py
  - app/main.py
  - configs/default.yaml
  - pyproject.toml
  - README.md
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
- La arquitectura quedó preparada para añadir coordinación estratégica posterior sin acoplarla al manager base.
- Cada perfil construye runtime aislado con configuración derivada y límites de recursos declarativos (soft limits).
- El manager actual implementa supervisión y lifecycle; no incluye aún scheduling/coordination entre bots, por diseño deliberado de fase.
- En esta fase se validó compilación; la ejecución de `pytest` depende del entorno.

Next Step:
Implementar TASK-010 para añadir supervisión continua (watchdog), backoff inteligente por instancia, telemetry agregada cross-instance y base de canal de coordinación opcional entre bots.
