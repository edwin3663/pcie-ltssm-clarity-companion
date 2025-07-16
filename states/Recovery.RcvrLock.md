# Recovery.RcvrLock

## Purpose:
To

## Behavior:

Send TS1s with the `Link` and `Lane` numbers set in `Configuration` on all configured lanes.

If the `directed_speed_change` variable is set to 1b, then the `speed_change` bit of the TS1 Ordered Sets are set to 1.

If any configured lane receives 8 consecutive TS1 Ordered Sets with the speed_change bit set to 1b




## Exit Conditions:



### Case : Timeout

After a 24 ms timeout.

If 8 consecutive TS1 or TS2 Ordered Sets on any configured lane with the same link and lane numbers that are being transmitted and the speed change bit = 1b.

Next State: `Recovery.RcvrCfg`



