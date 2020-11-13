---
title: Azure Monitor에서 Log Analytics 작업 영역 이동 | Microsoft Docs
description: Log Analytics 작업 영역을 다른 구독 또는 리소스 그룹으로 이동 하는 방법에 대해 알아봅니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/12/2020
ms.openlocfilehash: 04da4d6466d450d04d7008332e32ea3d59cd0252
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555535"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Log Analytics 작업 영역을 다른 구독 또는 리소스 그룹으로 이동

이 문서에서는 Log Analytics 작업 영역을 동일한 지역의 다른 리소스 그룹 또는 구독으로 이동 하는 단계에 대해 알아봅니다. Azure Portal, PowerShell, Azure CLI 또는 REST API를 통해 Azure 리소스를 이동 하는 방법에 대해 자세히 알아볼 수 있습니다. 에서 [리소스를 새 리소스 그룹 또는 구독으로 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)합니다. 

> [!IMPORTANT]
> 작업 영역을 다른 지역으로 이동할 수 없습니다.

## <a name="verify-active-directory-tenant"></a>Active Directory 테 넌 트 확인
작업 영역 원본 및 대상 구독은 동일한 Azure Active Directory 테 넌 트 내에 있어야 합니다. Azure PowerShell를 사용 하 여 두 구독의 테 넌 트 ID가 동일한 지 확인 합니다.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>작업 영역 이동 고려 사항
- 작업 영역에 설치 되는 관리형 솔루션은 Log Analytics 작업 영역 이동 작업과 함께 이동 됩니다. 
- 작업 영역 이동 (주 및 보조)으로 작업 영역 키가 다시 생성 됩니다. 작업 영역 키가 자격 증명 모음 인 경우 새로 생성 된 키를 사용 하 여 해당 키를 업데이트 합니다. 
- 연결 된 에이전트는 연결 된 상태를 유지 하 고 이동 후에 작업 영역으로 데이터를 전송 합니다. 
- 이동 작업을 수행 하려면 작업 영역에서 연결 된 서비스가 필요 하지 않으므로 작업 영역 이동을 허용 하려면 해당 링크를 사용 하는 솔루션을 제거 해야 합니다. Automation 계정 연결을 해제 하기 전에 제거 해야 하는 솔루션은 다음과 같습니다.
  - 업데이트 관리
  - 변경 내용 추적
  - 작업이 없는 동안 VM 시작/중지
  - Azure Security Center

>[!IMPORTANT]
> **Azure 센티널 고객**
> - 현재 작업 영역에 Azure 센티널을 배포한 후 작업 영역을 다른 리소스 그룹 또는 구독으로 이동 하는 것은 지원 되지 않습니다. 
> - 작업 영역을 이미 이동한 경우 **Analytics** 에서 모든 활성 규칙을 사용하지 않도록 설정하고 5분 후에 다시 사용하도록 설정합니다. 대부분의 경우에는이 솔루션을 효과적으로 사용할 수 있습니다. 그러나이 솔루션은 지원 되지 않으며 사용자의 위험에 따라 수행 됩니다.
> 
> **경고 다시 만들기**
> - 사용 권한은 작업 영역 이동 중에 변경 되는 작업 영역의 Azure 리소스 ID를 기반으로 하기 때문에 이동 후에는 모든 경고를 다시 만들어야 합니다.
>
> **리소스 경로 업데이트**
> - 작업 영역을 이동한 후에는 작업 영역을 가리키는 모든 Azure 또는 외부 리소스를 검토 하 고 업데이트 하 여 새 리소스 대상 경로를 가리키도록 해야 합니다.
> 
>   *예:*
>   - [Azure Monitor 경고 규칙](alerts-resource-move.md)
>   - 타사 애플리케이션
>   - 사용자 지정 스크립팅
>

### <a name="delete-solutions-in-azure-portal"></a>Azure Portal에서 솔루션 삭제
Azure Portal를 사용 하 여 솔루션을 제거 하려면 다음 절차를 따르십시오.

1. 솔루션이 설치 된 리소스 그룹의 메뉴를 엽니다.
2. 제거할 솔루션을 선택 합니다.
3. **리소스 삭제** 를 클릭 한 다음 **삭제** 를 클릭 하 여 제거할 리소스를 확인 합니다.

![솔루션 삭제](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>PowerShell을 사용 하 여 삭제

PowerShell을 사용 하 여 솔루션을 제거 하려면 다음 예제와 같이 [AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) cmdlet을 사용 합니다.

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>Vm 시작/중지 솔루션에 대 한 경고 규칙 제거
**Vm 시작/중지** 솔루션을 제거 하려면 솔루션에서 만든 경고 규칙도 제거 해야 합니다. Azure Portal에서 다음 절차를 사용 하 여 이러한 규칙을 제거 합니다.

1. **모니터** 메뉴를 열고 **경고** 를 선택 합니다.
2. **경고 규칙 관리** 를 클릭 합니다.
3. 다음 세 가지 경고 규칙을 선택한 다음 **삭제** 를 클릭 합니다.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![규칙 삭제](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Automation 계정 연결 해제
다음 절차에 따라 Azure Portal를 사용 하 여 작업 영역에서 Automation 계정의 연결을 해제할 수 있습니다.

1. **Automation 계정** 메뉴를 열고 제거할 계정을 선택 합니다.
2. 메뉴의 **관련 리소스** 섹션에서 **연결 된 작업 영역** 을 선택 합니다. 
3. **작업 영역 연결 해제** 를 클릭 하 여 Automation 계정에서 작업 영역의 연결을 해제 합니다.

    ![작업 영역 연결 해제](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>작업 영역 이동

### <a name="azure-portal"></a>Azure portal
다음 절차에 따라 Azure Portal를 사용 하 여 작업 영역을 이동할 수 있습니다.

1. **Log Analytics 작업 영역** 메뉴를 열고 작업 영역을 선택 합니다.
2. **개요** 페이지에서 **리소스 그룹** 또는 **구독** 옆의 **변경** 을 클릭 합니다.
3. 작업 영역과 관련 된 리소스 목록이 포함 된 새 페이지가 열립니다. 작업 영역과 동일한 대상 구독 및 리소스 그룹으로 이동할 리소스를 선택 합니다. 
4. 대상 **구독** 및 **리소스 그룹** 을 선택 합니다. 작업 영역을 동일한 구독의 다른 리소스 그룹으로 이동 하는 경우에는 **구독** 옵션이 표시 되지 않습니다.
5. **확인** 을 클릭 하 여 작업 영역 및 선택 된 리소스를 이동 합니다.

    ![스크린샷에는 리소스 그룹 및 구독 이름을 변경 하는 옵션과 함께 Log Analytics 작업 영역의 개요 창이 표시 됩니다.](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
PowerShell을 사용 하 여 작업 영역을 이동 하려면 다음 예제와 같이 [AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) 를 사용 합니다.

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> 이동 작업 후 작업 영역을 이전 상태로 되돌리려면 제거 된 솔루션 및 Automation 계정 링크를 다시 구성 해야 합니다.


## <a name="next-steps"></a>다음 단계
- 이동을 지 원하는 리소스 목록은 [리소스에 대 한 이동 작업 지원](../../azure-resource-manager/management/move-support-resources.md)을 참조 하세요.
