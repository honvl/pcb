# TPS55288 Smart Supply

This example is a digitally controlled CC/CV bench-supply schematic built around `TPS55288`.

It is intended as a more honest replacement for a generic `LTC3780` module:
- power stage: `TPS55288RPMR`
- MCU: `STM32G0B1CBT6`
- telemetry: `INA226AIDGSR`
- 3.3 V logic rail: `AP2112K-3.3TRG1`
- user input: EC11 rotary encoder with push switch
- display: 4-pin header for a common I2C `SSD1306` OLED module
- input options:
  - 3.3V to 28V screw-terminal input for the main power path
  - USB-C PD/PPS input handled directly by the MCU's `UCPD` peripheral

## What the hardware supports

- Software-set output voltage over I2C through the TPS55288 internal feedback path
- Software-set output current limit over I2C
- USB-PD / PPS sink negotiation through the STM32 `UCPD` peripheral
- Independent output voltage and current measurement with INA226
- MCU-visible input sensing:
  - the screw-terminal input is divided down to a GPIO/ADC pin so firmware can detect main-input presence
- A single encoder that firmware can use as:
  - rotate to change the active setpoint
  - press to switch between voltage-set and current-set editing
- A manual source-select input path:
  - a single SPDT switch selects either the screw-terminal input or the USB-C PD/PPS input
  - this removes the expensive ideal-diode priority mux at the cost of automatic failover

## Notes

- This schematic does not include firmware.
- The OLED interface is a simple `GND / 3V3 / SCL / SDA` header instead of a fixed display module footprint.
- The TPS55288 power stage is based on TI's own reference schematic, but simplified for internal-feedback operation.
- The honest advertised input range is:
  - main screw-terminal input: `3.3V to 28V`
  - USB-C PD/PPS input: `5V to 20V`
- The practical continuous current target is about `5A` to `6A`, not the inflated `10A` headline used by many cheap modules.
- For routing constraints, treat the main power rails and switching path as a `6A` design target, not `8A`.
- Low input voltage reduces available output power sharply. The board can start around `3.3V`, but the widest output range and highest current are only realistic from higher input voltage.
- The USB-C input is a real PD/PPS path, but it is still capped by cable/source contracts and the `3A` USB fuse. The main screw-terminal input remains the better choice for heavy continuous loads.
- The USB connector is still the simple 6-pin power-only receptacle because PD/PPS only needs `VBUS`, `GND`, and the `CC` pins in this design.
- The input source is user-selected with a switch. Do not hot-switch sources under heavy load unless the chosen hardware is rated for it.
- `SWIN` is pinned to Dailywell `1MS1T1B1M1QES-5` because it is a cheap JLC-available SPDT toggle. It is only a `28VDC / 5A` part, so it does not fully cover the schematic's `8A` rail target.
- For Quilter, assume a `Top / GND / Signal / Bottom` 4-layer stackup, not `Top / GND / Power / Bottom`.
- Keep `L2` as a solid uninterrupted ground plane.
- Use `L3` for signal escape and small local copper pours only; do not dedicate it as a global power plane.
- Route `VIN_MAIN`, `VIN`, `VOUT_RAW`, and `VOUT` as short outer-layer pours near the power stage instead of trying to bury them in an inner-plane island.
- Keep `SW1` and `SW2` short and wide on the outer layers, but do not pour them.

## Sources

- TI TPS55288 datasheet: <https://www.ti.com/lit/ds/symlink/tps55288.pdf>
- ST STM32G0B1 datasheet: <https://www.st.com/resource/en/datasheet/stm32g0b1cb.pdf>
- TI TPS55288EVM-045 user guide: <https://www.ti.com/lit/pdf/SLVUBO4B>
- TI TPS55288 layout guideline: <https://www.ti.com/lit/an/slvaer0/slvaer0.pdf>
