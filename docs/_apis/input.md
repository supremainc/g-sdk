---
title: "Input API"
toc_label: "Input"  
---

You can use __InputConfig__ to set up the device's Supervised input, Aux input, etc.

## Config

Set up supervised and aux inputs.

```protobuf
message SupervisedInputRange {
  uint32 MinValue;
  uint32 MaxValue;
}
```
{: #SupervisedInputRange }

MinValue
: Minimum voltage which has a range from 0 ~ 3300(3.3v). 

MaxValue
: Maximum voltage which has a range from 0 ~ 3300(3.3v). 

```protobuf
message SupervisedInputConfig {
  SupervisedInputRange short;
  SupervisedInputRange open;
  SupervisedInputRange on;
  SupervisedInputRange off;
}
```
{: #SupervisedInputConfig }

short
: Range of the voltage which will be distinguished as short input.

open
: Range of the voltage which will be distinguished as open input.

on
: Range of the voltage which will be distinguished as on input.

off
: Range of the voltage which will be distinguished as off input.

```protobuf
enum SupervisedResistanceValue {
  SUPERVISED_REG_1K = 0;
  SUPERVISED_REG_2_2K = 1;
  SUPERVISED_REG_4_7K = 2;
  SUPERVISED_REG_10K = 3;

  SUPERVISED_REG_UNUSED = 254;
  SUPERVISED_REG_CUSTOM = 255;
}
```
{: #SupervisedResistanceValue }

SUPERVISED_REG_1K
: 1k resistance

SUPERVISED_REG_2_2K
: 2.2k resistance

SUPERVISED_REG_4_7K
: 4.7k resistance

SUPERVISED_REG_10K
:	10k resistance

SUPERVISED_REG_UNUSED
: Unsupervised

SUPERVISED_REG_CUSTOM
: Custom

```protobuf
message SupervisedInput {
  uint32 portIndex;
  device.SwitchType type;
  uint32 duration;
  SupervisedResistanceValue resistance;
  SupervisedInputConfig config;
}
```
{: #SupervisedInput }

portIndex
: Input Port Number

[type]({{'/api/device/' | relative_url}}#SwitchType)
: Input Signal Type.

duration
: Input Signal Duration Time Measurement is milliseconds(ms).

[resistance](#SupervisedResistanceValue)
: You can set Supervised input resistance value type or unsupervise it.

[config](#SupervisedInputConfig)
: Configuration that distinguishes the supervised input signal type. This configuration will be valid only when the supervised input's __resistance__ is set as __SUPERVISED_REG_CUSTOM__.


```protobuf
enum AuxInputPort {
  AUX_INPUT_PORT_NORMAL = 0;
  AUX_INPUT_PORT_0 = 1;
  AUX_INPUT_PORT_1 = 2;
  AUX_INPUT_PORT_2 = 3;
}
```
{: #AuxInputPort }

AUX_INPUT_PORT_NORMAL
: Disable aux input. (Used as normal input)

AUX_INPUT_PORT_0
: Aux input port 0

AUX_INPUT_PORT_1
: Aux input port 1

AUX_INPUT_PORT_2
: Aux input port 2

```protobuf
message AuxInput {
  AuxInputPort acFail;
  device.SwitchType typeAux0;
  AuxInputPort tamper;
  device.SwitchType typeAux1;
  AuxInputPort fire;
  device.SwitchType typeAux2;
}
```
{: #AuxInput }

[acFail](#AuxInputPort)
: Specify the aux input port to be used with __AC Fail__.

[typeAux0]({{'/api/device/' | relative_url}}#SwitchType)
: The type of aux input port 0.

[tamper](#AuxInputPort)
: Specify the aux input port to be used with __Tamper__.

[typeAux1]({{'/api/device/' | relative_url}}#SwitchType)
: The type of aux input port 1.

[fire](#AuxInputPort)
: Specify the aux input port to be used with __Fire Alarm__.

[typeAux2]({{'/api/device/' | relative_url}}#SwitchType)
: The type of aux input port 2.

```protobuf
message InputConfig {
  uint32 numOfInput;
  uint32 numOfSupervisedInput;
  AuxInput auxInput;
  repeated SupervisedInput supervisedInputs;
}
```
{: #InputConfig }

numOfInput
: Number of input ports.

numOfSupervisedInput
: Number of the supervised input ports.

[auxInput](#AuxInput)
: Sets the operation of __Aux inputs__.

[supervisedInputs](#SupervisedInput)
: Sets the operation of __Supervised inputs__


### GetConfig

Get the input configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |

| Response |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| config | [InputConfig](#InputConfig) | The input configuration of the device |

### SetConfig

Change the input configuration of a device.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceID | uint32 | The ID of the device |
| config | [InputConfig](#InputConfig) | The input configuration to be written to the device |

### SetConfigMulti

Change the input configurations of multiple devices.

| Request |

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| deviceIDs | uint32[] | The IDs of the devices |
| config | [InputConfig](#InputConfig) | The input configuration to be written to the devices |
