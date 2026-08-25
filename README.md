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
