# RIA Fast Shutdown Simulator (v0.7)

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.17868291.svg)](https://doi.org/10.5281/zenodo.17868291)
[![License](https://img.shields.io/github/license/Maxbanker/ria-fast-shutdown-sim.svg)](https://github.com/Maxbanker/ria-fast-shutdown-sim/blob/main/LICENSE)

**Author:** Steven Lanier-Egu • **Credit:** SEAL Division

This repository pairs a Typora-ready paper and a runnable Python simulator that **validates a fast, orchestrated shutdown for reactivity-initiated accidents (RIA) in high-burnup fuel**:

* **Sense → Release → Terminate → Recover** control story
* **Worth-vs-time targets** (primary): ≥ **0.5 mk @ 20 ms**, ≥ **1.0 mk @ 50 ms**
* **Non-blocking invariant:** supervisory delay ≤ **500 µs**
* **Artifacts:** CSV, JSON log, reactivity & power plots
* **Zenodo record (paper + software):** [https://doi.org/10.5281/zenodo.17868291](https://doi.org/10.5281/zenodo.17868291)

## Contents

* `ria_sim_v07.py` — reference simulator with checks and artifact generation
* `docs/RIA_Protection_Upgrade_Stack_v0.7.md` — paper (with Appendix A: Simulation Methods & Validation)
* `.github/workflows/ci.yml` — smoke-test CI
* `pyproject.toml`, `requirements.txt`, `Makefile` — packaging and quick demo

## Quickstart

```bash
python3 -m venv .venv && source .venv/bin/activate
python -m pip install -r requirements.txt

# Run default rod-ejection scenario
python ria_sim_v07.py --outdir ./out

# Alternative: cold-water insertion
python ria_sim_v07.py --scenario cold_water --outdir ./out_cwi
```

### CLI Options

```bash
# show help
python ria_sim_v07.py -h

# knobs for experiments / CI sweeps
python ria_sim_v07.py --outdir ./out \
  --scenario rod_ejection \
  --primary-fail-prob 0.0 \
  --non-blocking-us 400 \
  --seed 1
```

### Outputs

* `out/timeseries.csv` — time, ρ, normalized power, primary/backup/poison worths, event timestamps
* `out/sim_log.json` — config, events, and pass/fail for each acceptance check
* `out/reactivity.png`, `out/power.png` — one figure per plot with event markers (trip / primary / backup / poison)

## What the Simulator Verifies

* **Worth-vs-time envelopes (relative to trip)**: primary ≥0.5 mk @ +20 ms and ≥1.0 mk @ +50 ms; backup path checked when it fires
* **Non-blocking invariant**: supervisory delay capped at 500 µs
* **Tail termination**: poison/spectrum path engages from ≥10 ms toward the 1 s margin goal
* **Artifacts**: all runs emit CSV/JSON/plots for auditability

## Paper

* See `docs/RIA_Protection_Upgrade_Stack_v0.7.md` (includes a one-page executive overview and **Appendix A — Simulation Methods & Validation**).

## CI

GitHub Actions (on push/PR) installs deps, runs the simulator, and asserts that the checks pass.

## Packaging & Make

* Editable install: `pip install -e .` (uses `pyproject.toml`)
* Quick demo: `make demo` (creates venv, installs, runs `ria-sim` entrypoint)

## Citation

If you use this work, please cite the Zenodo record:

**Lanier-Egu, S. (2025).** RIA Protection Upgrade Stack v0.7 — Simulation-Validated Fast Shutdown Orchestration for High-Burnup Reactors. Zenodo. [https://doi.org/10.5281/zenodo.17868291](https://doi.org/10.5281/zenodo.17868291)

```bibtex
@software{lanier-egu_ria_v07_2025,
  author    = {Steven Lanier-Egu},
  title     = {RIA Protection Upgrade Stack v0.7 — Simulation-Validated Fast Shutdown Orchestration for High-Burnup Reactors},
  year      = {2025},
  doi       = {10.5281/zenodo.17868291},
  url       = {https://doi.org/10.5281/zenodo.17868291},
  version   = {v0.7}
}
```

## License

MIT (see `LICENSE`). Documentation included under the same repository unless otherwise noted.

---

Reactivity units: **mk** denotes milli-k (Δk/k × 1e-3). One dollar equals **β_eff** of the core (plant-specific).
