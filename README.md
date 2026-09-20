# Home Assistant – Yamaha SR-X40A

Local Home Assistant control for the **Yamaha SR-X40A / True X Bar 40A** using its UPnP/SOAP interface.

This project documents a working alternative for devices where integrations relying on `/httpapi.asp` are not compatible with the tested SR-X40A firmware.

## Status

| Function | Status |
|---|---|
| UPnP/SOAP communication | Tested |
| Get volume | Tested |
| Set volume | Tested |
| Get mute state | Tested |
| Mute | Tested |
| Unmute | Tested |
| Power / Standby status | Tested via DeviceManager events |
| Power / Standby control | Under investigation |
| Input / Source | Under investigation |
| Sound modes | Under investigation |

## Tested environment

- Yamaha SR-X40A (True X Bar 40A)
- Home Assistant
- Local network connection
- UPnP RenderingControl v1
- TCP port `49152`

No Yamaha cloud service is required for the currently implemented functions.

## Installation

The quickest option is to use `packages/yamaha_srx40a.yaml`.

1. Give the soundbar a stable IP address.
2. Copy the package into your Home Assistant packages directory.
3. Replace `192.168.1.100` with the IP address of your Yamaha SR-X40A.
4. Make sure Home Assistant packages are enabled.
5. Check the configuration and restart Home Assistant.

Alternatively, copy the relevant sections from `configuration-example.yaml`.

## Home Assistant actions

Set volume to 30%:

```yaml
action: shell_command.yamaha_set_volume
data:
  volume: 30
```

Mute:

```yaml
action: shell_command.yamaha_mute_on
```

Unmute:

```yaml
action: shell_command.yamaha_mute_off
```

## Technical details

The tested soundbar exposes its UPnP device description on port `49152`. Volume and mute use:

```text
/upnp/control/rendercontrol1
```

Service:

```text
urn:schemas-upnp-org:service:RenderingControl:1
```

The SR-X40A tested here uses the RenderingControl channel **`Single`**, not `Master`.

See [docs/upnp.md](docs/upnp.md) for protocol details,
[docs/power-events.md](docs/power-events.md) for the verified power-state event
mapping, and [docs/tested-functions.md](docs/tested-functions.md) for the current
verification status.

## Roadmap

- Implement the verified DeviceManager power-state events in a native integration
- Verify power / standby control without guessing undocumented payloads
- Verify input / source selection
- Verify sound modes
- Develop a native Home Assistant custom integration with a single `media_player` entity

## Contributing

Testing on additional SR-X40A firmware versions is welcome. Do not publish credentials, public IP addresses or other private information in issues.

## Disclaimer

This is an independent community project and is not affiliated with or endorsed by Yamaha Corporation or Home Assistant.

## License

MIT License. See [LICENSE](LICENSE).
