---
title: "Card API"
toc_label: "Card"  
---

## Overview

### Card type

The supported cards can be categorized into several types.

| Type | Cards |
| ---- | ----------- |
| CSN | EM, Mifare, Felica |
| Wiegand | HID Prox, HID iClass |
| Smartcard | Mifare, Mifare DESFire, iClass, iClass SEOS |

Refer to [the article](https://support.supremainc.com/en/support/solutions/articles/24000006349--biostar-2-supported-card-and-card-formats-based-on-model-type) for more detailed information. 

```protobuf
enum Type {
  CARD_TYPE_UNKNOWN = 0x00;
  CARD_TYPE_CSN	= 0x01;
  CARD_TYPE_SECURE = 0x02;
  CARD_TYPE_ACCESS = 0x03;
  CARD_TYPE_QR = 0x06;
  CARD_TYPE_WIEGAND = 0x0A;    
}
```
{: #Type }
CARD_TYPE_CSN
: The CSN(Card Serial Number) is an ID written into a card by manufacturer. You cannot change it.

CARD_TYPE_SECURE
: With a smartcard, you can write data into it. CARD_TYPE_SECURE cards store user credential information which is used for authentication process. Since access group information is not stored in the cards, the device still needs to store user information.

CARD_TYPE_ACCESS
: CARD_TYPE_ACCESS cards stores access group information of users in addition to their credentials. Since all the information for authentication is on a card, the device does not need to store any user information. 

CARD_TYPE_QR
: [X-Station 2](https://www.supremainc.com/en/hardware/versatile-intelligent-terminal-xstation2.asp) supports QR codes as credentials. See [WriteQRCode](#writeqrcode) and [QRConfig](#QRConfig).

CARD_TYPE_WIEGAND
: For Wiegand cards, you have to configure the format to decode the data. See [the article](https://support.supremainc.com/en/support/solutions/articles/24000027804--biostar-2-wiegand-configuration-faq) for details. 


### Card data

```protobuf
message CardData {
  Type type;
  CSNCardData CSNCardData; // null if it is a smartcard
  SmartCardData smartCardData; // null if it is a CSN card
}
```
{: #CardData }

[type](#Type)
: 

[CSNCardData](#CSNCardData)
: Valid only with CARD_TYPE_CSN or CARD_TYPE_QR.

[smartCardData](#SmartCardData)
: Valid only with CARD_TYPE_SECURE or CARD_TYPE_ACCESS.


```protobuf
message CSNCardData {
  Type type;
  int32 size;
  bytes data;
}
```
{: #CSNCardData }

[type](#Type)
: 

size
: The value is always 32. Note that you cannot write a CSNCard.

data
: If the __type__ is __CARD_TYPE_SECURE__, it consists of 24 byte card ID, 4 byte issue count, and 4 byte timestamp.

```protobuf
message SmartCardData {
  SmartCardHeader header;
  bytes cardID;
  SmartCardCredential credential;
  AccessOnCardData accessOnData;
}
```
{: #SmartCardData }

[header](#SmartCardHeader)
: 

cardID
: 32 byte for CARD_TYPE_ACCESS and 24 byte for CARD_TYPE_SECURE

[credential](#SmartCardCredential)
: 

[accessOnData](#AccessOnCardData)
: Valid only with CARD_TYPE_ACCESS cards.


```protobuf
message SmartCardHeader {
  uint32 headerCRC;
  uint32 cardCRC;
  Type type;
  oneof templateCount {
    uint32 numOfTemplate;
    uint32 numOfFaceTemplate;   // Only for FaceStation F2 and BioStation 3
  }
  uint32 templateSize;
  uint32 issueCount;
  uint32 duressMask;
  uint32 cardAuthMode;
  bool useAlphanumericID;
  uint32 cardAuthModeEx;    // Only for FaceStation F2 and BioStation 3
}
```
{: #SmartCardHeader }

headerCRC/cardCRC
: They are calculated automatically in [Write](#write). So, you don't have to fill these values when writing.

type
: CARD_TYPE_SECURE or CARD_TYPE_ACCESS

numOfTemplate
: The number of fingerprint templates stored in the card. Maximum 4 templates can be stored on a card.

numOfFaceTemplate
: The number of face templates stored in the card. Maximum 2 templates can be stored on a card.

templateSize
: The size of fingerprint/face template in bytes. Typically, 384 or 552

issueCount
: You can issue a card multiple times. In this case, you can differentiate each issue by this parameter. 

duressMask
: Specify whether any of the fingerprint templates is of a duress finger. For example, if the first template is of a duress finger, it should be 0x01.

useAlphanumericID
: If true, cardID should be string.

cardAuthMode
: Specify the private authentication mode for the card.

cardAuthModeEx
: Specify the extended private authentication mode for the card.



```protobuf
message SmartCardCredential {
  bytes PIN;
  repeated bytes templates;
}
```
{: #SmartCardCredential}

PIN
: 32 byte hash value for maximum 16 byte PIN. See [User.GetPINHash]({{'/api/user/' | relative_url}}#getpinhash).

templates
: Fingerprint templates of the user.

```protobuf
message AccessOnCardData {
  repeated uint32 accessGroupIDs;
  uint32 startTime;
  uint32 endTime;
}
```
{: #AccessOnCardData}

accessGroupIDs
: IDs of access groups the user belongs to. See [Access.AccessGroup]({{'/api/access/' | relative_url}}#AccessGroup).

startTime/endTime
: Same as [UserSetting]({{'/api/user/' | relative_url}}#UserSetting).

## Read/Write

### Scan

Read a card on a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| cardData | [CardData](#CardData) | The card information read on the device |

### Erase

Erase a smartcard on a device. You have to erase a card before rewriting new data onto it.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |


### Write

Write a smartcard on a device. If the card is not empty, you have to erase it first. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| smartCardData | [SmartCardData](#SmartCardData) | The smartcard information to be written |

### WriteQRCode

Convert a QR code into [CSNCardData](#CSNCardData), which can be assigned to user using [User.Enroll]({{'/api/user/' | relative_url }}#enroll-1) or [User.SetCard]({{'/api/user/' | relative_url }}#setcard).

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| QRText | string | Up to 32 ASCII codes |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| cardData | [CSNCardData](#CSNCardData) | The card data which can be assigned to user |


## Config

The supported card configurations depend on the device type. See [the article](https://support.supremainc.com/en/support/solutions/articles/24000006349--biostar-2-supported-card-and-card-formats-based-on-model-type).

```protobuf
message CardConfig {
  CardByteOrder byteOrder;
  bool useWiegandFormat;
  CardDataType dataType;
  bool useSecondaryKey;

  MifareConfig mifareConfig;
  IClassConfig iClassConfig;
  DESFireConfig DESFireConfig;
  SEOSConfig SEOSConfig;

  uint32 formatID;

  bool cipher;
  CardByteOrder smartCardByteOrder;
}
```
{: #CardConfig }

[byteOrder](#CardByteOrder)
: The byte ordering of the data stored on the card. The default is __MSB__.

useWiegandFormat
: If true, use the [Wiegand format]({{'/api/wiegand/' | relative_url}}#WiegandFormat) to decode the card ID data.

[dataType](#CardDataType)
: The encoding type of the data stored on the card. The default is __DATA_BINARY__.

useSecondaryKey
: If true, try the secondary key when reading or writing with the primary key fails.

[mifareConfig](#MifareConfig)
: Valid only if the device supports Mifare cards.

[iClassConfig](#IClassConfig)
: Valid only if the device supports iClass cards.

[DESFireConfig](#DESFireConfig)
: Valid only if the device supports DESFire cards.

[SEOSConfig](#SEOSConfig)
: Valid only if the device supports SEOS cards.

formatID
: You can assign an ID for managing multiple card configurations in your application. It is not used by the device.

cipher
: If true, CardID entry via keypad on XPASS D2 will be accepted.

[smartCardByteOrder](#CardByteOrder)
: The byte ordering of the data stored on the SmartCard. The default is __MSB__.

```protobuf
enum CardByteOrder {
  MSB = 0;
  LSB = 1;
}
```
{: #CardByteOrder }

```protobuf
enum CardDataType {
  DATA_BINARY = 0;
  DATA_ASCII = 1;
  DATA_UTF16 = 2;
  DATA_BCD = 3;  
}
```
{: #CardDataType }

```protobuf
message MifareConfig {
  bytes primaryKey;
  bytes secondaryKey;
  int32 startBlockIndex;
}
```
{: #MifareConfig }

primaryKey
: A 6 byte key for encrypting/decrypting data. The default is 0xffffffffffff.

secondaryKey
: A 6 byte key. Used only if [CardConfig.useSecondaryKey](#CardConfig) is true.

startBlockIndex
: The index of the first data block on the Mifare card.


```protobuf
message IClassConfig {
  bytes primaryKey;
  bytes secondaryKey;
  int32 startBlockIndex;
}
```
{: #IClassConfig }

primaryKey
: An 8 byte key for encrypting/decrypting data. 

secondaryKey
: An 8 byte key. Used only if [CardConfig.useSecondaryKey](#CardConfig) is true.

startBlockIndex
: The index of the first data block on the iClass card.


```protobuf
message DESFireConfig {
  bytes primaryKey;
  bytes secondaryKey;
  bytes appID;
  uint32 fileID;
  DESFireEncryptionType encryptionType;
  DESFireOperationMode operationMode;
}
```
{: #DESFireConfig }

primaryKey
: A 16 byte key for encrypting/decrypting data.

secondaryKey
: A 16 byte key. Used only if [CardConfig.useSecondaryKey](#CardConfig) is true.

appID
: A 3 byte application ID.

fileID
: One byte file ID which is used for storing the card data.

[encryptionType](#DESFireEncryptionType)
: 

```protobuf
enum DESFireEncryptionType {
  ENC_DES_3DES = 0;
  ENC_AES = 1;
}
```
{: #DESFireEncryptionType }

[operationMode](#DESFireOperationMode)
:

```protobuf
enum DESFireOperationMode {
  OPERATION_LEGACY = 0;
  OPERATION_APPLEVELKEY = 1;
}
```
{: #DESFireOperationMode }

```protobuf
message SEOSConfig {
  bytes OIDADF;
  uint32 sizeADF;
  repeated uint32 OIDDataObjectID;
  repeated uint32 sizeDataObject;
  bytes primaryKeyAuth;
  bytes secondaryKeyAuth;
}
```
{: #SEOSConfig }

OIDADF
: 13 byte Application Dedicated Files(ADF) address. Read-only.

sizeADF
: The size of the ADF.

OIDDataObjectID
: Maximum 8 data object ID can be set.

sizeDataObject
: The size of each data object.

primaryKeyAuth
: A 16 byte key for encrypting/decrypting data.

secondaryKeyAuth
: A 16 byte key. Used only if [CardConfig.useSecondaryKey](#CardConfig) is true.


### GetConfig

Get the card configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [CardConfig](#CardConfig) | The card configuration stored on the device |

### SetConfig

Change the card configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [CardConfig](#CardConfig) | The card configuration to be written to the device |

### SetConfigMulti

Change the card configurations of multiple devices. 

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [CardConfig](#CardConfig) | The card configuration to be written to the devices |

### Get1xConfig

Some BioStar 2 devices can read Mifare cards issued by BioStar 1 devices. Please check [CapabilityInfo.card1xSupported]({{'/api/device/' | relative_url}}#CapabilityInfo) of the device first.

```protobuf
message Card1XConfig {
  bool useCSNOnly;
  bool bioEntryCompatible;

  bool useSecondaryKey;
  bytes primaryKey;
  bytes secondaryKey;

  uint32 CISIndex;
  uint32 numOfTemplate;
  uint32 templateSize;
  repeated uint32 templateStartBlocks;
}
```
{: #Card1XConfig }

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [Card1XConfig](#Card1XConfig) | The V1 card configuration of the device  |

### Set1xConfig

Change the V1 configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [Card1XConfig](#Card1XConfig) | The V1 card configuration to be written to the device |

### Set1xConfigMulti

Change the V1 configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [Card1XConfig](#Card1XConfig) | The V1 card configuration to be written to the devices |

### GetQRConfig

X-Station 2 supports QR codes as credentials. A QR code can be composed of up to 32 ASCII codes. See [WriteQRCode](#writeqrcode) for converting a QR code string into card data.

```protobuf
message QRConfig {
  bool useQRCode;
  uint32 scanTimeout;
  bool bypassData;
  bool treatAsCSN;
}
```
{: #QRConfig }

useQRCode
: Enable reading QR codes.

scanTimeout
: Timeout in seconds for reading a QR code.

bypassData
: If true, the QR data will be transfered to the device gateway.

treatAsCSN
: If true, the QR data will be treated as CSN.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [QRConfig](#QRConfig) | The QR configuration of the device  |

### SetQRConfig

Change the QR configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [QRConfig](#QRConfig) | The QR configuration to be written to the device |

### SetQRConfigMulti

Change the QR configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [QRConfig](#QRConfig) | The QR configuration to be written to the devices |

## CustomConfig

[+ 1.7] Supports reading of smart card data by arbitrarily specifying its location and size. Please check [DeviceCapability.customSmartCardSupported]({{'/api/device/' | relative_url}}#DeviceCapability) of the device first.

```protobuf
message CustomConfig {
  CardDataType dataType = 1;
  bool useSecondaryKey = 2;
  CustomMifareCard mifare = 3;
  CustomDESFireCard desfire = 4;
  CardByteOrder smartCardByteOrder = 5;
  uint32 formatID = 6;
}
```
{: #CustomConfig }

[dataType](#CardDataType)
: Type of card data.

useSecondaryKey
: Decides whether to use the secondary encryption key.

[mifare](#CustomMifareCard)
: Set the Mifare custom card information.

[desfire](#CustomDESFireCard)
: Set the DESFire custom card information.

[smartCardByteOrder](#CardByteOrder)
: The output method can be selected from MSB or LSB.

formatID
: This is an identifier that can be used when the BioStar 2 application needs to manage the card configuration as a database.

```protobuf
message CustomMifareCard {
  bytes primaryKey = 1;
  bytes secondaryKey = 2;
  uint32 startBlockIndex = 3;
  uint32 dataSize = 4;
  uint32 skipBytes = 5;
}
```
{: #CustomMifareCard }

primaryKey
: Primary encryption key to access the Mifare card information.

secondaryKey
: Secondary encryption key to access the Mifare card information.

startBlockIndex
: Start block index on the Mifare data storage.

dataSize
: The size in bytes of the card data.

skipBytes
: This is where the card data appears.
This is the starting point to read card data. It is 0 when reading from the starting point, and indicates the number of bytes skipped after the first.

```protobuf
message CustomDESFireCard {
  bytes primaryKey = 1;
  bytes secondaryKey = 2;
  bytes appID = 3;
  uint32 fileID = 4;
  DESFireEncryptionType encryptionType = 5;
  DESFireOperationMode operationMode = 6;
  uint32 dataSize = 7;
  uint32 skipBytes = 8;
  DESFireAppLevelKey desfireAppKey = 9;
}
```
{: #CustomDESFireCard }

primaryKey
: Primary encryption key to access the DESFire card information. (General settings)

secondaryKey
: Secondary encryption key to access the DESFire card information. (General settings)

appID
: Application Id that is stored inside the DESFire card for user authentication.

fileID
: File ID that is stored inside the DESFire card, which will be used by the application to read and write data.

[encryptionType](#DESFireEncryptionType)
: Type of data encryption.

[operationMode](#DESFireOperationMode)
: Operation mode.

dataSize
: The size in bytes of the card data.

skipBytes
: This is where the card data appears.
This is the starting point to read card data. It is 0 when reading from the starting point, and indicates the number of bytes skipped after the first.

[desfireAppKey](#DESFireAppLevelKey)
: Indicates key information to access DESFire card information. (Advanced settings)

```protobuf
message DESFireAppLevelKey {
  bytes appMasterKey = 1;
  bytes fileReadKey = 2;
  bytes fileWriteKey = 3;
  uint32 fileReadKeyNumber = 4;
  uint32 fileWriteKeyNumber = 5;
}
```
{: #DESFireAppLevelKey }

appMasterKey
: Application master key of DESFire.

fileReadKey
: The key used to read the file.

fileWriteKey
: The key used to write the file.

fileReadKeyNumber
: The index of the key for reading the file.

fileWriteKeyNumber
: The index of the key for writing the file.

desfireAppKey
: A structure containing DesFire key information.

### GetCustomConfig

Get the custom card configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [CustomConfig](#CustomConfig) | The custom smart card configuration of the device  |

### SetCustomConfig

Change the custom smart card configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [CustomConfig](#CustomConfig) | The custom smart card configuration to be written to the device |

### SetCustomConfigMulti

Change the custom smart card configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [CustomConfig](#CustomConfig) | The custom smart card configuration to be written to the devices |

## Blacklist

In some cases, you have to disable cards already assigned to users. For example, if a user lost a card, you have to disable it for security. Each device manages a blacklist for this purpose.

```protobuf
message BlacklistItem {
  bytes cardID;
  uint32 issueCount;
}
```
{: #BlacklistItem }

cardID
: 32 byte card ID.

issueCount
: Valid only for smartcards.

### GetBlacklist

Get the blacklist of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| blacklist | [BlacklistItem[]](#BlacklistItem) | The blacklist of the device |

### AddBlacklist

Add cards to the blacklist of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| cardInfos | [BlacklistItem[]](#BlacklistItem) | The cards to be added to the blacklist of the device |

### AddBlacklistMulti

Add cards to the blacklists of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| cardInfos | [BlacklistItem[]](#BlacklistItem) |  The cards to be added to the blacklists of the devices |

### DeleteBlacklist

Delete cards from the blacklist of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| cardInfos | [BlacklistItem[]](#BlacklistItem) | The cards to be deleted from the blacklist of the device |

### DeleteBlacklistMulti

Delete cards from the blacklists of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| cardInfos | [BlacklistItem[]](#BlacklistItem) | The cards to be deleted from the blacklists of the devices |

### DeleteAllBlacklist

Delete all cards from the blacklist of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

### DeleteAllBlacklistMulti

Delete all cards from the blacklists of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
