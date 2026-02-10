---
title: "System API"
toc_label: "System"  
---

You can configure some system-level options using __SystemConfig__.

## Config

```protobuf
message SystemConfig {
  int32 timeZone;
  bool syncTime;
  bool isLocked; 
  bool useInterphone;
  bool OSDPKeyEncrypted;
  bool useJobCode;
  bool useAlphanumericID;
  CameraFrequency cameraFrequency;
  bool useSecureTamper;
  uint32 useCardOperationMask;
  bool adminTwoStepAuth;
}
```
{: #SystemConfig}

timeZone/syncTime
: You can configure these options using [TimeConfig]({{'/api/time/' | relative_url}}#TimeConfig).

isLocked
: If true, it means that the device is locked and does not handle any user input. To unlock a locked device, use [Device.Unlock]({{'/api/device/' | relative_url}}#unlock).

useInterphone
: If the device has an interphone, you can enable/disable it with this flag. 

OSDPKeyEncrypted
: If true, use the secure OSDP key. 

useJobCode
: If true, ask users to enter a job code. See [JobCode]({{'/api/tna/' | relative_url}}#JobCode).

useAlphanumericID
: If true, allow alphanumeric IDs. The [CapabilityInfo.alphanumericIDSupported]({{'/api/device/' | relative_url}}#CapabilityInfo) should be true.

[cameraFrequency](#CameraFrequency) : 

useSecureTamper
: If true, delete sensitive data such as users, logs, keys, and certificates from the device when its tamper switch is on.

useCardOperationMask
: Provides a card selective option not to read all kinds of cards from the device. 
You can select multiple cards using MASK. The user can select or deselect of a specific card reading option using this option. 
However, it can be applied to the card types the device supporting. If you add a card type which isn't supported from the device would be ignored. 
Also, the required card type MASK should be combined with CARD_OPERATION_MASK_USE. 
For example, useCardOperationMask needs to be configured 0x80000001 when EM card is selected only.

| Value | Define | Description |
| --------- | ----------- | ----------- |
| 0xFFFFFFFF | CARD_OPERATION_MASK_DEFAULT | Please define and use it in your codes |
| 0x80000000 | CARD_OPERATION_MASK_USE | Please define and use it in your codes |
| 0x00000800 | CARD_OPERATION_MASK_CUSTOM_DESFIRE_EV1 | |
| 0x00000400 | CARD_OPERATION_MASK_CUSTOM_CLASSIC_PLUS | |
| 0x00000200 | CARD_OPERATION_MASK_BLE | |
| 0x00000100 | CARD_OPERATION_MASK_NFC | |
| 0x00000080 | CARD_OPERATION_MASK_SEOS | |
| 0x00000040 | CARD_OPERATION_MASK_SR_SE | |
| 0x00000020 | CARD_OPERATION_MASK_DESFIRE_EV1 | |
| 0x00000010 | CARD_OPERATION_MASK_CLASSIC_PLUS | |
| 0x00000008 | CARD_OPERATION_MASK_ICLASS | |
| 0x00000004 | CARD_OPERATION_MASK_MIFARE_FELICA | |
| 0x00000002 | CARD_OPERATION_MASK_HIDPROX | |
| 0x00000001 | CARD_OPERATION_MASK_EM | |

adminTwoStepAuth
: [+ V1.9] Sets whether to perform 2-step master admin authentication.
This value cannot be changed on new devices that support master admin due to CE RED (European Directive for Radio Equipment). It can only be changed when upgrading from an existing device that does not support it.
If set to false, only 1-step authentication is performed, and authentication is performed only with the credential information assigned to the master admin, regardless of information such as [AuthConfig]({{'/api/auth/' | relative_url}}#AuthConfig) that affect general user authentication.
If set to true, 2-step authentication will be performed, and authentication may fail if the device is only capable of performing 1-step authentication due to insufficient credentials or other reasons. See [MasterAdmin]({{'/api/masteradmin/' | relative_url}}#MasterAdmin).



```protobuf
enum CameraFrequency {
  FREQ_NONE = 0x00;
  FREQ_50HZ = 1;
  FREQ_60HZ = 2;
}
```
{: #CameraFrequency}


### GetConfig

Get the system configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [SystemConfig](#SystemConfig) | The system configuration of the device |

### SetConfig

Change the system configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [SystemConfig](#SystemConfig) | The system configuration to be written to the device |


### SetConfigMulti

Change the system configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [SystemConfig](#SystemConfig) | The system configuration to be written to the devices |
