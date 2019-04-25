---
title: Azure Digital Twins 사용자 정의 함수 클라이언트 라이브러리 참조 | Microsoft Docs
description: Azure Digital Twins 사용자 정의 함수 클라이언트 라이브러리 참조입니다.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 12/27/2018
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: d309765e6c4b0b0cc8a7e92977134d4135a69a13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60535588"
---
# <a name="user-defined-functions-client-library-reference"></a>사용자 정의 함수 클라이언트 라이브러리 참조

이 문서에서는 Azure Digital Twins 사용자 정의 함수 클라이언트 라이브러리에 대한 참조 정보를 제공합니다.

## <a name="helper-methods"></a>도우미 메서드

클라이언트 라이브러리는 일반적으로 사용되는 작업에 대한 도우미 메서드를 정의합니다.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

공간 식별자를 지정하면 이 함수는 그래프에서 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 공간 식별자 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

센서 식별자를 지정하면 이 함수는 그래프에서 센서를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 센서 식별자 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

디바이스 식별자를 지정하면 이 함수는 그래프에서 디바이스를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | 디바이스 식별자 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

센서 식별자 및 해당 데이터 형식을 지정하면 이 함수는 센서의 현재 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | 센서 식별자 |
| *dataType*  | `string` | 센서 데이터 형식 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

공간 식별자 및 값 이름을 지정하면 이 함수는 해당 공간 속성의 현재 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | 공간 식별자 |
| *valueName* | `string` | 공간 속성 이름 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

센서 식별자 및 해당 데이터 형식을 지정하면 이 함수는 해당 센서의 기록 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 센서 식별자 |
| *dataType* | `string` | 센서 데이터 형식 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

공간 식별자 및 값 이름을 지정하면 이 함수는 공간에 있는 해당 속성의 기록 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |
| *valueName* | `string` | 공간 속성 이름 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

공간 식별자를 지정하면 이 함수는 해당 부모 공간에 대한 자식 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

공간 식별자를 지정하면 이 함수는 해당 부모 공간에 대한 자식 센서를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

공간 식별자를 지정하면 이 함수는 해당 부모 공간에 대한 자식 디바이스를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

디바이스 식별자를 지정하면 이 함수는 해당 부모 디바이스에 대한 자식 센서를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | 디바이스 식별자 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

공간 식별자를 지정하면 이 함수는 해당 부모 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | 공간 식별자 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

센서 식별자를 지정하면 이 함수는 해당 부모 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | 센서 식별자 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

디바이스 식별자를 지정하면 이 함수는 해당 부모 공간을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | 디바이스 식별자 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

센서 식별자를 지정하면 이 함수는 해당 부모 디바이스를 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | 센서 식별자 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

공간 식별자를 지정하면 이 함수는 공간에서 속성 및 해당 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |
| *propertyName* | `string` | 공간 속성 이름 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

센서 식별자를 지정하면 이 함수는 센서에서 속성 및 해당 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 센서 식별자 |
| *propertyName* | `string` | 센서 속성 이름 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

디바이스 식별자를 지정하면 이 함수는 디바이스에서 속성 및 해당 값을 검색합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | 디바이스 식별자 |
| *propertyName* | `string` | 디바이스 속성 이름 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

이 함수는 지정된 데이터 형식을 사용하여 센서 개체의 값을 설정합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | 센서 식별자 |
| *dataType*  | `string` | 센서 데이터 형식 |
| *값*  | `string` | Value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

이 함수는 지정된 데이터 형식을 사용하여 공간 개체의 값을 설정합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 공간 식별자 |
| *dataType* | `string` | 데이터 형식 |
| *값* | `string` | Value |

### <a name="logmessage"></a>log(message)

이 함수는 사용자 정의 함수 내에서 다음 메시지를 기록합니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *message* | `string` | 기록할 메시지 |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

이 함수는 디스패치할 사용자 지정 알림을 보냅니다.

**Kind**: 글로벌 함수입니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | 그래프 개체 식별자. 예제는 공간, 센서 및 디바이스 ID입니다.|
| *topologyObjectType*  | `string` | 예제는 센서 및 디바이스입니다.|
| *payload*  | `string` | 알림을 사용하여 보낼 JSON 페이로드입니다. |

## <a name="return-types"></a>반환 유형

클라이언트 참조 도우미 메서드에서 반환된 응답 모델에 대한 설명은 다음과 같습니다.

### <a name="space"></a>공백

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
}
```

### <a name="space-methods"></a>공간 메서드

#### <a name="parent--space"></a>Parent() ⇒ `space`

이 함수는 현재 공간의 부모 공간을 반환합니다.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

이 함수는 현재 공간의 자식 센서를 반환합니다.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

이 함수는 현재 공간의 자식 디바이스를 반환합니다.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

이 함수는 현재 공간에 대한 확장 속성 및 해당 값을 반환합니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 확장 속성의 이름 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

이 함수는 현재 공간의 값을 반환합니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 값의 이름 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

이 함수는 현재 공간의 기록 값을 반환합니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 값의 이름 |

#### <a name="notifypayload"></a>Notify(payload)

이 함수는 지정된 페이로드를 사용하여 알림을 보냅니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 알림에 포함할 JSON 페이로드입니다. |

### <a name="device"></a>디바이스

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>디바이스 메서드

#### <a name="parent--space"></a>Parent() ⇒ `space`

이 함수는 현재 디바이스의 부모 공간을 반환합니다.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

이 함수는 현재 디바이스의 자식 센서를 반환합니다.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

이 함수는 현재 디바이스에 대한 확장 속성 및 해당 값을 반환합니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 확장 속성의 이름 |

#### <a name="notifypayload"></a>Notify(payload)

이 함수는 지정된 페이로드를 사용하여 알림을 보냅니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 알림에 포함할 JSON 페이로드입니다. |

### <a name="sensor"></a>센서

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>센서 메서드

#### <a name="space--space"></a>Space() ⇒ `space`

이 함수는 현재 센서의 부모 공간을 반환합니다.

#### <a name="device--device"></a>Device() ⇒ `device`

이 함수는 현재 센서의 부모 디바이스를 반환합니다.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

이 함수는 현재 센서에 대한 확장 속성 및 해당 값을 반환합니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 확장 속성의 이름 |

#### <a name="value--value"></a>Value() ⇒ `value`

이 함수는 현재 센서의 값을 반환합니다.

#### <a name="history--value"></a>History() ⇒ `value[]`

이 함수는 현재 센서의 기록 값을 반환합니다.

#### <a name="notifypayload"></a>Notify(payload)

이 함수는 지정된 페이로드를 사용하여 알림을 보냅니다.

| 매개 변수  | 형식                | 설명  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 알림에 포함할 JSON 페이로드입니다. |

### <a name="value"></a>Value

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>확장 속성

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Digital Twins 사용자 정의 함수](./concepts-user-defined-functions.md)에 대해 알아봅니다.

- [사용자 정의 함수를 만드는 방법](./how-to-user-defined-functions.md)에 대해 알아봅니다.

- [사용자 정의 함수를 디버그하는 방법](./how-to-diagnose-user-defined-functions.md)에 대해 알아봅니다.
