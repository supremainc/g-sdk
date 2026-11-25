---
title: "Door API"
toc_label: "Door"  
---

A door is the basic unit for access control. It consists of a relay and other optional components such as door sensor and exit button. A door can be configured on one or more devices  connected by RS485. 

## Information

```protobuf
message DoorInfo {
  uint32 doorID;
  string name;

  uint32 entryDeviceID;
  uint32 exitDeviceID;

  Relay relay;
  Sensor sensor;
  ExitButton button;

  uint32 autoLockTimeout;
  uint32 heldOpenTimeout;

  bool instantLock;
  uint32 unlockFlags;
  uint32 lockFlags;
  bool unconditionalLock;

  repeated action.Action forcedOpenActions;
  repeated action.Action heldOpenActions;

  uint32 dualAuthScheduleID;
  DualAuthDevice dualAuthDevice;
  DualAuthType dualAuthType;
  uint32 dualAuthTimeout;
  repeated uint32 dualAuthGroupIDs;

  apb_zone.ZoneInfo apbZone;

  uint32 extendedDoorOpenTime;
}
```
{: #DoorInfo }

doorID
: The ID of the door. 

name
: Maximum 48 characters in UTF-8 encoding.

entryDeviceID
: The ID of the entry device. If the door has only one device, you should set it as the entry device.

exitDeviceID
: The ID of the exit device. You have to set it only if the door consists of two devices connected by RS485. 

[relay](#Relay)
: The port information of the relay.

There must be one relay per each door. So, you cannot set 0 to [Relay.deviceID](#Relay).
{: .notice--warning}

[sensor](#Sensor)
: The port information of the door sensor. To use door alarms such as __FORCED_OPEN__ or __HELD_OPEN__, you have to configure the door sensor. If there needs to be no door sensor, just set 0 to [Sensor.deviceID](#Sensor).<br>
__apbUseDoorSensor__ decides whether to use a door sensor when using Global APB.
Should be used with useGlobalAPB in [AuthConfig]({{'/api/auth/' | relative_url }}#AuthConfig).

[button](#ExitButton)
: The port information of the exit button. If there needs to be no exit button, just set 0 to [ExitButton.deviceID](#ExitButton).

autoLockTimeout 
: If it is not 0, the door will be locked automatically after being open for __autoLockTimeout__ seconds. See also [unconditionalLock](#UnconditionalLock).
{: #AutoLockTimeout }

heldOpenTimeout
: If it is not 0, the door will generate the __HELD_OPEN__ alarm after being open for __heldOpenTimeout__ seconds.
{: #HeldOpenTimeout }

instantLock
: If true, the door will be locked immediately when the sensor detects it is closed.

unlockFlags/lockFlags
: A mask of [DoorFlag](#DoorFlag). Just set __NONE__ for V1.0. The other options will be available in the future versions of G-SDK.

unconditionalLock 
: This flag determines how to act after the [autoLockTimeout](#AutoLockTimeout). If it is true, the door will be locked even if the door sensor detects it is open. If it is false, the door will be locked only when the door sensor detects it is closed.
{: #UnconditionalLock }

forcedOpenActions
: When the sensor detects the door is open by force, i.e., not by normal operations, the __FORCED_OPEN__ alarm will be generated. You can configure which actions should be triggered by this alarm. See [Action]({{'/api/action/' | relative_url}}#action) for the available actions.
{: #forcedOpenActions }

heldOpenActions
: When the sensor detects that the door is open longer than the [heldOpenTimeout](#HeldOpenTimeout), the __HELD_OPEN__ alarm will be generated. You can configure which actions should be triggered by this alarm. See [Action]({{'/api/action/' | relative_url}}#action) for the available actions.
{: #heldOpenActions }

dualAuthScheduleID
: If it is not 0, the dual authentication will be enabled for the specified schedule. For the dual authentication, two users should authenticate themselves to access the door. 

[dualAuthDevice](#DualAuthDevice)
: Specify the devices to which the dual authentication will be applied. For example, if it is __DUAL_AUTH_ENTRY_DEVICE_ONLY__, it is applied to the entry device only.

[dualAuthType](#DualAuthType)
: If it is __DUAL_AUTH_LAST__, the second user belonging to [dualAuthGroupIDs](#DualAuthGroupIDs) should authenticate after the first user within __dualAuthTimeout__. If it is __DUAL_AUTH_NONE__, dual authentication is not needed.
{: #DualAuth }

dualAuthTimeout
: The maximum interval in seconds between the two authentications. That is, the second user should authenticate within this timeout after the first user's authentication.

dualAuthGroupIDs
: See [dualAuthType](#DualAuth). Maximum 16 access groups can be set as dual authentication groups.

apbZone
: Configures [Anti Passback]({{'api/zone/apb/' | relative_url}}) on the door. The Anti Passback zone ID and door ID is equivalent.

extendedDoorOpenTime
: [+ 1.9.0] Set the extended door opening time in seconds. The default is 10 seconds.


```protobuf
message Relay {
  uint32 deviceID;
  uint32 port;
}
```
{: #Relay }

```protobuf
message Sensor {
  uint32 deviceID;
  uint32 port;
  device.SwitchType type;
  bool apbUseDoorSensor = 4;
}
```
{: #Sensor }

[type]({{'/api/device/' | relative_url}}#SwitchType)
: The type of the sensor.


```protobuf
message ExitButton {
  uint32 deviceID;
  uint32 port;
  device.SwitchType type;
}
```
{: #ExitButton }

[type]({{'/api/device/' | relative_url}}#SwitchType)
: The type of the button.

```protobuf
enum DualAuthDevice {
  DUAL_AUTH_NO_DEVICE	= 0x00;
  DUAL_AUTH_ENTRY_DEVICE_ONLY	= 0x01;
  DUAL_AUTH_EXIT_DEVICE_ONLY = 0x02;
  DUAL_AUTH_BOTH_DEVICE	= 0x03;
};
```
{: #DualAuthDevice }

```protobuf
enum DualAuthType {
  DUAL_AUTH_NONE = 0x00;
  DUAL_AUTH_LAST = 0x01;
};
```
{: #DualAuthType }

DUAL_AUTH_NONE
: Do not check the access groups of the two users.

DUAL_AUTH_LAST
: The second user should belong to the dual authentication groups.

### GetList

Get the doors configured on a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| doors | [DoorInfo[]](#DoorInfo) | The doors configured on the device |

### GetStatus

Get the status of doors configured on a device.

```protobuf
message Status {
  uint32 doorID;
  bool isOpen;
  bool isUnlocked;
  bool heldOpen;
  uint32 unlockFlags;
  uint32 lockFlags;
  uint32 alarmFlags;
  uint32 lastOpenTime;
}
```
{: #Status }

doorID
: The ID of the door.

isOpen
: True if the door sensor detects it is open.

isUnlocked
: True if the door is unlocked.

heldOpen
: True if the door sensor detects it is open longer than the [heldOpenTimeout](#HeldOpenTimeout). 

unlockFlags
: Indicate how the door is unlocked. See [DoorFlag](#DoorFlag) for available values.

lockFlags
: Indicate how the door is locked. See [DoorFlag](#DoorFlag) for available values.

alarmFlags
: Indicate the alarm status of the door. See [AlarmFlag](#AlarmFlag) for available values.

lastOpenTime
: The last time when the door was open. In Unix time format.

```protobuf
enum DoorFlag {
  NONE = 0x00; 
  SCHEDULED = 0x01;
  EMERGENCY = 0x02;
  OPERATOR = 0x04;
}
```
{: #DoorFlag }

Each door flag has a different priority. The order of priorities are EMERGENCY > OPERATOR > SCHEDULED > NONE.

NONE
: The door is locked/unlocked by normal operations. 

SCHEDULED
: The door is locked/unlocked by Scheduled Lock/Unlock zones. Zones will be supported in the future versions of G-SDK.

EMERGENCY
: The door is locked/unlocked by Fire Alarm Zone or Intrusion Alarm Zone. Zones will be supported in the future versions of G-SDK.

OPERATOR
: The door is locked/unlocked by operators. 

```protobuf
enum AlarmFlag {
  NO_ALARM = 0x00;
  FORCED_OPEN = 0x01;
  HELD_OPEN = 0x02;
  APB_VIOLATION = 0x04;
  LOCK_OVERRIDE = 0x08;
  FIRE = 0x10;
}
```
{: #AlarmFlag }

__alarmFlags__ is a bit mask. Multiple alarms can be active on a door at the same time.

NO_ALARM
: No alarm is active on the door.

FORCED_OPEN
: The door was opened by force, i.e., not by normal operations. The door sensor has to be configured to detect this alarm. See [forcedOpenActions](#forcedOpenActions).

HELD_OPEN
: The door has been kept open longer than [heldOpenTimeout](#HeldOpenTimeout). The door sensor has to be configured to detect this alarm. See [heldOpenActions](#heldOpenActions).

APB_VIOLATION
: An anti-passback violation occurred at the door.

LOCK_OVERRIDE
: The door is under a lock override state set by a lock override credential. See [Lock Override]({{'/api/card/' | relative_url}}#LockOverride).

FIRE
: A fire alarm is active on the door, e.g., triggered by a [Fire Alarm Zone]({{'/api/zone/fire' | relative_url}}).

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| status | [Status[]](#Status) | The status of the doors configured on the device |  

### GetControlStatus

[+ 1.10.0] Get the status of doors together with the context of the last control: who changed it (__operatorID__), when (__actionTime__), why (__origin__), from where (__byServer__) and for how long (__normalizeTimer__). [Status](#Status) alone tells that a door is locked by an operator; this tells which operator, at what time and until when, so that the current state of a door can be explained without searching the event log.

__The control status is not about operators only.__ It is the record of the *last control* of the door, whatever performed it, and the device fills it from several places. What a G-SDK client can produce itself is the operator case; the other cases happen inside the device, and the same [GetControlStatus](#getcontrolstatus) and [DeviceNotify.SubscribeDoorControlStatus]({{'/api/deviceNotify/' | relative_url}}#SubscribeDoorControlStatus) report them.

| origin bit | Filled by | operatorID | byServer |
| ---------- | --------- | ---------- | -------- |
| CONTROL_ORIGIN_OPERATOR | [Lock](#lock), [Unlock](#unlock) or [Release](#release) with __operatorID__, the administrator menu of the device, a double-badge toggle on the device | the operator | true from the gateway, false from the device |
| CONTROL_ORIGIN_FIRE_ALARM | the fire alarm input of a [Fire Alarm Zone]({{'/api/zone/fire' | relative_url}}); __fireAlarmZoneID__ is set | kept as it was | false, unless an operator context remains |
| CONTROL_ORIGIN_FIRE_BUTTON | the fire button | kept as it was | false, unless an operator context remains |
| CONTROL_ORIGIN_FIREMAN_CARD | a fireman card authentication | the card holder's user ID | false, unless an operator context remains |
| (no change) | a schedule newly locks or unlocks the door: the current context is pushed once, nothing is changed | kept as it was | kept as it was |
| CONTROL_ORIGIN_NONE | [Release](#release) that returns the door to normal, or the expiry of a timed control (the device logs __BS2_EVENT_DOOR_NORMALIZED_EXPIRED__): the context is cleared | empty | false |

The fire origins are added as bits, so an operator timed unlock and a fire alarm can be present together; when only the emergency is released, its bit and __fireAlarmZoneID__ are removed and the operator context stays.

__The operator case needs an operatorID.__ A [Lock](#lock), [Unlock](#unlock) or [Release](#release) without __operatorID__ changes the door exactly as before, but the device records nothing and pushes nothing: __origin__ stays __CONTROL_ORIGIN_NONE__. The __doorFlag__ of the request does not take part in this decision. It is the lock-priority flag that [Lock](#lock) has always had (__OPERATOR__ outranks __SCHEDULED__, __EMERGENCY__ outranks both); the context is recorded for any __doorFlag__ as long as __operatorID__ is given, and __OPERATOR__ is simply the usual flag for a manual control from the gateway.

Supported devices report [DeviceCapability.doorUnlockLockImproveSupported]({{'/api/device/' | relative_url}}#DeviceCapability); other devices return the status with an empty context.

```protobuf
message ControlStatus {
  Status status;
  uint32 origin;
  bool byServer;
  string operatorID;
  uint32 actionTime;
  uint32 normalizeTimer;
  uint32 fireAlarmZoneID;
}
```
{: #ControlStatus }

[status](#Status)
: The current status of the door.

[origin](#ControlOrigin)
: What changed the door state last. A bit mask of [ControlOrigin](#ControlOrigin): an operator timed unlock and a fire alarm can be set at the same time.

byServer
: True if the control came from the gateway (G-SDK or BioStar X), false if it came from the device itself, e.g. the administrator menu.

operatorID
: The user ID of the operator who controlled the door last, or empty if none.

actionTime
: The time of the last control. In Unix time format.

normalizeTimer
: The __normalizeTimer__ of the operator lock/unlock in seconds, 0 if it was not timed. Together with __actionTime__ it tells when the door returns to normal.

fireAlarmZoneID
: The fire alarm zone that changed the door when __origin__ has __CONTROL_ORIGIN_FIRE_ALARM__, 0 otherwise.

```protobuf
enum ControlOrigin {
  CONTROL_ORIGIN_NONE = 0x00;
  CONTROL_ORIGIN_OPERATOR = 0x01;
  CONTROL_ORIGIN_FIRE_ALARM = 0x02;
  CONTROL_ORIGIN_FIREMAN_CARD = 0x04;
  CONTROL_ORIGIN_FIRE_BUTTON = 0x10;
}
```
{: #ControlOrigin }

CONTROL_ORIGIN_NONE
: No control context: the door is in its normal state, or it was controlled without an __operatorID__.

CONTROL_ORIGIN_OPERATOR
: Locked, unlocked or released by an operator through [Lock](#lock), [Unlock](#unlock) or [Release](#release) with __operatorID__, or through the administrator menu of the device.

CONTROL_ORIGIN_FIRE_ALARM
: Changed by a [Fire Alarm Zone]({{'/api/zone/fire' | relative_url}}). __fireAlarmZoneID__ is valid.

CONTROL_ORIGIN_FIREMAN_CARD
: Changed by a fireman card. __operatorID__ becomes the user ID of the card holder.

CONTROL_ORIGIN_FIRE_BUTTON
: Changed by the fire button.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| doorIDs | uint32[] | The IDs of the doors. If empty, every door configured on the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| status | [ControlStatus[]](#ControlStatus) | The control status of the doors |

## Management

### Add

Add doors to a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| doors | [DoorInfo[]](#DoorInfo) | The doors to be configured on the device |

### Delete

Delete doors from a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| doorIDs | uint32[] | The IDs of doors to be deleted from the device |


### DeleteAll

Delete all doors configured on a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

## Lock/Unlock

[+ 1.10.0] __operatorID__: name the operator who issues the control. The device then records the control context, which [GetControlStatus](#getcontrolstatus) returns and [DeviceNotify.SubscribeDoorControlStatus]({{'/api/deviceNotify/' | relative_url}}#SubscribeDoorControlStatus) pushes, and logs __BS2_EVENT_DOOR_OPERATOR_ID__ (0x5D10) with the operator as __userID__. Without __operatorID__ the door is controlled as before, but no context is kept. 1 to 32 bytes in UTF-8; a longer value is rejected as an invalid argument. __operatorID__ is independent of __doorFlag__: the flag only sets the lock priority, and the context is recorded with any flag. The __normalizeTimer__ still requires __OPERATOR__.

You can manually lock, unlock, or release doors.  
[+ 1.9.0] Door mode override support.

### Lock

Lock doors. If the __doorFlag__ has lower priority than [DoorStatus.unlockFlags](#Status), the operation will fail.  
[+ 1.9.0] Door mode override support.
| Version | Description |
| :-----: | :---------- |
| ~ 1.8.1 | The Lock command persists until the next Unlock or Release command. (The administrator may forget to Unlock or Release.) |
| 1.9.0 ~ | The Lock command can now be timed, and the door automatically returns to normal after the set time. <br>You can use the time setting in normalizeTimer, and it only works ___when doorFlag is set to OPERATOR___. |

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| doorIDs | uint32[] | The IDs of the doors to be locked |
| doorFlag | uint32 | One of [DoorFlag](#DoorFlag) |
| normalizeTimer | uint32 | [+ 1.9.0] Wait time until transition to normalize state. Can be set in seconds from 1 to 86400(24h), 0 is infinite. |
| operatorID | string | [+ 1.10.0] The user ID of the operator (1 ~ 32 bytes). Empty: no control context is kept |

### Unlock

Unlock doors. If the __doorFlag__ has lower priority than [DoorStatus.lockFlags](#Status), the operation will fail.  
[+ 1.9.0] Door mode override support.
| Version | Description |
| :-----: | :---------- |
| ~ 1.8.1 | The Unlock command persists until the next Lock or Release command. (The administrator may forget to Lock or Release.) |
| 1.9.0 ~ | The Unlock command can now be timed, and the door automatically returns to normal after the set time. <br>You can use the time setting in normalizeTimer, and it only works ___when doorFlag is set to OPERATOR___. |

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| doorIDs | uint32[] | The IDs of the doors to be unlocked |
| doorFlag | uint32 | One of [DoorFlag](#DoorFlag) |
| normalizeTimer | uint32 | [+ 1.9.0] Wait time until transition to normalize state. Can be set in seconds from 1 to 86400(24h), 0 is infinite. |
| operatorID | string | [+ 1.10.0] The user ID of the operator (1 ~ 32 bytes). Empty: no control context is kept |

### Release

Reset the door status. If the __doorFlag__ has lower priority than [DoorStatus.lockFlags/unlockFlags](#Status), the operation will fail.

| Request |
| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| doorIDss | uint32[] | The IDs of the doors to be reset |
| doorFlag | uint32 | One of [DoorFlag](#DoorFlag) |
| operatorID | string | [+ 1.10.0] The user ID of the operator (1 ~ 32 bytes). Logged in __BS2_EVENT_DOOR_OPERATOR_ID__; the control context is cleared when the door returns to normal |

### SetAlarm

Change the [Status.alarmFlags](#Status) of doors.

| Request |
| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| doorIDss | uint32[] | The IDs of the doors whose __alarmFlags__ will be changed |
| alarmFlag | uint32 | A mask of [AlarmFlag](#AlarmFlag) |


