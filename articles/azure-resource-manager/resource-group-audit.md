---
title: 리소스 모니터링을 위해 Azure 활동 로그 보기 | Microsoft Docs
description: 활동 로그를 사용하여 사용자 작업 및 오류를 검토합니다. Azure Portal, PowerShell, Azure CLI 및 REST를 보여 줍니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: tomfitz
ms.openlocfilehash: e7bcfce4e9d23839c623be3b54e97d931e6454cb
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268297"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>리소스에 대한 작업을 감사하기 위해 활동 로그 보기

활동 로그를 통해 다음 사항을 확인할 수 있습니다.

* 구독의 리소스에서 수행된 작업
* 작업을 시작한 사람
* 작업이 발생한 시간
* 작업의 상태
* 작업을 조사하는 데 도움이 될 수 있는 기타 속성 값

활동 로그에는 리소스에서 수행된 모든 쓰기 작업(PUT, POST, DELETE)이 포함됩니다. 읽기 작업(GET)은 포함되지 않습니다. 리소스 작업 목록은 [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md)을 참조하세요. 감사 로그를 사용하여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

활동 로그는 90일 동안 유지됩니다. 시작 날짜가 90일을 초과하지 않는 범위에서 활동 로그를 쿼리할 수 있습니다.

포털, PowerShell, Azure CLI, Insights REST API 또는 [Insights .NET 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 통해 활동 로그에서 정보를 검색할 수 있습니다.

## <a name="the-azure-portal"></a>Azure 포털

1. 포털을 통해 활동 로그를 보려면 **모니터**를 선택합니다.

    ![모니터 선택](./media/resource-group-audit/select-monitor.png)

1. **활동 로그**를 선택합니다.

    ![활동 로그 선택](./media/resource-group-audit/select-activity-log.png)

1. 최근 작업의 요약 정보가 표시됩니다. 기본 필터 세트가 작업에 적용됩니다.

    ![최근 작업의 요약 보기](./media/resource-group-audit/audit-summary.png)

1. 미리 정의된 필터 세트를 신속하게 실행하려면 **빠른 인사이트**를 선택하고 옵션 중 하나를 고릅니다.

    ![쿼리 선택](./media/resource-group-audit/quick-insights.png)

1. 특정 작업에 집중하려면 필터를 변경하거나 새 필터를 적용합니다. 예를 들어 다음 이미지는 **Timespan**의 새 값을 보여주며, **리소스 종류**는 스토리지 계정으로 설정됩니다. 

    ![필터 옵션 설정](./media/resource-group-audit/set-filter.png)

1. 나중에 쿼리를 다시 실행해야 하는 경우 **현재 필터 고정**을 선택합니다.

    ![필터 고정](./media/resource-group-audit/pin-filters.png)

1. 필터 이름을 지정합니다.

    ![필터 이름 지정](./media/resource-group-audit/name-filters.png)

1. 필터는 대시보드에서 사용할 수 있습니다.

    ![대시보드에 필터링 표시](./media/resource-group-audit/show-dashboard.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* 로그 항목을 검색하려면 **Get-AzLog** 명령을 실행합니다. 항목의 목록을 필터링하는 추가 매개 변수를 제공합니다. 시작 시간과 종료 시간을 지정하지 않으면 최근 7일의 항목이 반환됩니다.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup
  ```

    다음 예제에서는 지정된 시간 동안 수행된 작업을 조사하는 활동 로그를 사용하는 방법을 보여 줍니다. 시작 및 종료 날짜는 날짜 형식으로 지정됩니다.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-01-09T06:00 -EndTime 2019-01-15T06:00
  ```

    또는 날짜 기능을 사용하여 지난 14일 같은 날짜 범위를 지정할 수 있습니다.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

* 더 이상 존재하지 않는 리소스 그룹에 대해서도 특정 사용자가 수행한 작업을 조회할 수 있습니다.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

* 실패한 작업을 필터링할 수 있습니다.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -Status Failed
  ```

* 해당 항목에 대한 상태 메시지를 보고 한 가지 오류에 집중할 수 있습니다.

  ```azurepowershell-interactive
  ((Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties[0].Content.statusMessage | ConvertFrom-Json).error
  ```

* 특정 값을 선택하여 반환되는 데이터를 제한할 수 있습니다.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
  ```

* 지정한 시작 시간에 따라 이전 명령은 해당 리소스 그룹에 대한 긴 목록 작업을 반환할 수 있습니다. 검색 조건을 제공하여 찾고자 하는 결과를 필터링할 수 있습니다. 예를 들어 작업 유형별로 필터링할 수 있습니다.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
  ```

## <a name="azure-cli"></a>Azure CLI

* 로그 항목을 검색하려면 시간 범위를 나타내는 오프셋을 사용하여 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 명령을 실행합니다.

  ```azurecli-interactive
  az monitor activity-log list --resource-group ExampleGroup --offset 7d
  ```

  다음 예제에서는 지정된 시간 동안 수행된 작업을 조사하는 활동 로그를 사용하는 방법을 보여 줍니다. 시작 및 종료 날짜는 날짜 형식으로 지정됩니다.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --start-time 2019-01-01 --end-time 2019-01-15
  ```

* 더 이상 존재하지 않는 리소스 그룹에 대해서도 특정 사용자가 수행한 작업을 조회할 수 있습니다.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
  ```

* 실패한 작업을 필터링할 수 있습니다.

  ```azurecli-interactive
  az monitor activity-log list -g demoRG --status Failed --offset 1d
  ```

* 해당 항목에 대한 상태 메시지를 보고 한 가지 오류에 집중할 수 있습니다.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
  ```

* 특정 값을 선택하여 반환되는 데이터를 제한할 수 있습니다.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
  ```

* 지정한 시작 시간에 따라 이전 명령은 해당 리소스 그룹에 대한 긴 목록 작업을 반환할 수 있습니다. 검색 조건을 제공하여 찾고자 하는 결과를 필터링할 수 있습니다. 예를 들어 작업 유형별로 필터링할 수 있습니다.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
  ```

## <a name="rest-api"></a>REST API

활동 로그로 작업하기 위한 REST 작업은 [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)의 일부입니다. 활동 로그 이벤트를 검색하려면 [구독에서 관리 이벤트 나열](https://msdn.microsoft.com/library/azure/dn931934.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Power BI와 함께 Azure 활동 로그를 사용하면 구독의 작업을 면밀하게 살펴볼 수 있습니다. [Power BI 등에서 Azure 활동 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)을 참조하세요.
* 보안 정책 설정에 대해 자세히 알아보려면 [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)을 참조하세요.
* 배포 작업을 보는 명령에 대해 자세히 알아보려면 [배포 작업 보기](resource-manager-deployment-operations.md)를 참조하세요.
* 모든 사용자의 리소스에서 삭제 작업을 방지하는 방법을 알아보려면 [Azure Resource Manager를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.
* 각 Microsoft Azure Resource Manager 공급자에 대해 사용 가능한 작업의 목록을 보려면 [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md)을 참조하세요.
