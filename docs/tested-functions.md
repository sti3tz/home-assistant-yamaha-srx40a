# Tested functions

The following functions were manually verified against a Yamaha SR-X40A / True X Bar 40A.

| Function | Result | Interface |
|---|---|---|
| UPnP device description | Working | HTTP :49152 |
| GetVolume | Working | RenderingControl |
| SetVolume | Working | RenderingControl |
| GetMute | Working | RenderingControl |
| SetMute = 1 | Working | RenderingControl |
| SetMute = 0 | Working | RenderingControl |
| Power / Standby | Not yet verified | TBD |
| Input / Source | Not yet verified | TBD |
| Sound modes | Not yet verified | TBD |

## Notes

The currently tested RenderingControl commands use:

- `InstanceID: 0`
- `Channel: Single`
- UPnP port `49152`

The examples intentionally use the placeholder IP address `192.168.1.100` rather than a tester's private network address.
