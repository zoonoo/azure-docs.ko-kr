---
title: IoT Edge의 Live Video Analytics에서 직접 메서드 사용 - Azure
description: IoT Edge의 Live Video Analytics는 여러 직접 메서드를 노출합니다. 직접 메서드는 이 항목에 설명된 규칙을 기준으로 합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56fbf565af45a9b3877ff8d6c48a56713ddc3da6
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076992"
---
# <a name="direct-methods"></a>직접 메서드

IoT Edge의 Live Video Analytics는 IoT Hub에서 호출할 수 있는 몇 가지 직접 메서드를 노출합니다. 직접 메서드는 사용자가 지정한 시간 제한을 초과하는 즉시 성공하거나 실패한다는 점에서 HTTP 호출과 비슷한 디바이스와의 요청-응답 상호 작용을 나타냅니다. 이 방법은 즉각적인 조치 과정이 디바이스의 응답 여부에 따라 달라지는 시나리오에서 유용합니다. 자세한 내용은 [IoT Hub의 직접 메서드 이해 및 호출](../../iot-hub/iot-hub-devguide-direct-methods.md)을 참조하세요.

이 항목에서는 이러한 메서드 및 규칙에 대해 설명합니다.

## <a name="conventions"></a>규칙

직접 메서드는 다음 규칙을 기준으로 합니다.

1. 직접 메서드는 MAJOR.MINOR 형식에 지정된 버전입니다(다음 예제에 표시).

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. 지정된 IoT Edge의 Live Video Analytics 모듈 버전은 최신 버전까지의 모든 직접 메서드를 지원합니다. 예를 들어, 모듈 버전 1.3은 버전 1.3, 1.2, 1.1 및 1.0 버전의 직접 메서드를 지원합니다.
3. 모든 직접 메서드는 동기적입니다.
4. 오류 결과는 OData 오류 스키마를 따릅니다.
5. 이름은 다음 제약 조건을 따라야 합니다.
    
    * 영숫자 문자와 대시만 포함(단, 대시로 시작하고 끝나면 안 됨)
    * 공백 없음
    * 최대 32자

### <a name="example"></a>예제

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>최상위 오류 코드     

|상태 |코드   |메시지|
|---|---|---|
|400|   BadRequest| 요청이 잘못되었습니다.|
|400|   InvalidResource|    리소스가 잘못되었습니다.|
|400|   InvalidVersion| API 버전이 잘못되었습니다.|
|402|   ConnectivityRequired    |에지 모듈을 제대로 작동하려면 클라우드 연결이 필요합니다.|
|404|   NotFound    |리소스를 찾을 수 없습니다.|
|409|   충돌|   작업 충돌|
|500|   InternalServerError|    내부 서버 오류|
|503|   ServerBusy| 서버 작업 중|

### <a name="detailed-error-codes"></a>자세한 오류 로그

그래프 모듈 유효성 검사와 같은 자세한 유효성 검사 오류는 오류 정보로 추가됩니다.

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|상태|    자세한 코드   |Description|
|---|---|---|
|400|   GraphValidationError|   주기 또는 분할과 같은 일반적인 그래프 오류 등|
|400|   ModuleValidationError|  모듈 특정 유효성 검사 오류입니다.|
|409|   GraphTopologyInUse| 하나 이상의 그래프 인스턴스에서 그래프 토폴로지를 참조합니다.|
|409|   OperationNotAllowedInState| 요청한 작업은 현재 상태에서 수행할 수 없습니다.|
|409|   ResourceValidationError|    참조된 리소스(예: 자산)가 올바른 상태가 아닙니다.|

## <a name="details"></a>세부 정보  

### <a name="graphtopologyget"></a>GraphTopologyGet

이 직접 메서드는 단일 그래프 토폴로지를 검색합니다.

#### <a name="request"></a>요청

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 엔터티를 찾음 | 200 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 엔터티를 찾을 수 없음 | 404 |  |
| 일반 서버 오류 | 500 범위 |  |

### <a name="graphtopologyset"></a>GraphTopologySet

지정된 이름이나 업데이트를 포함하는 기존 토폴로지가 없거나 같은 이름의 기존 토폴로지가 없는 경우 단일 토폴로지를 만듭니다.

주요 측면:

* 토폴로지를 참조하는 그래프가 없는 경우 토폴로지를 자유롭게 업데이트할 수 있습니다.
* 다음과 같은 경우 모든 참조 그래프가 비활성화되면 토폴로지를 자유롭게 업데이트할 수 있습니다.

    * 새로 추가된 매개 변수에 기본값이 있는 경우
    * 제거된 매개 변수가 그래프에서 참조되지 않는 경우
* 활성 그래프가 있으면 토폴로지를 업데이트할 수 없습니다.

#### <a name="request"></a>요청

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>응답

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>상태 코드

|조건  |상태 코드    |오류 코드 세부 정보|
|---|---|---|
기존 엔터티가 업데이트됨 |200|   해당 없음|
새 엔터티가 만들어짐  |201|   해당 없음|
일반 사용자 오류 |400 범위  ||
그래프 유효성 검사 오류 |400    |GraphValidationError|
모듈 유효성 검사 오류|   400 |ModuleValidationError|
일반 서버 오류   |500 범위  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

단일 그래프 토폴로지를 삭제합니다.

#### <a name="request"></a>요청

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 엔터티가 삭제됨 | 200 | 해당 없음 |
| 엔터티를 찾을 수 없음 | 204 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 하나 이상의 그래프 인스턴스에서 그래프 토폴로지를 참조합니다. | 409 | GraphTopologyInUse |
| 일반 서버 오류 | 500 범위 |  |

### <a name="graphtopologylist"></a>GraphTopologyList

필터 조건과 일치하는 모든 그래프 토폴로지의 목록을 검색합니다.

#### <a name="request"></a>요청

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>필터 지원

|작업 |필드    |연산자|
|---|---|---|
|$orderby|name  |asc|


#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| Success | 200 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 일반 서버 오류 | 500 범위 |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

단일 그래프 인스턴스를 검색합니다.

#### <a name="request"></a>요청

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 엔터티를 찾음 | 200 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 엔터티를 찾을 수 없음 | 404 |  |
| 일반 서버 오류 | 500 범위 |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

지정된 이름이나 업데이트를 포함하는 기존 토폴로지가 없거나 같은 이름의 기존 토폴로지가 없는 경우 단일 그래프 인스턴스를 만듭니다.

주요 측면:

* 그래프 인스턴스는 "비활성화" 상태에서 자유롭게 업데이트할 수 있습니다.

    * 그래프를 업데이트될 때마다 유효한지 다시 검사합니다.
* 그래프 인스턴스 업데이트는 그래프가 "비활성" 상태가 아닌 경우 부분적으로 제한됩니다.
* 그래프 인스턴스 업데이트는 활성 그래프에서 허용되지 않습니다.

#### <a name="request"></a>요청

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>응답

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 기존 엔터티가 업데이트됨 | 200 | 해당 없음 |
| 새 엔터티가 만들어짐 | 201 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 그래프 유효성 검사 오류 | 400 | GraphValidationError |
| 모듈 유효성 검사 오류 | 400 | ModuleValidationError |
| 리소스 유효성 검사 오류 | 409 | ResourceValidationError |
| 일반 서버 오류 | 500 범위 |  | 

### <a name="graphinstancedelete"></a>GraphInstanceDelete

단일 그래프 인스턴스를 삭제합니다.

주요 측면:

* 비활성화된 그래프만 삭제할 수 있습니다.

#### <a name="request"></a>요청

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 그래프가 삭제됨 | 200 | 해당 없음 |
| 그래프를 찾을 수 없음 | 204 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 그래프가 "중지됨" 상태가 아님 | 409 | OperationNotAllowedInState |
| 일반 서버 오류 | 500 범위 |  |

### <a name="graphinstancelist"></a>GraphInstanceList

GraphTopologyList와 유사합니다. 그래프 인스턴스를 열거하는 데 사용할 수 있습니다.
필터 조건과 일치하는 모든 그래프 인스턴스의 목록을 검색합니다.

#### <a name="request"></a>요청

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>필터 지원

|작업  |   필드|   연산자|
|---|---|---|
|$orderby|  name|   asc|

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| Success | 200 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 일반 서버 오류 | 500 범위 |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

단일 그래프 인스턴스를 활성화합니다. 

주요 측면

* 그래프가 활성화된 경우에만 메서드를 반환합니다. 
* 그래프는 활성화된 경우 "활성화 중" 상태로 간주됩니다.

    * 그래프의 나열/가져오기 작업은 적절한 상태로 그래프를 반환합니다.
* 멱등성:

    * "활성화 중" 상태에서 그래프를 시작하면 그래프가 비활성화된 것과 동일한 방식으로 동작합니다(즉, 그래프가 활성화될 때까지 호출 차단).
    * "활성" 상태에서 그래프를 활성화하면 즉시 반환됩니다.

#### <a name="request"></a>요청

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 그래프가 활성화됨 | 200 | 해당 없음 |
| 새 엔터티가 만들어짐 | 201 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 모듈 유효성 검사 오류 | 400 | ModuleValidationError |
| 리소스 유효성 검사 오류 | 409 | ResourceValidationError |
| 그래프가 비활성화 상태임 | 409 | OperationNotAllowedInState |
| 일반 서버 오류 | 500 범위 |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

단일 그래프 인스턴스를 비활성화합니다. 그래프를 비활성화하면 미디어 처리가 비활성화되고 해당하는 경우 에지에 저장된 모든 이벤트 및 미디어가 일시적으로 클라우드로 커밋됩니다.

주요 측면:

* 그래프가 비활성화된 경우에만 메서드를 반환합니다.
* 그래프는 비활성화된 경우 "비활성화 중" 상태로 간주됩니다.

    * 그래프의 나열/가져오기 작업은 적절한 상태로 그래프를 반환합니다.
    * 중지는 모든 미디어가 클라우드에 업로드된 경우에만 완료됩니다.
* 멱등성:

    * "비활성화 중" 상태에서 그래프를 비활성화하면 그래프가 비활성화된 것과 동일한 방식으로 동작합니다(즉, 그래프가 비활성화될 때까지 호출 차단).
    * "비활성" 상태에서 그래프를 비활성화하면 즉시 반환됩니다.

#### <a name="request"></a>요청

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": null
}
```

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 그래프가 활성화됨 | 200 | 해당 없음 |
| 새 엔터티가 만들어짐 | 201 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 그래프가 활성화 중 상태임 | 409 | OperationNotAllowedInState |
| 일반 서버 오류 | 500 범위 |  |

## <a name="next-steps"></a>다음 단계

[모듈 쌍 구성 스키마](module-twin-configuration-schema.md)
