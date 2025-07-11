---
title: "Operator API"
toc_label: "Operator"  
---

## Overview

Sets all device operators.
When using [AuthConfig]({{'/api/auth/' | relative_url }}#AuthConfig), there is a limitation that you can set operators up to 10.
[Operator](#Operator) allows you to create and manage up to 1000 operators.
Only available higher than BS2 1.8.0, A2 1.7.0, L2 1.5.0, N2 1.2.0, FS 1.3.0, FaceLite 1.0.1, CS40 1.3.0, P2 1.3.0, W2 1.4.0, Xpass 2 1.0.0 + Devices newly released after 3Q 2019.

To use the Operator feature properly, __at least one administrator(OPERATOR_LEVEL_ADMIN)__ must be configured on the device.
If no administrator is assigned, __any user will be able to access the device menu without restriction__.
{: .notice--warning}

### OperatorLevel

You can assign administrators for managing devices. Each administrator has one of three operator levels, which has different privileges.

```protobuf
enum OperatorLevel {
  OPERATOR_LEVEL_NONE = 0;
  OPERATOR_LEVEL_ADMIN = 1;
  OPERATOR_LEVEL_CONFIG = 2;
  OPERATOR_LEVEL_USER = 3;
}
```
{: #OperatorLevel}


OPERATOR_LEVEL_ADMIN
: Can do all administrative tasks on a device.

OPERATOR_LEVEL_CONFIG
: Can change the configurations of a device.

OPERATOR_LEVEL_USER
: Can enroll/delete users on a device.

### Operator

Operators registered through [AuthConfig]({{'/api/auth/' | relative_url }}#AuthConfig) are migrated the moment they call the `Operator` related API, and the device will only manage the operator through `Operator` from then on.
{: .notice--warning}


```protobuf
message Operator {
  string userID;
  OperatorLevel level;
}
```
{: #Operator}

userID
: User ID to be specified as operator.

level
: [OperatorLevel](#OperatorLevel)

## Get

### GetList

Get the device's operators

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| operators | [Operator](#Operator) | The device's operators |

## Add

### Add

Add device operators of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| operators | [Operator](#Operator) | The device's operators |

### AddMulti

Add device operators of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| operators | [Operator](#Operator) | The device's operators |

## Delete

### Delete

Delete device operators of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| operatorIDs | string | The IDs of operators |

### DeleteMulti

Delete device operators of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| operatorIDs | string[] | The IDs of operators |

## DeleteAll

### DeleteAll

Delete all operators on the device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

### DeleteAllMulti

Delete all operators on multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |