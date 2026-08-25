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

The `.npz` files in the `OpenFAST_Files` directory contain selected
time-series channels extracted from the corresponding OpenFAST binary
output files (`.outb`).

The original OpenFAST binary files can require substantial storage space.
Therefore, the variables required for the wind-to-power analysis were
exported as compressed NumPy archives.

These `.npz` archives are derived datasets and are not native OpenFAST
files. They can be loaded directly using NumPy without requiring an
OpenFAST binary-output reader.

### Currently available dataset

The repository currently includes the following 1-hour OpenFAST dataset:

| File | Duration | Sampling interval | Samples |
|---|---:|---:|---:|
| `IEA-15-240-RWT-Monopile-1h_6ms_005dt.npz` | 24 h | 0.05 s | 1,728,001 |
| `IEA-15-240-RWT-Monopile-1h_8ms_005dt.npz` | 24 h | 0.05 s | 1,728,001 |
| `IEA-15-240-RWT-Monopile-1h_10ms_005dt.npz` | 24 h | 0.05 s | 1,728,001 |
| `IEA-15-240-RWT-Monopile-1h_12ms_005dt.npz` | 24 h | 0.05 s | 1,728,001 |

The archive contains the following arrays:

| Array | Description | Unit | Data type |
|---|---|---:|---:|
| `time_s` | OpenFAST simulation time | s | `float64` |
| `wind_ms` | Longitudinal wind speed | m/s | `float32` |
| `power_MW` | OpenFAST generator electrical power | MW | `float32` |

### Loading the 1-hour dataset

```python
import numpy as np

data = np.load(
    "OpenFAST_Files/IEA-15-240-RWT-Monopile-1day_8ms.npz"
)

time = data["time_s"]       # s
wind_speed = data["wind_ms"]  # m/s
generator_power = data["power_MW"]  # MW

print(data.files)
print(time.shape, wind_speed.shape, generator_power.shape)
```

### Relationship with the wind-input files

Each extracted OpenFAST dataset corresponds to one of the hub-height wind
series provided in the `Wind Files` directory.

| Mean wind speed | Wind input | Extracted OpenFAST output |
|---:|---|---|
| 6 m/s | `TurbSim_6ms_1h_005dt.hh` | `IEA-15-240-RWT-Monopile-1h_6ms_005dt.npz` |
| 8 m/s | `TurbSim_8ms_1h_005dt.hh` | `IEA-15-240-RWT-Monopile-1h_8ms_005dt.npz` |
| 10 m/s | `TurbSim_10ms_1h_005dt.hh` | `IEA-15-240-RWT-Monopile-1h_10ms_005dt.npz` |
| 12 m/s | `TurbSim_12ms_1h_005dt.hh` | `IEA-15-240-RWT-Monopile-1h_12ms_005dt.npz` |

The same wind-speed realization was supplied to OpenFAST, the quasi-steady
power-curve formulation, and the reduced-order model. This allows direct
sample-by-sample comparison of their wind-to-power responses.

### Process to create `.npz` files

```python
import numpy as np
from openfast_toolbox.io import FASTOutputFile

input_file = "IEA-15-240-RWT-Monopile-1h_6ms_005dt.outb"
output_file = "IEA-15-240-RWT-Monopile-1h_6ms_005dt.npz"

df = FASTOutputFile(input_file).toDataFrame()

np.savez_compressed(
    output_file,
    time_s=df["Time_[s]"].to_numpy(),
    wind_ms=df["Wind1VelX_[m/s]"].to_numpy(),
    rotor_speed_rpm=df["RotSpeed_[rpm]"].to_numpy(),
    generator_power_MW=df["GenPwr_[kW]"].to_numpy() / 1000.0,
    generator_torque_kNm=df["GenTq_[kN-m]"].to_numpy(),
)
```

The OpenFAST channels were extracted using `FASTOutputFile` from the
`openfast_toolbox` Python package. The selected arrays were stored using
`numpy.savez_compressed`, which applies lossless compression. Therefore,
compression does not introduce averaging, filtering, downsampling, or
numerical approximation beyond the original data type stored in the
OpenFAST output.

The extracted datasets retain the OpenFAST output sampling interval of
0.05 s, corresponding to a sampling frequency of 20 Hz.
