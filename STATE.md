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
