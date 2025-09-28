# Idioms — Qwen Image Edit CLI

These idioms illustrate how to implement the constitution and rules in everyday code. Adapt names as needed while preserving the documented patterns.

## CLI Structure (Typer + Rich)

```python
# cli.py
import typer
from rich.console import Console
from .commands import edit, batch, inpaint, composite, selftest, inspect

app = typer.Typer(add_completion=False, rich_markup_mode="markdown")
console = Console()

@app.callback()
def main(ctx: typer.Context) -> None:
    ctx.obj = {
        "console": console,
        "engine": build_engine(ctx.params),
    }

app.command()(edit.run)
app.command()(inpaint.run)
app.command()(composite.run)
app.command()(batch.run)
app.command()(selftest.run)
app.command()(inspect.run)
```

- Keep the CLI module declarative; heavy lifting moves to `commands/` and `core/`.
- Propagate shared objects (console, engine) through `ctx.obj` rather than globals.

## Engine Wrapper

```python
# core/engine.py
from .device import select_device
from .pipeline import QwenImageEditor

class Engine:
    def __init__(self, *, model_id: str, seed: int, device_preference: str | None = None) -> None:
        self.device = select_device(preferred=device_preference)
        self.pipeline = QwenImageEditor.from_pretrained(model_id, device=self.device)
        self.seed = seed

    def edit(self, request: EditRequest) -> EditResult:
        generator = torch.Generator(device=self.device).manual_seed(self.seed)
        return self.pipeline.edit(request, generator=generator)
```

- Device logic stays isolated in `device.py`; commands never import `torch` directly.
- All public methods accept dataclass-like request objects for clarity and testability.

## Rich Progress & Panels

```python
with console.status("Loading pipeline…"):
    engine = Engine(model_id=config.model_id, seed=config.seed)

with console.status("Running edit", spinner="dots"):
    result = engine.edit(request)

console.print(result.to_panel())
```

- Favor context managers for progress; wrap critical sections so logs remain structured.
- Represent user-facing summaries as Rich `Panel` or `Table` builders.

## Config Layering

```python
@dataclass
class RuntimeConfig:
    seed: int
    device: str
    model_id: str

    @classmethod
    def from_sources(cls, *, cli_args: dict[str, Any], env_prefix: str = "QWEN_EDIT") -> "RuntimeConfig":
        data = load_env(prefix=env_prefix)
        data.update(load_toml("~/.config/qwen-edit/config.toml"))
        data.update({k: v for k, v in cli_args.items() if v is not None})
        return cls(**data)
```

- Merge settings from env → user config → CLI flags; CLI always wins.
- Serialize the final config into the manifest to honor determinism.

## Testing Patterns

```python
import json
from typer.testing import CliRunner

from qwen_edit.cli import app

runner = CliRunner()

def test_selftest_smoke(tmp_path):
    result = runner.invoke(app, ["selftest", "--device", "cpu", "--manifest", tmp_path / "run.json"])
    assert result.exit_code == 0
    manifest = json.loads((tmp_path / "run.json").read_text())
    assert manifest["device"] == "cpu"
    assert manifest["seed"] == 1234
```

- Use `typer.testing.CliRunner` for CLI-level assertions.
- Persist manifests to temporary directories and assert deterministic fields.

## Golden Image Regression

```python
def test_edit_regression(golden_path, tmp_path, engine):
    request = EditRequest(prompt="Add a lighthouse", image=golden_path / "scene.png")
    result = engine.edit(request)
    assert_images_close(result.image, golden_path / "scene_expected.png", tolerance=2.5)
```

- Store golden assets under `tests/data/golden/` with README explaining provenance.
- Use perceptual diff tooling (e.g., SSIM) to reduce flakiness.

## Governance Hooks

- Surface doctrine references in docs using fragments like "See Constitution §Quality & Verification".
- TODO(OWNERS): Update README with named stewards once assigned.
- TODO(SECURITY_CONTACT): Publish security escalation path; reflect here when available.
- TODO(MODEL_SOURCES): Link to vetted model download instructions when ratified.
- TODO(LICENSE): Embed license obligations into contributor docs once chosen.
