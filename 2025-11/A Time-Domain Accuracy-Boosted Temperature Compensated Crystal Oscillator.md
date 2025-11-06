# A Time-Domain Accuracy-Boosted Temperature Compensated Crystal Oscillator

## Main Ideas
- Proposes a time-domain accuracy-boosted CL-trimming TCXO that suppresses sensor and actuator noise, offsets, and distortion so active compensation does not degrade clock quality.
- Introduces an enhanced-decimation filter (zero-stuffed IIR plus CIC) with an oversampled all-digital path feeding a delta-sigma and R-DAC tuning interface to strongly attenuate temperature-sensor noise while keeping fine mapping resolution.
- Implements hybrid AGC biasing, chopped amplifiers, and dynamic-weighted R-DAC switching so the compensation loop adds under 4 dB of phase-noise penalty and keeps spurs below the Pierce core floor.
- A 0.18 um CMOS prototype reaches +-0.6 ppm (-55 to 85 C) and +-1.4 ppm (-55 to 115 C) accuracy, about 38x better than uncompensated crystals, while preserving short-term stability (phase noise, Allan deviation).

## Thoughts
- The end-to-end time-domain framing is a clever way to convert analog error-budget issues into digital filtering problems; each block reshapes spectra to make later suppression easier.
- Dynamic-weighted averaging plus quad switching in the R-DAC seems pivotal for spur control; I am curious about its sensitivity to switch mismatch or divider clock skew in silicon.
- Calibration leans on high-order polynomials; exploring adaptive or lower-point fits per crystal class could shrink the digital footprint without giving up ppm-level accuracy.

## Questions
1. How tolerant is the enhanced-decimation filter to coefficient quantization or divider jitter when ported to finer CMOS nodes?
2. Could the mapping engine refresh coefficients in-field to track long-term crystal aging without full recalibration?
3. What dominates the residual +-0.6/+-1.4 ppm error: sensor quantization, actuator nonlinearity, or crystal hysteresis?
4. How would the scheme adapt for lower-frequency (kHz) resonators with very different CL sensitivities?
5. Can the R-DAC and varactor interface support spread-spectrum modulation without undoing the phase-noise gains?

## Summary To-Go
- All-digital-centric compensation chain uses time-domain filtering to push sensor noise out of the 0 to 100 Hz band.
- Chopped, dynamic-averaged R-DAC keeps the phase-noise penalty under 4 dB while enabling a 60 ppm tuning window.
- Silicon results validate +-0.6/+-1.4 ppm accuracy across 140/170 C swings with 6.31 mW draw.

## Mind Tree
- **A Time-Domain Accuracy-Boosted TCXO**
  - **Motivation & Context**
    - Megahertz clocks need ppm accuracy for GNSS and narrowband radios.
    - Temperature drift of quartz dominates uncompensated offset (20–100 ppm).
    - Goal: active CL trimming without degrading phase noise.
  - **Core Challenges**
    - Temperature sensor noise and offsets propagate into frequency tuning.
    - Mapping engine must fit high-order polynomials yet minimize quantization.
    - Load-capacitance tuning interface injects flicker noise and spurs.
  - **Time-Domain Accuracy-Boosted Architecture**
    - Hybrid AGC plus programmable bias keeps oscillator amplitude stable.
    - Bipolar TFE with CT-ΔΣ readout produces a 1-bit 51.2 kHz temperature stream.
    - Enhanced-decimation filter (zero-stuffed IIR plus CIC) narrows bandwidth to 100 Hz.
    - Digital polynomial mapping outputs 16-bit capacitance codes.
    - CIC interpolation and single-loop ΔΣ reshape quantization before actuation.
  - **CL Tuning Interface**
    - 5-bit thermometer R-DAC sets Vtune for piecewise-biased varactor arrays.
    - Dynamic-weighted averaging rotates elements to spread static mismatch.
    - Quad switching enforces constant on/off events each update.
    - Chopped rail-to-rail amplifiers and RC smoothing curb flicker noise.
  - **Noise and Stability Strategy**
    - Push sensor noise out of band so a narrow filter can reject it.
    - Keep added phase-noise penalty under 4 dB across offsets.
    - Allan deviation limited mainly by intrinsic crystal behavior.
  - **Silicon Results**
    - 0.18 µm CMOS, 3.18 mm² core, 6.31 mW with compensation active.
    - ±0.6 ppm (−55 to 85 °C) and ±1.4 ppm (−55 to 115 °C) over multiple samples.
    - Dynamic ramps at 21.3 °C/min stay within the ±1.4 ppm band.
    - Phase noise with compensation on matches baseline beyond 100 Hz offsets.
  - **Open Questions and Extensions**
    - Trade calibration order versus digital area for production targets.
    - Support in-field coefficient refresh to follow aging and hysteresis.
    - Adapt architecture for low-frequency or MEMS resonators with different CL sensitivity.
