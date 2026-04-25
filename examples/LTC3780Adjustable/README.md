# LTC3780 Adjustable Buck Example

This example adapts the official LTC3780 reference design to a buck-only use case:

- `VIN = 5V`
- `VOUT ≈ 3.3V adjustable`
- `IOUT target = 10A`

## Reference Sources

- LTC3780 datasheet: <https://www.analog.com/media/en/technical-documentation/data-sheets/ltc3780.pdf>
- DC686A evaluation board: <https://www.analog.com/en/resources/evaluation-hardware-and-software/evaluation-boards-kits/dc686a.html>
- DC686A schematic: <https://www.analog.com/media/en/technical-documentation/eval-board-schematic/686asch.pdf>

## Output Adjust Range

The feedback divider is:

- `RFB_BOTTOM = 10k`
- `RFB_FIXED = 26.1k`
- `RV1 = 0k..10k` wired as a rheostat

Using `VOUT = 0.8V * (1 + RTOP / RBOTTOM)`:

- minimum `VOUT ≈ 2.89V`
- maximum `VOUT ≈ 3.69V`
- nominal near mid-trim `VOUT ≈ 3.29V`

## Notes

- `FCB` is tied low for forced-continuous operation.
- `PLLFLTR` is tied low and `PLLIN` is left open, so the converter runs at the minimum internal frequency of about `200kHz`.
- `EXTVCC` is left open because this design stays below the `5.7V` switchover threshold.
- The power stage now uses concrete, JLC-checkable parts and footprints: `NTMFS5C670NLT1G` MOSFETs in KiCad's `ONSemi_SO-8FL_488AA`, `XAL7070-472MEC` on a local KiCad footprint generated from ComponentSearchEngine data, and a fully specified ceramic input/output capacitor bank built around `GRM32ER71A476KE15L`.
- The compensation network and local decoupling caps are also tied to real Murata and Panasonic parts, including `GRM1555C1H222GA01D` for `CITH` and `PA2512FKF7W0R01E` for `RSENSE`.
- `COUT4` was changed from a polymer placeholder to a fourth `47uF` MLCC so the example stays within exact, library-backed footprints.
- PCBparts/JLC results materially changed selection: `LTC3780EG#PBF`, `PA2512FKF7W0R01E`, `B340A-13-F`, `TC33X-2-103E`, and the Murata caps are stocked; the original `Si7884DP-T1-GE3` was not found in JLC and the original `XAL7030-472MEC` was only rated `9A`, so both were replaced.
- Compensation, thermal margin, inductor saturation, capacitor bias derating, and transient response still need validation on hardware.
