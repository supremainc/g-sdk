---
title: "MasterAdmin API"
toc_label: "MasterAdmin"  
---

## Overview

Suprema devices are CE RED (European Radio Equipment Directive) compliant and support MasterAdmin.
Users cannot start using a device that supports this feature without configuring MasterAdmin.
This differs from the existing Operator.
Once MasterAdmin is configured, the device becomes available.
Below is information on devices and versions that support MasterAdmin.

| Device Type | Supported Version |
| ----------- | ----------------- |
| BS3 | V1.4.1 or later |
| XS2 | V1.4.0 or later |
| BS2a | V1.2.0 or later |
| BEW3 | On schedule |

## Get

### Get

Get the master admin information.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| masterAdmin | [UserInfo[]]({{'/api/user/' | relative_url}}#UserInfo) | The master admin information on the device |


## Set

### Set

Set the master admin on a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| masterAdmin | [UserInfo[]]({{'/api/user/' | relative_url}}#UserInfo) | The information of the master admin to be set |

### SetMulti

Set the master admin to multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| masterAdmin | [UserInfo[]]({{'/api/user/' | relative_url}}#UserInfo) | The information of the master admin to be set  |
