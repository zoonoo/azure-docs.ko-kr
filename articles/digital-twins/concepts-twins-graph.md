---
title: Digital Twins 및 쌍 그래프
titleSuffix: Azure Digital Twins
description: 디지털 쌍의 개념과 관계가 어떻게 그래프를 만드는 지를 이해 합니다.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 5821a1d1f6713ef39d7475fb004164e7c0fd71ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87062055"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>디지털 쌍 및 쌍 그래프 이해

Azure digital 쌍 솔루션에서 환경의 엔터티는 azure **digital 쌍**로 표시 됩니다. 디지털 쌍은 사용자 정의 [모델](concepts-models.md)중 하나의 인스턴스입니다. 이는 **관계** 를 통해 다른 디지털 쌍에 연결 하 여 쌍 **그래프**를 형성할 수 있습니다 .이 쌍 그래프는 전체 환경에 대 한 표현입니다.

> [!TIP]
> "Azure Digital Twins"는 전체 Azure 서비스를 의미 합니다. "Digital 쌍" 또는 "쌍"은 서비스 인스턴스 내의 개별 쌍 노드를 나타냅니다.

## <a name="digital-twins"></a>Digital Twins

Azure Digital Twins 인스턴스에서 디지털 쌍을 만들려면 먼저 서비스에 *모델* 을 업로드 해야 합니다. 모델은 특정 쌍에 포함 될 수 있는 속성, 원격 분석 메시지 및 관계 집합을 설명 합니다. 모델에 정의 된 정보 유형은 [*개념: 사용자 지정 모델*](concepts-models.md)을 참조 하세요.

모델을 만들고 업로드 한 후 클라이언트 앱에서 형식의 인스턴스를 만들 수 있습니다. 디지털 쌍입니다. 예를 들어 *바닥*의 모델을 만든 후이 유형을 사용 하는 하나 또는 여러 개의 디지털 쌍을 만들 수 있습니다 (예: *GroundFloor*이라는 *Floor*쌍, *Floor2*라는 다른 유형의 쌍). 

## <a name="relationships-a-graph-of-digital-twins"></a>관계: 디지털 쌍의 그래프

Twins는 해당 관계에 따라 쌍으로 연결 됩니다. 쌍이 가질 수 있는 관계는 해당 모델의 일부로 정의 됩니다.  

예를 들어 모델 *층* 은 *대화방*형식의 쌍를 대상으로 하는 *포함* 관계를 정의할 수 있습니다. 이 정의를 사용 하 여 Azure Digital 쌍는 모든 *층* 쌍에서 *대화방* 쌍으로의 관계 ( *대화방* 하위 형식의 쌍 포함)를 만들 *수 있습니다.* 

이 프로세스의 결과는 그래프의 가장자리 (해당 관계)를 통해 연결 된 노드 (디지털 쌍) 집합입니다.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>Api를 사용 하 여 만들기

이 섹션에서는 클라이언트 응용 프로그램에서 디지털 쌍 및 관계를 만드는 방법을 보여 줍니다. [DigitalTwins api](how-to-use-apis-sdks.md)를 활용 하는 .net 코드 예제를 포함 하 여 이러한 각 개념 내에서 수행 되는 작업에 대 한 추가 컨텍스트를 제공 합니다.

### <a name="create-digital-twins"></a>디지털 트윈 만들기

다음은 [DigitalTwins api](how-to-use-apis-sdks.md) 를 사용 하 여 *대화방*형식의 쌍을 인스턴스화하는 클라이언트 코드의 코드 조각입니다.

Azure Digital Twins의 현재 미리 보기에서 쌍을 만들려면 쌍의 모든 속성을 초기화 해야 합니다. 이 작업은 필요한 초기화 값을 제공 하는 JSON 문서를 만들어 수행 합니다.

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

### <a name="create-relationships"></a>관계 만들기

다음은 [DigitalTwins api](how-to-use-apis-sdks.md) 를 사용 하 여 *GroundFloor* 라는 *바닥*형식의 디지털 쌍과 *Cafe*라는 *방*형식의 디지털 쌍 간에 관계를 만드는 클라이언트 코드의 몇 가지 예입니다.

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

## <a name="json-representations-of-graph-elements"></a>그래프 요소의 JSON 표현

디지털 쌍 데이터 및 관계 데이터는 모두 JSON 형식으로 저장 됩니다. 즉, Azure digital 쌍 인스턴스에서 쌍 [그래프를 쿼리하면](how-to-query-graph.md) 생성 된 디지털 쌍 및 관계의 JSON 표현이 생성 됩니다.

### <a name="digital-twin-json-format"></a>디지털 쌍 JSON 형식

JSON 개체로 표시 되는 경우 디지털 쌍은 다음 필드를 표시 합니다.

| 필드 이름 | Description |
| --- | --- |
| `$dtId` | 디지털 쌍의 ID를 나타내는 사용자 제공 문자열입니다. |
| `$etag` | 웹 서버에서 할당 한 표준 HTTP 필드 |
| `$conformance` | 이 디지털 쌍의 규칙 상태를 포함 하는 열거형입니다 (*준수*, 일치 *하지 않음*, *알 수 없음*). |
| `{propertyName}` | JSON ( `string` , number 형식 또는 object)의 속성 값입니다. |
| `$relationships` | 관계 컬렉션에 대 한 경로 URL입니다. 디지털 쌍에 나가는 관계 가장자리가 없으면이 필드가 없습니다. |
| `$metadata.$model` | 필드 이 디지털 쌍의 특징을 나타내는 모델 인터페이스의 ID입니다. |
| `$metadata.{propertyName}.desiredValue` | [쓰기 가능한 속성에만 해당] 지정 된 속성의 원하는 값입니다. |
| `$metadata.{propertyName}.desiredVersion` | [쓰기 가능한 속성에만 해당] 원하는 값의 버전입니다. |
| `$metadata.{propertyName}.ackVersion` | 디지털 쌍을 구현 하는 장치 앱에서 인정 하는 버전 |
| `$metadata.{propertyName}.ackCode` | [쓰기 가능한 속성에만 해당] `ack` 디지털 쌍을 구현 하는 장치 앱에서 반환 된 코드 |
| `$metadata.{propertyName}.ackDescription` | [쓰기 가능한 속성에만 해당] `ack` 디지털 쌍을 구현 하는 장치 앱에서 반환 하는 설명입니다. |
| `{componentName}` | 루트 개체의 속성 값과 메타 데이터를 포함 하는 JSON 개체입니다. 이 개체는 구성 요소에 속성이 없는 경우에도 존재 합니다. |
| `{componentName}.{propertyName}` | JSON의 구성 요소 속성 값 ( `string` , 숫자 형식 또는 개체)입니다. |
| `{componentName}.$metadata` | 루트 수준과 비슷한 구성 요소에 대 한 메타 데이터 정보입니다. `$metadata` |

JSON 개체로 형식이 지정 된 디지털 쌍의 예는 다음과 같습니다.

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>관계 JSON 형식

JSON 개체로 표시 되는 경우 디지털 쌍의 관계에 다음 필드가 표시 됩니다.

| 필드 이름 | Description |
| --- | --- |
| `$relationshipId` | 이 관계의 ID를 나타내는 사용자 제공 문자열입니다. 이 문자열은 원본 디지털 쌍의 컨텍스트에서 고유 하며,이는 `sourceId`  +  `relationshipId` Azure digital twins 인스턴스의 컨텍스트에서 고유한 것을 의미 하기도 합니다. |
| `$etag` | 웹 서버에서 할당 한 표준 HTTP 필드 |
| `$sourceId` | 원본 디지털 쌍의 ID입니다. |
| `$targetId` | 대상 디지털 쌍의 ID입니다. |
| `$relationshipName` | 관계의 이름입니다. |
| `{propertyName}` | 필드 이 관계의 속성 값 (JSON ( `string` , 숫자 형식 또는 개체))입니다. |

JSON 개체로 형식이 지정 된 관계의 예는 다음과 같습니다.

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>다음 단계

Azure 디지털 쌍 Api를 사용 하 여 그래프 요소를 관리 하는 방법을 참조 하세요.
* [*방법: digital 쌍 관리*](how-to-manage-twin.md)
* [*방법: 관계로 쌍 그래프 관리*](how-to-manage-graph.md)

또는 정보에 대 한 Azure Digital Twins 쌍 그래프를 쿼리 하는 방법에 대해 알아봅니다.
* [*개념: 쿼리 언어*](concepts-query-language.md)