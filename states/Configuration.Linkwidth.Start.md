# Configuration.Linkwidth.Start

## Purpose
Assign link numbers.

## Behavior

### Case 1: The Link Is Not Up or Not Initiating Upconfigration of Link Width

#### Downstream Ports:

Transmit TS1 Ordered Sets on all active Downstream Lanes. 

If `upconfigure_capable = 1b`, transmit TS1s on any inactive lane that:  
– Detected an exit from Electrical Idle during `Recovery`,
– Received two consecutive TS1s with both the `Link` and the `Lane` fields set to `PAD`.

The TS1s must:
- Set the `Link` field to the selected link number and the `Lane` field to `PAD` 
- Advertise all supported data rates from 2.5 to 32.0 GT/s using the `Data Rate Identifier` symbol

#### Upstream Ports:

Transmit TS1 Ordered Sets on all active Upstream Lanes.

If `upconfigure_capable = 1b`, transmit TS1s on any inactive lane that:  
– Detected an exit from Electrical Idle during `Recovery`,
– Received two consecutive TS1s with both the `Link` and the `Lane` fields set to `PAD`.

The TS1s must:
- Set both the `Link` and `Lane` field to `PAD` 
- Advertise all supported data rates from 2.5 to 32.0 GT/s using the `Data Rate Identifier` symbol


### Case 2: Link Is Up and Initiating Upconfiguration of the Link Width

Begin by transmitting TS1s with both the `Link` and `Lane` fields set to `PAD` on:  
– Active lanes  
- Inactive lanes to be added to the link
- Lanes that detected Electrical Idle exit and received two TS1s with `PAD` values

Once each of the transmitting lanes either:
- Receives two consecutive TS1s with both the `Link` and the `Lane` fields set to `PAD`, or
- after 1 ms
switch to transmitting TS1s with the `Link` field set to the selected link number and `Lane` field set to `PAD`.

\* **Note on active lanes:**  
Lanes are considered active based on how the state was entered:  
- From `Polling`: lanes that detected a receiver during `Detect`  
- From `Recovery`: lanes that were part of the last configured link during `Configuration.Complete`


## Exit Conditions

### Case 1: Directed to Disable

#### Downstream Ports
If a higher layer directs the port assert the `Disable Link` bit in TS1/TS2 on all lanes that detected a receiver during `Detect`.

#### Upstream Ports
If a higher layer directs the port assert the `Disable Link` bit in TS1/TS2 on all lanes that detected a receiver during `Detect`.


Next State: `Disabled`

### Case 2: Directed to Loopback

**Downstream Ports**
If a higher layer directs the port to assert the `Loopback` bit in TS1/TS2 on all lanes that detected a receiver during `Detect` and the Transmitter is capable of acting as a Loopback Lead.

**Upstream Ports**
If a higher layer directs the port to assert the `Loopback` bit in TS1/TS2 on all lanes that detected a receiver during `Detect`.

Next State: `Loopback`

### Case 3: Negotiated Loopback
If either of the following is true:
- All transmitting lanes receive two consecutive TS1s with the `Loopback` bit set.
- Any transmitting lane receives two consecutive TS1s with the `Loopback` bit set **and** `Enhanced Link Behavior Control` set to `01b`.

Next State: `Loopback`

### Case 4: Transition to Linkwidth.Accept
If a lane first receives one or more TS1s with `Link` and `Lane` fields set to `PAD`, and then receives two consecutive TS1s with a **non-PAD Link number that matches one of the transmitted values**, and the `Lane` field set to `PAD`.

Next State: `Configuration.Linkwidth.Accept`

### Case 5: Optional Crosslink Negotiation
If `LinkUp = 0b`, crosslink is supported, and:
1. All downstream lanes that detected a receiver transmit 16–32 TS1s with a non-PAD Link number and `Lane = PAD`, and
2. Any downstream lane receives two consecutive TS1s with a **non-PAD Link number** (different from PAD) and `Lane = PAD`.

Next State: `Configuration.Linkwidth.Start (as Upstream)`

### Case 6: Timeout
If none of the above conditions are met within 24 ms.

Next State: `Detect`

---

### Specification References

- PCI Express Base Specification Rev 6.2  
  - 4.2.7.3.1 Configuration.Linkwidth.Start
