---
title: "UDP API"
toc_label: "UDP"
---

For the device gateway only. Refer to [the UDP Master API]({{'/api/udpMaster/' | relative_url}}) for the master gateway.
{: .notice--info}

## Overview

The UDP API is for communicating with devices via UDP.

## IP

You can get/set IP configuration of a device within a subnet using [GetIPConfig](#getipconfig)/[SetIPConfig](#setipconfig).

### GetIPConfig

Get the IP configuration of a device.

```protobuf
message DeviceInfo {
  uint32 deviceID;
  string IPAddr; 
}
```
{: #DeviceInfo }

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceInfo | [DeviceInfo](#DeviceInfo) | The information of the device in the subnet |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [IPConfig]({{'/api/network/' | relative_url}}#IPConfig) | The IP configuration read from the device |

### SetIPConfig

Set the IP configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceInfo | [DeviceInfo](#DeviceInfo) | The information of the device in the subnet |
| config | [IPConfig]({{'/api/network/' | relative_url}}#IPConfig) | The IP configuration to be written to the device |
