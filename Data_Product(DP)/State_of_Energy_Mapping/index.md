# State of Energy Mapping

* [State of Energy Mapping](state-of-energy-mapping.md) - Mapping from installed state of energy (OEM fleet) to contractual state of energy (the offtaker's 0-to-contracted range): the two scales and buffers, the remap function block (inputs, outputs, knobs incl. the 2×2 accounting gains), the computation owner, and the layered counting/steering/recalibration algorithm.
* [Log](log.md) - Change history.

## Status

* **Template, v0.2 (2026-08-22).** Rebuilt from the first project clone's design review: function-block structure, MWh-native parameters, per-direction accounting defaults (charge = BMS SOE, discharge = POM meter −1:1), layered algorithm with steering and recalibration. Fill from the sizing basis (installed vs contracted energy), the offtake/supply agreements (contracted range, test definitions), and the DIR §3 SOC point mapping. No dedicated skill; scope a project instance through the `data-product` skill. Check first whether the telemetry chain passes raw OEM energy to the offtaker SOC point: that is the common gap this document exists to close.
