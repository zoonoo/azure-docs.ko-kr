---
title: Azure 리소스 로그
description: Azure Monitor에서 Log Analytics 작업 영역으로 Azure 리소스 로그를 스트리밍하는 방법에 대해 알아봅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 492aae69895d62c784d15cd77405d0c52ec13e3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84946962"
---
# <a name="azure-resource-logs"></a>Azure 리소스 로그
Azure 리소스 로그는 Azure 리소스 내에서 수행 된 작업에 대 한 통찰력을 제공 하는 [플랫폼 로그](platform-logs-overview.md) 입니다. 리소스 로그의 콘텐츠는 Azure 서비스 및 리소스 유형에 따라 달라 집니다. 리소스 로그는 기본적으로 수집 되지 않습니다. 각 Azure 리소스에 대 한 진단 설정을 만들어 [Azure Monitor 로그](data-platform-logs.md), Event Hubs azure 외부에서 전달 하는 데 사용 하는 Log Analytics 작업 영역에 리소스 로그를 전송 하거나 보관을 위해 Azure Storage 합니다.

진단 설정 만들기에 대 한 자세한 내용은 진단 설정 만들기를 참조 하 여 [다양 한 대상에 플랫폼 로그 및 메트릭을 전송](diagnostic-settings.md) 하는 방법에 대 한 자세한 내용은 Azure Policy [Azure Policy를 사용 하 여 규모에 따라 Azure Monitor 배포](deploy-scale.md) 를 참조 하세요.

## <a name="send-to-log-analytics-workspace"></a>Log Analytics 작업 영역으로 보내기
 다음을 포함 하는 [Azure Monitor 로그](data-platform-logs.md) 의 기능을 사용할 수 있도록 리소스 로그를 Log Analytics 작업 영역으로 보냅니다.

- Azure Monitor에서 수집한 다른 모니터링 데이터와 리소스 로그 데이터의 상관 관계를 바꿉니다.
- 여러 Azure 리소스, 구독 및 테 넌 트의 로그 항목을 분석을 위해 하나의 위치로 통합 합니다.
- 로그 쿼리를 사용 하 여 복잡 한 분석을 수행 하 고 로그 데이터에 대 한 심층 통찰력을 얻습니다.
- 복잡 한 경고 논리를 사용 하 여 로그 경고를 사용 합니다.

리소스 로그를 Log Analytics 작업 영역으로 보내는 [진단 설정을 만듭니다](diagnostic-settings.md) . 이 데이터는 [Azure Monitor 로그의 구조](../log-query/logs-structure.md)에 설명 된 대로 테이블에 저장 됩니다. 리소스 로그에서 사용 하는 테이블은 리소스에서 사용 하는 컬렉션 유형에 따라 달라 집니다.

- Azure 진단-기록 된 모든 데이터는 _Azurediagnostics_ 테이블에 저장 됩니다.
- 리소스 관련 데이터는 리소스의 각 범주에 대 한 개별 테이블에 기록 됩니다.

### <a name="azure-diagnostics-mode"></a>Azure 진단 모드 
이 모드에서는 모든 진단 설정의 모든 데이터가 _Azurediagnostics_ 테이블에 수집 됩니다. 이는 오늘날 대부분의 Azure 서비스에서 사용 하는 레거시 방법입니다. 여러 리소스 형식이 동일한 테이블에 데이터를 보내기 때문에 해당 스키마는 수집 되는 다른 모든 데이터 형식의 스키마 상위 집합입니다.

진단 설정이 다음 데이터 형식에 대해 동일한 작업 영역에서 수집 되는 다음 예를 살펴보세요.

- 서비스 1의 감사 로그 (A, B, C 열로 구성 된 스키마 포함)  
- 서비스 1의 오류 로그 (열 D, E 및 F 열로 구성 된 스키마 포함)  
- 서비스 2의 감사 로그 (G, H, I 열로 구성 된 스키마 포함)  

AzureDiagnostics 테이블은 다음과 같이 표시 됩니다.  

| ResourceProvider    | 범주     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft Service1 | AuditLogs    | x | y1 | z1 |    |    |    |    |    |    |
| Microsoft Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | 3 |
| Microsoft Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>리소스 관련
이 모드에서는 진단 설정에서 선택한 각 범주에 대해 선택한 작업 영역의 개별 테이블이 생성 됩니다. 이 방법은 로그 쿼리에서 데이터를 더 쉽게 사용할 수 있도록 하 고, 스키마 및 해당 구조를 보다 효율적으로 검색 하 고, 수집 대기 시간과 쿼리 시간 모두에서 성능을 향상 시키고, 특정 테이블에 대해 RBAC 권한을 부여 하는 기능을 제공 하기 때문에 권장 됩니다. 모든 Azure 서비스는 궁극적으로 리소스 특정 모드로 마이그레이션됩니다. 

위의 예에서는 세 개의 테이블을 만듭니다.
 
- *Service1AuditLogs* 테이블은 다음과 같습니다.

    | 리소스 공급자 | 범주 | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- *Service1ErrorLogs* 테이블은 다음과 같습니다.  

    | 리소스 공급자 | 범주 | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- *Service2AuditLogs* 테이블은 다음과 같습니다.  

    | 리소스 공급자 | 범주 | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 | k3 | 3|
    | ... |



### <a name="select-the-collection-mode"></a>컬렉션 모드를 선택 합니다.
대부분의 Azure 리소스는 사용자를 제공 하지 않고 **Azure 진단** 또는 **리소스 특정 모드** 에서 작업 영역에 데이터를 기록 합니다. 사용 모드에 대 한 자세한 내용은 [각 서비스에 대 한 설명서](diagnostic-logs-schema.md) 를 참조 하세요. 모든 Azure 서비스는 궁극적으로 리소스 특정 모드를 사용 합니다. 이러한 전환의 일부로 일부 리소스는 진단 설정에서 모드를 선택할 수 있습니다. 데이터를 더 쉽게 관리할 수 있도록 하 고 나중에 복잡 한 마이그레이션을 방지 하는 데 도움이 될 수 있으므로 모든 새 진단 설정에 대해 리소스 특정 모드를 지정 합니다.
  
   ![진단 설정 모드 선택기](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> 현재는 Azure Portal에서 진단 설정을 구성 하는 경우에만 **Azure 진단** 및 **리소스 특정** 모드를 선택할 수 있습니다. CLI, PowerShell 또는 Rest API를 사용 하 여 설정을 구성 하는 경우 **Azure 진단**이 기본값으로 사용 됩니다.

기존 진단 설정을 리소스 특정 모드로 수정할 수 있습니다. 이 경우 이미 수집 된 데이터는 작업 영역의 보존 설정에 따라 제거 될 때까지 _Azurediagnostics_ 테이블에 남아 있습니다. 새 데이터는 전용 테이블에 수집 됩니다. [Union](https://docs.microsoft.com/azure/kusto/query/unionoperator) 연산자를 사용 하 여 두 테이블에서 데이터를 쿼리 합니다.

리소스 특정 모드를 지 원하는 Azure 서비스에 대 한 공지를 보려면 [Azure 업데이트](https://azure.microsoft.com/updates/) 블로그를 계속 시청 하세요.

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics의 열 제한
Azure Monitor 로그의 모든 테이블에 대 한 500 속성 제한이 있습니다. 이 한도에 도달 하면 첫 번째 500 외부의 속성을 포함 하는 데이터를 포함 하는 모든 행이 수집 시 삭제 됩니다. *Azurediagnostics* 테이블은 모든 Azure 서비스에 대 한 속성을 포함 하므로 특히이 제한에 취약 합니다.

여러 서비스에서 리소스 로그를 수집 하는 경우 _Azurediagnostics_ 는이 제한을 초과할 수 있으며 데이터가 누락 됩니다. 모든 Azure 서비스에서 리소스 특정 모드를 지원할 때까지 여러 작업 영역에 쓰도록 리소스를 구성 하 여 500 열 제한에 도달할 가능성을 줄여야 합니다.

### <a name="azure-data-factory"></a>Azure 데이터 팩터리
Azure Data Factory는 자세한 로그 집합으로 인해 많은 열을 작성 하는 것으로 알려진 서비스 이며,이로 인해 _Azurediagnostics_ 가 제한을 초과 하 게 됩니다. 리소스 특정 모드를 사용 하기 전에 구성 된 모든 진단 설정의 경우 모든 작업에 대해 고유 하 게 명명 된 모든 사용자 매개 변수에 대해 만들어진 새 열이 있습니다. 활동 입력 및 출력의 자세한 특성으로 인해 더 많은 열이 생성 됩니다.
 
가능한 한 빨리 리소스 특정 모드를 사용 하려면 로그를 마이그레이션해야 합니다. 즉시 수행할 수 없는 경우에는 작업 영역에서 수집 되는 다른 로그 유형에 영향을 주는 로그를 최소화 하기 위해 Azure Data Factory 로그를 자체 작업 영역으로 분리 하는 것이 좋습니다.


## <a name="send-to-azure-event-hubs"></a>Azure Event Hubs에 보내기
Azure 외부 (예: 타사 SIEM 또는 기타 log analytics 솔루션)에 전송 하려면 리소스 로그를 이벤트 허브로 보냅니다. Event hubs의 리소스 로그는 `records` 각 페이로드의 레코드를 포함 하는 요소를 사용 하 여 JSON 형식으로 사용 됩니다. 이 스키마는 [Azure 리소스 로그에 대 한 공통 및 서비스별 스키마](resource-logs-schema.md)에 설명 된 리소스 유형에 따라 달라 집니다.

다음은 리소스 로그에 대 한 Event Hubs의 샘플 출력 데이터입니다.

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Azure Storage에 보내기
보관을 위해 Azure 저장소에 리소스 로그를 보냅니다. 진단 설정을 만들면 저장소 계정에서 사용 되는 로그 범주 중 하나에서 이벤트가 발생 하는 즉시 저장소 계정에 저장소 컨테이너가 만들어집니다. 컨테이너 내의 blob는 다음과 같은 명명 규칙을 사용 합니다.

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

예를 들어 네트워크 보안 그룹에 대 한 blob의 이름은 다음과 유사할 수 있습니다.

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

각 PT1H.json Blob은 Blob URL에 지정된 시간 내에서 발생한 이벤트의 JSON Blob을 포함합니다(예: h=12). 현재 시간 동안 이벤트는 발생하는 순서대로 PT1H.json 파일에 추가됩니다. 리소스 로그 이벤트는 시간당 개별 blob으로 분할 되므로 분 값 (m = 00)은 항상 00입니다.

파일의 PT1H.js내에서 각 이벤트는 다음과 같은 형식으로 저장 됩니다. 이는 [리소스 로그 스키마](diagnostic-logs-schema.md)에 설명 된 대로 공통 최상위 스키마를 사용 하지만 각 Azure 서비스에 대해 고유 합니다.

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> 플랫폼 로그는 [JSON 줄](http://jsonlines.org/)을 사용 하 여 blob 저장소에 기록 됩니다. 여기서 각 이벤트는 줄 이며 줄 바꿈 문자는 새 이벤트를 나타냅니다. 이 형식은 11 월 2018에 구현 되었습니다. 이 날짜 이전에는 [저장소 계정에 보관 된 Azure Monitor 플랫폼 로그에 대 한 형식 변경 준비](resource-logs-blob-format.md)에 설명 된 대로 blob 저장소에 레코드의 json 배열로 로그가 기록 되었습니다.


## <a name="next-steps"></a>다음 단계

* [자세한 내용은 리소스 로그를 참조](platform-logs-overview.md)하세요.
* [다른 대상에 플랫폼 로그 및 메트릭을 전송 하는 진단 설정을 만듭니다](diagnostic-settings.md).
