---
title: Azure AD 도메인 서비스에 대한 보안 감사 활성화 | 마이크로 소프트 문서
description: Azure AD 도메인 서비스에서 분석 및 경고를 위한 이벤트 로깅을 중앙 집중화하기 위해 보안 감사를 사용하도록 설정하는 방법 알아보기
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ce910b553e14d09eefa35efc5f2973337dfa1309
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654671"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Azure Active Directory 도메인 서비스에 대한 보안 감사 사용

Azure Active Directory 도메인 서비스(Azure AD DS) 보안 감사를 통해 Azure는 보안 이벤트를 대상 리소스로 스트리밍할 수 있습니다. 이러한 리소스에는 Azure 저장소, Azure Log Analytics 작업 영역 또는 Azure 이벤트 허브가 포함됩니다. 보안 감사 이벤트를 사용하도록 설정하면 Azure AD DS는 선택한 범주에 대한 모든 감사된 이벤트를 대상 리소스로 보냅니다.

Azure 이벤트 허브를 사용하여 이벤트를 Azure 저장소에 보관하고 이벤트를 SIEM(보안 정보 및 이벤트 관리) 소프트웨어(또는 이에 상응하는)로 스트리밍하거나 Azure Portal에서 자체 분석 및 Azure Log Analytics 작업 영역을 사용하여 수행할 수 있습니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사는 Azure 리소스 관리자 기반 인스턴스에서만 사용할 수 있습니다. 마이그레이션 하는 방법에 대 한 자세한 내용은 [참조 Azure AD DS 클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션.][migrate-azure-adds]

## <a name="security-audit-destinations"></a>보안 감사 대상

Azure 저장소, Azure 이벤트 허브 또는 Azure Log Analytics 작업 영역을 Azure AD DS 보안 감사의 대상 리소스로 사용할 수 있습니다. 이러한 대상을 결합할 수 있습니다. 예를 들어 보안 감사 이벤트를 보관하기 위해 Azure Storage를 사용할 수 있지만 Azure Log Analytics 작업 영역을 사용하여 단기적으로 정보를 분석하고 보고할 수 있습니다.

다음 표에서는 각 대상 리소스 유형에 대한 시나리오를 간략하게 설명합니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사를 사용하도록 설정하기 전에 대상 리소스를 만들어야 합니다. Azure 포털, Azure PowerShell 또는 Azure CLI를 사용하여 이러한 리소스를 만들 수 있습니다.

| 대상 리소스 | 시나리오 |
|:---|:---|
|Azure Storage| 이 대상은 보관을 위해 보안 감사 이벤트를 저장하는 것이 주요 필요할 때 사용해야 합니다. 다른 대상은 보관 목적으로 사용할 수 있지만 이러한 대상은 보관의 기본 필요 를 넘어서는 기능을 제공합니다. <br /><br />Azure AD DS 보안 감사 이벤트를 사용하도록 설정하기 전에 먼저 [Azure Storage 계정 만들기.](../storage/common/storage-account-create.md)|
|Azure Event Hubs| 이 대상은 데이터 분석 소프트웨어 또는 SIEM(이벤트 관리) 소프트웨어와 같은 추가 소프트웨어와 보안 감사 이벤트를 공유하는 & 경우 사용해야 합니다.<br /><br />Azure AD DS 보안 감사 이벤트를 사용하도록 설정하기 전에 [Azure 포털을 사용하여 이벤트 허브만들기](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure 로그 분석 작업 영역| 이 대상은 Azure 포털에서 보안 감사를 직접 분석하고 검토해야 하는 경우에 사용해야 합니다.<br /><br />Azure AD DS 보안 감사 이벤트를 사용하도록 설정하기 전에 [Azure 포털에서 로그 분석 작업 영역을 만듭니다.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Azure 포털을 사용하여 보안 감사 이벤트 사용

Azure 포털을 사용하여 Azure AD DS 보안 감사 이벤트를 사용하려면 다음 단계를 완료합니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사는 소급 적용되지 않습니다. 과거의 이벤트를 검색하거나 재생할 수 없습니다. Azure AD DS는 보안 감사를 사용하도록 설정한 후에만 발생하는 이벤트를 보낼 수 있습니다.

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
1. Azure 포털 상단에서 **Azure AD 도메인 서비스를**검색하고 선택합니다. *aaddscontoso.com.*
1. Azure AD DS 창에서 왼쪽에서 **진단 설정을** 선택합니다.
1. 기본적으로 진단 프로그램이 구성되지 않습니다. 시작하려면 진단 **추가 설정을**선택합니다.

    ![Azure AD 도메인 서비스에 대한 진단 설정 추가](./media/security-audit-events/add-diagnostic-settings.png)

1. *aadds-auditing*과 같은 진단 구성에 대한 이름을 입력합니다.

    원하는 보안 감사 대상에 대한 확인란을 선택합니다. Azure 저장소 계정, Azure 이벤트 허브 또는 로그 분석 작업 영역에서 선택할 수 있습니다. 이러한 대상 리소스는 Azure 구독에 이미 있어야 합니다. 이 마법사에서는 대상 리소스를 만들 수 없습니다.

    ![필요한 대상 및 유형의 감사 이벤트를 캡처할 수 있습니다.](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure 저장소**
        * **저장소 계정에 보관을**선택한 다음 **구성을**선택합니다.
        * 보안 감사 이벤트를 보관하는 데 사용할 **구독** 및 **저장소 계정을** 선택합니다.
        * 준비가 되면 **확인을**선택합니다.
    * **Azure 이벤트 허브**
        * **이벤트 허브로 스트림을**선택한 다음 **구성을**선택합니다.
        * **구독** 및 **이벤트 허브 네임스페이스를**선택합니다. 필요한 경우 이벤트 **허브 이름도** 선택한 다음 **이벤트 허브 정책 이름을**선택합니다.
        * 준비가 되면 **확인을**선택합니다.
    * **Azure 로그 분석 작업 영역**
        * **로그 분석으로 보내기를**선택한 다음 보안 감사 이벤트를 저장하는 데 사용할 **구독** 및 **로그 분석 작업 영역을** 선택합니다.

1. 특정 대상 리소스에 포함할 로그 범주를 선택합니다. 감사 이벤트를 Azure Storage 계정으로 보내는 경우 데이터를 보존할 일 수를 정의하는 보존 정책을 구성할 수도 있습니다. 기본 *설정이 0이면* 모든 데이터가 유지되고 시간이 지나도 이벤트가 회전되지 않습니다.

    단일 구성 내에서 각 대상 리소스에 대해 서로 다른 로그 범주를 선택할 수 있습니다. 이 기능을 사용하면 Log Analytics에 보관할 로그 범주와 보관하려는 로그 범주를 선택할 수 있습니다.

1. 작업이 완료되면 **저장을** 선택하여 변경 내용을 커밋합니다. 대상 리소스는 구성이 저장된 직후 Azure AD DS 보안 감사 이벤트를 수신하기 시작합니다.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Azure PowerShell을 사용하여 보안 감사 이벤트 사용

Azure PowerShell을 사용하여 Azure AD DS 보안 감사 이벤트를 사용하려면 다음 단계를 완료합니다. 필요한 경우 먼저 [Azure PowerShell 모듈을 설치하고 Azure 구독에 연결합니다.](/powershell/azure/install-az-ps)

> [!IMPORTANT]
> Azure AD DS 보안 감사는 소급 적용되지 않습니다. 과거의 이벤트를 검색하거나 재생할 수 없습니다. Azure AD DS는 보안 감사를 사용하도록 설정한 후에만 발생하는 이벤트를 보낼 수 있습니다.

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 구독을 인증합니다. 메시지가 표시되면 계정 자격 증명을 입력합니다.

    ```azurepowershell
    Connect-AzAccount
    ```

1. 보안 감사 이벤트에 대한 대상 리소스를 만듭니다.

    * **Azure 저장소** - [Azure PowerShell을 사용하여 저장소 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure 이벤트 허브** - [Azure PowerShell을 사용하여 이벤트 허브](../event-hubs/event-hubs-quickstart-powershell.md)만들기. [또한 New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) cmdlet을 사용하여 이벤트 허브 *네임스페이스에*Azure AD DS 권한을 부여하는 권한 부여 규칙을 만들어야 할 수도 있습니다. 권한 부여 규칙에는 **관리,** **수신 대기**및 **보내기** 권한이 포함되어야 합니다.

        > [!IMPORTANT]
        > 이벤트 허브 자체가 아닌 이벤트 허브 네임스페이스에 권한 부여 규칙을 설정해야 합니다.

    * **Azure 로그 분석 작업 영역** - [Azure PowerShell을 사용하여 로그 분석 작업 영역 만들기.](../azure-monitor/learn/quick-create-workspace-posh.md)

1. [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) cmdlet을 사용하여 Azure AD DS 관리 도메인의 리소스 ID를 가져옵니다. $aadds 라는 변수를 *만듭니다. *값을 보유하는 ResourceId:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Azure AD 도메인 서비스 보안 감사 이벤트에 대 한 대상 리소스를 사용 하 여 [Set-AzDiagnosticSet](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) cmdlet를 사용 하 여 Azure 진단 설정을 구성 합니다. 다음 예제에서는 변수가 *$aadds. ResourceId는* 이전 단계에서 사용됩니다.

    * **Azure 저장소** - 저장소 계정 *Id를* 저장소 계정 이름으로 바꿉니다.

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure 이벤트 허브** - *eventHubName을* 이벤트 허브 의 이름으로 바꾸고 *eventHubRuleId를* 권한 부여 규칙 ID로 바꿉니다.

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure 로그 분석 작업 영역** - *작업 영역Id를* 로그 분석 작업 영역의 ID로 바꿉니다.

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Azure 모니터를 사용하여 보안 감사 이벤트를 쿼리하고 볼 수 있습니다.

로그 분석 작업 영역을 사용하면 Azure Monitor 및 Kusto 쿼리 언어를 사용하여 보안 감사 이벤트를 보고 분석할 수 있습니다. 이 쿼리 언어는 읽기 쉬운 구문으로 전원 분석 기능을 자랑하는 읽기 전용 사용을 위해 설계되었습니다. Kusto 쿼리 언어를 시작하려면 다음 문서를 참조하세요.

* [Azure 모니터 설명서](https://docs.microsoft.com/azure/azure-monitor/)
* [Azure Monitor에서 Log Analytics 시작](../azure-monitor/log-query/get-started-portal.md)
* [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/log-query/get-started-queries.md)
* [Log Analytics 데이터의 대시보드 만들기 및 공유](../azure-monitor/learn/tutorial-logs-dashboards.md)

다음 샘플 쿼리를 사용하여 Azure AD DS에서 보안 감사 이벤트 분석을 시작할 수 있습니다.

### <a name="sample-query-1"></a>샘플 쿼리 1

지난 7일 동안의 모든 계정 잠금 이벤트를 봅니다.

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>샘플 쿼리 2

*2020년*2월 3일 오전 9시에 모든 계정 잠금 이벤트(4740)를 확인합니다. 2020년 2월 10일 자정, 날짜와 시간으로 오름차순 정렬:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>샘플 쿼리 3

사용자라는 계정에 대해 7일 전(지금부터) 계정 로그인 이벤트를 봅니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>샘플 쿼리 4

잘못된 암호 *(0xCC0000006a)를*사용하여 로그인을 시도한 사용자라는 계정에 대해 7일 전부터 계정 로그인 이벤트를 봅니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>샘플 쿼리 5

계정이 잠겨 있는 동안 로그인을 시도한 사용자라는 이름의 계정에 대해 7일 전부터 계정 로그인 이벤트*보기(0xC0000234)*

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>샘플 쿼리 6

잠긴 모든 사용자에 대해 발생한 모든 로그인 시도에 대해 7일 전부터 계정 로그인 이벤트 수를 확인합니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>이벤트 범주 감사

Azure AD DS 보안 감사는 기존 AD DS 도메인 컨트롤러에 대한 기존 감사와 일치합니다. 하이브리드 환경에서는 이벤트를 분석할 때 동일한 논리를 사용할 수 있도록 기존 감사 패턴을 다시 사용할 수 있습니다. 문제를 해결하거나 분석해야 하는 시나리오에 따라 다양한 감사 이벤트 범주를 대상으로 지정해야 합니다.

다음 감사 이벤트 범주를 사용할 수 있습니다.

| 감사 범주 이름 | 설명 |
|:---|:---|
| 계정 로그온|감사는 도메인 컨트롤러 또는 SAM(로컬 보안 계정 관리자)에서 계정 데이터를 인증하려고 시도합니다.</p>로그온 및 로그오프 정책 설정 및 이벤트는 특정 컴퓨터에 대한 액세스 시도를 추적합니다. 이 범주의 설정 및 이벤트는 사용되는 계정 데이터베이스에 중점을 둡니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[자격 증명 유효성 검사 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos 인증 오류 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Kerberos 서비스 티켓 작업 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[기타 로그온/로그오프 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| 계정 관리|사용자 및 컴퓨터 계정 및 그룹에 대한 변경 내용을 감사합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[애플리케이션 그룹 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[컴퓨터 계정 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[메일 그룹 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[기타 계정 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[보안 그룹 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[사용자 계정 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 세부 정보 추적|해당 컴퓨터에서 개별 응용 프로그램 및 사용자의 활동을 감사하고 컴퓨터가 사용되는 방식을 이해합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[DPAPI 활동 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP 활동 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[프로세스 만들기 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[프로세스 종료 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| 디렉터리 서비스 액세스|감사는 AD DS(Active Directory 도메인 서비스)에서 개체에 액세스하고 수정하려고 시도합니다. 이러한 감사 이벤트는 도메인 컨트롤러에서만 기록됩니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[세부 디렉터리 서비스 복제 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[디렉터리 서비스 액세스 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[디렉터리 서비스 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[디렉터리 서비스 복제 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| 로그온-로그오프|감사는 대화형 또는 네트워크를 통해 컴퓨터에 로그온하려고 시도합니다. 이러한 이벤트는 사용자 활동을 추적하고 네트워크 리소스에 대한 잠재적인 공격을 식별하는 데 유용합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[계정 잠금 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[사용자/장치 클레임 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec 확장 모드 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[감사 그룹 구성원](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec 주 모드 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec 빠른 모드 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[로그오프 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[로그온 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[네트워크 정책 서버 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[기타 로그온/로그오프 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[특수 로그온 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|개체 액세스| 감사는 네트워크 또는 컴퓨터에서 특정 개체 또는 유형의 개체에 액세스하려고 시도합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[애플리케이션 생성됨 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[인증 서비스 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[세부 파일 공유 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[파일 공유 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[감사 파일 시스템](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[필터링 플랫폼 연결 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[필터링 플랫폼 패킷 삭제 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[감사 핸들 조작](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[커널 객체 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[기타 개체 액세스 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[감사 레지스트리](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[이동식 스토리지 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[중앙 액세스 정책 준비 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|정책 변경|로컬 시스템 또는 네트워크의 중요한 보안 정책에 대한 변경 사항을 감사합니다. 정책은 일반적으로 네트워크 리소스를 보호하는 데 도움이 되는 관리자에 의해 설정됩니다. 이러한 정책을 변경하거나 변경하려는 시도를 모니터링하는 것은 네트워크의 보안 관리에서 중요한 측면이 될 수 있습니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[감사 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[인증 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[권한 부여 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[필터링 플랫폼 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC 규칙 수준 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[기타 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|권한 사용| 하나 이상의 시스템에 대한 특정 사용 권한의 사용을 감사합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[중요하지 않은 권한 사용 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[중요한 권한 사용 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[기타 권한 사용 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|시스템| 다른 범주에 포함되지 않고 보안에 영향을 미칠 수 있는 컴퓨터에 대한 시스템 수준 변경 사항을 감사합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[IPsec 드라이버 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[기타 시스템 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[보안 상태 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[보안 시스템 확장 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[시스템 무결성 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>카테고리별 이벤트 아이디

 Azure AD DS 보안 감사는 특정 작업이 감사 가능한 이벤트를 트리거할 때 다음 이벤트 ID를 기록합니다.

| 이벤트 범주 이름 | 이벤트 ID |
|:---|:---|
|계정 로그온 보안|4767, 4774, 4775, 4776, 4777|
|계정 관리 보안|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|세부 정보 추적 보안|None|
|DS 액세스 보안|5136, 5137, 5138, 5139, 5141|
|로그온-로그오프 보안|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|개체 액세스 보안|None|
|정책 변경 보안|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|권한 사용 보안|4985|
|시스템 보안|4612, 4621|

## <a name="next-steps"></a>다음 단계

Kusto에 대한 자세한 내용은 다음 문서를 참조하십시오.

* Kusto 쿼리 언어의 [개요입니다.](/azure/kusto/query/)
* [Kusto 자습서쿼리](/azure/kusto/query/tutorial) 기본 사항을 숙지합니다.
* 데이터를 볼 수 있는 새로운 방법을 알아보는 데 도움이 되는 [쿼리를 샘플링합니다.](/azure/kusto/query/samples)
* 성공을 위해 쿼리를 최적화하는 Kusto [모범 사례입니다.](/azure/kusto/query/best-practices)

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
