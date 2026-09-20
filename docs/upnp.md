# UPnP / SOAP notes

## Discovery

The tested Yamaha SR-X40A exposes a UPnP device description at:

```text
http://<SOUNDBAR-IP>:49152/description.xml
```

The device advertises standard UPnP/DLNA services including RenderingControl and AVTransport.

## RenderingControl

Tested control URL:

```text
/upnp/control/rendercontrol1
```

Service type:

```text
urn:schemas-upnp-org:service:RenderingControl:1
```

### Important channel detail

The tested SR-X40A accepts **Single** as the channel for volume and mute operations. Using `Master` did not provide the working behavior used by this project.

## Tested SOAP actions

- `GetVolume`
- `SetVolume`
- `GetMute`
- `SetMute`

All use `InstanceID` value `0`.

## Example: GetVolume

```bash
curl -s 'http://192.168.1.100:49152/upnp/control/rendercontrol1' \
  -H 'Content-Type: text/xml; charset="utf-8"' \
  -H 'SOAPAction: "urn:schemas-upnp-org:service:RenderingControl:1#GetVolume"' \
  -d '<?xml version="1.0" encoding="utf-8"?><s:Envelope s:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" xmlns:s="http://schemas.xmlsoap.org/soap/envelope/"><s:Body><u:GetVolume xmlns:u="urn:schemas-upnp-org:service:RenderingControl:1"><InstanceID>0</InstanceID><Channel>Single</Channel></u:GetVolume></s:Body></s:Envelope>'
```

A successful response contains a `CurrentVolume` element.

## HTTPS API observation

A Linkplay-style HTTPS endpoint was observed during testing, but the TLS server requested a client certificate. This project therefore currently uses the local UPnP/SOAP interface instead.

## Other services

AVTransport is also advertised by the device.

## DeviceManager events

The tested device advertises:

```text
Service:  urn:schemas-linkplay-com:service:DeviceManager:1
Control:  /upnp/control/DeviceManager1
Events:   /upnp/event/DeviceManager1
```

Its `LastChange` state variable is event-enabled. A standard UPnP `SUBSCRIBE`
request produced `NOTIFY` callbacks containing reproducible Standby and On
transitions. See [power-events.md](power-events.md) for the captured mapping.

Input/source selection and sound modes have not yet been verified and are
intentionally not implemented until working commands are confirmed. No guessed
`SetAvsProperty`, power, shutdown, reboot, OTA, or firmware payloads should be
used.
