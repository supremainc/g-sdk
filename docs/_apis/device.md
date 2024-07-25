---
title: "Device API"
toc_label: "Device"  
---

The APIs can be classified into three categories.

[Information APIs](#information)
: Provide the version and capability information of the device.

[Management APIs](#management)
: Provide functions for locking/unlocking/rebooting the device and upgrading its firmware.

[Reset APIs](#reset)
: Reset the configurations or user database of the device.

## Information

### GetInfo

Get the version information of a device.

```protobuf
message FactoryInfo {
  string MACAddr;
  string modelName;
  string firmwareVersion;
  string kernelVersion;
  string BSCoreVersion;
  string boardVersion;
}
```
{: #FactoryInfo }

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| info | [FactoryInfo](#FactoryInfo) | Version information of the device |

### GetCapabilityInfo

Each device type has its own functionalities. For example, WLAN is supported by BioStation A2, but not by BioStation L2. 

```protobuf
message CapabilityInfo {
  Type type;

  uint32 maxNumOfUser;

  bool PINSupported;
  bool cardSupported;
  bool card1xSupported;
  bool SEOSSupported;
  bool fingerSupported;
  bool faceSupported;

  bool userNameSupported;
  bool userPhotoSupported;
  bool userPhraseSupported;
  bool alphanumericIDSupported;

  bool WLANSupported;
  bool imageLogSupported;
  bool VOIPSupported;	

  bool TNASupported;
  bool jobCodeSupported;

  bool wiegandSupported;
  bool wiegandMultiSupported;
  bool triggerActionSupported;
  bool DSTSupported;
  bool DNSSupported;	

  bool OSDPKeySupported;
  bool RS485ExtSupported;

  bool QRSupported;
}
```
{: #CapabilityInfo }

```protobuf
enum Type {
  BIOENTRY_PLUS	= 0x01;
  BIOENTRY_W = 0x02;
  BIOLITE_NET = 0x03;
  XPASS	= 0x04;
  XPASS_S2 = 0x05;
  ENTRY_MAX = 0x05;
  SECURE_IO_2 = 0x06;
  DOOR_MODULE_20 = 0x07;
  BIOSTATION_2 = 0x08;
  BIOSTATION_A2	= 0x09;
  FACESTATION_2	= 0x0A;
  IO_DEVICE = 0x0B;
  BIOSTATION_L2	= 0x0C;
  BIOENTRY_W2 = 0x0D;
  RS485_SLAVE = 0x80;
  CORESTATION_40 = 0x0E;
  OUTPUT_MODULE = 0x0F;
  INPUT_MODULE = 0x10;
  BIOENTRY_P2 = 0x11;
  BIOLITE_N2 = 0x12;
  XPASS2 = 0x13;
  XPASS_S3 = 0x14;
  BIOENTRY_R2 = 0x15;
  XPASS_D2 = 0x16;
  DOOR_MODULE_21 = 0x17;
  XPASS_D2_KEYPAD = 0x18;
  FACELITE = 0x19;
  XPASS2_KEYPAD	= 0x1A;
  XPASS_D2_REV = 0x1B;
  XPASS_D2_KEYPAD_REV = 0x1C;
  FACESTATION_F2_FP = 0x1D;
  FACESTATION_F2 = 0x1E;
  XSTATION_2_QR = 0x1F;
  XSTATION_2 = 0x20;
  IM_120 = 0x21;
  XSTATION_2_FP = 0x22;
  BIOSTATION_3 = 0x23;
  BIOSTATION_2A = 0x26;
  BIOENTRY_W3 = 0x2A;
}
```
{: #Type }

```protobuf
enum SwitchType {
  NORMALLY_OPEN = 0x00;
  NORMALLY_CLOSED = 0x01;
}
```
{: #SwitchType }
NORMALLY_OPEN
: The switch is open in normal time.

NORMALLY_CLOSED
: The switch is closed in normal time.

```protobuf
enum LEDColor {
  LED_COLOR_OFF = 0x00;
  LED_COLOR_RED = 0x01;
  LED_COLOR_YELLOW = 0x02;
  LED_COLOR_GREEN = 0x03;
  LED_COLOR_CYAN = 0x04;
  LED_COLOR_BLUE = 0x05;
  LED_COLOR_MAGENTA = 0x06;
  LED_COLOR_WHITE = 0x07;
}
```
{: #LEDColor }

```protobuf
enum BuzzerTone {
  BUZZER_TONE_OFF = 0x00;
  BUZZER_TONE_LOW = 0x01;
  BUZZER_TONE_MIDDLE = 0x02;
  BUZZER_TONE_HIGH = 0x03;
}
```
{: #BuzzerTone }


| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| info | [CapabilityInfo](#CapabilityInfo) | The capability information of the device type |


### GetCapability

Each device type has its own functionalities. For example, "Extended Auth" is supported by BioStation 3 and FaceStation F2, but not by BioStation 2 and FaceStation 2. 

```protobuf
message DeviceCapability {
	uint32 maxUsers;
	uint32 maxEventLogs;
	uint32 maxImageLogs;
	uint32 maxBlacklists;
	uint32 maxOperators;
	uint32 maxCards;
	uint32 maxFaces;
	uint32 maxFingerprints;
	uint32 maxUserNames;
	uint32 maxUserImages;
	uint32 maxUserJobs;
	uint32 maxUserPhrases;
	uint32 maxCardsPerUser;
	uint32 maxFacesPerUser;
	uint32 maxFingerprintsPerUser;
	uint32 maxInputPorts;
	uint32 maxOutputPorts;
	uint32 maxRelays;
	uint32 maxRS485Channels;
	
	bool cameraSupported;
	bool tamperSupported;
	bool wlanSupported;
	bool displaySupported;
	bool thermalSupported;
	bool maskSupported;
	bool faceExSupported;
	bool voipExSupported;
	
	bool EMCardSupported;
	bool HIDProxCardSupported;
	bool MifareFelicaCardSupported;
	bool iClassCardSupported;
	bool ClassicPlusCardSupported;
	bool DesFireEV1CardSupported;
	bool SRSECardSupported;
	bool SEOSCardSupported;
	bool NFCSupported;
	bool BLESupported;
	bool CustomClassicPlusSupported;
	bool CustomDesFireEV1Supported;
	bool TOM_NFCSupported;
	bool TOM_BLESupported;
	bool CustomFelicaSupported;
	bool useCardOperation;

	bool extendedAuthSupported;
	
	bool cardInputSupported;
	bool fingerprintInputSupported;
	bool faceInputSupported;
	bool idInputSupported;
	bool PINInputSupported;
	
	bool biometricOnlySupported;
	bool biometricPINSupported;

	bool cardOnlySupported;
	bool cardBiometricSupported;
	bool cardPINSupported;
	bool cardBiometricOrPINSupported;
	bool cardBiometricPINSupported;
	
	bool idBiometricSupported;
	bool idPINSupported;
	bool idBiometricOrPINSupported;
	bool idBiometricPINSupported;
	
	bool extendedFaceOnlySupported;
	bool extendedFaceFingerprintSupported;
	bool extendedFacePINSupported;
	bool extendedFaceFingerprintOrPINSupported;
	bool extendedFaceFingerprintPINSupported;
	
	bool extendedFingerprintOnlySupported;
	bool extendedFingerprintFaceSupported;
	bool extendedFingerprintPINSupported;
	bool extendedFingerprintFaceOrPINSupported;
	bool extendedFingerprintFacePINSupported;
	
	bool extendedCardOnlySupported;
	bool extendedCardFaceSupported;
	bool extendedCardFingerprintSupported;
	bool extendedCardPINSupported;
	bool extendedCardFaceOrFingerprintSupported;
	bool extendedCardFaceOrPINSupported;
	bool extendedCardFingerprintOrPINSupported;
	bool extendedCardFaceOrFingerprintOrPINSupported;
	bool extendedCardFaceFingerprintSupported;
	bool extendedCardFacePINSupported;
	bool extendedCardFingerprintFaceSupported;
	bool extendedCardFingerprintPINSupported;
	bool extendedCardFaceOrFingerprintPINSupported;
	bool extendedCardFaceFingerprintOrPINSupported;
	bool extendedCardFingerprintFaceOrPINSupported;

	bool extendedIdFaceSupported;
	bool extendedIdFingerprintSupported;
	bool extendedIdPINSupported;
	bool extendedIdFaceOrFingerprintSupported;
	bool extendedIdFaceOrPINSupported;
	bool extendedIdFingerprintOrPINSupported;
	bool extendedIdFaceOrFingerprintOrPINSupported;
	bool extendedIdFaceFingerprintSupported;
	bool extendedIdFacePINSupported;
	bool extendedIdFingerprintFaceSupported;
	bool extendedIdFingerprintPINSupported;
	bool extendedIdFaceOrFingerprintPINSupported;
	bool extendedIdFaceFingerprintOrPINSupported;
	bool extendedIdFingerprintFaceOrPINSupported;

	bool intelligentPDSupported;
	bool updateUserSupported;
	bool simulatedUnlockSupported;
	bool smartCardByteOrderSupported;
	bool qrAsCSNSupported;
	bool rtspSupported;
	bool lfdSupported;
	bool visualQRSupported;

	uint32 maxVoipExtensionNumbers;

	bool osdpStandardCentralSupported;
	bool enableLicenseFuncSupported;
	bool keypadBacklightSupported;
	bool uzWirelessLockDoorSupported;
	bool customSmartCardSupported;
	bool tomSupported;
	bool tomEnrollSupported;
	bool showOsdpResultbyLED;

	bool customSmartCardFelicaSupported;
	bool ignoreInputAfterWiegandOut;
	bool setSlaveBaudrateSupported;

	uint32 visualFaceTemplateVersion;
}
```
{: #DeviceCapability }

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| info | [DeviceCapability](#DeviceCapability) | The capability information of the device type |


## Management

### Lock

Disable a device temporarily. A locked device will not process any user input. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device to be locked |

### LockMulti

Disable multiple devices temporarily. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices to be locked |


### Unlock

Unlock a locked device. The device will start processing user input again. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device to be unlocked |

### UnlockMulti

Unlock multiple locked devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices to be unlocked  |

### Reboot

Reboot a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device to be rebooted |

### RebootMulti

Reboot multiple devices

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices to be rebooted |

### UpgradeFirmware

Upgrade the firmware of a device. You can download new firmware at [our support site](https://www.supremainc.com/en/support/technical-resources.asp).

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device to be upgraded |
| firmwareData | byte[] | The firmware downloaded from [the support site](https://www.supremainc.com/en/support/technical-resources.asp) |

### UpgradeFirmwareMulti

Upgrade the firmware of multiple devices. These devices should be of a same type.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices to be upgraded | |
| firmwareData | byte[] | The firmware downloaded from [the support site](https://www.supremainc.com/en/support/technical-resources.asp) |


## Reset

### FactoryReset

Reset a device to its initial status. 

Be cautious. All the database and configurations of the device will be lost.
{: .notice--warning}

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device to be reset |

### FactoryResetMulti

Reset multiple devices to their initial status. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | IDs of the devices to be reset |

### ClearDB

Clear the user database of a device. 

All user information will be lost. You can backup the user database using [User.Get]({{'/api/user/' | relative_url}}#get) before deleting it.
{: .notice--warning}

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device whose database will be deleted |

### ClearDBMulti

Clear the user databases of multiple devices. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices whose databases will be deleted |

### ResetConfig

Reset the configurations of a device. If you want to preserve the network configuration, set __withNetwork__ to false. If you want to preserve data such as access groups, access levels, and schedules, set __withDB__ to false.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device whose configurations will be reset |
| withNetwork | bool | If true, reset the network configuration, too | 
| withDB | bool | If true, delete data such as access groups, access levels, and schedules, too | 

### ResetConfigMulti

Reset the configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices whose configurations will be reset |

