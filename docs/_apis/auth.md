---
title: "Authentication API"
toc_label: "Authentication"  
---

With [AuthConfig](#AuthConfig), you can specify the options related to authenticating credentials. The most important option is __authSchedules__, which sets the authentication modes available to a device. 

[AuthFailLockoutConfig](#AuthFailLockoutConfig) protects the PIN against brute-force attacks: after too many failures the device blocks PIN input for a while. [GetAuthFailStatus](#getauthfailstatus) reads the failure counters and [ReleaseAuthFailLockout](#releaseauthfaillockout) ends a lockout early.

## Config

```protobuf
message AuthConfig {
  repeated AuthSchedule authSchedules;
  bool useGlobalAPB;
  GlobalAPBFailActionType globalAPBFailAction;
  bool useGroupMatching;
  bool usePrivateAuth;
  FaceDetectionLevel faceDetectionLevel;
  bool useServerMatching;
  bool useFullAccess;
  uint32 matchTimeout;
  uint32 authTimeout;
  repeated Operator operators;
  AuthFailLockoutConfig authFailLockoutConfig;
}
```
{: #AuthConfig}

[authSchedules](#AuthSchedule)
: Authentication modes available for the device. 

useGlobalAPB
: If true, ask the gateway whether to allow access to a user after successful authentication.

[globalAPBFailAction](#GlobalAPBFailActionType)
: Specify what to do when the gateway is unreachable when __useGlobalAPB__ is true.

useGroupMatching
: If true, use group matching for face authentication. Valid only for FaceStation 2 and FaceLite. See [AuthGroup]({{'/api/face/' | relative_url }}#auth-group)

usePrivateAuth
: If true, allow different authentication modes per each user. You can set the private authentication modes in [User.UserSetting]({{'/api/user/' | relative_url}}#UserSetting).

[faceDetectionLevel](#FaceDetectionLevel)
: If it is not FACE_DETECTION_NONE, the device will try to detect a face after a user authenticates successfully. Valid only for BioStation A2.

useServerMatching
: If true, defer the authentication to the gateway. When a user scans a fingerprint or a card, the device will send the information to the gateway and wait for the authentication result.

useFullAccess
: If true, allow access to all registered users regardless of their access groups.

matchTimeout
: Timeout in seconds for fingerprint or face matching.

authTimeout
: Timeout in seconds for the whole authentication process.

[operators](#Operator)
: Up to 10 administrators can be assigned to a device. 

[authFailLockoutConfig](#AuthFailLockoutConfig)
: Lock PIN input after repeated authentication failures. Valid only for the devices with [DeviceCapability.authFailLockoutSupported]({{'/api/device/' | relative_url}}#DeviceCapability); other devices ignore it.


```protobuf
message AuthSchedule {
  AuthMode mode;
  uint32 scheduleID;
}
```
{: #AuthSchedule}

You can set different authentication modes per different schedule. For example, you can allow AUTH_MODE_CARD_ONLY in working time, and set more secure AUTH_MODE_CARD_BIOMETRIC at night.

mode
: [Authentication mode](#AuthMode)

scheduleID
: The ID of the schedule during which the mode is applied. Refer to [Schedule]({{'/api/schedule/' | relative_url}}#Schedule).

```protobuf
enum AuthMode {
  AUTH_MODE_BIOMETRIC_ONLY = 0;
  AUTH_MODE_BIOMETRIC_PIN = 1;

  AUTH_MODE_CARD_ONLY = 2;
  AUTH_MODE_CARD_BIOMETRIC = 3;
  AUTH_MODE_CARD_PIN = 4;
  AUTH_MODE_CARD_BIOMETRIC_OR_PIN = 5;
  AUTH_MODE_CARD_BIOMETRIC_PIN = 6;

  AUTH_MODE_ID_BIOMETRIC = 7;
  AUTH_MODE_ID_PIN = 8;
  AUTH_MODE_ID_BIOMETRIC_OR_PIN = 9;
  AUTH_MODE_ID_BIOMETRIC_PIN = 10;

  AUTH_MODE_NONE = 0xff;
  AUTH_MODE_PROHIBITED = 0xfe;

  // The below modes are only for RGB-based visual face authentication devices
  AUTH_EXT_MODE_FACE_ONLY = 11;
  AUTH_EXT_MODE_FACE_FINGERPRINT = 12;
  AUTH_EXT_MODE_FACE_PIN = 13;
  AUTH_EXT_MODE_FACE_FINGERPRINT_OR_PIN = 14;
  AUTH_EXT_MODE_FACE_FINGERPRINT_PIN = 15;

  AUTH_EXT_MODE_FINGERPRINT_ONLY = 16;
  AUTH_EXT_MODE_FINGERPRINT_FACE = 17;
  AUTH_EXT_MODE_FINGERPRINT_PIN = 18;
  AUTH_EXT_MODE_FINGERPRINT_FACE_OR_PIN = 19;
  AUTH_EXT_MODE_FINGERPRINT_FACE_PIN = 20;

  AUTH_EXT_MODE_CARD_ONLY = 21;
  AUTH_EXT_MODE_CARD_FACE = 22;
  AUTH_EXT_MODE_CARD_FINGERPRINT = 23;
  AUTH_EXT_MODE_CARD_PIN = 24;
  AUTH_EXT_MODE_CARD_FACE_OR_FINGERPRINT = 25;
  AUTH_EXT_MODE_CARD_FACE_OR_PIN = 26;
  AUTH_EXT_MODE_CARD_FINGERPRINT_OR_PIN = 27;
  AUTH_EXT_MODE_CARD_FACE_OR_FINGERPRINT_OR_PIN = 28;
  AUTH_EXT_MODE_CARD_FACE_FINGERPRINT = 29;
  AUTH_EXT_MODE_CARD_FACE_PIN = 30;
  AUTH_EXT_MODE_CARD_FINGERPRINT_FACE = 31;
  AUTH_EXT_MODE_CARD_FINGERPRINT_PIN = 32;
  AUTH_EXT_MODE_CARD_FACE_OR_FINGERPRINT_PIN = 33;
  AUTH_EXT_MODE_CARD_FACE_FINGERPRINT_OR_PIN = 34;
  AUTH_EXT_MODE_CARD_FINGERPRINT_FACE_OR_PIN = 35;

  AUTH_EXT_MODE_ID_FACE = 36;
  AUTH_EXT_MODE_ID_FINGERPRINT = 37;
  AUTH_EXT_MODE_ID_PIN = 38;
  AUTH_EXT_MODE_ID_FACE_OR_FINGERPRINT = 39;
  AUTH_EXT_MODE_ID_FACE_OR_PIN = 40;
  AUTH_EXT_MODE_ID_FINGERPRINT_OR_PIN = 41;
  AUTH_EXT_MODE_ID_FACE_OR_FINGERPRINT_OR_PIN = 42;
  AUTH_EXT_MODE_ID_FACE_FINGERPRINT = 43;
  AUTH_EXT_MODE_ID_FACE_PIN = 44;
  AUTH_EXT_MODE_ID_FINGERPRINT_FACE = 45;
  AUTH_EXT_MODE_ID_FINGERPRINT_PIN = 46;
  AUTH_EXT_MODE_ID_FACE_OR_FINGERPRINT_PIN = 47;
  AUTH_EXT_MODE_ID_FACE_FINGERPRINT_OR_PIN = 48;
  AUTH_EXT_MODE_ID_FINGERPRINT_FACE_OR_PIN = 49;  
}
```
{: #AuthMode}

### Authentication mode

AUTH_MODE_BIOMETRIC_ONLY
: Fingerprint or Face

AUTH_MODE_BIOMETRIC_PIN
: (Fingerprint or Face) + PIN

AUTH_MODE_CARD_ONLY
: Card

AUTH_MODE_CARD_BIOMETRIC
: Card + (Fingerprint or Face)

AUTH_MODE_CARD_PIN
: Card + PIN

AUTH_MODE_CARD_BIOMETRIC_OR_PIN
: Card + (Fingerprint or Face or PIN)

AUTH_MODE_CARD_BIOMETRIC_PIN
: Card + (Fingerprint or Face) + PIN

AUTH_MODE_ID_BIOMETRIC
: ID + (Fingerprint or Face)

AUTH_MODE_ID_PIN
: ID + PIN

AUTH_MODE_ID_BIOMETRIC_OR_PIN
: ID + (Fingerprint or Face or PIN)

AUTH_MODE_ID_BIOMETRIC_PIN
: ID + (Fingerprint or Face) + PIN

Some authentication modes are available only for specific devices. For example, __AUTH_MODE_ID_XXX__ modes are available only for the devices with key pads such as BioStation N2 and BioStation 2. 
{: .notice--warning}

### Authentication mode for RGB-based visual face authentication devices

Some models of RGB-based visual face authentication devices provide both face and fingerprint authentication. To make full use of this multimodal function, a new set of authentication modes, __AUTH_EXT_MODE_XXX__, are adopted for RGB-based visual face authentication devices. Please note that there is no backward compatibility. You have to use these modes only for RGB-based visual face authentication devices. 

AUTH_EXT_MODE_FACE_ONLY
: Face 

AUTH_EXT_MODE_FACE_FINGERPRINT
: Face + Fingerprint

AUTH_EXT_MODE_FACE_PIN
: Face + PIN 

AUTH_EXT_MODE_FACE_FINGERPRINT_OR_PIN
: Face + (Fingerprint or PIN)

AUTH_EXT_MODE_FACE_FINGERPRINT_PIN
: Face + Fingerprint + PIN

AUTH_EXT_MODE_FINGERPRINT_ONLY
: Fingerprint 

AUTH_EXT_MODE_FINGERPRINT_FACE
: Fingerprint + Face

AUTH_EXT_MODE_FINGERPRINT_PIN
: Fingerprint + PIN

AUTH_EXT_MODE_FINGERPRINT_FACE_OR_PIN
: Fingerprint + (Face or PIN)

AUTH_EXT_MODE_FINGERPRINT_FACE_PIN
: Fingerprint + FACE + PIN

AUTH_EXT_MODE_CARD_ONLY
: Card

AUTH_EXT_MODE_CARD_FACE
: Card + Face

AUTH_EXT_MODE_CARD_FINGERPRINT
: Card + Fingerprint

AUTH_EXT_MODE_CARD_PIN
: Card + PIN

AUTH_EXT_MODE_CARD_FACE_OR_FINGERPRINT
: Card + (Face or Fingerprint)

AUTH_EXT_MODE_CARD_FACE_OR_PIN
: Card + (Face or PIN)

AUTH_EXT_MODE_CARD_FINGERPRINT_OR_PIN
: Card + (Fingerprint or PIN)

AUTH_EXT_MODE_CARD_FACE_OR_FINGERPRINT_OR_PIN
: Card + (Face or Fingerprint or PIN)

AUTH_EXT_MODE_CARD_FACE_FINGERPRINT
: Card + Face + Fingerprint

AUTH_EXT_MODE_CARD_FACE_PIN
: Card + Face + PIN

AUTH_EXT_MODE_CARD_FINGERPRINT_FACE
: Card + Fingerprint + Face

AUTH_EXT_MODE_CARD_FINGERPRINT_PIN
: Card + Fingerprint + PIN

AUTH_EXT_MODE_CARD_FACE_OR_FINGERPRINT_PIN
: Card + (Face or Fingerprint) + PIN

AUTH_EXT_MODE_CARD_FACE_FINGERPRINT_OR_PIN
: Card + Face + (Fingerprint or PIN)

AUTH_EXT_MODE_CARD_FINGERPRINT_FACE_OR_PIN
: Card + Fingerprint + (Face or PIN)

AUTH_EXT_MODE_ID_FACE
: ID + Face

AUTH_EXT_MODE_ID_FINGERPRINT
: ID + Fingerprint

AUTH_EXT_MODE_ID_PIN
: ID + PIN

AUTH_EXT_MODE_ID_FACE_OR_FINGERPRINT
: ID + (Face or Fingerprint)

AUTH_EXT_MODE_ID_FACE_OR_PIN
: ID + (Face or PIN)

AUTH_EXT_MODE_ID_FINGERPRINT_OR_PIN
: ID + (Fingerprint or PIN)

AUTH_EXT_MODE_ID_FACE_OR_FINGERPRINT_OR_PIN
: ID + (Face or Fingerprint or PIN)

AUTH_EXT_MODE_ID_FACE_FINGERPRINT
: ID + Face + Fingerprint

AUTH_EXT_MODE_ID_FACE_PIN
: ID + Face + PIN

AUTH_EXT_MODE_ID_FINGERPRINT_FACE
: ID + Fingerprint + Face

AUTH_EXT_MODE_ID_FINGERPRINT_PIN
: ID + Fingerprint + PIN

AUTH_EXT_MODE_ID_FACE_OR_FINGERPRINT_PIN
: ID + (Face or Fingerprint) + PIN

AUTH_EXT_MODE_ID_FACE_FINGERPRINT_OR_PIN
: ID + Face + (Fingerprint or PIN)

AUTH_EXT_MODE_ID_FINGERPRINT_FACE_OR_PIN
: ID + Fingerprint + (Face or PIN)

```protobuf
enum FaceDetectionLevel {
  FACE_DETECTION_NONE = 0;
  FACE_DETECTION_NORMAL = 1;
  FACE_DETECTION_STRICT = 2;
}
```
{: #FaceDetectionLevel}

FACE_DETECTION_NONE
: Face detection is not used.

FACE_DETECTION_NORMAL
: Normal detection level is used.

FACE_DETECTION_STRICT 
: Strict detection level is used.

Note that face detection is not face authentication. This option applies only to BioStation A2. Neither FaceStation 2 nor FaceLite uses this option. 
{: .notice--warning}


```protobuf
enum GlobalAPBFailActionType {
  GLOBAL_APB_FAIL_ACTION_NONE = 0;
  GLOBAL_APB_FAIL_ACTION_SOFT = 1;
  GLOBAL_APB_FAIL_ACTION_HARD = 2;
}
```
{: #GlobalAPBFailActionType}

Global APB needs a gateway to determine whether the user is allowed to enter the door. If the device cannot connect to the server, it will decide by itself according to this parameter.

GLOBAL_APB_FAIL_ACTION_NONE
: Allow access.

GLOBAL_APB_FAIL_ACTION_SOFT
: Allow access, but write a log record denoting the APB violation.

GLOBAL_APB_FAIL_ACTION_HARD
: Disallow access and write a log record denoting the APB violation.


```protobuf
enum OperatorLevel {
  OPERATOR_LEVEL_NONE = 0;
  OPERATOR_LEVEL_ADMIN = 1;
  OPERATOR_LEVEL_CONFIG = 2;
  OPERATOR_LEVEL_USER = 3;
}
```
{: #OperatorLevel}

You can assign administrators for managing devices. Each administrator has one of three operator levels, which has different privileges. 

OPERATOR_LEVEL_ADMIN
: Can do all administrative tasks on a device.

OPERATOR_LEVEL_CONFIG
: Can change the configurations of a device.

OPERATOR_LEVEL_USER
: Can enroll/delete users on a device.


```protobuf
message Operator {
  string userID;
  OperatorLevel level;
}
```
{: #Operator}

## Auth fail lockout

To block brute-force attacks on the PIN, the device counts the PIN failures within a time window. When the count reaches __countAuthFail__, the device locks PIN input for __accumulatedLockoutTimeLevel1__. If it is locked again before __accumulatedLockoutTimeReset__ has passed without a failure, the second lockout lasts __accumulatedLockoutTimeLevel2__. A successful authentication or the end of the time window clears the counter.

The device keeps two independent counters, one per [LockoutType](#LockoutType): the lockout for access authentication does not block the administrator or intrusion (arm, disarm) authentication, and vice versa. A lock override user can authenticate while the device is locked.

The device writes __BS2_EVENT_DEVICE_AUTHFAIL_LOCKOUT__ when it locks and __BS2_EVENT_DEVICE_AUTHFAIL_LOCKOUT_RESET__ when the lockout ends. See [the Event API]({{'/api/event/' | relative_url}}).

Only the devices with [DeviceCapability.authFailLockoutSupported]({{'/api/device/' | relative_url}}#DeviceCapability) support the lockout.
{: .notice--warning}

```protobuf
message AuthFailLockoutConfig {
  bool useAuthFailLockout;
  uint32 countAuthFail;
  uint32 timeWindowOfAuthFail;
  uint32 accumulatedLockoutTimeReset;
  uint32 accumulatedLockoutTimeLevel1;
  uint32 accumulatedLockoutTimeLevel2;
}
```
{: #AuthFailLockoutConfig}

useAuthFailLockout
: If true, lock PIN input after repeated failures.

countAuthFail
: The number of failures within __timeWindowOfAuthFail__ that locks the device. 3 - 15, default 5.

timeWindowOfAuthFail
: The time window in seconds in which the failures are counted. Failures older than this are not counted. 30 - 300, default 60.

accumulatedLockoutTimeReset
: If no failure occurs for this many seconds after a lockout ends, the next lockout starts again at the first level. 600 - 3600, default 1800.

accumulatedLockoutTimeLevel1
: The duration in seconds of the first lockout. 30 - 900, default 60.

accumulatedLockoutTimeLevel2
: The duration in seconds of the second lockout, which applies when the device is locked again before __accumulatedLockoutTimeReset__ has passed. 900 - 3600, default 900. 0 means no second-level lockout.

```protobuf
enum LockoutType {
  LOCKOUT_TYPE_NONE = 0;
  LOCKOUT_TYPE_AUTH = 1;
  LOCKOUT_TYPE_OTHERS = 2;
}
```
{: #LockoutType}

LOCKOUT_TYPE_AUTH
: The lockout for access authentication.

LOCKOUT_TYPE_OTHERS
: The lockout for administrator and intrusion (arm, disarm) authentication.

```protobuf
enum LockoutLevel {
  LOCKOUT_LEVEL_NONE = 0;
  LOCKOUT_LEVEL_1 = 1;
  LOCKOUT_LEVEL_2 = 2;
}
```
{: #LockoutLevel}

LOCKOUT_LEVEL_1
: The first lockout. __accumulatedLockoutTimeLevel1__ applies.

LOCKOUT_LEVEL_2
: A lockout recurring before __accumulatedLockoutTimeReset__ has passed. __accumulatedLockoutTimeLevel2__ applies.

```protobuf
enum LockoutMask {
  LOCKOUT_MASK_NONE = 0;
  LOCKOUT_MASK_AUTH = 1;
  LOCKOUT_MASK_OTHERS = 2;
  LOCKOUT_MASK_ALL = 3;
}
```
{: #LockoutMask}

The [LockoutType](#LockoutType) values as bit flags for [ReleaseAuthFailLockout](#ReleaseAuthFailLockout).

LOCKOUT_MASK_AUTH
: The lockout for access authentication (LOCKOUT_TYPE_AUTH).

LOCKOUT_MASK_OTHERS
: The lockout for administrator and intrusion (arm, disarm) authentication (LOCKOUT_TYPE_OTHERS).

LOCKOUT_MASK_ALL
: Both lockouts.

LOCKOUT_MASK_NONE
: No lockout type. Not accepted by [ReleaseAuthFailLockout](#ReleaseAuthFailLockout).

```protobuf
message AuthFailStatus {
  uint32 deviceID;
  repeated AuthFailStatusDetail details;
}
```
{: #AuthFailStatus}

deviceID
: The ID of the device or the slave device.

[details](#AuthFailStatusDetail)
: One entry per [LockoutType](#LockoutType) the device tracks.

```protobuf
message AuthFailStatusDetail {
  LockoutType lockoutType;
  uint32 remainingCount;
  bool isLockedOut;
  LockoutLevel lockoutLevel;
  uint32 lastLockoutStartTime;
  uint32 lastLockoutEndTime;
  uint32 numOfOccurred;
  repeated uint32 authFailTimes;
}
```
{: #AuthFailStatusDetail}

[lockoutType](#LockoutType)
: The counter this entry describes.

remainingCount
: The number of failures still allowed before the lockout.

isLockedOut
: True while the device is locked.

[lockoutLevel](#LockoutLevel)
: The level of the current or last lockout.

lastLockoutStartTime
: The start time of the last lockout in Unix time (seconds since the epoch, UTC).

lastLockoutEndTime
: The time the last lockout ends or ended in Unix time.

numOfOccurred
: The accumulated number of failures.

authFailTimes
: The times of the counted failures in Unix time.


### GetConfig

Get the configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [AuthConfig](#AuthConfig) | The authentication configuration of the device  |

### SetConfig

Set the configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [AuthConfig](#AuthConfig) | The authentication configuration to be set to the device |

### SetConfigMulti

Set the configurations of multiple devices.

Since RGB-based visual face authentication devices has its own authentication modes, you can not mix them with other models. 
{: .notice--warning}

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [AuthConfig](#AuthConfig) | The authentication configuration to be set to the devices |

### GetAuthFailStatus

Get the authentication failure counters and the lockout state of a device and its slave devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| slaveIDs | uint32[] | The IDs of the slave devices to read. If empty, the device and all of its slave devices are read |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| status | [AuthFailStatus[]](#AuthFailStatus) | The status of each device |

### ReleaseAuthFailLockout

Release the lockout of a device and its slave devices before the lockout time runs out. The failure counters of the released lockout types are cleared.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| slaveIDs | uint32[] | The IDs of the slave devices to release. If empty, the device and all of its slave devices are released |
| lockoutMask | uint32 | The [LockoutMask](#LockoutMask) values to release, combined with bitwise OR. LOCKOUT_MASK_NONE (0) is rejected with INVALID_ARGUMENT |
