---
title: "리소스 관리자로 작업 감사 | Microsoft Docs"
description: "리소스 관리자에서 활동 로그를 사용하여 사용자 작업 및 오류를 검토합니다. Azure 포털, PowerShell, Azure CLI 및 REST를 보여 줍니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 0cb9d6acbe318ced7bc41e6a896d0b0fc77e2b64


---
# <a name="audit-operations-with-resource-manager"></a>리소스 관리자로 작업 감사
활동 로그를 통해 다음 사항을 확인할 수 있습니다.

* 구독의 리소스에서 수행된 작업
* 작업을 시작한 사람(백 엔드 서비스에 의해 시작된 작업이라도 사용자를 호출자로 반환하지 않음)
* 작업이 발생한 시간
* 작업의 상태
* 작업을 조사하는 데 도움이 될 수 있는 기타 속성 값

[!INCLUDE [resource-manager-audit-limitations](../../includes/resource-manager-audit-limitations.md)]

이 토픽에서는 감사 작업에 중점을 둡니다. 활동 로그를 사용하여 배포 문제를 해결하는 방법에 대해 알아보려면 [Azure에서 리소스 그룹 배포 문제 해결](resource-manager-troubleshoot-deployments-portal.md)을 참조하세요.

포털, PowerShell, Azure CLI, Insights REST API 또는 [Insights .NET 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 통해 활동 로그에서 정보를 검색할 수 있습니다.

## <a name="portal-to-view-activity-logs"></a>포털을 통해 활동 로그 보기
1. 포털을 통해 활동 로그를 보려면 **더 많은 서비스**, **활동 로그**를 선택합니다.
   
    ![활동 로그 선택](./media/resource-group-audit/select-audit-logs.png)
2. **활동 로그** 블레이드에서 구독의 모든 리소스 그룹에 대한 최근 작업의 요약을 볼 수 있습니다. 여기에는 최근 작업 목록이 포함됩니다.
   
    ![작업 표시](./media/resource-group-audit/audit-summary.png)
3. 여러 조건을 선택하여 표시되는 작업의 수를 제한할 수 있습니다. 예를 들어 다음 이미지는 지난 달에 특정 사용자 또는 응용 프로그램이 수행한 작업을 표시하도록 변경된 **시간 간격** 및 **이벤트를 시작한 사람** 필드를 보여줍니다.
   
    ![필터 옵션 설정](./media/resource-group-audit/set-filter.png)
4. 쿼리 결과를 확인하려면 **적용** 을 선택합니다.
5. 나중에 쿼리를 다시 실행해야 하는 경우 **저장** 을 선택하고 쿼리 이름을 지정합니다.
   
    ![쿼리 저장](./media/resource-group-audit/save-query.png)
6. 특정 리소스 또는 리소스 그룹에 대해 자동으로 필터링하려면 해당 리소스 블레이드에서 **활동 로그** 를 선택합니다. 활동 로그는 선택한 리소스에 의해 자동으로 필터링됩니다.
   
    ![리소스로 필터링](./media/resource-group-audit/filtered-by-resource.png)

## <a name="powershell-to-view-activity-logs"></a>PowerShell을 통해 활동 로그 보기
1. 로그 항목을 검색하려면 **Get-AzureRmLog** 명령을 실행합니다. 항목의 목록을 필터링하는 추가 매개 변수를 제공합니다. 시작 및 종료 시간을 지정하지 않으면 지난 시간에 대한 항목이 반환됩니다. 예를 들어 지난 1시간 동안 리소스 그룹에 대해 수행된 작업을 검색하려면 다음 명령을 실행합니다.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup
   
    다음 예제에서는 지정된 시간 동안 수행된 작업을 조사하는 감사 로그를 사용하는 방법을 보여 줍니다. 시작 및 종료 날짜는 날짜 형식으로 지정됩니다.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
   
    또는 날짜 기능을 사용하여 지난 14일 같은 날짜 범위를 지정할 수 있습니다.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
2. 지정한 시작 시간에 따라 이전 명령은 해당 리소스 그룹에 대한 긴 목록 작업을 반환할 수 있습니다. 검색 조건을 제공하여 찾고자 하는 결과를 필터링할 수 있습니다. 예를 들어 웹앱이 중지된 방법을 조사하려는 경우 다음 명령을 실행할 수 있습니다.  
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
   
    이 예에서는 중지 작업이 someone@contoso.com에 의해 수행되었음을 보여 줍니다. 
   
        Authorization     :
        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Action    : Microsoft.Web/sites/stop/action
        Role      : Subscription Admin
        Condition :
        Caller            : someone@contoso.com
        CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
        EventSource       : Administrative
        EventTimestamp    : 8/28/2015 4:08:18 PM
        OperationName     : Microsoft.Web/sites/stop/action
        ResourceGroupName : ExampleGroup
        ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Status            : Succeeded
        SubscriptionId    : xxxxx
        SubStatus         : OK
3. 더 이상 존재하지 않는 리소스 그룹에 대해서도 특정 사용자가 수행한 작업을 조회할 수 있습니다.
   
        Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## <a name="azure-cli-to-view-activity-logs"></a>Azure CLI를 통해 활동 로그 보기
1. 로그 항목을 검색하려면 **Azure 그룹 로그 표시** 명령을 실행합니다.
   
        azure group log show ExampleGroup
2. [jq](http://stedolan.github.io/jq/download/)와 같은 JSON 유틸리티로 결과 필터링할 수 있습니다. 다음 예제에서는 웹 구성 파일을 업데이트하는 작업을 찾는 방법을 보여 줍니다.
   
        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == \"Update web sites config\")"
3. 특정 사용자에 대한 작업을 조회할 수 있습니다.
   
        azure group log show ExampleGroup --json | jq ".[] | select(.caller==\"someone@contoso.com\")"

## <a name="rest-api-to-view-audit-logs"></a>REST API를 통해 감사 로그 보기
활동 로그로 작업하기 위한 REST 작업은 [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)의 일부입니다. 활동 로그 이벤트를 검색하려면 [구독에서 관리 이벤트 나열](https://msdn.microsoft.com/library/azure/dn931934.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* Power BI와 함께 Azure 활동 로그를 사용하면 구독의 작업을 면밀하게 살펴볼 수 있습니다. [Power BI 등에서 Azure 활동 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)을 참조하세요.
* 보안 정책 설정에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.
* 배포 문제를 해결하는 명령에 대해 알아보려면 [Azure에서 리소스 그룹 배포 문제 해결](resource-manager-troubleshoot-deployments-portal.md)을 참조하세요.
* 모든 사용자의 리소스에서 삭제 작업을 방지하는 방법을 알아보려면 [Azure Resource Manager를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.




<!--HONumber=Nov16_HO3-->


