---
title: Azure Monitor를 사용 하 여 & 보고서 보관-Azure AD 자격 관리
description: Azure Active Directory 권한 관리에서 로그를 보관 하 고 Azure Monitor를 사용 하 여 보고서를 만드는 방법을 알아봅니다.
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
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59a508d03730a51e793a5e30e2c99a91af77ce8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380184"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Azure Monitor에서 Azure AD 자격 관리에 대 한 로그 및 보고를 보관 합니다.

Azure AD는 감사 로그에 최대 30 일간 감사 이벤트를 저장 합니다. 그러나 [AZURE AD 저장소에서 데이터를 보고 하는 시간](../reports-monitoring/reference-reports-data-retention.md)을 Azure Storage 계정으로 라우팅 하거나 Azure Monitor를 사용 하 여 기본 보존 기간 보다 오랫동안 감사 데이터를 유지할 수 있습니다. 그런 다음이 데이터에 대 한 통합 문서와 사용자 지정 쿼리 및 보고서를 사용할 수 있습니다.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Azure Monitor 사용 하도록 Azure AD 구성
Azure Monitor 통합 문서를 사용 하기 전에 감사 로그의 복사본을 Azure Monitor로 보내도록 Azure AD를 구성 해야 합니다.

Azure AD 감사 로그를 보관 하려면 Azure 구독에 Azure Monitor 있어야 합니다. [Azure Monitor의 AZURE AD 활동 로그](../reports-monitoring/concept-activity-logs-azure-monitor.md)에서 Azure Monitor 사용의 필수 구성 요소 및 예상 비용에 대 한 자세한 내용을 확인할 수 있습니다.

**필수 역할**: 전역 관리자

1. 전역 관리자 인 사용자로 Azure Portal에 로그인 합니다. Azure Monitor 작업 영역을 포함 하는 리소스 그룹에 액세스할 수 있는지 확인 합니다.
 
1. 왼쪽 탐색 메뉴에서 모니터링 아래에 있는 **진단 설정** 을 클릭 하 **Azure Active Directory** 를 선택 합니다. 감사 로그를 해당 작업 영역으로 보낼 설정이 이미 있는지 확인 합니다.

1. 설정이 아직 없는 경우 **진단 설정 추가**를 클릭 합니다. Azure ad [로그를 Azure Monitor 로그와 통합](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) 문서에 있는 지침을 사용 하 여 azure ad 감사 로그를 Azure Monitor 작업 영역으로 보냅니다.

    ![진단 설정 창](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 로그가 Azure Monitor 전송 되 면 **Log Analytics 작업 영역**을 선택 하 고 Azure AD 감사 로그가 포함 된 작업 영역을 선택 합니다.

1. **사용량 및 예상 비용** 을 선택 하 고 **데이터 보존**을 클릭 합니다. 슬라이더를 감사 요구 사항을 충족 하는 데이터를 보관할 일 수로 변경 합니다.

    ![Log Analytics 작업 영역 창](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. 나중에 작업 영역에 유지 되는 날짜 범위를 확인 하기 위해 *보관 된 로그 날짜 범위* 통합 문서를 사용할 수 있습니다.  
    
    1. **Azure Active Directory** 선택한 다음 **통합 문서**를 클릭 합니다. 
    
    1. **문제 해결 Azure Active Directory**섹션을 확장 하 고 **보관 된 로그 날짜 범위**를 클릭 합니다. 


## <a name="view-events-for-an-access-package"></a>액세스 패키지에 대 한 이벤트 보기  

액세스 패키지에 대 한 이벤트를 보려면 기본 Azure monitor 작업 영역에 대 한 액세스 권한이 있어야 합니다 (정보는 [Azure Monitor의 로그 데이터 및 작업 영역](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) 에 대 한 액세스 관리 참조) 및 다음 역할 중 하나를 참조 하세요. 

- 전역 관리자  
- 보안 관리자  
- 보안 판독기  
- 보고서 구독자  
- 애플리케이션 관리자  

다음 절차를 사용 하 여 이벤트를 볼 수 있습니다. 

1. Azure Portal에서 **Azure Active Directory** 를 선택 하 고 **통합 문서**를 클릭 합니다. 구독이 하나만 있는 경우 3 단계로 이동 합니다. 

1. 구독이 여러 개인 경우에는 작업 영역을 포함 하는 구독을 선택 합니다.  

1. *액세스 패키지 작업*이라는 통합 문서를 선택 합니다. 

1. 해당 통합 문서에서 시간 범위 ( **모든** 경우로 변경)를 선택 하 고 해당 시간 범위 동안 활동이 있는 모든 액세스 패키지의 드롭다운 목록에서 액세스 패키지 Id를 선택 합니다. 선택한 시간 범위 동안 발생 한 액세스 패키지와 관련 된 이벤트가 표시 됩니다.  

    ![액세스 패키지 이벤트 보기](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    각 행에는 시간, 액세스 패키지 Id, 작업 이름, 개체 Id, UPN, 작업을 시작한 사용자의 표시 이름 등이 포함 됩니다.  추가 세부 정보는 JSON에 포함 되어 있습니다.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Azure Portal를 사용 하 여 사용자 지정 Azure Monitor 쿼리 만들기
권한 관리 이벤트를 포함 하 여 Azure AD 감사 이벤트에 대 한 고유한 쿼리를 만들 수 있습니다.  

1. Azure Portal Azure Active Directory에서 왼쪽 탐색 메뉴의 모니터링 섹션 아래에 있는 **로그** 를 클릭 하 여 새 쿼리 페이지를 만듭니다.

1. 작업 영역은 쿼리 페이지의 왼쪽 위에 표시 됩니다. 여러 Azure Monitor 작업 영역이 있고 Azure AD 감사 이벤트를 저장 하는 데 사용 하는 작업 영역이 표시 되지 않으면 **범위 선택**을 클릭 합니다. 그런 다음 올바른 구독 및 작업 영역을 선택 합니다.

1. 그런 다음 쿼리 텍스트 영역에서 "검색 *" 문자열을 삭제 하 고 다음 쿼리로 바꿉니다.

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 그런 다음 **실행**을 클릭합니다. 

    ![실행을 클릭 하 여 쿼리를 시작 합니다.](./media/entitlement-management-logs-and-reporting/run-query.png)

테이블에는 기본적으로 지난 1 시간 동안 자격 관리에 대 한 감사 로그 이벤트가 표시 됩니다. "시간 범위" 설정을 변경 하 여 오래 된 이벤트를 볼 수 있습니다. 그러나이 설정을 변경 하면 Azure AD가 Azure Monitor에 이벤트를 보내도록 구성 된 후에 발생 한 이벤트만 표시 됩니다.

Azure Monitor에 유지 되는 가장 오래 되 고 최신 감사 이벤트를 확인 하려면 다음 쿼리를 사용 합니다.

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Azure Monitor 감사 이벤트에 대해 저장 된 열에 대 한 자세한 내용은 [Azure Monitor에서 AZURE AD audit logs 스키마 해석](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)을 참조 하세요.

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 사용자 지정 Azure Monitor 쿼리 만들기

Azure Monitor로 로그를 보내도록 Azure AD를 구성한 후 PowerShell을 통해 로그에 액세스할 수 있습니다. 그런 다음 테 넌 트에서 전역 관리자가 될 필요 없이 스크립트나 PowerShell 명령줄에서 쿼리를 보냅니다. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>사용자 또는 서비스 주체에 게 올바른 역할 할당이 있는지 확인

Azure AD에 인증 하는 사용자 또는 서비스 사용자가 Log Analytics 작업 영역에서 적절 한 Azure 역할에 있는지 확인 합니다. 역할 옵션은 Log Analytics 판독기 또는 Log Analytics 참가자 중 하나입니다. 이러한 역할 중 하나에 이미 있는 경우 [Azure 구독 하나를 사용 하 여 LOG ANALYTICS ID 검색](#retrieve-log-analytics-id-with-one-azure-subscription)으로 건너뜁니다.

역할 할당을 설정 하 고 쿼리를 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 [Log Analytics 작업 영역](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)을 찾습니다.

1. **Access Control (IAM)** 을 선택 합니다.

1. 그런 다음 **추가** 를 클릭 하 여 역할 할당을 추가 합니다.

    ![역할 할당 추가](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Azure PowerShell 모듈 설치

적절 한 역할 할당이 있으면 PowerShell을 시작 하 고 다음을 입력 하 여 [Azure PowerShell 모듈을 설치 합니다](/powershell/azure/install-az-ps?view=azps-3.3.0) (아직 없는 경우).

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
이제 Azure AD에 인증 하 고, 쿼리 하는 Log Analytics 작업 영역의 ID를 검색할 준비가 되었습니다.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>하나의 Azure 구독을 사용 하 여 Log Analytics ID 검색
단일 Azure 구독 및 단일 Log Analytics 작업 영역만 있는 경우 다음을 입력 하 여 Azure AD에 인증 하 고 해당 구독에 연결 하 고 해당 작업 영역을 검색 합니다.
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>여러 Azure 구독을 사용 하 여 Log Analytics ID 검색

 [AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) 는 한 번에 하나의 구독에서 작동 합니다. 따라서 여러 Azure 구독이 있는 경우 Azure AD 로그를 사용 하 여 Log Analytics 작업 영역을 포함 하는 구독에 연결 하는지 확인 하는 것이 좋습니다. 
 
 다음 cmdlet은 구독 목록을 표시 하 고 Log Analytics 작업 영역이 있는 구독의 ID를 찾습니다.
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
과 `Connect-AzAccount –Subscription $subs[0].id`같은 명령을 사용 하 여 PowerShell 세션을 다시 인증 하 고 해당 구독에 연결할 수 있습니다. 비 대화형 기능을 포함 하 여 PowerShell에서 Azure에 인증 하는 방법에 대 한 자세한 내용은 [Azure PowerShell으로 로그인](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)을 참조 하세요.

해당 구독에 여러 Log Analytics 작업 영역이 있는 경우 [AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) cmdlet은 작업 영역 목록을 반환 합니다. 그런 다음 Azure AD 로그를 포함 하는 항목을 찾을 수 있습니다. 이 `CustomerId` cmdlet이 반환 하는 필드는 Log Analytics 작업 영역 개요의 Azure Portal에 표시 되는 "작업 영역 Id"의 값과 동일 합니다.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Log Analytics 작업 영역으로 쿼리 보내기
마지막으로 작업 영역을 확인 한 후에는 [AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) 를 사용 하 여 해당 작업 영역에 kusto 쿼리를 보낼 수 있습니다. 이러한 쿼리는 [Kusto 쿼리 언어로](https://docs.microsoft.com/azure/kusto/query/)작성 됩니다.
 
예를 들어 다음과 같은 쿼리를 전송 하는 PowerShell cmdlet을 사용 하 여 Log Analytics 작업 영역에서 감사 이벤트 레코드의 날짜 범위를 검색할 수 있습니다.
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

다음과 같은 쿼리를 사용 하 여 권한 관리 이벤트를 검색할 수도 있습니다.

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>다음 단계:
- [Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../../azure-monitor/app/usage-workbooks.md) 

