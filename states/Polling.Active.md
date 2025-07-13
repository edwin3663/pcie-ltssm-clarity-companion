# Polling.Active

## Purpose
Establish Bit Lock and either Symbol Lock (for 8b/10b) or Block Lock (for 128b/130b) by sending Training Sequences (TS1 Ordered Sets).

---

## Entry Conditions
- Entered from `Detect.Active` after receiver detection is complete.
- Transmitter must exit Electrical Idle and begin sending TS1 Ordered Sets.

---

## Behavior

### TS1 Transmission
- The Transmitter sends TS1 Ordered Sets on all lanes that detected a receiver in the `Detect` state.
- TS1 Ordered Sets must have the Link and Lane numbers set to PAD.
- The Data Rate Identifier Symbol must advertise **all supported data rates between 2.5 GT/s and 32.0 GT/s**, including those not intended for use.
- Ports are **not permitted** to advertise speeds greater than 32.0 GT/s in this state.

### Electrical Requirements
- The Transmitter must allow its TX common mode voltage to settle before exiting Electrical Idle.
- Within 192 ns of entry to `Polling.Active`, the Transmitter must begin driving the default voltage level as specified by the Transmit Margin field.
- This voltage level remains until the link transitions to either `Polling.Compliance` or `Recovery.RcvrLock`.

---

## Exit Conditions

### Case 1: Enter Compliance Bit Set Before Entry
If the `Enter Compliance` bit in the Link Control 2 Register is set to 1b **prior** to entering `Polling.Active`, TS1 Ordered Set transmision is skipped.
Next State: `Polling.Compliance`

---

### Case 2: Completed Training Sequences Exchange on All Lanes
If at least 1024 TS1 Ordered Sets have been transmitted, and **All lanes that detected a receiver during Detect** receive eight consecutive training sequences (or their complements) of one of the following:
  - TS1 with PAD/PAD and Compliance Receive bit = 0b
  - TS1 with PAD/PAD and Loopback bit = 1b
  - TS2 with PAD/PAD

Next State: `Polling.Configuration`

---

### Case 3: Timeout with Partial Lane Success
If 24 ms have elapsed, transition to `Polling.Configuration` if both of the following are true:

**(1)** Any lane that detected a receiver during Detect receives eight consecutive training sequences satisfying:
- TS1 (PAD/PAD) with Compliance Receive = 0b, or
- TS1 (PAD/PAD) with Loopback = 1b, or
- TS2 (PAD/PAD)

**AND**

**(2)** A predetermined subset of lanes (implementation-specific, but traditionally equal to all lanes that detected a receiver) must have detected at least one exit from Electrical Idle since entering Polling.Active.

Note: This rule helps prevent a single bad receiver or transmitter from blocking link configuration.

Next State: `Polling.Configuration`

---

### Case 4: Fallback Due to Passive Load or Incomplete Exit
If either of the following is true:
- Not all lanes from the predetermined set in (ii) have detected an exit from Electrical Idle.
- Any lane receives eight consecutive TS1s with:
  - Lane and Link = PAD,
  - Compliance Receive = 1b,
  - Loopback = 0b.

This indicates that a passive load may be present or that compliance testing is intended.

Next State: `Polling.Compliance`

---

### Case 5: No Matching Condition
If none of the conditions for transitioning to `Polling.Configuration` or `Polling.Compliance` are met.

Next state: `Detect`

---

## Notes
- A port supporting 64.0 GT/s may receive TS1s with `Flit Mode Supported = 1b` and `Supported Link Speeds = 10111b`.
- Devices may still follow older behavior (from PCIe 1.1) of waiting to transmit 1024 TS1s after receiving the **first TS1**, rather than after any TS1 or TS2.

---

## Spec References
- PCI Express Base Spec 6.2  
  - Section 4.2.7.2.1 — Polling.Active
  - Table for TS1/TS2 Ordered Sets
