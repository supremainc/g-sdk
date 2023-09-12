---
title: "UDP Master API"
toc_label: "UDP Master"
---

For the master gateway only. Refer to [the UDP API]({{'/api/udp/' | relative_url}}) for the device gateway.
{: .notice--info}

## Overview

A master gateway manages devices through one or more device gateways. So, you have to specify a gateway ID when calling some of the APIs. Apart from this difference, the UDP Master API shares most of the data structures with [the UDP API]({{'/api/udp/' | relative_url}}).

## IP

You can get/set IP configuration of a device within a subnet using [GetIPConfig](#getipconfig)/[SetIPConfig](#setipconfig).

### GetIPConfig

Get the IP configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| gatewayID | string | The ID of the gateway |
| deviceInfo | [DeviceInfo]({{'/api/udp/' | relative_url}}#DeviceInfo) | The information of the device in the subnet |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [IPConfig]({{'/api/network/' | relative_url}}#IPConfig) | The IP configuration read from the device |

### SetIPConfig

Set the IP configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| gatewayID | string | The ID of the gateway |
| deviceInfo | [DeviceInfo]({{'/api/udp/' | relative_url}}#DeviceInfo) | The information of the device in the subnet |
| config | [IPConfig]({{'/api/network/' | relative_url}}#IPConfig) | The IP configuration to be written to the device |
