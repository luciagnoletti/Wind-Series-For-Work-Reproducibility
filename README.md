## Wind-speed time series

The files in this directory contain turbulent longitudinal wind-speed time
series extracted at hub height from the corresponding full-field TurbSim
`.bts` files.

The extraction preserves the original temporal resolution and frequency
content of the hub-point signal. No temporal averaging, downsampling, or
additional filtering was applied.

### General characteristics

| File | Mean wind speed | Duration | Sampling interval | Sampling frequency | Samples |
|---|---:|---:|---:|---:|---:|
| `TurbSim_6ms_1h_005dt.hh`  | 6 m/s  | 1 h | 0.05 s | 20 Hz | 72,000 |
| `TurbSim_8ms_1h_005dt.hh`  | 8 m/s  | 1 h | 0.05 s | 20 Hz | 72,000 |
| `TurbSim_10ms_1h_005dt.hh` | 10 m/s | 1 h | 0.05 s | 20 Hz | 72,000 |
| `TurbSim_12ms_1h_005dt.hh` | 12 m/s | 1 h | 0.05 s | 20 Hz | 72,000 |

The `.hh` files were prepared as spatially uniform, time-varying wind inputs
for the OpenFAST InflowWind module using `WindType = 2`. Consequently, the
same scalar wind-speed input can be applied directly to OpenFAST, the
quasi-steady power-curve model, and the proposed reduced-order model.

This configuration enables a controlled comparison of the wind-to-power
dynamic responses because all models are subjected to exactly the same
hub-height wind-speed realization.

> **Note:** These `.hh` files contain the hub-point longitudinal wind-speed
> signal and should not be interpreted as complete three-dimensional TurbSim
> full-field wind files.

## Extracted OpenFAST output files

The `.npz` files provided in this repository contain selected time-series
channels extracted from the corresponding OpenFAST binary output files
(`.outb`).

The original `.outb` files contain a large number of output channels and can
require substantial storage space. Therefore, only the variables required
for the reduced-order-model validation were exported to compressed NumPy
archives.

The `.npz` files are derived datasets and are not native OpenFAST input or
output files. They can be loaded directly in Python using NumPy, without
requiring the complete OpenFAST binary-output reader.

### Available channels

Each `.npz` archive contains the following arrays:

| Array | Description | Unit |
|---|---|---:|
| `time` | OpenFAST simulation time | s |
| `wind_speed` | Longitudinal wind speed reported by `Wind1VelX` | m/s |
| `rotor_speed` | Rotor rotational speed reported by `RotSpeed` | rpm |
| `generator_power` | Electrical generator power reported by `GenPwr` | MW |
| `generator_torque` | Generator torque reported by `GenTq` | kN·m |

The generator-power values were converted from kW, as reported by OpenFAST,
to MW before being stored in the archives.

### Loading the data in Python

The extracted OpenFAST results can be loaded using:

```python
import numpy as np

data = np.load("IEA-15-240-RWT-Monopile-1day_8ms.npz")

time = data["time"]                       # s
wind_speed = data["wind_speed"]           # m/s
rotor_speed = data["rotor_speed"]         # rpm
generator_power = data["generator_power"] # MW
generator_torque = data["generator_torque"] # kN·m
```
### Relationship with the wind-input files

Each extracted OpenFAST dataset corresponds to one of the hub-height wind
series provided in the `Wind Files` directory.

| Mean wind speed | Wind input | Extracted OpenFAST output |
|---:|---|---|
| 6 m/s | `TurbSim_6ms_1h_005dt.hh` | `IEA-15-240-RWT-Monopile-1day_6mst.npz` |
| 8 m/s | `TurbSim_8ms_1h_005dt.hh` | `IEA-15-240-RWT-Monopile-1day_8ms.npz` |
| 10 m/s | `TurbSim_10ms_1h_005dt.hh` | `IEA-15-240-RWT-Monopile-1day_10ms.npz` |
| 12 m/s | `TurbSim_12ms_1h_005dt.hh` | `IEA-15-240-RWT-Monopile-1day_12ms.npz` |

The same wind-speed realization was supplied to OpenFAST, the quasi-steady
power-curve formulation, and the reduced-order model. This allows direct
sample-by-sample comparison of their wind-to-power responses.
