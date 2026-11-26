---
title: "Display API"
toc_label: "Display"  
---

You can configure UI options using __DisplayConfig__. You can also change the language pack, background images, and sounds. [+ 1.10.0] The home screen (normal, notice, logo image, slide show or PDF) is best read and written with [GetHomeScreen](#gethomescreen) / [SetHomeScreen](#sethomescreen), because the two __DisplayConfig__ fields that encode it mean different things on different device families; see [Home screen](#home-screen).

## Config

```protobuf
message DisplayConfig {
  LanguageType language;
  BackgroundType background;
  BackgroundTheme theme;

  uint32 volume;
  bool useVoice;

  DateFormat dateFormat;
  TimeFormat timeFormat;
  bool showDateTime;

  uint32 menuTimeout;
  uint32 msgTimeout;
  uint32 backlightTimeout;

  bool useUserPhrase;
  bool queryUserPhrase;

  bool useScreenSaver; // deprecated, use standbyType
  ShowOSDPResult showOSDPResult;

  ShowOptionUserInfo showOptionUserName;
  ShowOptionUserInfo showOptionUserId;
  KeypadType keypadType;

  StandbyType standbyType;
}
```
{: #DisplayConfig }

[language](#LanguageType)
: As default, English and Korean are supported.

[background](#BackgroundType)
: What the home screen shows on the BioStation 2 family. On the BioStation 3 family it is read only when __theme__ is __BS2_BG_THEME_03__ and then decides between __BS2_BG_LOGO__ and __BS2_BG_SLIDE__; in the other states the device leaves it as it was, so a value read back may be stale. See [Home screen](#home-screen).

[theme](#BackgroundTheme)
: [+ 1.10.0] The colour theme on the BioStation 2 family. On the BioStation 3 family (BioStation 3, BioStation 3 Max / Max FP, FaceStation F2 / F2 FP, BioEntry W3) it selects the home screen instead: __BS2_BG_THEME_01__ normal, __BS2_BG_THEME_02__ notice, __BS2_BG_THEME_03__ the custom content chosen by __background__. Before 1.10.0 this field was documented as not supported. Unless you need the colour theme, use [SetHomeScreen](#sethomescreen) rather than writing __background__ and __theme__ yourself.

volume
: Volume level between 0(no sound) and 100(loudest).

useVoice
: Activate the voice instruction.

[dateFormat](#DateFormat)
: 

[timeFormat](#TimeFormat)
: 

showDateTime
: If true, show the clock on the screen.

menuTimeout
: Close the menu after the timeout in seconds. The default is 20 seconds.

msgTimeout
: Close the message dialog after the timeout in milliseconds. The default is 2,000 milliseconds.

backlightTimeout
: Turn off the backlight after the timeout in seconds. The default is 20 seconds.

useUserPhrase
: Show a specific message when a user is authenticated. [CapabilityInfo.userPhraseSupported]({{'/api/device/' | relative_url}}#CapabilityInfo) of the device should be true.

queryUserPhrase
: If __useUserPhrase__ is true, ask the device gateway of the phrase to be displayed. Refer to [Server API]({{'/api/server/' | relative_url}}) for details.

useScreenSaver
: If true, the screen saver will be used. Deprecated since 1.10.0: use __standbyType__ instead, which also covers the home screen and video. Devices that support __standbyType__ follow it rather than this flag.

[showOSDPResult](#ShowOSDPResult)
: [+ 1.7.0] In environments where Suprema devices are connected as peripheral devices to Intelligent Slaves or third-party controllers, this option allows authentication results to be displayed on the device screen.

[showOptionUserName](#ShowOptionUserInfo)
: [+ 1.8.0] Change the user name display condition for successful authentication results. It can be used in places where personal information protection is important.

| Value | Description |
| --------- | ----------- |
| BS2_SHOW_USER_INFO_ALL | Show full user name |
| BS2_SHOW_USER_INFO_PARTIAL | Show partial user name |
| BS2_SHOW_USER_INFO_NOTHING | Hide user name |

[showOptionUserId](#ShowOptionUserInfo)
: [+ 1.8.0] Change the user ID display condition for successful authentication results. It can be used in places where personal information protection is important.

| Value | Description |
| --------- | ----------- |
| BS2_SHOW_USER_INFO_ALL | Show full user ID |
| BS2_SHOW_USER_INFO_PARTIAL | Show partial user ID |
| BS2_SHOW_USER_INFO_NOTHING | Hide user ID |

[keypadType](#KeypadType)
: [+ 1.8.0] Turns the Scramble Keypad on and off. Default is on.

| Value | Description |
| --------- | ----------- |
| BS2_KEYPAD_TYPE_SCRAMBLE | Scramble Keypad on. (Security first) |
| BS2_KEYPAD_TYPE_NORMAL | Scramble Keypad off. (Usability first) |

[standbyType](#StandbyType)
: [+ 1.10.0] What the screen shows after __backlightTimeout__ expires. It replaces __useScreenSaver__: the device keeps the two consistent, so reading the configuration back returns __useScreenSaver__ true only when __standbyType__ is __BS2_STANDBY_SCREENSAVER__.

| Value | Description |
| --------- | ----------- |
| BS2_STANDBY_NONE | Not set: the device falls back to useScreenSaver (false: home screen, true: screen saver) and reports the type it chose |
| BS2_STANDBY_HOME | Stay on the home screen |
| BS2_STANDBY_SCREENSAVER | Play the slide images. See [UpdateSlideImages](#updateslideimages) |
| BS2_STANDBY_VIDEO | Play the video stored on the device. G-SDK has no API to upload the video |


```protobuf
enum LanguageType {
  BS2_LANGUAGE_KOREAN = 0;
  BS2_LANGUAGE_ENGLISH = 1;
  BS2_LANGUAGE_CUSTOM = 2;
}
```
{: #LanguageType }

```protobuf
enum BackgroundType {
  BS2_BG_LOGO = 0;
  BS2_BG_NOTICE = 1;
  BS2_BG_SLIDE = 2;
  BS2_BG_PDF = 3;
}
```
{: #BackgroundType }

BS2_BG_LOGO
: Show a background image. You can change the background image using [UpdateBackgroundImage](#updatebackgroundimage).

BS2_BG_NOTICE
: Show a message. You can change the message using [UpdateNotice](#updatenotice);

BS2_BG_SLIDE
: Show image slides. You can change the slides using [UpdateSlideImages](#updateslideimages).

BS2_BG_PDF
: Show a PDF. BioStation 2 family only.

```protobuf
enum BackgroundTheme {
  BS2_BG_THEME_01 = 0;
  BS2_BG_THEME_02 = 1;
  BS2_BG_THEME_03 = 2;
  BS2_BG_THEME_04 = 3;
  BS2_BG_THEME_DEFAULT = 4;
}
```
{: #BackgroundTheme }

[+ 1.10.0] How __background__ and __theme__ are read depends on the device family. The device protocol carries the same two bytes for every model; the user interface of the model decides what they mean.

| | BioStation 2 family | BioStation 3 family |
| --- | --- | --- |
| Models | Every model with a display that is not in the other column: BioStation 2, BioStation A2, BioStation 2A, FaceStation 2, FaceLite, X-Station 2, ... | BioStation 3, BioStation 3 Max, BioStation 3 Max FP, FaceStation F2, FaceStation F2 FP, BioEntry W3 |
| background | What the home screen shows: __BS2_BG_LOGO__, __BS2_BG_NOTICE__, __BS2_BG_SLIDE__ or __BS2_BG_PDF__ | Read only when __theme__ is __BS2_BG_THEME_03__: __BS2_BG_LOGO__ shows the image, __BS2_BG_SLIDE__ the slide show |
| theme | The colour theme, __BS2_BG_THEME_01__ to __BS2_BG_THEME_04__, independent of __background__ | Selects the home screen: __BS2_BG_THEME_01__ normal, __BS2_BG_THEME_02__ notice, __BS2_BG_THEME_03__ the content chosen by __background__. Anything else shows the normal screen |
| Not available | A plain "normal" screen: one of the four contents is always shown | __BS2_BG_THEME_04__, __BS2_BG_PDF__, the colour theme |

Writing the values of one family to a device of the other selects a different screen than intended. For example __theme__ = __BS2_BG_THEME_01__ with __background__ = __BS2_BG_NOTICE__ is the notice with colour theme 1 on a BioStation 2, but the normal screen on a BioStation 3. [GetHomeScreen](#gethomescreen) and [SetHomeScreen](#sethomescreen) take one [HomeScreenType](#HomeScreenType) and the gateway, which knows the type of the connected device, reads or writes the right pair of fields.

```protobuf
enum HomeScreenType {
  HOME_SCREEN_NORMAL = 0;
  HOME_SCREEN_NOTICE = 1;
  HOME_SCREEN_LOGO = 2;
  HOME_SCREEN_SLIDE = 3;
  HOME_SCREEN_PDF = 4;
}
```
{: #HomeScreenType }

| Value | Description |
| --------- | ----------- |
| HOME_SCREEN_NORMAL | The default home screen. BioStation 3 family only |
| HOME_SCREEN_NOTICE | The notice text. See [UpdateNotice](#updatenotice) |
| HOME_SCREEN_LOGO | The custom background image. See [UpdateBackgroundImage](#updatebackgroundimage) |
| HOME_SCREEN_SLIDE | The slide show. See [UpdateSlideImages](#updateslideimages) |
| HOME_SCREEN_PDF | A PDF. BioStation 2 family only |


```protobuf
enum DateFormat {
  BS2_DATE_FORMAT_YYYYMMDD = 0;
  BS2_DATE_FORMAT_MMDDYYYY = 1;
  BS2_DATE_FORMAT_DDMMYYYY = 2;
}
```
{: #DateFormat }

```protobuf
enum TimeFormat {
  BS2_TIME_FORMAT_12_HOUR = 0;
  BS2_TIME_FORMAT_24_HOUR = 1;
}
```
{: #TimeFormat }

```protobuf
enum ShowOSDPResult {
  BS2_SHOW_OSDP_RESULT_ON = 0;
  BS2_SHOW_OSDP_RESULT_OFF = 1;
}
```
{: #ShowOSDPResult }

```protobuf
enum ShowOptionUserInfo {
  BS2_SHOW_USER_INFO_ALL = 0;
  BS2_SHOW_USER_INFO_PARTIAL = 1;
  BS2_SHOW_USER_INFO_NOTHING = 2;
}
```
{: #ShowOptionUserInfo }

```protobuf
enum KeypadType {
  BS2_KEYPAD_TYPE_SCRAMBLE = 0;
  BS2_KEYPAD_TYPE_NORMAL = 1;
}
```
{: #KeypadType }

```protobuf
enum StandbyType {
  BS2_STANDBY_NONE = 0;
  BS2_STANDBY_HOME = 1;
  BS2_STANDBY_SCREENSAVER = 2;
  BS2_STANDBY_VIDEO = 3;
}
```
{: #StandbyType }


### GetConfig

Get the display configuration of a device. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [DisplayConfig](#DisplayConfig) | The display configuration of the device |


### SetConfig

Change the display configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [DisplayConfig](#DisplayConfig) | The display configuration to be written to the device |

### SetConfigMulti

Change the display configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [DisplayConfig](#DisplayConfig) | The display configuration to be written to the devices  |


## Home screen

[+ 1.10.0] The home screen of a device as one value, whatever the device family. See [BackgroundTheme](#BackgroundTheme) for why this is needed: the same __background__ / __theme__ pair of [DisplayConfig](#DisplayConfig) means a different screen on the BioStation 2 family and on the BioStation 3 family. These functions let the gateway, which knows the type of the device, do the mapping. The other fields of __DisplayConfig__ are not changed: [SetHomeScreen](#sethomescreen) reads the configuration, changes only the fields the family uses and writes it back.

A screen the family does not have is rejected with the gRPC status __UNIMPLEMENTED__: __HOME_SCREEN_NORMAL__ on the BioStation 2 family, which always shows one of the four contents, and __HOME_SCREEN_PDF__ on the BioStation 3 family. In [SetHomeScreenMulti](#sethomescreenmulti) such a device is reported in __deviceErrors__ and the other devices are still set.

The device shows the uploaded notice, image or slides only while the matching home screen is selected, so call [SetHomeScreen](#sethomescreen) after [UpdateNotice](#updatenotice), [UpdateBackgroundImage](#updatebackgroundimage) or [UpdateSlideImages](#updateslideimages) when the screen is not already selected.

### GetHomeScreen

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| homeScreen | [HomeScreenType](#HomeScreenType) | The home screen the device shows, decoded by its family. On the BioStation 3 family a combination the device does not recognise is reported as __HOME_SCREEN_NORMAL__, as the device shows it |

### SetHomeScreen

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| homeScreen | [HomeScreenType](#HomeScreenType) | The home screen to show |

### SetHomeScreenMulti

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices. They may be of different families; each device is encoded by its own |
| homeScreen | [HomeScreenType](#HomeScreenType) | The home screen to show |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceErrors | [ErrorResponse[]]({{ 'api/err/' | relative_url }}#ErrorResponse) | The errors of the devices that failed, including the __UNIMPLEMENTED__ ones |

## Language pack

You can change the language pack used in the UI. For the available language packs, contact [us](https://www.supremainc.com/en/about/contact-us.asp).

### UpdateLanguagePack

Update the language pack of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| data | byte[] | The language pack to be installed on the device |

### UpdateLanguagePackMulti

Update the language packs of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| data | byte[] | The language pack to be installed on the devices |

## Notice

You can change the notice message displayed in the main UI. The device shows it only while the notice is the home screen: select it with [SetHomeScreen](#sethomescreen) and __HOME_SCREEN_NOTICE__. (Setting [DisplayConfig.background](#DisplayConfig) to __BS2_BG_NOTICE__ does this on the BioStation 2 family only; see [BackgroundTheme](#BackgroundTheme).)

### UpdateNotice

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| notice | string | The message to be displayed |

### UpdateNoticeMulti

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| notice | string | The message to be displayed |

## Background image

You can change the background image displayed in the main UI. The device shows it only while the image is the home screen: select it with [SetHomeScreen](#sethomescreen) and __HOME_SCREEN_LOGO__. (Setting [DisplayConfig.background](#DisplayConfig) to __BS2_BG_LOGO__ does this on the BioStation 2 family only; see [BackgroundTheme](#BackgroundTheme).)

### UpdateBackgroundImage

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| PNGImage | byte[] | The PNG image data to be displayed |

### UpdateBackgroundImageMulti

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32 | The IDs of the devices |
| PNGImage | byte[] | The PNG image data to be displayed |

## Background slides

You can change the slides displayed in the main UI. The device shows them only while the slide show is the home screen: select it with [SetHomeScreen](#sethomescreen) and __HOME_SCREEN_SLIDE__. (Setting [DisplayConfig.background](#DisplayConfig) to __BS2_BG_SLIDE__ does this on the BioStation 2 family only; see [BackgroundTheme](#BackgroundTheme).)

### UpdateSlideImages

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| PNGImages | byte[][] | The array of PNG image data to be displayed |

### UpdateSlideImagesMulti

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32 | The IDs of the devices |
| PNGImages | byte[][] | The array of PNG image data to be displayed |

## Sound

Change the sound files for the specified cases.

```protobuf
enum SoundIndex {
  SOUND_INDEX_WELCOME = 0;
  SOUND_INDEX_AUTH_SUCCESS = 1;
  SOUND_INDEX_AUTH_FAIL = 2;
  SOUND_INDEX_ALARM_1 = 3;
  SOUND_INDEX_ALARM_2 = 4;
}
```
{: #SoundIndex }

### UpdateSound

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| index | [SoundIndex](#SoundIndex) | The sound index whose file to be updated |
| waveData | byte[] | The sound data in WAVE file format |

### UpdateSoundMulti

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32 | The IDs of the devices |
| index | [SoundIndex](#SoundIndex) | The sound index whose file to be updated |
| waveData | byte[] | The sound data in WAVE file format |


