---
title: Azure Digital Twins에서 UDF를 디버그하는 방법 | Microsoft Docs
description: Azure Digital Twins에서 UDF를 디버그하는 방법에 대한 지침
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: stefanmsft
ms.openlocfilehash: ac7664e94c6e02ab90dbb1b32a54c8234614afe2
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636274"
---
# <a name="how-to-debug-issues-with-user-defined-functions-in-azure-digital-twins"></a>Azure Digital Twins에서 사용자 정의 함수를 사용하여 문제를 디버그하는 방법

이 문서에서는 사용자 정의 함수를 진단하는 방법을 요약합니다. 그런 다음, 해당 함수를 사용하여 작업할 때 발생하는 가장 일반적인 시나리오를 식별합니다.

## <a name="debug-issues"></a>문제 디버그

Azure Digital Twins 인스턴스 내에서 발생하는 문제를 진단하는 방법을 알면 문제, 문제점의 원인 및 해결 방법을 효과적으로 식별하는 데 도움이 됩니다.

### <a name="enable-log-analytics-for-your-instance"></a>인스턴스에 로그 분석 사용

Azure Digital Twins 인스턴스에 대한 로그 및 메트릭은 Azure Monitor를 통해 표시됩니다. 다음 설명서에서는 [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md), [Azure CLI](../log-analytics/log-analytics-quick-create-workspace-cli.md) 또는 [PowerShell](../log-analytics/log-analytics-quick-create-workspace-posh.md)을 통해 [Azure Log Analytics](../log-analytics/log-analytics-queries.md) 작업 영역을 만들었다고 가정합니다.

> [!NOTE]
> **Log Analytics**에 이벤트를 처음 보낼 때 5분 지연을 경험할 수 있습니다.

Azure Portal, Azure CLI 또는 PowerShell을 통해 Azure Digital Twins 인스턴스에 진단 설정을 사용하려면 ["Azure 리소스에서 로그 데이터 수집 및 사용"](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 문서를 읽으세요. 모든 로그 범주, 메트릭 및 Azure Log Analytics 작업 영역을 선택해야 합니다.

### <a name="trace-sensor-telemetry"></a>센서 원격 분석 추적

Azure Digital Twins 인스턴스에 대해 진단 설정을 사용하도록 설정하고, 모든 로그 범주를 선택하고, 로그를 Azure Log Analytics에 보내는지 확인하십시오.

센서 원격 분석 메시지를 해당 로그에 일치시키려면 보내는 이벤트 데이터에 대한 상관 관계 ID를 지정할 수 있습니다. 이렇게 하려면 `x-ms-client-request-id` 속성을 GUID로 설정합니다.

원격 분석을 보낸 후 Azure Log Analytics를 열고 설정된 상관 관계 ID를 사용하여 로그를 쿼리합니다.

```Kusto
AzureDiagnostics
| where CorrelationId = 'YOUR_CORRELATION_IDENTIFIER'
```

| 쿼리 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | 이벤트 데이터에 대해 지정된 상관 관계 ID |

사용자 정의 함수를 기록하면 해당 로그는 Azure Log Analytics 인스턴스에 범주 `UserDefinedFunction`으로 나타납니다. 이들을 검색하려면 Azure Log Analytics에 다음 쿼리 조건을 입력합니다.

```Kusto
AzureDiagnostics
| where Category = 'UserDefinedFunction'
```

강력한 쿼리 작업에 대한 자세한 내용은 [쿼리 시작하기](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)를 참조하세요.

## <a name="identify-common-issues"></a>일반적인 문제 식별

솔루션 문제를 해결할 때 일반적인 문제의 진단과 식별이 모두 중요합니다. 사용자 정의 함수를 개발할 때 발생하는 여러 가지 일반적인 문제를 아래에 요약합니다.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="ensure-a-role-assignment-was-created"></a>역할 할당이 만들어졌는지 확인

관리 API 내에서 만들어진 역할 할당이 없는 경우 사용자 정의 함수가 알림 보내기, 메타데이터 검색 및 토폴로지 내에서 계산된 값 설정 등과 같은 작업을 수행하기 위한 액세스 권한을 갖지 못하게 됩니다.

관리 API를 통해 사용자 정의 함수에 대한 역할 할당이 있는지 확인합니다.

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_USER_DEFINED_FUNCTION_ID* | 역할 할당을 검색할 사용자 정의 함수의 ID|

역할 할당이 검색되지 않은 경우 [사용자 정의 함수에 대한 역할 함수를 만드는 방법](./how-to-user-defined-functions.md)에 관한 이 문서를 따르십시오.

### <a name="check-if-the-matcher-will-work-for-a-sensors-telemetry"></a>선택기가 센서의 원격 분석에 대해 작동할지 확인

Azure Digital Twins 인스턴스의 관리 API에 대해 다음 호출을 사용하여 지정된 선택기가 지정된 센서에 적용되는지 결정할 수 있습니다.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | 평가하려는 검사기의 ID |
| *YOUR_SENSOR_IDENTIFIER* | 평가하려는 센서의 ID |

응답:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-will-trigger"></a>센서가 트리거할 내용 확인

Azure Digital Twins의 관리 API에 대해 다음 호출을 사용하여 지정된 센서의 수신 원격 측정에서 트리거할 사용자 정의 함수의 ID를 결정할 수 있습니다.

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | 원격 측정을 보낼 센서의 ID |

응답:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>알림 수신 문제

트리거한 사용자 정의 함수 내에서 알림을 수신하지 않는 경우 토폴로지 개체 형식 매개 변수가 사용하는 ID의 형식과 일치하는지 확인합니다.

**잘못 된** 예제:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

이 시나리오는 지정된 토폴로지 개체 형식이 '공간'일 때 사용한 ID가 센서를 참조하기 때문에 발생합니다.

**올바른** 예제:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

이 문제에 빠져들지 않는 가장 쉬운 방법은 메타데이터 개체에 대해 `Notify` 메서드를 사용하는 것입니다.

예제:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>일반적인 진단 예외

진단 설정을 사용하도록 설정하면 이러한 일반적인 예외가 발생할 수 있습니다.

1. **제한**: 사용자 정의 함수는 [서비스 제한](./concepts-service-limits.md) 문서에 요약한 실행 속도 제한을 초과하는 경우 제한됩니다. 제한은 제한이 만료될 때까지 성공적으로 실행하는 추가 작업을 수반하지 않습니다.

1. **데이터 없음**: 사용자 정의 함수가 존재하지 않는 메타데이터에 액세스를 시도하면 작업이 실패합니다.

1. **권한 없음**: 사용자 정의 함수가 역할 할당 집합을 포함하지 않거나 토폴로지에서 특정 메타데이터에 액세스할 권한이 부족한 경우 작업이 실패합니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에서 [모니터링 및 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)를 사용하도록 설정하는 방법을 알아봅니다.
