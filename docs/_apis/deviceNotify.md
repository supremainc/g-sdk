---
title: "Device Notify API"
toc_label: "Device Notify"
---

The Device Notify API streams notifications that devices push to the server on their own (the __BS2_CMD_NOTIFY_*__ commands), as opposed to responses to requests from the server. It currently delivers QR/barcode scans; other device-initiated notifications can be added in later releases.

## QRScan

```protobuf
message QRScan {
  uint32 deviceID = 1;
  bytes QRData = 2;
}
```
{: #QRScan}

deviceID
: The device that scanned the QR/barcode.

QRData
: The raw scanned QR/barcode data (up to 512 bytes).

To make a device forward its scanned QR/barcode data to the server, enable __bypassData__ in its QR configuration using [Card.SetQRConfig]({{'/api/card/' | relative_url}}#setqrconfig). When __bypassData__ is set, the device sends the scanned data as a QR notification instead of treating it as a card credential.

## SubscribeQRScan
{: #SubscribeQRScan}

Subscribe to the QR/barcode scans of the specified devices. The scans are delivered as a stream of [QRScan](#QRScan).

```protobuf
message SubscribeQRScanRequest {
  int32 queueSize = 1;
  repeated uint32 deviceIDs = 2;
}
```
{: #SubscribeQRScanRequest}

queueSize
: If the queue is full, the gateway will discard the QR scans. So, it should be large enough for receiving concurrent scans.

deviceIDs
: If it is not empty, receive QR scans from the specified devices only.

The way of receiving the streamed scans will vary according to your selected language. Please refer to the quick start guide for details.
