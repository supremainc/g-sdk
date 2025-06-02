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
  RTSP_RESOLUTION_TYPE resolution;
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

[resolution](#RTSP_RESOLUTION_TYPE)
: Specifies the resolution for RTSP streaming.

```protobuf
enum RTSP_RESOLUTION_TYPE {
  RTSP_RESOLUTION_DEFAULT = 0;
  RTSP_RESOLUTION_TYPE_1 = 0;
  RTSP_RESOLUTION_TYPE_2 = 1;
}
```
{: #RTSP_RESOLUTION_TYPE }

RTSP_RESOLUTION_DEFAULT
: Specifies the video resolution of RTSP. It means the default value, and the default value is RTSP_RESOLUTION_TYPE_1.

RTSP_RESOLUTION_TYPE_1
: Set the resolution to Type1 (360*640).

RTSP_RESOLUTION_TYPE_2
: Set the resolution to Type2 (720x480).

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
