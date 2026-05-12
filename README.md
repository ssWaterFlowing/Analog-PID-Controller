# Analog PID Controller
### A Fully Analog Closed-Loop LED Brightness Controller

## Project Overview

A **fully analog PID (Proportional-Integral-Derivative) controller** that regulates LED brightness using only op-amp circuits, passive components, and a BJT output driver — no microcontroller, no software, no digital processing. The system senses LED current, computes the error against a setpoint, and drives a corrective output through three parallel analog compensation paths (P, I, D) summed into a single control signal.

Designed as an independent project to physically realize classical control theory in hardware, where gain is set by resistor ratios, time constants by RC products, and stability by circuit topology.

**Authors:** Devansh Joshi and Suren Shirani

## System Architecture

The controller implements a standard closed-loop PID topology in five analog stages:

```
                         ┌──────────────────────────────────────────┐
                         │              PID Compensation            │
                         │                                          │
V_set ──┐                │  ┌─────────────┐                        │
        │  ┌───────────┐ │  │ Proportional │──┐                    │
        ├─►│ Difference ├─┼─►│   (Kp gain)  │  │  ┌─────────────┐  │  ┌────────────┐
        │  │ Amplifier  │ │  ├─────────────┤  ├─►│  Summing     ├──┼─►│  2N3904    ├──► LED
V_curr ─┘  │  (TL082)   │ │  │  Integral   │  │  │  Inverting   │  │  │  BJT       │
           └───────────┘ │  │  (Ki gain)   │──┘  │  Amplifier   │  │  │  Driver    │
              e(t)       │  ├─────────────┤      │  (TL084)     │  │  └────────────┘
                         │  │ Derivative   │──┘  └─────────────┘  │
                         │  │  (Kd gain)   │                       │
                         │  └─────────────┘                        │
                         │      (TL084)                            │
                         └──────────────────────────────────────────┘
                                                                    │
                         ┌──────────────────────────────────────────┘
                         │ Feedback: V_curr = f(I_LED)
                         └─────────────────────────────────────────►V_curr
```

### Signal Flow

1. **Setpoint (V_set):** A calculated reference voltage representing the desired LED operating point
2. **Current Sense (V_curr):** The LED forward voltage is measured directly as a proxy for LED current
3. **Error Computation:** A TL082 difference amplifier computes `e(t) = V_set − V_curr`
4. **PID Compensation:** The error signal is routed in parallel to three TL084-based op-amp stages:
   - **P stage** — Resistor-ratio gain provides immediate proportional correction
   - **I stage** — Op-amp integrator (RC time constant) accumulates past error to eliminate steady-state offset
   - **D stage** — Op-amp differentiator (RC time constant) responds to rate-of-change to reduce overshoot
5. **Summation:** A TL084 summing inverting amplifier combines the P, I, and D outputs into a single control voltage
6. **Output Driver:** A 2N3904 NPN BJT in emitter-follower configuration converts the low-power control signal into a current drive capable of sourcing 20–40 mA to the LED
7. **Feedback:** The resulting LED voltage feeds back to the difference amplifier, closing the loop

## Circuit Design

### Component Summary

| Component | Part | Quantity | Role |
|---|---|---|---|
| Dual Op-Amp | TL082CP | 1 (2 channels) | Difference amplifier (error computation) |
| Quad Op-Amp | TL084CN | 1 (4 channels) | P, I, D stages + summing amplifier |
| NPN BJT | 2N3904 | 1 | Emitter-follower output driver |
| Potentiometers | PV37W104C01B00 (100 kΩ) | 3 | Kp, Ki, Kd gain tuning |
| Resistors | 10 kΩ | 14 | Gain-setting, biasing, feedback networks |
| Resistor | 2.2 kΩ | 1 | Biasing / current limiting |
| Resistor | 1 MΩ | 1 | High-impedance feedback path |
| Resistor | 620 Ω | 1 | LED current-limiting resistor |
| Capacitors | 0.1 µF | 2 | Bypass / filtering |
| Capacitors | 1 µF | 2 | Integrator time constant |
| Capacitor | 220 nF | 1 | PID compensation |
| Capacitor | 2.7 nF | 1 | Differentiator time constant |
| Capacitor | 10 µF | 1 | Power supply bypass |
| LEDs | — | 2 | Controlled output LED + indicator |
| Test Points | — | 17 | Probing at every stage of the signal path |

### Op-Amp Stage Details

**Difference Amplifier (TL082):**
Computes the error signal `e(t) = V_set − V_curr` using matched 10 kΩ resistor pairs for unity differential gain. The TL082's JFET inputs provide high input impedance to avoid loading the sense node.

**Proportional Stage (TL084, Channel A):**
An inverting amplifier whose gain `Kp` is set by the ratio of a feedback resistor to the input resistor, with a PV37W104C01B00 potentiometer (100 kΩ) for real-time Kp adjustment.

**Integral Stage (TL084, Channel B):**
A Miller integrator with a capacitor (1 µF) in the feedback path. The time constant `τ_i = R × C` determines how aggressively past error is accumulated. A 100 kΩ potentiometer tunes Ki.

**Derivative Stage (TL084, Channel C):**
A differentiator with an input capacitor (2.7 nF) and feedback resistor. The time constant `τ_d = R × C` sets the sensitivity to the rate of error change. A 100 kΩ potentiometer tunes Kd. The small capacitance value limits high-frequency noise amplification.

**Summing Inverting Amplifier (TL084, Channel D):**
Combines the P, I, and D outputs through input resistors into a single inverted control signal, which is then fed to the BJT driver.

### Output Driver

A **2N3904 NPN transistor** in emitter-follower (common-collector) configuration provides current gain to drive the LED at 20–40 mA. The emitter-follower topology offers:
- Near-unity voltage gain with high current gain (β ≈ 100–300)
- Low output impedance for stable LED drive
- Voltage-controlled current source (VCCS) behavior through the feedback loop

### Test Points

17 test points are distributed across the signal path for oscilloscope probing at every critical node:

| Test Point | Node | Purpose |
|---|---|---|
| TP_Vset | Setpoint voltage | Verify reference level |
| TP_Vcurr | Sensed LED voltage | Verify feedback signal |
| TP_Error_output | Difference amp output | Verify e(t) computation |
| TP_PID_error_input | PID stage input | Confirm error signal routing |
| TP_P_Vn_amp | P stage output | Verify proportional response |
| TP_I_Vn_amp | I stage output | Verify integrator behavior |
| TP_D_Vn_amp | D stage output | Verify differentiator response |
| TP_Summing_Amp_Vn | Summing amp output | Verify combined PID signal |
| TP_BJT_Base | BJT base | Verify driver input |
| TP_Vcc | Supply rail | Monitor supply stability |
| GND TPs | Ground references | Differential probe anchors |

## PCB Design

- **EDA Tool:** KiCad
- Full schematic capture with hierarchical net labels for signal tracing
- Custom PCB layout with dedicated analog ground plane for noise isolation
- Clean routing to minimize crosstalk between the high-impedance integrator/differentiator stages and the power output stage
- Custom footprint libraries created for the TL082CP, TL084CN, 2N3904, and PV37W104C01B00

## Testing & Validation

The controller is validated using standard bench equipment:

- **Oscilloscope:** Capture transient response (step input) and steady-state waveforms at each test point
- **Function Generator:** Inject controlled step and sinusoidal error signals to characterize P, I, and D responses independently
- **DC Power Supply:** Provide regulated supply voltage

### Performance Metrics

| Metric | Description |
|---|---|
| Rise Time | Time for output to reach 90% of setpoint after a step input |
| Overshoot | Peak deviation above setpoint (indicates D-stage tuning quality) |
| Settling Time | Time to stay within ±2% of setpoint |
| Steady-State Error | Residual offset after settling (should approach zero with I-stage) |
| Stability | Absence of sustained oscillation under varying gain settings |

All measurements are taken directly from hardware — not simulation alone.

## Project Structure

```
Analog-PID-Controller/
├── README.md
├── LICENSE
├── simpleDesignIdea.png              Hand-drawn system block diagram
│
├── AnalogPIDController/              KiCad project files
│   ├── AnalogPIDController.kicad_sch     Full schematic
│   ├── AnalogPIDController.kicad_pcb     PCB layout
│   ├── AnalogPIDController.kicad_pro     Project configuration
│   ├── AnalogPIDController.kicad_prl     Project settings
│   └── AnalogPIDController-backups/      Timestamped design backups
│
└── Additional Footprints/            Custom component libraries
    ├── 2N3904/                       NPN BJT symbol + footprint + 3D model
    ├── PV37W104C01B00/               100 kΩ trimmer potentiometer
    ├── TL082CP/                      Dual JFET op-amp
    └── TL084CN/                      Quad JFET op-amp
```

## Authors

**Devansh Joshi** — [GitHub](https://github.com/DJdaGOAT1)

**Suren Shirani** — [GitHub](https://github.com/ssWaterFlowing)

## License

This project is licensed under the [MIT License](LICENSE).
