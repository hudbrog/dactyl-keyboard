# Repository Guidelines

## Project Structure & Module Organization
- `src/`: Python sources for generating models. `dactyl_manuform.py` is the main entrypoint; `generate_configuration.py` scaffolds configs; `model_builder.py`/`bulk_build.py` handle batch builds; `clusters/`, `parts/`, and `helpers_*` house geometry helpers and cluster variants.
- `configs/`: Versionable JSON configs; scripts accept `--config=<name>` (without extension) to load from here. Root `run_config.json`/`.yaml` provide defaults.
- `things/`: Generated output (STL/STEP/OpenSCAD artifacts). Safe to delete/regenerate.
- `docker/`, `Makefile`, `run.sh`, `dactyl.sh`: Containerized build helpers and interactive menu tools.
- `guide/`, `resources/`, `gallery/`: Reference docs, wiring imagery, and example renders.

## Build, Test, and Development Commands
- Local run (preferred): activate the `uv`-managed venv, then `python src/dactyl_manuform.py` to use `run_config.json` and emit artifacts into `things/` (or pass `--config=<name>` for a file in `configs/`). Expect the run to take a couple minutes; the current config renders right-side only.
- Quick config scaffold: `python src/generate_configuration.py --config my-layout` writes `configs/my-layout.json`.
- Dockerized pipeline (optional): `./run.sh generate` (or `./run.sh configure`, `./run.sh release`, `./run.sh build`) wraps the same scripts in a container with bind mounts.
- Make targets (Docker required): `make build` to rebuild the image and generate config + models; `make shell` to open an interactive container; `make config` or `make build-models` for individual stages.
- Conda setup (from README, alternative to `uv`): create/activate `dactyl-keyboard`, install `cadquery`, `numpy`, `scipy`, `solidpython`, then run the commands above.

## Coding Style & Naming Conventions
- Python 3, 4-space indentation, trailing commas for long structures, and snake_case for functions/variables; class names in PascalCase (see `clusters/`).
- Keep configuration keys consistent with existing JSON (uppercase for engine-wide flags, snake_case for options). Store shared defaults in `run_config.json`.
- Prefer small, focused modules; place new geometry helpers beside related parts/cluster files.

## Testing Guidelines
- No automated test suite; verify changes by generating both OpenSCAD (`ENGINE=solid`) and cadquery (`ENGINE=cadquery`) outputs for at least one sample config in `configs/`.
- Inspect resulting models in `things/` (STL/STEP) for obvious geometry regressions; spot-check trackball/thumb cluster fits when those areas change.
- If adding params, ensure defaults keep existing configs working and add an example to a config file.

## Commit & Pull Request Guidelines
- Follow the repo’s short, imperative commit style (e.g., “fix thumb rotation”, “add new cluster mount”); squash noisy WIP history before opening a PR.
- Include: summary of the change, configs used to validate, notes on engines tested (solid/cadquery), and before/after screenshots or renders when geometry shifts.
- Link related issues or discussion threads; call out breaking changes or new dependencies explicitly.

## Configuration & Safety Tips
- Treat `configs/*.json` as the source of truth; avoid editing generated files in `things/`.
- Docker runs mount `src/`, `configs/`, and `things/`; keep local paths stable to avoid permission issues.
- Large builds are compute-heavy—run cadquery batches (`./run.sh release` or `python src/model_builder.py`) when you can let them complete uninterrupted.
