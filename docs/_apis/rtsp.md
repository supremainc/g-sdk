---
title: "RTSP API"
toc_label: "RTSP"
---

With BioStation 3, you can use RTSP.

## Config

```protobuf
message RTSPConfig {
  string serverURL;
  uint32 serverPort;
  string userID;
  string userPW;
  bool enabled;
}
```
{: #RTSPConfig}

serverURL
: Tthe address of the RTSP server.

serverPort
: The RTSP server connection port. The default port is 554.

userID
: Account information when connecting to the RTSP server.

userPW
: The password when connecting to the RTSP server.

enabled
: Indicate whether an RTSP connection is enabled.

### GetConfig

Get the RTSP configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [RTSPConfig](#RTSPConfig) | The RTSP configuration of the device |

### SetConfig

Change the RTSP configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [RTSPConfig](#RTSPConfig) | The RTSP configuration to be written to the device |


### SetConfigMulti

Change the RTSP configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [RTSPConfig](#RTSPConfig) | The RTSP configuration to be written to the devices |
