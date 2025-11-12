---
title: "DeviceLicense API"
toc_label: "DeviceLicense"  
---

## Overview

[+ 1.9.0] DeviceLicense provides a way to activate specific features on a device.

| Device Type | Supported Version | Visual QR | Wireless lock |
| ----------- | ----------------- | :-------: | :-----------: |
| XS2 | V1.2.0 or later | O |  |
| BS3 | V1.1.0 or later | O |  |
| CS40 | V1.7.1 or later |  | O |

## Config

Retrieves device license related settings for the device.

```protobuf
message DeviceLicenseConfig {
    uint32 version = 1;
    repeated DeviceLicenseInfo licenseInfo = 2;
}
```
{: #DeviceLicenseConfig }

version
: Version of device license settings information.

[licenseInfo](#DeviceLicenseInfo)
: Device license information. Up to 16 can be configured.

```protobuf
message DeviceLicenseInfo {
    uint32 index = 1;
    uint32 hasCapability = 2;
    bool enabled = 3;
    uint32 enableCount = 4;
    DeviceLicenseType type = 5;
    DeviceLicenseSubType subType = 6;
    uint32 enableTime = 7;
    uint32 expiredTime = 8;
    uint32 issueNumber = 9;
    string name = 10;
}
```
{: #DeviceLicenseInfo }

index
: License index.

hasCapability
: Whether the device supports that license. It usually has a value of 1.

enabled
: Whether the license is active.

enableCount
: This is the number that can be supported. For wireless locks, this indicates the number of slaves that can be connected.

[type](#DeviceLicenseType)
: The type of license.

[subType](#DeviceLicenseSubType)
: Detailed form of license type.

enableTime
: License activation start time, expressed in POSIX time.

expiredTime
: License activation end time, 0 means unlimited.

issueNumber
: Issuing unique number.

name
: License name.


```protobuf
enum DeviceLicenseType {
    TYPE_NONE                = 0x0000;
    TYPE_VISUAL_QR           = 0x0001;
    TYPE_UZ_WIRELESS_LOCK    = 0x0002;
}
```
{: #DeviceLicenseType }

```protobuf
enum DeviceLicenseSubType {
    SUBTYPE_NONE                = 0;
    SUBTYPE_VISUAL_QR_CODE_CORP = 1;
}
```
{: #DeviceLicenseSubType }

### GetConfig

Get the display configuration of a device. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [DeviceLicenseConfig](#DeviceLicenseConfig) | The device license configuration of the device |



## Enable/Disable/Query

Activate/deactivate/check device license on the device.

```protobuf
message DeviceLicenseBlob {
    DeviceLicenseType type = 1;
    repeated uint32 deviceIDs = 2;
    bytes data = 3;
}
```
{: #DeviceLicenseBlob }

[type](#DeviceLicenseType)
: The type of license.

deviceIDs
: Slave devices for which license information will be issued.

data
: License activation data block.

```protobuf
message DeviceLicenseResult {
    uint32 deviceID = 1;
    DeviceLicenseStatus status = 2;
}
```
{: #DeviceLicenseResult }

deviceID
: Device identifier.

[status](#DeviceLicenseStatus)
: License status information.


```protobuf
enum DeviceLicenseStatus {
    NOT_SUPPORTED   = 0;
    DISABLE         = 1;
    ENABLE          = 2;
    EXPIRED         = 3;
    NOT_EXIST       = 4;
    ALREADY_ADD     = 5;
}
```
{: #DeviceLicenseStatus }

### Enable

Activate device license on the device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| licenseBlob | [DeviceLicenseBlob](#DeviceLicenseBlob) | Device license information to be activated. |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| results | [DeviceLicenseResult](#DeviceLicenseResult) | Activation results. |


### Disable

Deactivate device license on the device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| licenseBlob | [DeviceLicenseBlob](#DeviceLicenseBlob) | Device license information to be deactivated. |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| results | [DeviceLicenseResult](#DeviceLicenseResult) | Deactivation results. |

### Query

Check activation status on the device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| type | [DeviceLicenseType](#DeviceLicenseType) | Device license type information to check. |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| results | [DeviceLicenseResult](#DeviceLicenseResult) | Activation status on the device. |
