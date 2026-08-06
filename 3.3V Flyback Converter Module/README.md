## 3.3V Flyback Converter PCB Module | Altium Designer

Designed a 3.3V Flyback Converter PCB Module using Altium Designer with an input of 90 VAC - 275 VAC and operating frequency of 60 Hz to 400 Hz that can be plugged directly into wall outlets.

Components & their functions:

i) Input Stage:
- The 14-gauge Pads are used to solder in wires from a plug connected to the wall outlet.
- FW30A10R0JA 3W fusible resistor breaks and opens the circuit if the current moving through the system is excessive i.e. when the power dissipation in it is 3W.
- Metal oxide varistor RV1 and capacitor C5 100 nF is implemented as additional circuit protection in case of power surges.
- Bridge Rectifier BR1 600V HD06-T converts the AC input 90 VAC - 275 VAC into high D.C voltage. 
- The TV1 TVS Diode and bulk capacitors are used to suppress transients and reduce ripples.

ii) Primary Switcher:
- The UCC28881 controller drives an internal 14 Ω, 700V MOSFET by using a switching waveform to transfer energy through the primary side of the custom transformer. 
- Directly handles the high-voltage rectified DC mains voltage around 120V - 375V, chopping it at a maximum frequency of 62 kHz to feed current into the primary winding of the transformer.
- When the MOSFET turns off, the magnetic energy stored in the transformer core is transferred to the secondary side to feed the 3.3V output stage.
- A snubber network across the MOSFET damps ringing and improves efficiency.

iii) Output and Feedback:
- On the secondary side, the B340A-13-F diode rectifies the flyback pulse. An optocoupler and LMV431 shunt regulator are used to complete an isolated feedback loop which provides input to the Switcher Circuit used to regulate the output voltage. 
- An additional circuitry of RC elements smoothens the 3.3V rail across the LED in the optocoupler which biases the photo transistor on the primary side to complete a portion of the Switcher feedback.
- The J1 terminal block serves as the output where the 3.3V output can be connected to a load by screwing in wires.

iv) Transformer Design:
- A custom transformer of 69:4 turns ratio is used to obtain the desired primary side inductance of 984 μH to get a peak current of 0.44A entering the drain of the Switcher.
- The turns ratio and input bus voltage together produce a peak drain voltage of approx. 235V.

## Transformer Design Calculations

The custom transformer (69:4 turns ratio) was designed to obtain a regulated 3.3 V
output while keeping the primary-side peak current and inductance within a
reasonable range for the UCC28881 controller running at 62 kHz.

### 1. Turns Ratio → Reflected Voltage

The turns ratio sets how much of the secondary-side voltage gets "reflected"
back onto the primary during the flyback interval:

$$
V_{OR} = N \times (V_{OUT} + V_{DIODE})
$$

Where:
- `N` = turns ratio (Np:Ns) = 69:4 ≈ 17.25
- `V_OUT` = 3.3 V (target output)
- `V_DIODE` ≈ 0.5 V (forward drop of the output rectifier, D2)

$$
V_{OR} = 17.25 \times (3.3 + 0.5) \approx 65.6\ V
$$

### 2. Duty Cycle

Duty cycle is set by the ratio of reflected voltage to input bus voltage:

$$
D = \frac{V_{OR}}{V_{OR} + V_{IN}}
$$

With a rectified DC bus (`V_IN`) around ~96 V (based on the 120 VAC input
after rectification and ripple):

$$
D = \frac{65.6}{65.6 + 96} \approx 0.28 
$$

This corresponds to a duty cycle of approximately **28%**.

### 3. Primary Inductance

Primary-side (magnetizing) inductance is derived from the target peak
current and switching frequency:

$$
L_P = \frac{V_{IN} \times D}{I_{PK} \times f_{SW}}
$$

Where:
- `I_PK` = 0.44 A (target peak primary current)
- `f_SW` = 62 kHz (UCC28881 switching frequency)

$$
L_P = \frac{96 \times 0.28}{0.44 \times 62{,}000} \approx 984\ \mu H
$$

### 4. Power Cross-Check

To validate the numbers, input power can be estimated from the
inductor energy-transfer equation:

$$
P_{IN} = \frac{1}{2} L_P \, I_{PK}^2 \, f_{SW}
$$

$$
P_{IN} = 0.5 \times 984\mu H \times (0.44\ A)^2 \times 62\ kHz \approx 5.9\ W
$$

This aligns with the module's target output of 1–2 A at 3.3 V
(3.3–6.6 W) at ~85–90% conversion efficiency.

### Summary

| Parameter                  | Value      |
|-----------------------------|-----------|
| Turns ratio (Np:Ns)         | 69:4      |
| Reflected voltage (V_OR)    | ~65.6 V   |
| Duty cycle (D)               | ~28%      |
| Primary inductance (L_P)    | 984 µH    |
| Peak primary current (I_PK) | 0.44 A    |
| Switching frequency (f_SW)  | 62 kHz    |
