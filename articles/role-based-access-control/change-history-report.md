---
title: Azure RBAC 변경 내용에 대 한 활동 로그 보기
description: 지난 90 일간 azure 리소스에 대 한 azure RBAC (역할 기반 액세스 제어) 변경 내용에 대 한 활동 로그를 봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68b1cdede5fddd0bf74571da1924e0059a21d3af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791812"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Azure RBAC 변경 내용에 대 한 활동 로그 보기

경우에 따라 감사 또는 문제 해결 목적으로 Azure RBAC (역할 기반 액세스 제어) 변경에 대 한 정보가 필요할 수 있습니다. 누군가가 구독 내에서 역할 할당 또는 역할 정의를 변경 하면 변경 내용이 [Azure 활동 로그](../azure-monitor/platform/platform-logs-overview.md)에 기록 됩니다. 활동 로그를 확인 하 여 지난 90 일간의 모든 Azure RBAC 변경 내용을 볼 수 있습니다.

## <a name="operations-that-are-logged"></a>기록되는 작업

활동 로그에 기록 되는 Azure RBAC 관련 작업은 다음과 같습니다.

- 역할 할당 만들기
- 역할 할당 삭제
- 사용자 지정 역할 정의 만들기 또는 업데이트
- 사용자 지정 역할 정의 삭제

## <a name="azure-portal"></a>Azure portal

가장 손쉽게 시작할 수 있는 방법은 Azure Portal을 사용하여 활동 로그를 보는 것입니다. 다음 스크린샷은 활동 로그의 역할 할당 작업 예를 보여 줍니다. 또한 로그를 CSV 파일로 다운로드 하는 옵션도 포함 되어 있습니다.

![포털을 사용한 활동 로그 - 스크린샷](./media/change-history-report/activity-log-portal.png)

포털에서 활동 로그에는 몇 가지 필터가 있습니다. Azure RBAC 관련 필터는 다음과 같습니다.

| Assert | 값 |
| --------- | --------- |
| 이벤트 범주 | <ul><li>관리</li></ul> |
| 연산 | <ul><li>역할 할당 만들기</li><li>역할 할당 삭제</li><li>사용자 지정 역할 정의 만들기 또는 업데이트</li><li>사용자 지정 역할 정의 삭제</li></ul> |

활동 로그에 대 한 자세한 내용은 [활동 로그를 확인 하 여 리소스에 대 한 작업 모니터링](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)을 참조 하세요.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Azure PowerShell을 사용하여 활동 로그를 보려면 [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) 명령을 사용합니다.

이 명령은 지난 7일 동안 구독에서 발생한 모든 역할 할당 변경 내용을 나열합니다.

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

이 명령은 지난 7일 동안 리소스 그룹에서 발생한 모든 역할 정의 변경 내용을 나열합니다.

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

이 명령은 지난 7일 동안 구독에서 발생한 모든 역할 할당 및 역할 정의 변경 내용을 나열하고 결과를 목록에 표시합니다.

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 활동 로그를 보려면 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 명령을 사용합니다.

이 명령은 2 월 27 일에 리소스 그룹의 활동 로그를 7 일 앞으로 나열 합니다.

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

이 명령은 2 월 27 일에 인증 리소스 공급자에 대 한 활동 로그를 7 일 후에 나열 합니다.

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure Monitor 로그

[Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 는 모든 azure 리소스에 대 한 azure RBAC 변경을 수집 하 고 분석 하는 데 사용할 수 있는 또 다른 도구입니다. Azure Monitor 로그에는 다음과 같은 이점이 있습니다.

- 복잡한 쿼리 및 로직 작성
- 경고, Power BI 및 기타 도구와 통합
- 장기 보존 기간 동안 데이터 저장
- 보안, 가상 머신, 사용자 지정과 같은 다른 로그와 상호 참조

기본적인 시작 단계는 다음과 같습니다.

1. [Log Analytics 작업 영역을 만듭니다](../azure-monitor/learn/quick-create-workspace.md).

1. 작업 영역에 대해 [활동 로그 분석 솔루션을 구성](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)합니다.

1. [활동 로그를 봅니다](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). 활동 로그 분석 솔루션 개요 페이지로 이동 하는 빠른 방법은 **로그** 옵션을 클릭 하는 것입니다.

   ![포털의 Azure Monitor 로그 옵션](./media/change-history-report/azure-log-analytics-option.png)

1. 필요에 따라 [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) 를 사용 하 여 로그를 쿼리하고 볼 수 있습니다. 자세한 내용은 [Azure Monitor 로그 쿼리 시작](../azure-monitor/log-query/get-started-queries.md)을 참조 하세요.

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
* [활동 로그의 이벤트 보기](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Azure 활동 로그로 구독 활동 모니터링](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
