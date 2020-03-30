---
title: Azure 활동 로그 보기로 리소스 모니터링
description: 활동 로그를 사용하여 사용자 작업 및 오류를 검토합니다. Azure 포털 PowerShell, Azure CLI 및 REST를 표시합니다.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 9511090099894fad6708843f106570ed029f0c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478137"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>활동 로그 를 보고 리소스에 대한 작업을 모니터링합니다.

활동 로그를 통해 다음 사항을 확인할 수 있습니다.

* 구독의 리소스에서 수행된 작업
* 작업을 시작한 사람
* 작업이 발생한 시간
* 작업의 상태
* 작업을 조사하는 데 도움이 될 수 있는 기타 속성 값

활동 로그에는 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)이 포함됩니다. 읽기 작업(GET)은 포함되지 않습니다. 리소스 작업 목록은 [Azure Resource Manager 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md)을 참조하세요. 활동 로그를 사용하여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

활동 로그는 90일 동안 유지됩니다. 시작 날짜가 90일을 초과하지 않는 범위에서 활동 로그를 쿼리할 수 있습니다.

포털, PowerShell, Azure CLI, Insights REST API 또는 [Insights .NET 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 통해 활동 로그에서 정보를 검색할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

포털을 통한 활동 로그를 보려면 다음 단계를 따르십시오.

1. Azure 포털 메뉴에서 **모니터를**선택하거나 모든 페이지에서 **모니터를** 검색하고 선택합니다.

    ![모니터 선택](./media/view-activity-logs/select-monitor-from-menu.png)

1. **활동 로그**를 선택합니다.

    ![활동 로그 선택](./media/view-activity-logs/select-activity-log.png)

1. 최근 작업의 요약 정보가 표시됩니다. 기본 필터 세트가 작업에 적용됩니다. 요약에 대한 정보에는 작업을 시작한 사람과 작업이 발생한 시기가 포함됩니다.

    ![최근 작업의 요약 보기](./media/view-activity-logs/audit-summary.png)

1. 미리 정의된 필터 집합을 빠르게 실행하려면 **빠른 인사이트**를 선택합니다.

    ![빠른 인사이트 선택](./media/view-activity-logs/select-quick-insights.png)

1. 옵션 중 하나를 선택합니다. 예를 들어 **실패한 배포를** 선택하여 배포의 오류를 확인합니다.

    ![실패한 배포 선택](./media/view-activity-logs/select-failed-deployments.png)

1. 필터가 지난 24시간 동안 배포 오류에 초점을 맞추도록 변경되었습니다. 필터와 일치하는 작업만 표시됩니다.

    ![필터 보기](./media/view-activity-logs/view-filters.png)

1. 특정 작업에 집중하려면 필터를 변경하거나 새 필터를 적용합니다. 예를 들어 다음 이미지는 **Timespan**의 새 값을 보여주며, **리소스 종류**는 스토리지 계정으로 설정됩니다.

    ![필터 옵션 설정](./media/view-activity-logs/set-filter.png)

1. 나중에 쿼리를 다시 실행해야 하는 경우 **현재 필터 고정**을 선택합니다.

    ![필터 고정](./media/view-activity-logs/pin-filters.png)

1. 필터 이름을 지정합니다.

    ![필터 이름 지정](./media/view-activity-logs/name-filters.png)

1. 필터는 대시보드에서 사용할 수 있습니다. Azure Portal 메뉴에서 **대시보드**를 선택합니다.

    ![대시보드에 필터링 표시](./media/view-activity-logs/activity-log-on-dashboard.png)

1. 포털에서 리소스에 대한 변경 내용을 볼 수 있습니다. 모니터의 기본 보기로 돌아가서 리소스 변경과 관련된 작업을 선택합니다.

    ![작업 선택](./media/view-activity-logs/select-operation.png)

1. **기록 변경(미리 보기)을** 선택하고 사용 가능한 작업 중 하나를 선택합니다.

    ![변경 기록 선택](./media/view-activity-logs/select-change-history.png)

1. 리소스의 변경 내용이 표시됩니다.

    ![변경 내용 표시](./media/view-activity-logs/show-changes.png)

변경 기록에 대한 자세한 내용은 [리소스 변경 받기를](../../governance/resource-graph/how-to/get-resource-changes.md)참조하십시오.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

로그 항목을 검색하려면 **Get-AzLog** 명령을 실행합니다. 항목의 목록을 필터링하는 추가 매개 변수를 제공합니다. 시작 시간과 종료 시간을 지정하지 않으면 최근 7일의 항목이 반환됩니다.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

다음 예제에서는 지정된 시간 동안 수행된 작업을 조사하는 활동 로그를 사용하는 방법을 보여 줍니다. 시작 및 종료 날짜는 날짜 형식으로 지정됩니다.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

또는 날짜 기능을 사용하여 지난 14일 같은 날짜 범위를 지정할 수 있습니다.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

특정 사용자가 수행한 작업을 조회할 수 있습니다.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

실패한 작업을 필터링할 수 있습니다.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

해당 항목에 대한 상태 메시지를 보고 한 가지 오류에 집중할 수 있습니다.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

특정 값을 선택하여 반환되는 데이터를 제한할 수 있습니다.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

지정한 시작 시간에 따라 이전 명령은 해당 리소스 그룹에 대한 긴 목록 작업을 반환할 수 있습니다. 검색 조건을 제공하여 찾고자 하는 결과를 필터링할 수 있습니다. 예를 들어 작업 유형별로 필터링할 수 있습니다.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

리소스 그래프를 사용하여 리소스의 변경 내역을 확인할 수 있습니다. 자세한 내용은 [리소스 변경 내용 받기를](../../governance/resource-graph/how-to/get-resource-changes.md)참조하십시오.

## <a name="azure-cli"></a>Azure CLI

로그 항목을 검색하려면 시간 범위를 나타내는 오프셋을 사용하여 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 명령을 실행합니다.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

다음 예제에서는 지정된 시간 동안 수행된 작업을 조사하는 활동 로그를 사용하는 방법을 보여 줍니다. 시작 및 종료 날짜는 날짜 형식으로 지정됩니다.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

더 이상 존재하지 않는 리소스 그룹에 대해서도 특정 사용자가 수행한 작업을 조회할 수 있습니다.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

실패한 작업을 필터링할 수 있습니다.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

해당 항목에 대한 상태 메시지를 보고 한 가지 오류에 집중할 수 있습니다.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

특정 값을 선택하여 반환되는 데이터를 제한할 수 있습니다.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

지정한 시작 시간에 따라 이전 명령은 해당 리소스 그룹에 대한 긴 목록 작업을 반환할 수 있습니다. 검색 조건을 제공하여 찾고자 하는 결과를 필터링할 수 있습니다. 예를 들어 작업 유형별로 필터링할 수 있습니다.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

리소스 그래프를 사용하여 리소스의 변경 내역을 확인할 수 있습니다. 자세한 내용은 [리소스 변경 내용 받기를](../../governance/resource-graph/how-to/get-resource-changes.md)참조하십시오.

## <a name="rest-api"></a>REST API

활동 로그로 작업하기 위한 REST 작업은 [Insights REST API](/rest/api/monitor/)의 일부입니다. 활동 로그 이벤트를 검색하려면 [구독에서 관리 이벤트 나열](/rest/api/monitor/activitylogs)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Power BI와 함께 Azure 활동 로그를 사용하면 구독의 작업을 면밀하게 살펴볼 수 있습니다. [Power BI 등에서 Azure 활동 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)을 참조하세요.
* 보안 정책 설정에 대해 자세히 알아보려면 [Azure 역할 기반 Access Control](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
* 인프라 계층에서 응용 프로그램 배포에 이르는 응용 프로그램의 변경 사항에 대한 자세한 내용은 [Azure Monitor의 응용 프로그램 변경 분석 사용을](../../azure-monitor/app/change-analysis.md)참조하세요.
* 배포 작업을 보는 명령에 대해 자세히 알아보려면 [배포 작업 보기](../templates/deployment-history.md)를 참조하세요.
* 모든 사용자의 리소스에서 삭제 작업을 방지하는 방법을 알아보려면 [Azure Resource Manager를 사용하여 리소스 잠그기](lock-resources.md)를 참조하세요.
* 각 Microsoft Azure Resource Manager 공급자에 대해 사용 가능한 작업의 목록을 보려면 [Azure Resource Manager 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md)을 참조하세요.
