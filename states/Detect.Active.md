# Detect.Active

## Purpose  
Perform receiver detection on all unconfigured lanes to determine which ones can form part of a Link.

## Entry Conditions  
- Entered from `Detect.Quiet`

## Behavior  
- Perform receiver detection on all unconfigured lanes that can form one or more Links  
- See PCIe Base Spec 6.0, Section 8.4.5.7

## Exit Conditions

### Case 1: No lanes detect a receiver  
→ Transition to `Detect.Quiet`

### Case 2: All lanes detect a receiver  
→ Transition to `Polling`

### Case 3: Only some lanes detected (Partial detection)
1. Wait 12 ms  
2. Retry detection on unconfigured lanes  
3. Compare to first attempt:

- If results are the same: → `Polling`  
- If different: → `Detect.Quiet`

## Optional Multi-Link Behavior  
If supported, lanes that failed detection in both attempts may be assigned to a new LTSSM.  
Otherwise:
- Transition to Electrical Idle (no EIOS required)  
- Reassociate after the LTSSM returns to Detect

## Spec Reference  
- Base Spec 6.2 2024-01-25, Section 4.2.5.3.2  
- Receiver Detection: Section 8.4.5.7
