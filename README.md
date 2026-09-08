# Design and Analysis of a 24W Open-Loop DC-DC Boost Converter

## Overview

This project presents the design, simulation, and analysis of an open-loop DC-DC boost converter built in MATLAB Simulink (Simscape Electrical). The converter steps up a 12V input to a 24V output at 2A load current, switching at 100kHz. All component values (inductor, output capacitor) were analytically derived from first-principles power electronics equations rather than assumed, and the simulated results are compared against theoretical predictions.

## Design Specifications

| Parameter | Value |
|---|---|
| Input voltage (Vin) | 12V |
| Output voltage (Vout) | 24V |
| Output current (Iout) | 2A |
| Output power | 48W |
| Switching frequency (f) | 100kHz |
| Topology | Boost (step-up), open-loop |
| Conduction mode | Continuous (CCM) |

## Design Equations and Calculations

### Duty Cycle

For an ideal boost converter in CCM:

$$D = 1 - \frac{V_{in}}{V_{out}} = 1 - \frac{12}{24} = 0.5$$

### Average Inductor Current

Since the inductor sits on the input side of a boost converter, its average current equals the average input current:

$$I_{in} = \frac{P_{out}}{V_{in}} = \frac{48}{12} = 4A$$

### Inductor Sizing

Targeting a current ripple of 30% of average inductor current (ΔIL = 1.2A):

$$L = \frac{V_{in} \cdot D}{f \cdot \Delta I_L} = \frac{12 \times 0.5}{100{,}000 \times 1.2} = 50\mu H$$

### Output Capacitor Sizing

Targeting an output voltage ripple of 1% of Vout (ΔVout = 0.24V):

$$C = \frac{I_{out} \cdot D}{f \cdot \Delta V_{out}} = \frac{2 \times 0.5}{100{,}000 \times 0.24} \approx 42\mu F$$

Rounded to a standard value of **47µF**.

### CCM Verification

Load resistance: R = Vout / Iout = 24 / 2 = 12Ω

$$L_{crit} = \frac{(1-D)^2 \cdot D \cdot R}{2f} = \frac{(0.5)^2 \times 0.5 \times 12}{2 \times 100{,}000} = 7.5\mu H$$

Since the chosen inductance (50µH) is well above L_crit (7.5µH), the converter operates safely in continuous conduction mode.

## Final Design Values

| Parameter | Value |
|---|---|
| Duty cycle (D) | 50% |
| Inductor (L) | 50µH |
| Output capacitor (C) | 47µF |
| Load resistor (R) | 12Ω |

## Simulink Model

The circuit was built in Simscape Electrical using:
- DC voltage source (12V)
- Inductor (50µH, on the input side — characteristic of boost topology)
- Ideal switching MOSFET, gate-driven by a 100kHz pulse generator at 50% duty cycle
- Diode (forward voltage 0.6V, on-resistance 0.3Ω)
- Output capacitor (47µF) and load resistor (12Ω)
- Current sensors on the inductor and output branch, and a voltage sensor across the load, feeding a scope for observation

![Block Diagram](block_diagram.png)

## Results

| Parameter | Theoretical | Simulated | Notes |
|---|---|---|---|
| Vout | 24V | ~23–24V | Small gap due to non-ideal component losses |
| IL (average) | 4A | ~4A | Matches, with expected switching ripple |
| Iout | 2A | ~1.9–2A | Matches |

![Output Waveforms](output_waveforms.png)

### Startup Transient

At t=0, the inductor current and output capacitor voltage both start at zero. Before the converter reaches its periodic steady-state operating point, the inductor current must build up over several switching cycles, and the output capacitor must charge. This produces a pronounced startup overshoot (Vout briefly peaking above 30V, IL peaking near 18A) followed by a damped oscillation before settling to steady-state values within approximately 2–3ms.

### Sources of Deviation from Ideal Theory

The simulated steady-state values are close to but not exactly at their theoretical targets, due to non-ideal component behavior included in the model:
- Diode forward voltage drop: 0.6V
- Diode on-resistance: 0.3Ω
- MOSFET drain-source on-resistance (Rds(on)): 0.01Ω

These losses are deliberately included (rather than zeroed out) to reflect realistic component behavior, since an idealized lossless simulation would not represent how a physical boost converter actually performs.

## Limitations and Future Work

This is an **open-loop** design — the duty cycle is fixed at 50% regardless of load or input variations. As a result, the output voltage would droop under load changes or input voltage variation, since there is no feedback mechanism to correct for it. A natural extension of this project would be to add closed-loop voltage-mode control (e.g., a PI controller adjusting duty cycle based on output voltage error) to maintain regulation under varying conditions.

## Tools Used

- MATLAB Simulink / Simscape Electrical

## Author

Abhishek Rajora (Avi)
B.Tech Electrical Engineering, Delhi Technological University
