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

### Register Reset Behavior
These register fields are **reset to 0b**:
- Link Status 2 Register:
  - Equalization 8.0GT/s Phase1 Successful
  - Equalization 8.0GT/s Phase2 Successful
  - Equalization 8.0GT/s Phase3 Successful
  - Equalization 8.0GT/s Complete
  - Equalization 16.0GT/s Phase1 Successful
  - Equalization 16.0GT/s Phase2 Successful
  - Equalization 16.0GT/s Phase3 Successful
  - Equalization 16.0GT/s Complete
  - Equalization 32.0GT/s Phase1 Successful
  - Equalization 32.0GT/s Phase2 Successful
  - Equalization 32.0GT/s Phase3 Successful
  - Equalization 32.0GT/s Complete
  - Equalization 64.0GT/s Phase1 Successful
  - Equalization 64.0GT/s Phase2 Successful
  - Equalization 64.0GT/s Phase3 Successful
  - Equalization 64.0GT/s Complete
- Device Status 3 Register
  - Remote L0p Supported

### Variable Reset Behavior
All the following variables are **reset to 0**:
- `use_modified_TS1_TS2_Ordered_Set`
- `Flit_Mode_Enabled`
- `L0p_capable`
- `SRIS_Mode_Enabled`
- `directed_speed_change`
- `upconfigure_capable`
- `idle_to_rlock_transitioned` (set to 00h)
- `equalization_done_8GT_data_rate`
- `equalization_done_16GT_data_rate`
- `equalization_done_32GT_data_rate`
- `equalization_done_64GT_data_rate`
- `perform_equalization_for_loopback`
- `perform_equalization_for_loopback_64GT`

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

