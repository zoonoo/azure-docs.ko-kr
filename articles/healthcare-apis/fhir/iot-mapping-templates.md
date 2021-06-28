---
title: '개념: Azure API for FHIR Azure IoT Connector for FHIR(미리 보기) 기능의 템플릿 매핑'
description: Azure IoT Connector for FHIR(미리 보기)에서 두 가지 유형의 매핑 템플릿을 만드는 방법을 알아봅니다. 디바이스 매핑 템플릿은 디바이스 데이터를 정규화된 스키마로 변환합니다. FHIR 매핑 템플릿은 정규화된 메시지를 FHIR 기반 관찰 리소스로 변환합니다.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: rabhaiya
ms.openlocfilehash: 690a406c9cb0dd7644656c78236a2001ae0f0f2e
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112991598"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Azure IoT Connector for FHIR(미리 보기) 매핑 템플릿
이 문서에서는 매핑 템플릿을 사용하여 전자 의료 기록 교환(FHIR&#174;)*용 Azure IoT Connector를 구성하는 방법을 자세히 설명합니다.

Azure IoT Connector for FHIR에는 두 가지 유형의 JSON 기반 매핑 템플릿이 필요합니다. 첫 번째 유형인 **디바이스 매핑은** `devicedata` Azure Event Hub 엔드포인트에 전송된 디바이스 페이로드를 매핑하는 것을 담당합니다. 형식, 디바이스 식별자, 측정 날짜 시간 및 측정값을 추출합니다. 두 번째 형식인 **FHIR 매핑은** FHIR 리소스에 대한 매핑을 제어합니다. 관찰 기간의 길이, 값을 저장하는 데 사용되는 FHIR 데이터 형식 및 용어 코드를 구성할 수 있습니다. 

매핑 템플릿은 형식에 따라 JSON 문서로 구성됩니다. 그런 다음, 이러한 JSON 문서는 Azure Portal 통해 Azure IoT Connector for FHIR에 추가됩니다. 디바이스 매핑 문서는 **디바이스 매핑 구성** 페이지 및 FHIR 매핑 구성 페이지를 통해 **FHIR** 매핑 문서를 통해 추가됩니다.

> [!NOTE]
> 매핑 템플릿은 기본 Blob Storage에 저장되고 컴퓨팅 실행당 Blob에서 로드됩니다. 업데이트되면 즉시 적용됩니다. 

## <a name="device-mapping"></a>디바이스 매핑
디바이스 매핑은 추가 평가를 위해 디바이스 콘텐츠를 일반적인 형식으로 추출하는 매핑 기능을 제공합니다. 받은 각 메시지는 모든 템플릿에 대해 평가됩니다. 이 방법을 사용하면 단일 인바운드 메시지를 여러 아웃바운드 메시지에 프로젝션할 수 있으며, 나중에 FHIR의 다른 관찰에 매핑됩니다. 결과는 템플릿에서 구문 분석된 값 또는 값을 나타내는 정규화된 데이터 개체입니다. 정규화된 데이터 모델에는 찾아서 추출해야 하는 몇 가지 필수 속성이 있습니다.

| 속성 | 설명 |
| - | - |
|**Type**|측정값을 분류할 이름/형식입니다. 이 값은 필요한 FHIR 매핑 템플릿에 바인딩하는 데 사용됩니다.  여러 템플릿이 동일한 형식으로 출력할 수 있어 여러 디바이스의 다양한 표현을 단일 공통 출력에 매핑할 수 있습니다.|
|**OccurenceTimeUtc**|측정이 발생한 시간입니다.|
|**DeviceId**|디바이스의 식별자입니다. 이 값은 대상 FHIR 서버에 있는 디바이스 리소스의 식별자와 일치해야 합니다.|
 |**속성**|값을 만든 Observation 리소스에 저장할 수 있도록 하나 이상의 속성을 추출합니다.  속성은 정규화 중에 추출된 키 값 쌍의 컬렉션입니다.|

다음은 정규화 중에 발생하는 작업의 개념 예제입니다.

![정규화 예제](media/concepts-iot-mapping-templates/normalization-example.png)

콘텐츠 페이로드 자체는 Azure Event Hub 메시지로, 본문, 속성 및 SystemProperties의 세 부분으로 구성됩니다. `Body`는 UTF-8로 인코딩된 문자열을 나타내는 바이트 배열입니다. 템플릿을 평가하는 동안 바이트 배열은 문자열 값으로 자동으로 변환됩니다. `Properties` 는 메시지 작성자가 사용할 키 값 컬렉션입니다. `SystemProperties` 는 Azure Event Hub 프레임워크에서 예약한 키 값 컬렉션이기도 하며 항목이 자동으로 채워집니다.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>JSON 경로를 통해 매핑
현재 지원되는 세 가지 디바이스 콘텐츠 템플릿 유형은 JSON 경로를 사용하여 필요한 템플릿과 추출된 값을 모두 일치시켜야 합니다. JSON 경로에 대한 자세한 내용은 여기에서 찾을 수 [있습니다.](https://goessner.net/articles/JsonPath/) 세 가지 템플릿 형식은 모두 JSON 경로 식을 해결하기 위해 JSON [.NET 구현을](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 사용합니다.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate을 사용하면 JSON 경로를 사용하여 이벤트 허브 메시지에서 값을 일치시키고 추출할 수 있습니다.

| 속성 | Description |<div style="width:150px">예제</div>
| --- | --- | --- 
|**Typename**|템플릿과 일치하는 측정값과 연결할 형식입니다.|`heartrate`
|**TypeMatchExpression**|이벤트 허브 페이로드에 대해 평가되는 JSON 경로 식입니다. 일치하는 JToken이 발견되면 템플릿이 일치하는 것으로 간주됩니다. 모든 후속 식은 여기에서 일치하는 추출된 JToken에 대해 평가됩니다.|`$..[?(@heartRate)]`
|**TimestampExpression**|측정값의 OccurenceTimeUtc에 대한 타임스탬프 값을 추출하는 JSON 경로 식입니다.|`$.endDate`
|**DeviceIdExpression**|디바이스 식별자를 추출하는 JSON 경로 식입니다.|`$.deviceId`
|**PatientIdExpression**|*선택 사항:* 환자 식별자를 추출하는 JSON 경로 식입니다.|`$.patientId`
|**EncounterIdExpression**|*선택 사항:* 발견 식별자를 추출하는 JSON 경로 식입니다.|`$.encounterId`
|**값[]. ValueName**|후속 식에서 추출한 값과 연결할 이름입니다. FHIR 매핑 템플릿에서 필요한 값/구성 요소를 바인딩하는 데 사용됩니다. |`hr`
|**값[]. ValueExpression**|필요한 값을 추출하는 JSON 경로 식입니다.|`$.heartRate`
|**값[]. 필수**|페이로드에 값이 있어야 합니다.  찾을 수 없으면 측정값이 생성되지 않고 InvalidOperationException이 throw됩니다.|`true`

##### <a name="examples"></a>예
---
**심박수**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*템플릿*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**혈압**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*템플릿*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**단일 메시지에서 여러 측정값 프로젝션**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*템플릿 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*템플릿 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**메시지의 배열에서 여러 측정값 프로젝션**

*Message*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*템플릿*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate은 DeviceIdExpression 및 TimestampExpression이 필요하지 않다는 점을 제외하고 JsonPathContentTemplate과 비슷합니다.

이 템플릿을 사용하는 경우 평가 중인 메시지가 [Azure IoT Central 의 Azure IoT Hub 디바이스 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 또는 데이터 [내보내기(레거시)](../../iot-central/core/howto-export-data-legacy.md) 기능을 사용하여 전송되었다고 가정합니다. [](../../iot-central/core/overview-iot-central.md) 이러한 SDK를 사용하는 경우 디바이스 ID(Azure Iot Hub/Central의 디바이스 식별자가 대상 FHIR 서버의 디바이스 리소스에 대한 식별자로 등록되었다고 가정) 및 메시지의 타임스탬프를 알 수 있습니다. Azure IoT Hub 디바이스 SDK를 사용하지만 디바이스 ID 또는 측정 타임스탬프에 대한 메시지 본문에서 사용자 지정 속성을 사용하는 경우에도 JsonPathContentTemplate을 사용할 수 있습니다.

*참고: IotJsonPathContentTemplate을 사용하는 경우 TypeMatchExpression은 전체 메시지를 JToken으로 확인해야 합니다. 아래 예제를 참조하세요.* 
##### <a name="examples"></a>예
---
**심박수**

*Message*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*템플릿*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**혈압**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*템플릿*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

또한 IotCentralJsonPathContentTemplate에는 DeviceIdExpression 및 TimestampExpression이 필요하지 않으며, 평가 중인 메시지가 [Azure IoT Central](../../iot-central/core/overview-iot-central.md) [데이터 내보내기](../../iot-central/core/howto-export-data.md) 기능을 통해 전송될 때 사용됩니다. 이 기능을 사용하는 경우 디바이스 ID(Azure Iot Central의 디바이스 식별자가 대상 FHIR 서버의 디바이스 리소스에 대한 식별자로 등록되었다고 가정)와 메시지의 타임스탬프가 알려져 있습니다. Azure IoT Central 데이터 내보내기 기능을 사용하지만 디바이스 ID 또는 측정 타임스탬프에 대한 메시지 본문에서 사용자 지정 속성을 사용하는 경우에도 JsonPathContentTemplate을 사용할 수 있습니다.

*참고: IotCentralJsonPathContentTemplate을 사용하는 경우 TypeMatchExpression은 전체 메시지를 JToken으로 확인해야 합니다. 아래 예제를 참조하세요.* 
##### <a name="examples"></a>예
---
**심박수**

*Message*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*템플릿*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**혈압**

*Message*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*템플릿*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="fhir-mapping"></a>FHIR 매핑
디바이스 콘텐츠가 정규화된 모델로 추출되면 디바이스 식별자, 측정 유형 및 기간에 따라 데이터가 수집되고 그룹화됩니다. 이 그룹화의 출력은 FHIR 리소스(현재[관찰)로](https://www.hl7.org/fhir/observation.html) 변환하기 위해 전송됩니다. 여기서 FHIR 매핑 템플릿은 데이터가 FHIR 관찰에 매핑되는 방법을 제어합니다. 한 시점 또는 1시간 동안 관찰을 만들어야 합니까? 관찰에 추가해야 하는 코드는 무엇인가요? 값을 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 또는 Quantity로 표현해야 [합니까?](https://www.hl7.org/fhir/datatypes.html#Quantity) 이러한 데이터 형식은 FHIR 매핑 구성 컨트롤의 모든 옵션입니다.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate은 현재 FHIR 매핑에서 지원되는 유일한 템플릿입니다.  이를 통해 코드, 유효 기간 및 관찰 값을 정의할 수 있습니다. [SampledData,](https://www.hl7.org/fhir/datatypes.html#SampledData) [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)및 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)등의 여러 값 형식이 지원됩니다. 이러한 구성 가능한 값과 함께 관찰 리소스의 식별자와 적절한 디바이스 및 환자 리소스에 대한 연결이 자동으로 처리됩니다.

| 속성 | Description 
| --- | ---
|**Typename**| 이 템플릿이 바인딩해야 하는 측정 형식입니다. 이 형식을 출력하는 디바이스 매핑 템플릿이 하나 이상 있어야 합니다.
|**PeriodInterval**|생성된 관찰이 나타내는 기간입니다. 지원되는 값은 0(인스턴스), 60(시간), 1440(일)입니다.
|**범주**|생성된 관찰 유형을 분류하는 [CodeableConcepts의](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) 개수
|**코드**|생성된 [관찰에](http://hl7.org/fhir/datatypes-definitions.html#coding) 적용할 하나 이상의 코딩입니다.
|**Codes[]. 코드**|코딩에 대한 [코드입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Codes[]. 시스템**|코딩 에 대한 [시스템입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Codes[]. 표시**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)에 대한 표시입니다.
|**값**|관찰에서 추출하고 나타낼 값입니다. 자세한 내용은 [값 형식 템플릿을 참조하세요.](#valuetypes)
|**Components**|*선택 사항:* 관찰에서 만들 하나 이상의 구성 요소입니다.
|**구성 요소[]. 코드**|구성 [요소에](http://hl7.org/fhir/datatypes-definitions.html#coding) 적용할 하나 이상의 코딩입니다.
|**구성 요소[]. 값**|구성 요소에서 추출하고 나타낼 값입니다. 자세한 내용은 [값 형식 템플릿을 참조하세요.](#valuetypes)

### <a name="value-type-templates"></a>값 형식 템플릿 <a name="valuetypes"></a>
다음은 현재 지원되는 값 형식 템플릿입니다. 나중에 추가 템플릿이 추가될 수 있습니다.
#### <a name="sampleddata"></a>SampledData
[SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR 데이터 형식을 나타냅니다. 관찰 측정값은 지정 시간에 시작하여 정의된 기간을 사용하여 앞으로 증분하는 값 스트림에 기록됩니다. 값이 없으면 `E` 가 데이터 스트림에 기록됩니다. 마침표가 두 개 이상의 값이 데이터 스트림에서 동일한 위치를 차지하게 되면 최신 값이 사용됩니다. SampledData를 사용한 관찰이 업데이트되면 동일한 논리가 적용됩니다.

| 속성 | Description 
| --- | ---
|**DefaultPeriod**|사용할 기본 기간(밀리초)입니다. 
|**단위**|SampledData의 원점에서 설정할 단위입니다. 

#### <a name="quantity"></a>수량
Quantity FHIR 데이터 [형식을](http://hl7.org/fhir/datatypes.html#Quantity) 나타냅니다. 그룹화에 두 개 이상의 값이 있는 경우 첫 번째 값만 사용됩니다. 동일한 관찰에 매핑되는 새 값이 도착하면 이전 값을 덮어쓰게 됩니다.

| 속성 | Description 
| --- | --- 
|**단위**| 단위 표현입니다.
|**코드**| 단위의 코딩된 형식입니다.
|**System**| 코딩된 단위 폼을 정의하는 시스템입니다.

### <a name="codeableconcept"></a>CodeableConcept
[CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR 데이터 형식을 나타냅니다. 실제 값은 사용되지 않습니다.

| 속성 | Description 
| --- | --- 
|**Text**|일반 텍스트 표현입니다. 
|**코드**|생성된 [관찰에](http://hl7.org/fhir/datatypes-definitions.html#coding) 적용할 하나 이상의 코딩입니다.
|**Codes[]. 코드**|코딩에 대한 [코드입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Codes[]. 시스템**|코딩 에 대한 [시스템입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Codes[]. 표시**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)에 대한 표시입니다.

### <a name="examples"></a>예
**심박수 - SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**단계 - SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**압력 - SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**압력 - 수량**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**장치가 제거 됨-CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>다음 단계

Azure IoT 커넥터에서 FHIR (미리 보기)에 대 한 질문과 대답을 확인 하세요.

>[!div class="nextstepaction"]
>[FHIR Faq 용 Azure IoT 커넥터](fhir-faq.yml)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다. FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.