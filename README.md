# Case `dip2D_hydrostatic_n10` — OpenFOAM-14 case template

> **Auto-generated** by `tools/gen_case.py` from `params/params.yaml`. Do not hand-edit the
> dictionaries: change `params/params.yaml` (or the generator) and re-run
> `python3 tools/gen_case.py dip2D_hydrostatic_n10 --force`.
> Full per-case report (methodology, equations, dimensional analysis, results, verification):
> [`report/dip2D_hydrostatic_n10/README.md`](../../report/dip2D_hydrostatic_n10/README.md) and
> [`report/dip2D_hydrostatic_n10/report.pdf`](../../report/dip2D_hydrostatic_n10/report.pdf).

**Static verification case:** both inlet velocities are fixed to zero and the liquid is initialised with the exact hydrostatic `p_rgh`, so the initial state is static to round-off; the case is used to measure the hydrostatic pressure error and the spurious-current amplitude.

## 1. Software environment and dependencies

* **OpenFOAM-14** (Foundation build, openfoam.org), solver `foamRun -solver compressibleVoF`.
  Generated and tested with git `aef427a`.
* Linux (verified on Ubuntu, 12 cores); `bash`.
* OpenMPI only if `NPROC>1` (serial is faster on these meshes; see §3.5).
* Python 3.10+ with `numpy`, `pandas`, `matplotlib`, `pyyaml` for the project pipeline
  (`python3 -m pip install numpy pandas matplotlib pyyaml`).
* `latexmk` + `pdflatex` only to rebuild the per-case report.
* Environment: `source /opt/openfoam14/etc/bashrc`.

## 2. What this case is

* Kind: **dip**, D/dx = **10**, cells = **12000**, dx = 5.000 mm.
* Planar 2D: vertical height H = D/cos(theta) = 50.191 mm, thickness 10.0 mm,
  front/back = `empty`.
* Fluids: oil (rho=900.0, mu=0.2) /
  air (perfectGas).
* Operating point: J_L=0.2 m/s, J_G=0.5 m/s
  -> inlet U_l=0.0000, U_g=0.0000 m/s over a liquid fraction f=0.3000 of the height
  (flow rates per unit width: Q_L=0.0100,
  Q_G=0.0250 m2/s).
* Initial pool: horizontal free surface y = 0.15 m (liquid below).
* endTime 5.0 s, field write every 0.25 s, maxCo=0.5,
  maxAlphaCo=0.5, thermo=adiabatic.

## 3. Reproduction instructions

### 3.1 In-place standard OpenFOAM run

```bash
cd ./dip2D_hydrostatic_n10
./Allclean          # remove old results, keep 0/ (safe to repeat)
./Allrun            # blockMesh + setFields + checkMesh + foamRun (serial)
```

`Allrun` logs to `log.blockMesh`, `log.setFields`, `log.checkMesh`, `log.foamRun` and stops on
the first error (`set -eo pipefail`).


`new_run.sh` records `run.pid`, `run.info`, `meta.json` and the solver clocks, which is what the
cost accounting and the report use.

### 3.2 VTK for ParaView

```bash
cd ./dip2D_hydrostatic_n10_runs   # or cases/dip2D_hydrostatic_n10
source /opt/openfoam14/etc/bashrc
foamToVTK -latestTime          # or -time 10,20,30
```
Then open `VTK/*.vtk` in ParaView; colour by `alpha.oil` (interface), `p`, `U`.

## 4. Automation

| script | what it does | typical use |
|---|---|---|
| `run.sh` | in-place run with optional `NPROC`/`--continue`, metadata recording | quick local run |
| `Allrun` | standard OpenFOAM driver: blockMesh, setFields, checkMesh, foamRun | `cd cases/<case> && ./Allrun` |
| `Allclean` | removes written times (keeps `0/`), `postProcessing/`, `VTK/`, logs, `polyMesh` | reset before re-running |

