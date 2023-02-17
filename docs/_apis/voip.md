---
title: "Voice over IP API"
toc_label: "VoIP"  
---

With BioStation A2, FaceStation 2, X-Station 2, and BioStation 3, you can use VoIP(Voice over IP). Refer to [the article](http://kb.supremainc.com/knowledge/doku.php?id=en:tc_appnote_setting_up_a_voip_server_for_a2&s[]=voip) for details. 

## Config

```protobuf
message VOIPConfig {
  string serverURL;
  uint32 serverPort;
  string userID;
  string userPW;

  bool enabled;

  uint32 exitButton;
  uint32 DTMFMode;

  uint32 registrationDuration;
  uint32 speakerVolume;
  uint32 micVolume;

  string AuthorizationCode;

  bool showExtensionNumber;
  bool useOutboundProxy;
  string proxyURL;
  uint32 proxyPort;

  repeated UserPhone phones;
}
```
{: #VOIPConfig}

serverURL
: The URL of the SIP server.

serverPort
: The port number of the SIP server.

userID
: The user ID to access the SIP server.

userPW
: The password to access the SIP server.

enabled
: Indicate whether VoIP is enabled.

exitButton
: A key to be used as an exit button.

  | Value | Key |  
  | ----- | --- | 
  | 0 | '*' |
  | 1 | '#' |
  | 2 ~ 11 | | '0' ~ '9' |

DTMFMode
: Specify how the DTMF(Dual Tone Multi Frequency) signals are delivered.

  | Value | Mode |  
  | ----- | --- | 
  | 0 | RFC2833 |
  | 1 | SIP-INFO |

registrationDuration
: Specify the cycle of updating the relevant information to the SIP server. 
Set in seconds and must be between 60 and 600.

speakerVolume
: Specify the speaker volume information for the intercom in the range 0 to 100. The default value is 50.

micVolume
: Specify the microphone volume information for the intercom in the range 0 to 100. The default value is 50.

authorizationCode
: Specify the authentication code value required to connect to the SIP server.

useOutboundProxy
: Specify whether the Outbound Proxy Server is configured.

proxyURL
: Specify the IP address of the Outbound Proxy Server.

proxyPort
: Specify the Outbound Proxy Server port.

[phones](#UserPhone)
: Up to 32 extension numbers can be configured.


```protobuf
message UserPhone {
  string phoneNumber;
  string description;
}
```
{: #UserPhone}

phoneNumber
: The extension number.

description
: Textual description of the extension number.

### GetConfig

Get the VoIP configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [VOIPConfig](#VOIPConfig) | The VoIP configuration of the device |

### SetConfig

Change the VoIP configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [VOIPConfig](#VOIPConfig) | The VoIP configuration to be written to the device |


### SetConfigMulti

Change the VoIP configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [VOIPConfig](#VOIPConfig) | The VoIP configuration to be written to the devices |
