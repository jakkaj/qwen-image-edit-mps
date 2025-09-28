# Architecture — Qwen Image Edit CLI

## Overview

A CLI-first system for deterministic image editing powered by the Qwen Image Edit pipeline. The application is layered to keep user interaction, command orchestration, and model execution cleanly separated.

```
cli (Typer entry point)
│
├── commands/ (command handlers: edit, inpaint, composite, batch, selftest, inspect)
│   └── adapters manifesting requests/responses for the engine
│
└── core/
    ├── engine.py          # high-level coordinator for the pipeline
    ├── pipeline.py        # thin wrapper around diffusers Qwen Image Edit pipeline
    ├── device.py          # device discovery, MPS/CPU selection
    ├── io.py              # file loading, manifest writing, checksum utilities
    ├── config.py          # environment + file + CLI config merging
    └── telemetry.py       # structured logging and Rich integration helpers

supporting/
├── data/                  # fixture inputs, golden images, manifests
├── tests/                 # pytest suites covering core + CLI
└── scripts/               # optional automation (nox, packaging helpers)
```

## Boundaries & Responsibilities

- `cli/` MUST depend only on `commands/` and standard-library helpers; it MUST NOT import `torch` or diffusers directly.
- `commands/` MAY import `core/` modules but MUST avoid cross-command coupling (share utilities via `commands/common.py`).
- `core/pipeline.py` is the only module allowed to reference the Qwen model APIs or diffusers primitives.
- `core/device.py` encapsulates device selection; other modules receive resolved devices.
- `core/io.py` owns file system interactions, manifest persistence, and checksum verification.
- Tests MAY import private modules when necessary but SHOULD prefer public surfaces exposed via the engine or command handlers.

## Data Flow

1. Typer parses CLI arguments and constructs a `RuntimeConfig`.
2. `commands/*` build typed request objects and invoke `Engine` methods.
3. `Engine` configures the Qwen pipeline with deterministic seeds and device info.
4. Pipeline outputs are persisted via `core/io.py`, which also emits JSON manifests.
5. Commands report success via Rich panels and exit statuses.

## Integration & External Dependencies

- PyTorch and diffusers provide the Qwen Image Edit pipeline; versions MUST be pinned and captured in manifests.
- Rich handles terminal UX; logging SHOULD integrate with Rich `Console` to avoid double rendering.
- Optional caching can persist model weights under `~/.cache/qwen-edit/`; access MUST go through `core/io.py`.

## Deployment & Environment

- Primary target: macOS (Apple Silicon) with MPS. Secondary target: Linux (CPU) for CI verification.
- GPU acceleration (CUDA) is out-of-scope unless governance approves a doctrine amendment.
- Packaging MUST emit an entry point `qwen-edit` accessible via `pipx` or `uv tool install`.
- TODO(MODEL_SOURCES): Document canonical weight locations and verification steps once confirmed.

## Anti-Patterns (Reject During Review)

- Bypassing `core/device.py` for ad-hoc device selection.
- Embedding Rich styling or Typer configuration inside core modules.
- Mutating manifests after initial write; they MUST be immutable records.
- Mixing synchronous CLI code with background threads without explicit governance approval.
- Introducing cloud service calls without architectural review and doctrine update.

## Reviewer Checklist

- Does the change keep deterministic inputs/outputs (seed, prompt, model hash) intact?
- Are manifests updated atomically with the generated assets?
- Do new commands live under `commands/` and leverage shared infrastructure?
- Are device selection and fallbacks routed through `core/device.py`?
- Do tests cover the new pathways with both CPU and MPS contexts when applicable?
- Are Rich UX changes snapshot-tested or otherwise documented?
- TODO(OWNERS): Assign reviewers responsible for doctrine enforcement.
- TODO(SECURITY_CONTACT): Ensure security-sensitive modifications tag the correct contact.
- TODO(LICENSE): Confirm new dependencies comply with the chosen license once ratified.
