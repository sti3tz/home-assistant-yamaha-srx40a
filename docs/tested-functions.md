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
| Power / Standby status | Working | DeviceManager `LastChange` event |
| Power / Standby control | Not yet verified | No documented action found |
| Input / Source | Not yet verified | TBD |
| Sound modes | Not yet verified | TBD |

## Notes

The currently tested RenderingControl commands use:

- `InstanceID: 0`
- `Channel: Single`
- UPnP port `49152`

The power-state event mapping was reproduced with separate physical Standby and
On transitions on firmware `1.8.010000`:

- On: `power_saving_changed=true`
- Standby: `power_saving_changed=false`

The naming is counterintuitive, so integrations should use the observed mapping
rather than infer the meaning from the property name. See
[power-events.md](power-events.md) for the complete event sequences and callback
requirements.

The examples intentionally use the placeholder IP address `192.168.1.100` rather than a tester's private network address.
