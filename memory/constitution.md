<!-- Sync Impact Report:
Version: v0.1.0
Change type: initial baseline
Sections touched: all
Supporting docs updated: docs/rules-idioms-architecture/rules.md, docs/rules-idioms-architecture/idioms.md, docs/rules-idioms-architecture/architecture.md
Outstanding TODOs: TODO(LICENSE), TODO(OWNERS), TODO(SECURITY_CONTACT), TODO(MODEL_SOURCES)
-->

# Qwen Image Edit CLI Constitution

- Version: v0.1.0
- Ratified: 2025-09-28
- Last amended: 2025-09-28

## Guiding Principles

1. **Determinism is non-negotiable.** Every edit command MUST be reproducible via explicit seeds, captured parameters, and generated JSON manifests.
2. **Local-first performance on Apple Silicon.** The engine SHOULD prefer MPS acceleration on supported Macs and MUST fall back to CPU without breaking determinism or stability.
3. **Great terminal UX.** The CLI MUST use Rich-powered feedback for progress, warnings, and errors, preserving a scriptable interface for automation.
4. **Separation of concerns.** The Typer CLI MUST remain a thin layer delegating to an `Engine` that wraps model operations; business logic lives in reusable core modules.
5. **Safe experimentation.** Contributors SHOULD gate new features behind configuration flags or subcommands and document how to disable them when instability is detected.

## Quality & Verification Strategy

- Continuous formatting with `black`, linting with `ruff`, and static typing with `mypy` MUST pass before merge, as enforced locally and in CI.
- Automated tests with `pytest` MUST cover core modules, with golden-image regression suites for critical edit paths.
- A smoke test MUST exercise the MPS execution path; when unavailable, the CLI MUST expose a `--device cpu` escape hatch and run an equivalent CPU smoke test.
- Rich console output SHOULD be snapshot-tested to catch breaking UX regressions.
- Contributors MUST document test expectations inside each manifest for traceability back to these policies.
- TODO(MODEL_SOURCES): Document the canonical Qwen Image Edit model source, version, and checksum once procurement is finalized.

## Delivery Practices

- Releases follow semantic versioning; increment MINOR for new user-facing commands and PATCH for internal fixes that do not alter behaviour.
- Packaging MUST use PEP 621 metadata in `pyproject.toml` and include entry points for the CLI commands.
- Every image output MUST emit a sidecar manifest capturing seed, prompt, model hash, device choice, and CLI version.
- The repository SHOULD include a `noxfile.py` or equivalent to orchestrate lint/test flows consistently.
- TODO(LICENSE): Choose and document the outbound software license before the first public release.

## Governance

- Doctrine amendments require approval from at least two project owners or delegates following a pull request review.
- The constitution and supporting doctrine MUST be reviewed quarterly or prior to any MAJOR release bump.
- Compliance checks MUST appear in CI to confirm doctrine-aligned tooling (formatters, linters, tests) execute successfully.
- TODO(OWNERS): Record the permanent list of project owners responsible for doctrine stewardship.
- TODO(SECURITY_CONTACT): Nominate a security contact to manage vulnerability reports and emergency updates.
