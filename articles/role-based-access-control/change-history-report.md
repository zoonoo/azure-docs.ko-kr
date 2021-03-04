---
title: Azure RBAC 변경 내용에 대 한 활동 로그 보기
description: 지난 90 일 동안 azure RBAC (역할 기반 액세스 제어) 변경 내용에 대 한 활동 로그를 봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042136"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Azure RBAC 변경 내용에 대 한 활동 로그 보기

경우에 따라 감사 또는 문제 해결 목적으로 Azure RBAC (역할 기반 액세스 제어) 변경에 대 한 정보가 필요할 수 있습니다. 누군가가 구독 내에서 역할 할당 또는 역할 정의를 변경 하면 변경 내용이 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에 기록 됩니다. 활동 로그를 확인 하 여 지난 90 일간의 모든 Azure RBAC 변경 내용을 볼 수 있습니다.

## <a name="operations-that-are-logged"></a>기록되는 작업

활동 로그에 기록 되는 Azure RBAC 관련 작업은 다음과 같습니다.

- 역할 할당 만들기
- 역할 할당 삭제
- 사용자 지정 역할 정의 만들기 또는 업데이트
- 사용자 지정 역할 정의 삭제

## <a name="azure-portal"></a>Azure Portal

가장 손쉽게 시작할 수 있는 방법은 Azure Portal을 사용하여 활동 로그를 보는 것입니다. 다음 스크린샷은 활동 로그의 역할 할당 작업 예를 보여 줍니다. 또한 로그를 CSV 파일로 다운로드 하는 옵션도 포함 되어 있습니다.

![포털을 사용한 활동 로그 - 스크린샷](./media/change-history-report/activity-log-portal.png)

자세한 내용을 보려면 항목을 클릭 하 여 요약 창을 엽니다. 자세한 로그를 보려면 **JSON** 탭을 클릭 합니다.

![요약 창이 열려 있는 포털을 사용 하는 활동 로그-스크린샷](./media/change-history-report/activity-log-summary-portal.png)

포털에서 활동 로그에는 몇 가지 필터가 있습니다. Azure RBAC 관련 필터는 다음과 같습니다.

| Assert | 값 |
| --------- | --------- |
| 이벤트 범주 | <ul><li>관리</li></ul> |
| 작업 | <ul><li>역할 할당 만들기</li><li>역할 할당 삭제</li><li>사용자 지정 역할 정의 만들기 또는 업데이트</li><li>사용자 지정 역할 정의 삭제</li></ul> |

활동 로그에 대 한 자세한 내용은 [활동 로그를 확인 하 여 리소스에 대 한 작업 모니터링](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)을 참조 하세요.


## <a name="interpret-a-log-entry"></a>로그 항목 해석

JSON 탭, Azure PowerShell 또는 Azure CLI의 로그 출력에 많은 정보가 포함 될 수 있습니다. 로그 항목을 해석 하려고 할 때 찾을 수 있는 몇 가지 주요 속성은 다음과 같습니다. Azure PowerShell 또는 Azure CLI를 사용 하 여 로그 출력을 필터링 하는 방법은 다음 섹션을 참조 하십시오.

> [!div class="mx-tableFixed"]
> | 속성 | 예제 값 | 설명 |
> | --- | --- | --- |
> | 권한 부여: 작업 | Microsoft.Authorization/roleAssignments/write | 역할 할당 만들기 |
> |  | Microsoft.Authorization/roleAssignments/delete | 역할 할당 삭제 |
> |  | Microsoft.Authorization/roleDefinitions/write | 역할 정의 만들기 또는 업데이트 |
> |  | Microsoft.Authorization/roleDefinitions/delete | 역할 정의 삭제 |
> | 권한 부여: 범위 | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | 작업 범위 |
> | caller | admin@example.com<br/>ObjectId | 작업을 시작한 사람 |
> | eventTimestamp | 2021-03-01T22:07:41.126243 Z | 작업이 발생 한 시간 |
> | 상태: 값 | 시작됨<br/>성공<br/>실패 | 작업 상태 |

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 활동 로그를 보려면 [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) 명령을 사용합니다.

이 명령은 지난 7일 동안 구독에서 발생한 모든 역할 할당 변경 내용을 나열합니다.

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

이 명령은 지난 7일 동안 리소스 그룹에서 발생한 모든 역할 정의 변경 내용을 나열합니다.

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>로그 출력 필터링

로그 출력에 많은 정보가 포함 될 수 있습니다. 이 명령은 지난 7 일간 구독의 모든 역할 할당 및 역할 정의 변경 내용을 나열 하 고 출력을 필터링 합니다.

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

다음은 역할 할당을 만들 때 필터링 된 로그 출력의 예를 보여 줍니다.

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

서비스 주체를 사용 하 여 역할 할당을 만드는 경우 호출자 속성은 서비스 주체 개체 ID입니다. [AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) 를 사용 하 여 서비스 주체에 대 한 정보를 가져올 수 있습니다.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 활동 로그를 보려면 [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) 명령을 사용합니다.

이 명령은 3 월 1 일을 기준으로 7 일 후에 리소스 그룹의 활동 로그를 나열 합니다.

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

이 명령은 3 월 1 일부 터 인증 리소스 공급자에 대 한 활동 로그를 7 일 앞으로 표시 합니다.

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>로그 출력 필터링

로그 출력에 많은 정보가 포함 될 수 있습니다. 이 명령은 7 일을 전달 하는 구독의 모든 역할 할당 및 역할 정의 변경을 나열 하 고 출력을 필터링 합니다.

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

다음은 역할 할당을 만들 때 필터링 된 로그 출력의 예를 보여 줍니다.

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Azure Monitor 로그

[Azure Monitor 로그](../azure-monitor/logs/log-query-overview.md) 는 모든 azure 리소스에 대 한 azure RBAC 변경을 수집 하 고 분석 하는 데 사용할 수 있는 또 다른 도구입니다. Azure Monitor 로그에는 다음과 같은 이점이 있습니다.

- 복잡한 쿼리 및 로직 작성
- 경고, Power BI 및 기타 도구와 통합
- 장기 보존 기간 동안 데이터 저장
- 보안, 가상 머신, 사용자 지정과 같은 다른 로그와 상호 참조

기본적인 시작 단계는 다음과 같습니다.

1. [Log Analytics 작업 영역을 만듭니다](../azure-monitor/logs/quick-create-workspace.md).

1. 작업 영역에 대해 [활동 로그 분석 솔루션을 구성](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution)합니다.

1. [활동 로그를 봅니다](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). 활동 로그 분석 솔루션 개요 페이지로 이동 하는 빠른 방법은 **로그** 옵션을 클릭 하는 것입니다.

   ![포털의 Azure Monitor 로그 옵션](./media/change-history-report/azure-log-analytics-option.png)

1. 필요에 따라 [Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) 를 사용 하 여 로그를 쿼리하고 볼 수 있습니다. 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/logs/get-started-queries.md)을 참조 하세요.

다음은 대상 리소스 공급자가 구성한 새 역할 할당을 반환하는 쿼리입니다.

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

다음은 차트에 표시되는 역할 할당 변경 내용을 반환하는 쿼리입니다.

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![고급 분석 포털을 사용한 활동 로그 - 스크린샷](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>다음 단계
* [활동 로그를 확인 하 여 리소스에 대 한 작업 모니터링](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Azure 활동 로그를 사용 하 여 구독 활동 모니터링](../azure-monitor/essentials/platform-logs-overview.md)