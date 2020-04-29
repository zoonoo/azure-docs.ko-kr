---
title: Log Analytics 경고 REST API 사용
description: Log Analytics 경고 REST API를 사용 하 여 Log Analytics의 일부인 Log Analytics에서 경고를 만들고 관리할 수 있습니다.  이 문서에서는 다음 작업을 수행하기 위한 API 및 여러 예제의 세부 정보를 제공합니다.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77665003"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>REST API로 Log Analytics에서 경고 규칙 만들기 및 관리 

Log Analytics 경고 REST API를 사용하여 Log Analytics에서 경고를 만들고 관리할 수 있습니다.  이 문서에서는 다음 작업을 수행하기 위한 API 및 여러 예제의 세부 정보를 제공합니다.

> [!IMPORTANT]
> [앞서 발표](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)했 듯이 *2019 년 6 월 1 일* 이후에 생성 된 log Analytics 작업 영역은 azure ScheduledQueryRules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), [azure Resource Mananger 템플릿](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) 및 [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell) **만** 사용 하 여 경고 규칙을 관리할 수 있습니다. 고객은 이전 작업 영역에 대 한 기본 [설정 된 경고 규칙 관리 수단](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) 을 쉽게 전환 하 여 Azure Monitor scheduledQueryRules를 기본값으로 활용할 수 있으며, 기본 PowerShell cmdlet을 사용 하는 기능, 규칙에서 lookback 기간 증가, 별도의 리소스 그룹 또는 구독에서 규칙 생성 등의 많은 [새로운 이점을](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) 누릴 수 있습니다.

Log Analytics Search REST API는 RESTful이며 Azure Resource Manager REST API를 통해 액세스할 수 있습니다. 이 문서에서 API가 Azure Resource Manager API를 호출하여 단순화하는 공개 소스 명령줄 도구인 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용하여 PowerShell 명령줄에서 액세스하는 예제를 찾을 수 있습니다. ARMClient 및 PowerShell의 사용은 Log Analytics 검색 API에 액세스하는 다양한 옵션 중 하나입니다. 이러한 도구를 사용하면 RESTful Azure Resource Manager API를 활용하여 Log Analytics 작업 영역을 호출하고, 이 작업 영역 내에서 검색 명령을 수행할 수 있습니다. API은 JSON 형식으로 검색 결과를 출력하여 다양한 프로그래밍 방식으로 검색 결과를 사용하게 됩니다.

## <a name="prerequisites"></a>사전 요구 사항
현재 Log Analytics에 저장된 검색을 사용해서만 경고를 만들 수 있습니다.  자세한 내용은 [로그 검색 REST API](../../azure-monitor/log-query/log-query-overview.md) 를 참조하세요.

## <a name="schedules"></a>일정
저장된 검색은 하나 이상의 일정을 가질 수 있습니다. 일정은 검색이 실행되는 빈도 및 조건이 식별되는 기간을 정의합니다.
일정은 다음 표의 속성을 가집니다.

| 속성 | Description |
|:--- |:--- |
| 간격 |검색이 실행되는 빈도입니다. 분 단위로 측정됩니다. |
| QueryTimeSpan |조건이 평가되는 시간 간격입니다. 간격보다 크거나 같아야 합니다. 분 단위로 측정됩니다. |
| 버전 |사용 중인 API 버전입니다.  현재 항상 1로 설정해야 합니다. |

예를 들어 간격이 15 분이고 Timespan이 30 분인 이벤트 쿼리를 고려합니다. 이 경우 쿼리는 매 15분마다 실행되며 조건이 30분의 기간 동안 계속 True로 확인되었으면 경고가 트리거될 수 있습니다.

### <a name="retrieving-schedules"></a>일정 검색
Get 메서드를 사용하여 저장된 검색에 대한 모든 일정을 검색합니다.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

일정 ID와 Get 메서드를 사용하여 저장된 검색에 대한 특정 일정을 검색합니다.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

다음은 일정에 대한 샘플 응답입니다.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>일정 만들기
고유 일정 ID와 Put 메서드를 사용하여 새 일정을 만듭니다.  서로 다른 저장 된 검색에 연결 된 경우에도 두 일정의 ID는 같을 수 없습니다.  Log Analytics 콘솔에서 일정을 만드는 경우 일정 ID에 대해 GUID가 생성됩니다.

> [!NOTE]
> Log Analytics API를 사용하여 만든 저장된 모든 검색, 일정 및 작업의 이름은 소문자여야 합니다.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>일정 편집
동일한 저장된 검색에 대해 기존 일정 ID와 Put 메서드를 사용하여 해당 일정을 수정합니다. 예를 들어 아래 일정을 사용할 수 없습니다. 요청의 본문에는 일정의 *etag* 가 포함 되어야 합니다.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>일정 삭제
일정 ID와 함께 Delete 메서드를 사용하여 일정을 삭제합니다.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>작업
일정이 여러 작업을 가질 수 있습니다. 작업은 메일 보내기 또는 Runbook 시작과 같은 하나 이상의 수행할 프로세스를 정의하거나 검색 결과가 일부 조건과 일치하는 경우를 결정하는 임계값을 정의할 수 있습니다.  일부 작업은 임계값을 만족할 때 프로세스가 수행되도록 정의합니다.

모든 작업은 다음 표의 속성을 가집니다.  서로 다른 유형의 경고는 아래에 설명하는 서로 다른 추가 속성을 가집니다.

| 속성 | Description |
|:--- |:--- |
| `Type` |작업의 유형입니다.  현재 가능한 값은 경고 및 웹후크입니다. |
| `Name` |경고에 대한 표시 이름입니다. |
| `Version` |사용 중인 API 버전입니다.  현재 항상 1로 설정해야 합니다. |

### <a name="retrieving-actions"></a>작업 검색

Get 메서드를 사용하여 일정에 대한 모든 작업을 검색합니다.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

작업 ID와 함께 Get 메서드를 사용하여 일정에 대한 특정 작업을 검색합니다.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>작업 만들기 또는 편집
일정에 고유한 작업 ID와 Put 메서드를 사용하여 새 작업을 만듭니다.  Log Analytics 콘솔에서 작업을 만드는 경우 GUID는 작업 ID에 대한 것입니다.

> [!NOTE]
> Log Analytics API를 사용하여 만든 저장된 모든 검색, 일정 및 작업의 이름은 소문자여야 합니다.

동일한 저장된 검색에 대해 기존 작업 ID와 Put 메서드를 사용하여 해당 일정을 수정합니다.  요청 본문에는 일정의 etag가 포함되어야 합니다.

새 작업을 만들기 위한 요청 형식은 작업 유형에 따라 다르므로 이러한 예제를 아래 섹션에 제공합니다.

### <a name="deleting-actions"></a>작업 삭제

작업 ID와 함께 Delete 메서드를 사용하여 작업을 삭제합니다.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>경고 작업
일정은 경고 작업을 한 개만 가져야 합니다.  경고 작업은 다음 표의 섹션 중 하나 이상을 가집니다.  아래에서 각 섹션을 자세히 설명합니다.

| 단원 | Description | 사용 |
|:--- |:--- |:--- |
| 임계값 |작업이 실행되기 위한 조건입니다.| Azure로 확장되기 이전 또는 이후에 모든 경고에 필요합니다. |
| 심각도 |트리거되는 경우 경고를 분류하는 데 사용되는 레이블| Azure로 확장되기 이전 또는 이후에 모든 경고에 필요합니다. |
| 표시 안 함 |경고의 알림을 중지하는 옵션입니다. | 모든 경고(아직 Azure로 확장되지 않은 경고나 Azure로 확장된 경고)에서 선택 사항입니다. |
| 작업 그룹 |이메일, SMS, 음성 통화, 웹후크, Automation Runbook, ITSM 커넥터 등과 같이 필요한 작업이 지정된 Azure ActionGroup의 ID입니다.| 경고가 Azure로 확장되면 필요함|
| 사용자 지정 동작|ActionGroup에서 select 동작에 대한 표준 출력 수정| 경고가 Azure로 확장된 후 모든 경고에 대한 옵션을 사용할 수 있습니다. |

### <a name="thresholds"></a>임계값
경고 작업은 임계값을 한 개만 가져야 합니다.  저장된 검색의 결과가 해당 검색과 연결된 작업의 임계값과 일치하는 경우 해당 작업의 다른 프로세스가 실행됩니다.  작업은 임계값을 포함하지 않은 다른 유형의 작업과 함께 사용할 수 있도록 하는 임계값만 포함할 수 있습니다.

임계값은 다음 표의 속성을 가집니다.

| 속성 | Description |
|:--- |:--- |
| `Operator` |임계값 비교를 위한 연산자입니다. <br> gt = 보다 큰 <br>  lt = 보다 작은 |
| `Value` |임계값에 대한 값입니다. |

예를 들어 간격이 15 분이고 Timespan이 30 분이고 임계값이 10보다 큰 이벤트 쿼리를 고려합니다. 이 경우 쿼리는 매 15분마다 실행되며 경고는 30분의 기간 동안 생성된 이벤트 10개를 반환하면 경고가 트리거될 수 있습니다.

다음은 임계값만 가진 작업에 대한 샘플 응답입니다.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

고유 작업 ID와 Put 메서드를 사용하여 일정에 대한 새 임계값 작업을 만듭니다.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

고유 작업 ID와 Put 메서드를 사용하여 일정에 대한 임계값 작업을 수정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>심각도
Log Analytics를 통해 쉽게 관리하고 심사할 수 있도록 경고를 범주로 분류할 수 있습니다. 정의된 경고 심각도는 정보 제공, 경고 및 중요입니다. 이러한 항목은 다음으로 Azure Alerts의 정규화된 심각도 규모로 매핑됩니다.

|Log Analytics 심각도 수준  |Azure Alerts 심각도 수준  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

다음은 임계값 및 심각도만 가진 작업에 대한 샘플 응답입니다. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

고유한 작업 ID와 함께 Put 메서드를 사용하여 심각도로 일정에 대한 새 작업을 만듭니다.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

고유 작업 ID와 Put 메서드를 사용하여 일정에 대한 심각도 작업을 수정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>표시 안 함
임계값에 도달하거나 임계값이 초과될 때마다 Log Analytics 기반 쿼리 경고가 생성됩니다. 그러므로 쿼리에 내재된 논리에 따라 일련의 간격으로 경고가 생성되고, 그로 인해 알림도 계속 전송될 수 있습니다. 이러한 상황을 방지하려는 경우 사용자는 알림 규칙에 대해 알림이 두 번째로 생성되기 전에 정해진 시간 동안 기다리도록 Log Analytics에 명령하는 표시 안 함 옵션을 설정할 수 있습니다. 예를 들어 표시 안 함 시간을 30분으로 설정하면 경고가 처음 생성되어 구성된 알림이 전송된 후 30분 동안 기다렸다가 경고 규칙에 대한 알림이 다시 사용됩니다. 이 중간 기간 동안에도 경고 규칙은 계속 실행되며, 이 기간 동안 경고 규칙이 실행된 횟수에 관계없이 Log Analytics에서 지정된 시간 동안 알림만 표시하지 않는 것입니다.

Log Analytics 경고 규칙의 표시 안 함 속성은 *Throttling* 값을 사용하여 지정하며 표시 안 함 기간은 *DurationInMinutes* 값을 사용하여 지정합니다.

아래에는 임계값, 심각도 및 표시 안 함 속성만 포함된 작업의 샘플 응답이 나와 있습니다.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

고유한 작업 ID와 함께 Put 메서드를 사용하여 심각도로 일정에 대한 새 작업을 만듭니다.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

고유 작업 ID와 Put 메서드를 사용하여 일정에 대한 심각도 작업을 수정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>작업 그룹
Azure에서 모든 경고는 작업을 처리하기 위한 기본 메커니즘으로 작업 그룹을 사용합니다. 작업 그룹을 사용하여 작업을 한 번 지정한 다음, 작업 그룹을 Azure 전체에서 여러 경고에 연결할 수 있습니다. 필요가 없으면, 반복적으로 동일한 작업을 반복하고 다시 선언합니다. 작업 그룹은 이메일, SMS, 음성 통화, ITSM 연결, Automation Runbook, 웹후크 URI 등을 포함하는 여러 작업을 지원합니다. 

자신의 경고를 Azure로 확장한 사용자의 경우 일정은 이제 경고를 만들 수 있도록 임계값과 함께 전달된 작업 그룹 세부 정보가 있어야 합니다. 이메일 세부 정보, 웹후크 URL, Runbook Automation 세부 정보 및 기타 작업은 경고를 만들기 전에 먼저 작업 그룹 내에서 정의되어야 합니다. 하나의 항목은 포털에서 [Azure Monitor에서 작업 그룹](../../azure-monitor/platform/action-groups.md)을 만들거나 [작업 그룹 API](https://docs.microsoft.com/rest/api/monitor/actiongroups)를 사용할 수 있습니다.

경고에 작업 그룹의 연결을 추가하려면 경고 정의에 작업 그룹의 고유한 Azure Resource Manager ID를 지정합니다. 아래에서 샘플 그림이 제공됩니다.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

고유한 작업 ID와 Put 메서드를 사용하여 일정에 대한 기존 작업 그룹을 연결합니다.  다음은 사용의 샘플 그림입니다.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

고유 작업 ID와 Put 메서드를 사용하여 일정에 대해 연결된 작업 그룹을 수정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>사용자 지정 동작
기본적으로 작업은 알림에 대한 표준 템플릿 및 형식을 따릅니다. 하지만 사용자는 작업 그룹에 의해 제어되는 경우에도 일부 작업을 사용자 지정할 수 있습니다. 현재 사용자 지정은 이메일 제목 및 웹후크 페이로드에 가능합니다.

##### <a name="customize-e-mail-subject-for-action-group"></a>작업 그룹에 대한 이메일 제목 사용자 지정
기본적으로 경고에 대한 이메일 제목은 `<WorkspaceName>`에 대한 경고 알림 `<AlertName>`입니다. 하지만 받은 편지함에서 필터 규칙을 쉽게 사용하도록 단어 또는 태그를 명시할 수 있도록 사용자 지정할 수 있습니다. 사용자 지정 이메일 헤더 세부 정보는 아래 예제에서와 같이 ActionGroup 세부 정보와 함께 전송되어야 합니다.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

고유한 작업 ID와 Put 메서드를 사용하여 일정에 대한 사용자 지정과 기존 작업 그룹을 연결합니다.  다음은 사용의 샘플 그림입니다.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

고유 작업 ID와 Put 메서드를 사용하여 일정에 대해 연결된 작업 그룹을 수정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>작업 그룹에 대한 웹후크 페이로드 사용자 지정
기본적으로 로그 분석에 대한 작업 그룹을 통해 전송되는 웹후크는 고정된 구조를 가집니다. 하지만 하나의 항목은 웹후크 엔드포인트의 요구 사항을 충족하기 위해 지원되는 특정 변수를 사용하여 JSON 페이로드를 사용자 지정할 수 있습니다. 자세한 내용은 [로그 경고 규칙에 대한 웹후크 작업](../../azure-monitor/platform/alerts-log-webhook.md)을 참조하세요. 

사용자 지정 웹후크 세부 정보를 ActionGroup 세부 정보와 함께 보내야 하며 다음 샘플에서와 같이 작업 그룹 내에서 지정된 모든 웹후크 URI에 적용됩니다.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

고유한 작업 ID와 Put 메서드를 사용하여 일정에 대한 사용자 지정과 기존 작업 그룹을 연결합니다.  다음은 사용의 샘플 그림입니다.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

고유 작업 ID와 Put 메서드를 사용하여 일정에 대해 연결된 작업 그룹을 수정합니다.  요청 본문에는 작업의 etag가 포함되어야 합니다.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>다음 단계

* Log Analytics에서 [REST API를 사용하여 로그 검색을 수행](../../azure-monitor/log-query/log-query-overview.md) 합니다.
* [Azure monitor의 로그 경고](../../azure-monitor/platform/alerts-unified-log.md) 에 대해 알아보기
* [Azure monitor에서 로그 경고 규칙을 생성, 편집 또는 관리](../../azure-monitor/platform/alerts-log.md) 하는 방법

