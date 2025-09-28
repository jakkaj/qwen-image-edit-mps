# AGENTS.md

## Why this project exists
Give me a fast, local, privacy-preserving way to edit images from the command line on a Mac (Apple Silicon) using Qwen’s image editing pipeline via PyTorch MPS. No cloud, no GUI required—just reliable, scriptable edits with clear, reproducible results.

## What we’re trying to achieve
- **CLI-first workflow:** One clean command (`edit`, `inpaint`, `composite`, `batch`) with explicit flags.
- **Determinism & reproducibility:** Fixed seeds, captured settings, and a small JSON manifest for every output.
- **Great terminal UX:** Helpful progress, warnings, and errors using Rich; sane defaults with safe fallbacks to CPU.
- **Simple architecture:** A thin Typer CLI calling a small `Engine` that wraps the model; easy to test and extend.

## Doctrine quick links
- [Constitution (`/memory/constitution.md`)](./memory/constitution.md) — start here for the guiding principles, quality strategy, delivery practices, and governance you’re expected to follow.
- [Rules (`docs/rules-idioms-architecture/rules.md`)](./docs/rules-idioms-architecture/rules.md) — the enforceable MUST/SHOULD statements every contribution is reviewed against.
- [Idioms (`docs/rules-idioms-architecture/idioms.md`)](./docs/rules-idioms-architecture/idioms.md) — practical patterns and examples that show how to implement the doctrine in code.
- [Architecture (`docs/rules-idioms-architecture/architecture.md`)](./docs/rules-idioms-architecture/architecture.md) — structural boundaries, data flows, and reviewer checklists.

Read these before you open a PR or start a feature; they explain the "why" behind our CLI-first, deterministic workflow and keep everyone aligned.

