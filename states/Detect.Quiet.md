# Detect.Quiet

## Purpose
Establish a stable Electrical Idle condition before receiver detection. Ensure that all receivers meet DC specifications and the link operates at 2.5 GT/s.

---

## Entry Conditions
- Entered from Reset or Power on.
- From other states: Polling, Configuration, Recovery, Disable, Loopback, Hot Reset
---

## Behavior

### Electrical and Timing Requirements
- Transmitter remains in Electrical Idle. DC common mode voltage is not required to be within spec.
- If the previous data rate was not 2.5 GT/s, the LTSSM must remain in `Detect.Quiet` for **at least 1 ms**, during which the data rate is changed to 2.5 GT/s.
- Receivers must meet the **ZRX-DC** spec for 2.5 GT/s within **1 ms** of entering this state.
- The LTSSM must not exit `Detect.Quiet` until all receivers meet the ZRX-DC spec.

### Register and Variable Reset Behavior
All the following are **reset to 0b**:
- Equalization phase status bits (Phase1, Phase2, Phase3, Complete) for:
  - 8.0 GT/s
  - 16.0 GT/s
  - 32.0 GT/s
  - 64.0 GT/s
- `use_modified_TS1_TS2_Ordered_Set`
- `Remote_L0p_Supported`
- `Flit_Mode_Enabled`
- `L0p_capable`
- `SRIS_Mode_Enabled`
- `directed_speed_change`
- `upconfigure_capable`
- `idle_to_rlock_transitioned` (set to 00h)
- `equalization_done_*GT_data_rate` for all data rates
- `perform_equalization_for_loopback` and `perform_equalization_for_loopback_64GT`

### Legacy Device Behavior
Older devices that do not implement:
- `directed_speed_change`
- `upconfigure_capable`
- `idle_to_rlock_transitioned`

...should behave as if:
- `directed_speed_change = 0b`
- `upconfigure_capable = 0b`
- `idle_to_rlock_transitioned = FFh`

---

## Exit Conditions

Transition to `Detect.Active`:
- After a **12 ms timeout**, or
- If **Electrical Idle Exit** (EIE) is detected on any lane.

If entering `Detect.Quiet` from `Hot Reset`, the Downstream Port is recommended to wait **2 ms** before enabling EIE detection.

---

## Spec References
- PCI Express Base Specification Rev 6.2  
  - Section 4.2.7.1 — Detect  
  - Section 4.2.7.1.1 — Detect.Quiet  
  - Table 8-12 — ZRX-DC timing
