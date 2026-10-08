# As-built assembly overrides

The native EasyEDA Pro project preserves the repaired PCB connectivity used to manufacture the prototype. A few component values were changed during procurement and assembly without changing the PCB topology.

Use these values for the tested population:

| Reference | As-built value | Note |
|---|---|---|
| R21 | 2.2 kΩ, 0805 | Input limiting/isolation; replaces the early 0 Ω value. |
| R24 | 47 Ω, 0805 | Output source damping; replaces the early 2 kΩ value. |
| D1 | BAV199-7-F, fitted | Low-leakage dual diode. Imported hidden metadata may still say DNP. |
| C2, C3 | 1 nF, 3 kV, C0G | 1812 parts adapted to the original through-hole pads; effective series capacitance is approximately 500 pF. |
| C12 | 1 nF, 3 kV, C0G | Cable-side high-voltage bypass. |

`C5` and `C6` remain DNP. `C4` and `C11` are both fitted as nominal 1 pF C0G parts.
