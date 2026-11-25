---
title: "Device Notify API"
toc_label: "Device Notify"
---

The Device Notify API streams notifications that devices push to the server on their own (the `BS2_CMD_NOTIFY_*` commands), as opposed to responses to requests from the server. It currently delivers QR/barcode scans and, since 1.10.0, the door and floor control status changes; other device-initiated notifications can be added in later releases.

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

## DoorControlStatus
{: #DoorControlStatus}

__[+ 1.10.0]__

```protobuf
message DoorControlStatus {
  uint32 deviceID = 1;
  door.ControlStatus status = 2;
}
```

deviceID
: The device that reported the change.

[status]({{'/api/door/' | relative_url}}#ControlStatus)
: The door status with the context of the last control.

A device pushes a __DoorControlStatus__ whenever the control context of a door changes: an operator lock, unlock or release with __operatorID__ ([Door.Lock]({{'/api/door/' | relative_url}}#lock), [Unlock]({{'/api/door/' | relative_url}}#unlock), [Release]({{'/api/door/' | relative_url}}#release) or the administrator menu of the device), a fire alarm, fireman card or fire button, the expiry of a timed control and the return to normal. The pushed record is the same as [Door.GetControlStatus]({{'/api/door/' | relative_url}}#getcontrolstatus) returns, so a monitoring client can keep the current state of every door without polling and without replaying the event log. The operator case is pushed only when the control carries an __operatorID__; a [Door.Lock]({{'/api/door/' | relative_url}}#lock) without it changes the door silently. The other causes (fire alarm, fire button, fireman card, a schedule newly locking or unlocking the door, the expiry of a timed control, the return to normal) are pushed by the device on their own; see the table in [Door.GetControlStatus]({{'/api/door/' | relative_url}}#getcontrolstatus). Supported devices report [DeviceCapability.doorUnlockLockImproveSupported]({{'/api/device/' | relative_url}}#DeviceCapability).

## SubscribeDoorControlStatus
{: #SubscribeDoorControlStatus}

Subscribe to the door control status changes of the specified devices. They are delivered as a stream of [DoorControlStatus](#DoorControlStatus).

```protobuf
message SubscribeDoorControlStatusRequest {
  int32 queueSize = 1;
  repeated uint32 deviceIDs = 2;
}
```
{: #SubscribeDoorControlStatusRequest}

queueSize
: If the queue is full, the gateway will discard the notifications. It must be larger than 0.

deviceIDs
: If it is not empty, receive the changes from the specified devices only.

## FloorControlStatus
{: #FloorControlStatus}

__[+ 1.10.0]__

```protobuf
message FloorControlStatus {
  uint32 deviceID = 1;
  lift.FloorControlStatus status = 2;
}
```

deviceID
: The device that reported the change.

[status]({{'/api/lift/' | relative_url}}#FloorControlStatus)
: The status of one floor with the context of its last control.

The lift counterpart of [DoorControlStatus](#DoorControlStatus): pushed for every floor whose control context changes ([Lift.Activate]({{'/api/lift/' | relative_url}}#activate), [Deactivate]({{'/api/lift/' | relative_url}}#deactivate), [Release]({{'/api/lift/' | relative_url}}#release) with __operatorID__, the administrator menu, a fire alarm, a timed control expiry or the return to normal).

## SubscribeFloorControlStatus
{: #SubscribeFloorControlStatus}

Subscribe to the floor control status changes of the specified devices. They are delivered as a stream of [FloorControlStatus](#FloorControlStatus).

```protobuf
message SubscribeFloorControlStatusRequest {
  int32 queueSize = 1;
  repeated uint32 deviceIDs = 2;
}
```
{: #SubscribeFloorControlStatusRequest}

queueSize
: If the queue is full, the gateway will discard the notifications. It must be larger than 0.

deviceIDs
: If it is not empty, receive the changes from the specified devices only.

The way of receiving the streamed notifications will vary according to your selected language. Please refer to the quick start guide for details.
