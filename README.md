# Low-Cost CSA

[中文说明](README.zh-CN.md)

An experimental, low-cost charge-sensitive preamplifier (CSA) and detector bias-tee for radiation-detector pulse experiments.

The board uses a single OPA192, a 1 pF nominal feedback capacitor, approximately 4 MΩ feedback resistance, an onboard charge-injection input, and a 47 Ω damped analog output. It was designed for a single-wire detector connection in which detector bias and signal share one coaxial conductor. The high-voltage supply itself is **not** included.

> **Prototype status:** one assembled board has passed low-voltage power, baseline and onboard charge-injection tests. It has not completed precision calibration, detector counting validation, EMC testing or safety certification.

![Assembly map](docs/images/assembly-map.png)

## Key specifications

| Item | Prototype value |
|---|---:|
| Amplifier | OPA192IDR |
| Supply | +5 V / AGND / -5 V |
| Nominal feedback capacitance | 1 pF C0G |
| Nominal feedback resistance | 4 MΩ |
| Nominal decay constant | approximately 4 µs |
| Theoretical charge gain | approximately 1 V/pC |
| Preliminary measured charge gain | approximately 0.60–0.64 V/pC |
| Analog output | SMA, 47 Ω source damping |
| Test input | SMA, onboard 1 pF charge-injection capacitor |

The measured result is preliminary. It was obtained with a nominal 0–3 V, 1 kHz oscilloscope calibration square wave, an external 5.65 kΩ series resistor and the onboard 1 pF injection capacitor. The observed output peak was approximately 1.8–1.9 V. The calibration output is not a precision source, and the two 1 pF capacitors have wide absolute tolerance, so this result should not be treated as a traceable calibration.

## Signal path

```text
Detector/bias coax
      |
  HV blocking capacitors
      |
  2.2 kΩ + BAV199 input protection
      |
  OPA192 charge-sensitive amplifier
      |
  47 Ω source damping
      |
  ANALOG_OUT (SMA)
```

The test path is:

```text
TEST_IN -> 1 kΩ -> 1 pF C0G -> CSA summing node
                     |
                   1 MΩ
                     |
                    AGND
```

A positive step at `TEST_IN` produces a negative-going output pulse. A square wave therefore produces opposite-polarity pulses on its rising and falling edges.

## Repository contents

- `hardware/Low-Cost-CSA-RevD-EasyEDA-Pro.epro2` — native EasyEDA Pro project containing schematic and four-layer PCB.
- `docs/assembly-checklist.xlsx` — assembly and bring-up checklist.
- `bom/BOM.csv` — per-board BOM with LCSC ordering numbers where available.
- `bom/external-and-dnp.csv` — high-voltage capacitors, connectors, mechanical parts and optional parts.

## EasyEDA Pro import

1. In EasyEDA Pro, choose **File -> Import -> EasyEDA Pro** and select the `.epro2` project.
2. Open the PCB and rebuild copper pours with `Shift+B`.
3. Confirm the planes are assigned as follows:
   - bottom layer: `-5VA`;
   - inner layer 1: `AGND`;
   - inner layer 2: `+5VA`.
4. Run DRC again. The repaired reference project previously passed all 124 configured DRC checks with zero reported violations.
5. Before fabrication, visually inspect every exported copper layer. Do not rely on a green DRC result alone.

No Gerber archive is included in this initial release. Regenerate manufacturing data from the native project after rebuilding the copper pours and checking the actual connector footprints.

## As-built population overrides

The assembled prototype intentionally differs from a few old CAD metadata fields. Use the repository BOM and the following values when reproducing the tested population:

- `R21 = 2.2 kΩ`, not 0 Ω;
- `R24 = 47 Ω`, not 2 kΩ;
- fit `D1 = BAV199-7-F` even if an imported hidden field still says DNP;
- the assembled prototype used 1 nF, 3 kV C0G parts at `C2`, `C3` and `C12`; the `C2`/`C3` value text in the native schematic still shows the earlier 560 pF option.

The circuit topology and PCB connectivity are unchanged by these population overrides. The EasyEDA project is provided because it contains the repaired, manufacturable copper connectivity; the CSV BOM records the tested component choices.

## Basic test procedure

1. Do not connect detector high voltage.
2. Power the board from a current-limited ±5 V supply.
3. Check the output baseline with a high-impedance oscilloscope input.
4. Apply a small positive step to `TEST_IN`; begin below 0.5 V when using a signal generator.
5. Confirm a negative output pulse, stable baseline and no sustained oscillation.
6. Repeat at several amplitudes and fit output peak versus injected charge, using `Q = C_inj × ΔV`.

The oscilloscope square-wave test described above used a larger 3 V step only for the first functional check.

## Safety

This is an experimental laboratory design, not a certified high-voltage product.

- The detector and bias connectors may carry approximately 1 kV when used with the intended external supply.
- The BAV199 and 2.2 kΩ input network are prototype-level protection only. They are not rated to make hot-plugging or detector arcing safe.
- De-energize the high-voltage supply, wait for discharge and verify voltage before touching or reconnecting anything.
- Operate only in a clean, dry, insulated enclosure with inaccessible high-voltage conductors.
- Recheck the footprints of all coaxial and power connectors against the actual purchased parts.

## Known limitations

- The sensitive summing-node trace is longer than ideal and may increase parasitic capacitance and pickup.
- The measured charge gain is lower than the nominal calculation and requires calibration with a known pulse source.
- The current test only proves low-voltage CSA and injection-path operation; detector counting remains to be validated.
- High-voltage blocking capacitors and some connectors must be sourced separately.

## AI disclosure

Circuit review, documentation, connectivity checks and release preparation used AI assistance from OpenAI Codex. Physical assembly, measurements and final experimental decisions were performed by the project owner. AI-generated analysis is not a substitute for independent electrical and high-voltage safety review.

## License

Released under the MIT License. See [LICENSE](LICENSE).
