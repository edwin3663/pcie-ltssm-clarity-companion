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

### Case 1. Immediate transition to Polling.Compliance
If the `Enter Compliance` bit (bit 4) in the Link Control 2 Register is set to 1b **prior** to entering `Polling.Active`, then:
- Transition to `Polling.Compliance` immediately without sending any TS1 Ordered Sets.

---

### Case 2. Transition to Polling.Configuration
Occurs when:
- At least 1024 TS1 Ordered Sets have been transmitted, and  
- **All lanes that detected a receiver during Detect** receive eight consecutive training sequences (or complements) of one of the following:
  - TS1 with PAD/PAD and Compliance Receive bit = 0b
  - TS1 with PAD/PAD and Loopback bit = 1b
  - TS2 with PAD/PAD

---

### Case 3. Timeout-driven transition to Polling.Configuration
If 24 ms have elapsed, transition to `Polling.Configuration` if both of the following are true:

**(i)** Any lane that detected a receiver during Detect receives eight consecutive TS1 or TS2 sequences satisfying:
- TS1 (PAD/PAD) with Compliance Receive = 0b, or
- TS1 (PAD/PAD) with Loopback = 1b, or
- TS2 (PAD/PAD)

**AND**

**(ii)** A predetermined subset of lanes (implementation-specific, but traditionally equal to all lanes that detected a receiver) must have detected at least one exit from Electrical Idle since entering Polling.Active.

Note: This rule helps prevent a single bad receiver or transmitter from blocking link configuration.

---

### Case 4. Transition to Polling.Compliance (fallback)
If either of the following is true:
- Not all lanes from the predetermined set in (ii) have detected an exit from Electrical Idle.
- Any lane receives eight consecutive TS1s with:
  - Lane and Link = PAD,
  - Compliance Receive = 1b,
  - Loopback = 0b.

This indicates that a passive load may be present or that compliance testing is intended.

---

### Case 5. Transition to Detect
If none of the conditions for transitioning to `Polling.Configuration` or `Polling.Compliance` are met.

---

## Notes
- A port supporting 64.0 GT/s may receive TS1s with `Flit Mode Supported = 1b` and `Supported Link Speeds = 10111b`.
- Devices may still follow older behavior (from PCIe 1.1) of waiting to transmit 1024 TS1s after receiving the **first TS1**, rather than after any TS1 or TS2.

---

## Spec References
- PCI Express Base Spec 6.2  
  - Section 4.2.7.2.1 — Polling.Active
  - Table for TS1/TS2 Ordered Sets
