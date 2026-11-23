# Case `dip2D_hydrostatic_n10` (auto-generated -- edit `params/params.yaml` and re-run the generator)

* OpenFOAM 14 (`foamRun`, module `compressibleVoF`), git `uncommitted`
* Kind: **dip**, D/dx = **10**, cells = **12000**, dx = 5.000 mm
* 2D planar: vertical height H = D/cos(theta) = 50.191 mm, thickness 10.0 mm, front/back = empty
* Fluids: oil (rho=900.0, mu=0.2) / air (perfect gas)
* Operating point: J_L=0.2 m/s, J_G=0.5 m/s
  -> inlet U_l=0.0000, U_g=0.0000 m/s over a liquid fraction f=0.3000 of the height
  (flow rates per unit width: Q_L=0.0100, Q_G=0.0250 m2/s)
* Initial pool: horizontal free surface y = 0.15 m (liquid below)
* End time 5.0 s, field write every 0.25 s, maxCo=0.5, maxAlphaCo=0.5, thermo=adiabatic

## Run
```bash
./run.sh              # serial
NPROC=12 ./run.sh     # parallel, 12 cores
./run.sh --continue   # restart from latest time
```
First run does blockMesh + setFields + checkMesh. Delete `constant/polyMesh`, `0.*` and time
directories to start from scratch (`foamCleanCase`), or use `Allclean`-style manual deletion.

## Files a beginner should look at
| file | role |
|---|---|
| `system/blockMeshDict` | mesh: 4 blocks (2 legs x 2 vertical layers), patches inletLiquid/inletGas/outlet/walls/frontAndBack |
| `0/alpha.oil` | liquid volume fraction (1=liquid, 0=gas); `setFields` fills the initial pool |
| `0/U`, `0/p_rgh`, `0/p`, `0/T` | velocity, dynamic pressure (p_rgh = p - rho*gh), absolute pressure, temperature |
| `constant/physicalProperties.*` | per-phase thermophysical properties |
| `constant/phaseProperties` | phase list + sigma |
| `system/fvSchemes` | discretisation: `interfaceCompression vanLeer` for alpha, upwind for U |
| `system/fvSolution` | linear solvers, MULES settings, PIMPLE |
| `system/functions` | probes, interface-height columns, mass balance, outlet pressure |
