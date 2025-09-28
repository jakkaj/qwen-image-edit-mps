# Rules — Qwen Image Edit CLI

Aligned with Constitution v0.1.0 (ratified 2025-09-28). These rules are normative; violations require explicit exemption from project owners (see Governance).

## Source Control & Process

- All work MUST branch from `main` using the pattern `feature/{slug}` or `fix/{slug}`.
- Pull requests MUST remain focused on one deliverable and reference the generated output manifests when applicable.
- `main` MUST stay releaseable; merges require passing doctrine-aligned checks and at least one approving review.
- Releases MUST be tagged using semantic versioning (e.g., `v0.1.0`).

## Coding Standards

- Code MUST target Python 3.11+ and remain compatible with Apple Silicon macOS and Linux runners.
- Formatting with `black` and linting with `ruff` MUST produce clean diffs prior to review.
- Static typing via `mypy` MUST pass with `--strict` (or a documented configuration agreed by owners).
- Public APIs SHOULD include docstrings that reference relevant guiding principles (e.g., `GP-1 Determinism`).
- CLI entry points MUST use Typer and delegate user-facing output to Rich.
- Each command MUST persist a JSON manifest that captures seed, prompt, model hash, device, and CLI version.

## CLI Commands & UX

- The CLI MUST expose subcommands: `edit`, `inpaint`, `composite`, `batch`, `selftest`, and `inspect`.
- Commands MUST accept explicit `--seed`, `--device`, and `--output-manifest` options with sensible defaults.
- Rich progress indicators MUST wrap long-running operations; warnings and errors MUST surface through Rich panels.
- The CLI MUST provide a `--json` quiet mode for scripting scenarios.

## Testing & Verification

- `pytest` suites MUST include unit tests for core modules, integration tests for the engine, and golden-image comparisons for regression sensitive flows.
- Snapshot tests of Rich output SHOULD guard terminal UX contracts.
- A `selftest` command MUST execute an MPS accelerated smoke test; when MPS is unavailable it MUST exercise the CPU fallback.
- Test runs MUST be orchestrated through `nox` (or an equivalent automation) targeting at minimum `lint`, `typecheck`, and `tests` sessions.
- CI pipelines MUST execute on macOS (with MPS) and Linux to verify cross-platform stability.

## Tooling & Automation

- Dependencies MUST be managed through `uv` or `pip` with locked hashes; if alternative tooling is used it MUST be documented alongside rationale.
- Pre-commit hooks SHOULD run `ruff`, `black`, and `mypy --install-types --non-interactive` to keep local state healthy.
- Build artifacts MUST NOT be committed; use `.gitignore` to enforce.
- Packaging MUST define a Typer entry point under the name `qwen-edit` (or successors ratified by governance).

## Security & Governance

- Secrets (API keys, model weights beyond public releases) MUST NOT enter the repository or manifests.
- TODO(SECURITY_CONTACT): Publish the security response contact information in `SECURITY.md` and mirror it here.
- TODO(OWNERS): Record the canonical list of doctrine owners in this section once finalized.
- TODO(LICENSE): Document the outbound license in `LICENSE` and reflect any constraints in contributor guidelines.

## Model & Data Stewardship

- Model weights MUST be verified against documented checksums before first use.
- TODO(MODEL_SOURCES): Capture the authoritative download location(s) and integrity verification steps for the Qwen Image Edit pipeline.
- User-provided prompts SHOULD default to local storage; any cloud uplinks require architectural review.
