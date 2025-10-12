## Repository: repositorio — Copilot instructions

This repository is a small Dash web application that renders an energy demand forecasting dashboard.
Keep instructions concise and make changes only when you can validate locally (see "How to run").

Key files/directories
- `app.py` — single-entry Dash app (server is `app.server`). Most logic lives here: data loading, layout, callbacks, plotting helpers.
- `datos_energia.csv` — primary dataset. CSV is read with pandas in `load_data()` and indexed by `time`.
- `assets/` — contains CSS (`base.css`, `clinical-analytics.css`) used automatically by Dash. Prefer style tweaks here.

Big picture & architecture
- This is a monolithic Dash app (no separate backend service). `app.py` creates `dash.Dash(...)`, sets `server = app.server`, and runs the app with `app.run(debug=True, port=8050)` when executed.
- Data flow: `app.py` loads `datos_energia.csv` once at import time via `load_data()` into the module-level `data` variable. Callbacks read from this in-memory dataframe and pass slices to `plot_series()` which returns a Plotly `Figure`.
- UI pattern: layout is built procedurally in `app.layout` using helper functions (`description_card`, `generate_control_card`) and Dash components (`dcc.DatePickerSingle`, `dcc.Slider`, `dcc.Graph`). Callbacks are defined with `@app.callback` and use `Input`/`Output` from `dash.dependencies`.

Project-specific conventions & patterns
- Single-file app: Most work should modify `app.py`. Avoid creating additional runtime data readers unless necessary; the app expects `datos_energia.csv` in the repo root.
- Data indexing: `load_data()` sets `time` as datetime index and sorts it. Use the same convention for any additional preprocessing.
- Projection window: the slider `slider-proyeccion` uses integer hours from 0 to 119 and `plot_series()` expects a projection length `proy` (0-119). When changing UI controls keep these ranges consistent.

Developer workflows (how to run & debug)
- Run locally (Windows PowerShell):

```powershell
python .\app.py
```

- App listens on port 8050 by default. When developing, run with `debug=True` as in `app.py` to get auto-reload and useful error messages.
- Dataset edits: `datos_energia.csv` is loaded at import. When you change the CSV, restart the app or rely on Dash auto-reload if executed from the file.

Integration points and external dependencies
- External libraries: `dash`, `plotly`, `pandas`, `numpy`. These are imported at the top of `app.py`. There is no requirements file; if missing, install via pip (e.g., `pip install dash plotly pandas numpy`).
- No networked services or databases are present in the repo. All I/O is the local CSV and static `assets/` files.

Safety and quick heuristics for edits
- When modifying plots, preserve color and layout conventions used in `plot_series()` (e.g., green `#188463` for main series, light green `#bbffeb` for forecast). These match the CSS visual language in `assets/`.
- Prefer small, incremental changes. Because the app imports data at module load, large data transformations should be added as separate functions and tested locally before integrating into callbacks.

Examples to follow
- To add a new control, create a helper that returns a Dash component and insert it into `app.layout` alongside existing helpers (`description_card`, `generate_control_card`). Mirror the existing callback signature pattern (Input list, Output single figure).
- To change data preprocessing do it inside `load_data()` and keep the function idempotent (safe to call at import time).

What not to change
- Avoid renaming `data` or removing `app.server` — tests or deployment scripts may expect `server` to exist.
- Don't move `datos_energia.csv` from the repo root without updating `load_data()` path.

If something is unclear
- Ask for clarification and include the exact lines you plan to change in `app.py` and a brief explanation why. Provide a small, testable patch.

---
If you'd like, I can also:
- add a minimal `requirements.txt` or `pyproject.toml` (small, low-risk improvement),
- create a short `README.md` with the run instructions shown above.

Please review and tell me which parts you want expanded or changed.
