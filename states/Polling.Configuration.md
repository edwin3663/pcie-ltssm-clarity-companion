# Polling.Configuration

## Purpose

Signal readiness to proceed to the Configuration state. The receiver applies polarity inversion if the differential pair is reversed, as required by the PCIe specification.

---

## Entry Conditions

- Entered from `Polling.Active`

---

## Behavior

- The Receiver performs polarity inversion if it determines that the differential signal pair is reversed, such as when the positive and negative lines (P/N) are swapped due to connector wiring or PCB layout.

- Upon entry to this substate, the `Transmit Margin` field bit in the **Link Control 2 Register** is reset to `000b`.

- If required, the Transmitter's **Polling.Compliance sequence setting** is updated as described in Section: § 4.2.7.2.2

- The Transmitter sends **TS2 Ordered Sets** on all lanes that detected a receiver during the `Detect` state.

  - The `Link` and `Lane` fields must be set to `PAD`.

  - The `Data Rate Identifier` field must advertise all data rates the Port supports between 2.5 GT/s and 32.0 GT/s, including those it does not intend to use.

  - Data rates above 32.0 GT/s must not be advertised in this substate.


---

## Exit Conditions

### Case 1: Device is Ready to Proceed

If both of the following are true:

1. Eight consecutive TS2 Ordered Sets with `Link` and `Lane` set to `PAD` are received on any lane that detected a receiver during `Detect`.

2. At least 16 TS2s have been transmitted after the first TS2 was received.

Next State: `Configuration`

### Case 2: Timeout

If the above condition is not met within **48 ms**.

Next State: `Detect`

