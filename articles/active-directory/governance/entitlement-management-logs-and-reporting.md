---
title: Azure 모니터를 사용 하 여 & 보고서 보관 - Azure AD 권한 관리
description: Azure Active Directory 권한 관리에서 Azure Monitor를 사용하여 로그를 보관하고 보고서를 만드는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 070b7c5e0fef7d50f84271190432a65d29699bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128634"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Azure 모니터에서 Azure AD 권한 관리에서 로그 및 보고 보관

Azure AD는 감사 로그에 최대 30일 동안 감사 이벤트를 저장합니다. 그러나 [Azure AD가 보고 데이터를](../reports-monitoring/reference-reports-data-retention.md)저장하는 기간에 설명된 기본 보존 기간보다 더 오래 감사 데이터를 Azure Storage 계정으로 라우팅하거나 Azure Monitor를 사용하여 모니터링할 수 있습니다. 그런 다음 이 데이터에 대한 통합 문서 및 사용자 지정 쿼리 및 보고서를 사용할 수 있습니다.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Azure 모니터를 사용하도록 Azure AD 구성
Azure Monitor 통합 문서를 사용하기 전에 Azure AD를 구성하여 감사 로그의 복사본을 Azure Monitor로 보내야 합니다.

Azure AD 감사 로그를 보관하려면 Azure 구독에 Azure 모니터가 있어야 합니다. [Azure AD 활동 로그에서 Azure AD 작업 로그에서](../reports-monitoring/concept-activity-logs-azure-monitor.md)Azure 모니터를 사용하는 데 드는 필수 구성 조건 및 예상 비용에 대해 자세히 확인할 수 있습니다.

**필수 구성 조건 역할**: 글로벌 관리자

1. 전역 관리자인 사용자로 Azure 포털에 로그인합니다. Azure Monitor 작업 영역을 포함하는 리소스 그룹에 액세스할 수 있는지 확인합니다.
 
1. **Azure Active Directory를** 선택한 다음 왼쪽 탐색 메뉴에서 모니터링 에서 **진단 설정을** 클릭합니다. 감사 로그를 해당 작업 영역으로 보낼 설정이 이미 있는지 확인합니다.

1. 설정이 아직 없는 경우 **진단 추가 설정을**클릭합니다. Azure [AD 로그와 Azure AD 로그를 통합하는](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) 문서의 지침을 사용하여 Azure AD 감사 로그를 Azure 모니터 작업 영역으로 보냅니다.

    ![진단 설정 창](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 로그가 Azure Monitor로 전송된 후 **로그 분석 작업 영역을**선택하고 Azure AD 감사 로그가 포함된 작업 영역을 선택합니다.

1. **사용량 및 예상 비용을** 선택하고 데이터 **보존을 클릭합니다.** 감사 요구 사항을 충족하기 위해 데이터를 유지할 일 수로 슬라이더를 변경합니다.

    ![로그 분석 작업 영역 창](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Azure 포털을 사용하여 사용자 지정 Azure 모니터 쿼리 만들기
권한 관리 이벤트를 포함하여 Azure AD 감사 이벤트에 대한 고유한 쿼리를 만들 수 있습니다.  

1. Azure 포털의 Azure Active Directory에서 왼쪽 탐색 메뉴의 모니터링 섹션 아래의 **로그를** 클릭하여 새 쿼리 페이지를 만듭니다.

1. 작업 영역은 쿼리 페이지의 왼쪽 상단에 표시되어야 합니다. Azure Monitor 작업 영역이 여러 개 있고 Azure AD 감사 이벤트를 저장하는 데 사용하는 작업 영역이 표시되지 않는 경우 **범위 선택을**클릭합니다. 그런 다음 올바른 구독 및 작업 영역을 선택합니다.

1. 그런 다음 쿼리 텍스트 영역에서 문자열 "search *"를 삭제하고 다음 쿼리로 바꿉습니다.

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 그런 다음 **실행**을 클릭합니다. 

    ![실행을 클릭하여 쿼리를 시작합니다.](./media/entitlement-management-logs-and-reporting/run-query.png)

테이블에는 기본적으로 지난 시간의 권한 관리를 위한 감사 로그 이벤트가 표시됩니다. '시간 범위' 설정을 변경하여 이전 이벤트를 볼 수 있습니다. 그러나 이 설정을 변경하면 Azure AD가 Azure Monitor에 이벤트를 보내도록 구성된 후에 발생한 이벤트만 표시됩니다.

Azure Monitor에서 가장 오래되고 최신 감사 이벤트를 알고 싶다면 다음 쿼리를 사용하십시오.

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Azure Monitor에서 감사 이벤트에 대해 저장된 열에 대한 자세한 내용은 [Azure 모니터의 Azure AD 감사 로그 해석 을](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)참조하십시오.

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Azure PowerShell을 사용하여 사용자 지정 Azure 모니터 쿼리 만들기

Azure AD를 구성하여 Azure 모니터로 로그를 보내도록 구성한 후 PowerShell을 통해 로그에 액세스할 수 있습니다. 그런 다음 테넌트의 전역 관리자가 될 필요 없이 스크립트 또는 PowerShell 명령줄에서 쿼리를 보냅니다. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>사용자 또는 서비스 주체가 올바른 역할 할당을 수행했는지 확인

Azure AD에 인증할 사용자 또는 서비스 주체가 Log Analytics 작업 영역에서 적절한 Azure 역할에 있는지 확인합니다. 역할 옵션은 로그 분석 리더 또는 로그 분석 기여자입니다. 이러한 역할 중 하나에 이미 있는 경우 [하나의 Azure 구독으로 Log Analytics ID 검색으로](#retrieve-log-analytics-id-with-one-azure-subscription)건너뜁니다.

역할 할당을 설정하고 쿼리를 만들려면 다음 단계를 수행합니다.
1. Azure 포털에서 [로그 분석 작업 영역을](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)찾습니다.

1. **액세스 제어(IAM)를**선택합니다.

1. 그런 다음 **추가를** 클릭하여 역할 할당을 추가합니다.

    ![역할 할당 추가](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Azure PowerShell 모듈 설치

적절한 역할 할당이 완료되면 PowerShell을 시작하고 다음을 입력하여 [Azure PowerShell 모듈을](/powershell/azure/install-az-ps?view=azps-3.3.0) 설치합니다(아직 설치하지 않은 경우).

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
이제 Azure AD를 인증하고 쿼리하는 Log Analytics 작업 영역의 ID를 검색할 준비가 되었습니다.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>하나의 Azure 구독으로 로그 분석 ID 검색
단일 Azure 구독및 단일 Log Analytics 작업 영역이 있는 경우 다음을 입력하여 Azure AD에 인증하고 해당 구독에 연결하고 해당 작업 영역을 검색합니다.
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>여러 Azure 구독으로 로그 분석 ID 검색

 [Get-Az운영인사이트워크스페이스는](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) 한 번에 하나의 구독으로 운영됩니다. 따라서 여러 Azure 구독이 있는 경우 Azure AD 로그가 있는 Log Analytics 작업 영역이 있는 구독에 연결해야 합니다. 
 
 다음 cmdlet구독 목록을 표시하고 로그 분석 작업 영역이 있는 구독의 ID를 찾습니다.
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
`Connect-AzAccount –Subscription $subs[0].id`와 같은 명령을 사용하여 PowerShell 세션을 다시 인증하고 해당 구독에 연결할 수 있습니다. 비대화형을 포함하여 PowerShell에서 Azure를 인증하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 로그인을](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)참조하세요.

해당 구독에 여러 개의 로그 분석 작업 영역이 있는 경우 cmdlet [Get-Az운영인사이트워크스페이스가](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) 작업 영역 목록을 반환합니다. 그런 다음 Azure AD 로그가 있는 로그를 찾을 수 있습니다. 이 `CustomerId` cmdlet에서 반환되는 필드는 로그 분석 작업 영역 개요의 Azure 포털에 표시되는 "작업 영역 ID" 값과 동일합니다.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>로그 분석 작업 영역으로 쿼리 보내기
마지막으로 작업 영역이 식별되면 [Invoke-Az운영 인사이트쿼리를](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) 사용하여 Kusto 쿼리를 해당 작업 영역으로 보낼 수 있습니다. 이러한 쿼리는 [Kusto 쿼리 언어로](https://docs.microsoft.com/azure/kusto/query/)작성됩니다.
 
예를 들어, PowerShell cmdlet을 사용하여 Log Analytics 작업 영역에서 감사 이벤트 레코드의 날짜 범위를 검색하여 다음과 같은 쿼리를 보낼 수 있습니다.
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

다음과 같은 쿼리를 사용하여 권한 관리 이벤트를 검색할 수도 있습니다.

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>다음 단계:
- [Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../../azure-monitor/app/usage-workbooks.md) 

