# Configuration.Linkwidth.Start

## Purpose
Assign link numbers.

## Behavior

### Case 1: The Link Not Up or Not Initiating Upconfigration of Link Width

Transmit TS1 Ordered Sets on all active lanes. 

If `upconfigure_capable = 1b`, also transmit TS1s on each inactive lane that:  
- Detected an exit from Electrical Idle during `Recovery`, and
- Received two consecutive TS1s with both the `Link` and the `Lane` fields set to `PAD`.

In this case, TS1s must advertise all supported data rates from 2.5 to 32.0 GT/s using the `Data Rate Identifier` symbol. Also:
- Downstream ports must set the `Link` field to the selected link number and the `Lane` field to `PAD` 
- Upstream ports must set both the `Link` and `Lane` field to `PAD` 

#### Crosslink Support (Optional)

If Crosslink is supported, all lanes that detected a receiver during detect must transmit 16-32 TS1 Ordered Sets.

Afterward:
- If any Downstream Lane receives two consecutive TS1 Ordered Sets with the `Link` field set to non-PAD and the `Lane` number set to `PAD`, it is reclassified as an Upstream Lane and a new random Crosslink timeout is chosen.
- If any Upstream Lane receives two consecutive TS1 Ordered Sets with both the `Link` and the `Lane` fields set to `PAD`, it is reclassified as a Downstream Lane and a new random Crosslink timeout is chosen.

**Note:** This mechnaism supports the optional Crosslink where both sides initially operate as both Downstream Ports or both Upstream Ports. Role reesoulution is acheveived by reclassifing ports, based on recieved TS1s, and assigning a random timeout until one side of the Link becomes a Downstream Port and the other side remains an Upstream Port. This timeout must be random, even when connecting identical devices, so as to eventually break any possible deadlock.

Next state: `Configuration.Linkwidth.Start`

### Case 2: Link Is Up and Initiating Upconfiguration of the Link Width

Begin by transmitting TS1s with both the `Link` and `Lane` fields set to `PAD` on:  
- Active lanes  
- Inactive lanes to be added to the link
- Lanes that detected Electrical Idle exit and received two TS1s with `PAD` values

#### Upstream Ports

Once each of the transmitting lanes either:
- Receives two consecutive TS1s with both the `Link` and the `Lane` fields set to `PAD`, or
- after 1 ms
switch to transmitting TS1s with the `Link` field set to the selected link number and `Lane` field set to `PAD`.

#### Downstream Ports

asdfasdf

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
