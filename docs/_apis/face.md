---
title: "Face API"
toc_label: "Face"  
---

There are IR-based authentication devices and RGB-based visual face authentication devices for face authentication devices.
IR-based devices include FaceStation 2 and FaceLite.
RGB-based visual face authentication devices include FaceStation F2, BioStation 3, BioEntry W3, etc.
There are some differences between these two types.
<!-- Face authentication is provided by FaceStation 2, FaceLite, FaceStation F2, and BioStation 3. The newest model, FaceStation F2 and BioStation 3 uses a fusion matching algorithm to improve its authentication performance. Due to this, there are a couple of differences between them. -->

|      |       | IR-based authentication devices | RGB-based visual face authentication devices |
| ---- | ----- | ------------------------ | -----------    |
| FaceData | flag  | BS2_FACE_FLAG_NONE  |  BS2_FACE_FLAG_WARPED / BS2_FACE_FLAG_EX / BS2_FACE_FLAG_TEMPLATE_ONLY |
|          | templates  | Maximum 30  | Maximum 10 |
|          | imageData  | Visual Image  | Visual Image |
|          | irTemplates | Not Used | Maximum 10 |
|          | irImageData | Not Used | IR Image |
| Auth Group |  | Supported  | Not Supported  |

{: #F2Differences}

## Scan

```protobuf
enum FaceFlag {
  BS2_FACE_FLAG_NONE = 0x00;
  BS2_FACE_FLAG_WARPED = 0x01;
  BS2_FACE_FLAG_TEMPLATE_ONLY = 0x20;
  BS2_FACE_FLAG_EX = 0x100;
}

message FaceData {
  int32 index;
  uint32 flag;
  repeated bytes templates;     // When BS2_FACE_FLAG_TEMPLATE_ONLY, only templates is used.
  bytes imageData;

  // only for RGB-Based Visual Face Authentication Device (flag & BS2_FACE_FLAG_EX is true)
  repeated bytes irTemplates;
  bytes irImageData;
}
```
{: #FaceData}

index
: Can be used for managing face data in your applications. Not used by the device.

flag
: If __BS2_FACE_FLAG_EX__ is set, it means that the face data is acquired by RGB-based visual face authentication devices. And, the data will include __irTemplates__ and __irImageData__. Otherwise, it is from IR-based authentication devices, and there will be neither __irTemplates__ nor __irImageData__. __BS2_FACE_FLAG_WARPED__ indicates that the image has been normalized. <BR>
__[+ 1.7.1]__ When registered users, The __BS2_FACE_FLAG_TEMPLATE_ONLY__ flag is supported so that only templates can be transmitted.
Templates are specified via the __templates__ field.

Basically, the IR-based face recognition device (FaceStation 2, FaceLite) is very different from the visual camera-based face recognition device (FaceStation F2, BioStation 3) in its method. 
G-SDK's FaceData message structure is a single structure designed to transmit face data to both IR-based and visual camera-based devices of different styles.
This does not mean that template mixing is supported.
For example, you can set FaceStation 2 face data in the FaceData message and send it to FaceStation 2 or FaceLite.
That data cannot be transferred to BioStation 3 and used as is.
{: .notice--warning}


templates
: Maximum 30 face templates can be returned from IR-based authentication devices. For RGB-based visual face authentication devices, the maximum number is 10. <BR>
__[+ 1.7.1]__ You can register users with only templates by setting templates with the __BS2_FACE_FLAG_TEMPLATE_ONLY__ flag.

imageData
: A BMP image of the face will be returned.

irTemplates
: Maximum 10 IR templates can be returned from RGB-based visual face authentication devices.

irImageData
: An IR image of the face will be returned.

```protobuf
enum FaceEnrollThreshold {
  BS2_FACE_ENROLL_THRESHOLD_0 = 0x00;
  BS2_FACE_ENROLL_THRESHOLD_1 = 0x01;
  BS2_FACE_ENROLL_THRESHOLD_2 = 0x02;
  BS2_FACE_ENROLL_THRESHOLD_3 = 0x03;
  BS2_FACE_ENROLL_THRESHOLD_4 = 0x04;
  BS2_FACE_ENROLL_THRESHOLD_5 = 0x05;
  BS2_FACE_ENROLL_THRESHOLD_6 = 0x06;
  BS2_FACE_ENROLL_THRESHOLD_7 = 0x07;
  BS2_FACE_ENROLL_THRESHOLD_8 = 0x08;
  BS2_FACE_ENROLL_THRESHOLD_9 = 0x09;
}
```
{: #FaceEnrollThreshold}

BS2_FACE_ENROLL_THRESHOLD_0
: The least strict threshold.

BS2_FACE_ENROLL_THRESHOLD_4
: The default.

BS2_FACE_ENROLL_THRESHOLD_9
: The most strict threshold.


### Scan

Scan a face and get its template data. With higher __enrollThreshold__, you can get face templates with better qualities. However, it will take more time to scan a face. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| enrollThreshold | [FaceEnrollThreshold](#FaceEnrollThreshold) | The strictness of face enrollment |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| faceData | [FaceData](#FaceData) | The scanned face data |

### Extract

Extract face templates from normalized images. 
It can be used to write a face template on a smart card.

Only supported by FaceStation F2 and BioStation 3. If the image file is not acquired by FaceStation F2 or BioStation 3, the image data should be in JPG or PNG file format. 
{: .notice--warning}

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| imageData | byte[] | If the image file is acquired by FaceStation F2 or BioStation 3, it will be in BMP file format. Otherwise, only JPG file format is supported. |
| isWarped | bool  | If the image file is acquired by FaceStation F2 or BioStation 3, it should be true. Otherwise, it should be false. |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| templateData | byte[] | The template data extracted from the image |

### Normalize

Extract warped images from unwarped images. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| unwrappedImageData | byte[] | Unwarped image data. |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| wrappedImageData | byte[] | Warped image data. |


## Config

The default values would be good for most cases. Since some of these parameters could have a bad effect on the authentication performance, read the descriptions carefully before changing them. 

```protobuf
message FaceConfig {
  FaceSecurityLevel securityLevel;
  FaceLightCondition lightCondition;
  FaceEnrollThreshold enrollThreshold;
  FaceDetectSensitivity detectSensitivity;
  uint32 enrollTimeout; 
  FaceLFDLevel LFDLevel;
  bool quickEnrollment;
  FacePreviewOption previewOption;
  bool checkDuplicate;
  FaceOperationMode operationMode;
  uint32 maxRotation;
  uint32 faceWidthMin;
  uint32 faceWidthMax;
  uint32 searchRangeX;
  uint32 searchRangeWidth;
  uint32 detectDistanceMin;
  uint32 detectDistanceMax;
  bool wideSearch;
  bool unableToSaveImageOfVisualFace;
}
```
{: #FaceConfig}

[securityLevel](#FaceSecurityLevel)
: With more secure levels, False Acceptance Ratio(FAR) would get lower. However, False Rejection Ratio(FRR) would become higher. The default is __BS2_FACE_SECURITY_NORMAL__.

[lightCondition](#FaceLightCondition)
: The lighting condition could have a big effect on the authentication performance. The default is __BS2_FACE_LIGHT_CONDITION_INDOOR__.

[enrollThreshold](#FaceEnrollThreshold)
: The strictness of face enrollment. The default is __BS2_FACE_ENROLL_THRESHOLD_4__.

[detectSensitivity](#FaceDetectSensitivity)
: Face authentication starts automatically after detecting a face. This parameter specifies the sensitivity of detecting faces. The default is __BS2_FACE_DETECT_SENSITIVITY_MIDDLE__.

enrollTimeout
: Timeout in seconds for enrolling a face. The default is 60 seconds.

LFDLevel
: Configuration for the LFD(Live Face Detection - fake face detection) sensitivity.

| Value | Description | Default |
| ----- | ----------- | ------- |
| 0 | Normal  | IR-Based Face Authentication Device |
| 1 | Secure | RGB-Based Visual Face Authentication Device |
| 2 | More Secure | |
| 3 | Most Secure | |

quickEnrollment
: Quick face enrollment process. Please use false if you want to enroll with a high quality of face templates.

| Value | Description |
| ----- | ----------- |
| true | Face enrollment process with a single step  |
| false | Face enrollment process with 3 steps |

previewOption
: IR camera preview option when you authenticate with the face.
Only used to FaceLite.

| Value | Description |
| ----- | ----------- |
| 0 |	Preview not used |
| 1 |	Preview not used at first of authentication, preview at 1/2 stage |
| 2 |	Preview of all stages on authentication |

checkDuplicate
: Check whether the scanned face is duplicated in the device.

__*operationMode*__
* __FaceStation F2 V1.0.0__ can be set to the following operation modes, default is Fusion mode.

| Value | Mode | Description | Default |
| ----- | ---- | ----------- | ------- |
| 0 |	Fusion Mode | Visual matching + IR matching	| Default |
| 1 |	Visual Mode | Visual matching | |
| 2 |	Visual + IR | Visual matching, IR detects only face | |

* __FaceStation F2 V1.0.1 or later versions__, __RGB-Based Visual Face Authentication Device__ are used in the following sense.

| Value |	Mode | Description | Default |
| ----- | ---- | ----------- | ------- |
| 0 |	Fusion Mode	Visual matching + IR matching	| Default |
| 1 |	Fast Mode	Visual matching	| |

maxRotation
: __RGB-Based Visual Face Authentication Device__
When face is recognized normally it's front side.
Still, it is possible to determine how many degrees the image has been rotated from the front when device detects a face.
This enables detection failure in the case of images rotated over a certain angle.
maxRotation represents the maximum allowable value in this case, and the default value is 15 degrees.

| Definition | Value |
| ---------- | ----- |
| BS2_MAX_ROTATION_DEFAULT | 15 |
| BS2_MAX_ROTATION_ANGLE_15 | 15 |
| BS2_MAX_ROTATION_ANGLE_30 | 30 |
| BS2_MAX_ROTATION_ANGLE_45 | 45 |
| BS2_MAX_ROTATION_ANGLE_60 | 60 |
| BS2_MAX_ROTATION_ANGLE_75 | 75 |
| BS2_MAX_ROTATION_ANGLE_90 | 90 |
| BS2_MAX_ROTATION_ANGLE_MAX | 90 |

faceWidthMin
: Not used. Replaced by __detectDistanceMin__.

faceWidthMax
: Not used. Replaced by __detectDistanceMax__.

searchRangeX
: Not used. Replaced by __wideSearch__.

searchRangeWidth
: Not used. Replaced by __wideSearch__.

detectDistance ([detectDistanceMin](#detectDistanceMin), [detectDistanceMax](#detectDistanceMax))
: __RGB-Based Visual Face Authentication Device__
This configures the minimum and maximun detection range for facial recognition.
We no longer support faceWidth to pinpoint the face location using pixel units due to its complexity.
Instead, we set the detection range of the subject(face). The unit is set to cm, and the value must be inputted as a multiple of 10.

| Type | Min limit for min detection range | Max limit for min detection range | Min detection range(Default) | Min limit for max detection range | Max limit for max detection range | Max sensing range(No limit) | Max sensing range(Default) |
| ---- | -- | --- | -- | -- | --- | --- | --- |
| FSF2 | 30 | 130 | 30 | 40 | 130 | 255 | 130 |
| BS3 | 30 | 100 | 30 | 40 | 100 | 255 | 100 |
| BEW3 | 30 | 100 | 30 | 40 | 100 | 255 | 100 |

detectDistanceMin
: The minimum detection range for facial recognition.
{: #detectDistanceMin}

detectDistanceMax
: The maximum detection range for facial recognition.
{: #detectDistanceMax}

wideSearch
: __RGB-Based Visual Face Authentication Device except FSF2__
This can increase the detection range for face detection.
We no longer support searchRange to set the x-coordinate and width due to its complexity.
Instead, we set the face detection setting as default(FALSE), or a wide area(TRUE).
The details of the settings and protocols for the detection of wide area is set within the device, which the user cannot change.
If this setting is set to TRUE, the camera detects subjects within a large range, and unintentionally detect and authenticate multiple subjects at once.
Therefore, the default setting is at FALSE.

unableToSaveImageOfVisualFace
: __[+ 1.7.1]__ Indicates whether devices that use visual face as a credential will store facial images on the device.
Enabling this setting will immediately delete image information from all user's facial data stored in the device, leaving only the templates.
Additionally, even if face information containing user images is obtained through the [User.Enroll]({{'/api/user/' | relative_url}}#enroll) API, the device will ignore it.
The default value is false, which means both facial data and images are stored.


```protobuf
enum FaceSecurityLevel {
  BS2_FACE_SECURITY_NORMAL = 0x00;
  BS2_FACE_SECURITY_SECURE = 0x01;
  BS2_FACE_SECURITY_MORE_SECURE = 0x02;
}
```
{: #FaceSecurityLevel}

```protobuf
enum FaceLightCondition {
  BS2_FACE_LIGHT_CONDITION_INDOOR = 0x00;
  BS2_FACE_LIGHT_CONDITION_OUTDOOR = 0x01;
  BS2_FACE_LIGHT_CONDITION_AUTO = 0x02;
  BS2_FACE_LIGHT_CONDITION_DARK = 0x03;
}
```
{: #FaceLightCondition}

```protobuf
enum FaceDetectSensitivity {
  BS2_FACE_DETECT_SENSITIVITY_OFF = 0x00;
  BS2_FACE_DETECT_SENSITIVITY_LOW = 0x01;
  BS2_FACE_DETECT_SENSITIVITY_MIDDLE = 0x02;
  BS2_FACE_DETECT_SENSITIVITY_HIGH = 0x03;
}
```
{: #FaceDetectSensitivity}


### GetConfig

Get the face configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [FaceConfig](#FaceConfig) | The face configuration of the device |

### SetConfig

Change the face configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [FaceConfig](#FaceConfig) | The face configuration to be written to the device |


### SetConfigMulti

Change the face configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [FaceConfig](#FaceConfig) | The face configuration to be written to the devices |


## Auth group

The more the number of face templates, the higher the False Acceptance Ratio(FAR). To lower this error, you can divide users into several groups, and try face authentication in a specific group only. To use this feature, you have to do the followings.

* Enable [AuthConfig.useGroupMatching]({{'/api/auth/' | relative_url}}#AuthConfig).
* Create the authentication groups using [AddAuthGroup](#addauthgroup) or [AddAuthGroupMulti](#addauthgroupmulti).
* Set [UserHdr.authGroupID]({{'/api/user/' | relative_url}}#UserHdr).
* Enroll or update users using [Enroll]({{'/api/user/' | relative_url}}#enroll) or [EnrollMulti]({{'/api/user/' | relative_url}}#enrollmulti).

Authentication groups are not supported by FaceStation F2 and BioStation 3. 
{: .notice--warning}

```protobuf
message AuthGroup {
  uint32 ID;
  string name; 
}
```
{: #AuthGroup}

### GetAuthGroup

Get the list of authentication groups stored in a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| authGroups | [AuthGroup[]](#AuthGroup) | The authentication groups stored in the device |

### AddAuthGroup

Add authentication groups to a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| authGroups | [AuthGroup[]](#AuthGroup) | The authentication groups to be added to the device |

### AddAuthGroupMulti

Add authentication groups to multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| authGroups | [AuthGroup[]](#AuthGroup) | The authentication groups to be added to the devices |

### DeleteAuthGroup

Delete authentication groups from a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| groupIDs | uint32[] | The IDs of the groups to be deleted from the device |

### DeleteAuthGroupMulti

Delete authentication groups from multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| groupIDs | uint32[] | The IDs of the groups to be deleted from the devices |

### DeleteAllAuthGroup

Delete all authentication groups from a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

### DeleteAllAuthGroupMulti

Delete all authentication groups from multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |