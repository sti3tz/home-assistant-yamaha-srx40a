# DeviceManager power-state events

This document records a local, non-destructive UPnP event investigation of a
Yamaha SR-X40A / True X Bar 40A running firmware `1.8.010000`.

No power, shutdown, reboot, OTA, firmware, or guessed `SetAvsProperty` command
was sent. Standby and On transitions were triggered physically and observed via
the documented UPnP event mechanism.

## Service

```text
Device description: http://<SOUNDBAR-IP>:49152/description.xml
Service:            urn:schemas-linkplay-com:service:DeviceManager:1
Control URL:        /upnp/control/DeviceManager1
Event URL:          /upnp/event/DeviceManager1
Event variable:     LastChange (sendEvents="yes")
```

## Callback requirement

UPnP subscriptions require an HTTP callback that the soundbar can reach from
the LAN. A listener inside Home Assistant's Terminal & SSH add-on was not
reachable because that add-on published only its SSH port; an arbitrary port
opened inside the add-on container was not exposed on the Home Assistant host.

The successful test used a temporary LAN-reachable HTTP listener that accepted
the UPnP `NOTIFY` method and returned HTTP 200. The soundbar accepted
`SUBSCRIBE`, renewal by SID, and `UNSUBSCRIBE` with HTTP 200 responses.

A permanent Home Assistant implementation should register a narrowly scoped
callback through Home Assistant's existing HTTP server. Because the endpoint
must be unauthenticated for the soundbar, it should validate both a random path
token and the source address of the configured soundbar.

## Verified event mapping

The following sequences were reproduced with separate physical transitions.

### Entering Standby

```text
ADKEvent {"power_saving_changed":"false"}
Mode "normal"
ADKEvent {"txmode_changed":"disabled"}
```

### Powering on to HDMI

```text
ADKEvent {"power_saving_changed":"true"}
Mode "HDMI"
ADKEvent {"txmode_changed":"enabled"}
```

The `power_saving_changed` value is counterintuitive but was unambiguous on the
tested device:

| Event value | Observed physical state |
|---|---|
| `true` | On |
| `false` | Standby |

`Mode` and `txmode_changed` are useful corroborating signals. The
`power_saving_changed` event is the primary confirmed power-state signal.

The soundbar also emitted `A_ARG_TYPE_OTA` events after power-on. They reported
`otaState="idle"`, `otaStatus="checkEnd"`, and `hasNew=0`; these were passive
status events, and no update command was issued.

## Read-only polling comparison

The documented SOAP action `GetDeviceInfo` was queried in both states:

| Physical state | `state` | `mode` |
|---|---|---|
| On (HDMI) | `normal` | `HDMI` |
| Standby | `normal` | `normal` |

The `state` field is not a power indicator. The `mode` field distinguishes the
tested HDMI-on state from Standby, but it should be treated only as a provisional
startup hint until other active input modes have been tested. The event mapping
above is stronger evidence.

## Next implementation step

Build a native Home Assistant custom integration that:

1. exposes a protected LAN callback capable of receiving `NOTIFY`;
2. subscribes to `/upnp/event/DeviceManager1` and renews before timeout;
3. decodes the outer UPnP XML, XML-escaped `LastChange`, and JSON in `ADKEvent`;
4. maps `power_saving_changed=true` to On and `false` to Standby;
5. uses `Mode` and `txmode_changed` as secondary diagnostics;
6. removes the subscription cleanly when the config entry unloads.

Power control remains intentionally unresolved. The published DeviceManager
SCPD has no `SetPower` action and does not document valid `SetAvsProperty`
payloads.
