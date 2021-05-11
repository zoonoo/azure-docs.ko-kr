---
title: Azure AD Domain Services에 대한 보안 감사 사용 | Microsoft Docs
description: 보안 감사를 사용하여 Azure AD Domain Services의 분석 및 경고에 대한 이벤트 로깅을 중앙 집중화하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: caf46850b3d8d6946225575b8a9a732a90847482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574139"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에 대한 보안 감사 사용

Azure AD DS(Azure Active Directory Domain Services) 보안 감사를 통해 Azure에서 대상 리소스에 대한 보안 이벤트를 스트리밍할 수 있습니다. 이러한 리소스에는 Azure Storage, Azure Log Analytics 작업 영역 또는 Azure Event Hub가 포함됩니다. 보안 감사 이벤트를 사용하도록 설정한 후 Azure AD DS는 선택한 범주의 감사된 모든 이벤트를 대상 리소스로 보냅니다.

Azure Event Hubs를 사용하여 Azure 스토리지에 이벤트를 보관하고 이벤트를 SIEM(보안 정보 및 이벤트 관리) 소프트웨어(또는 이와 동등한 소프트웨어)로 스트리밍할 수 있습니다. Azure Portal에서 Azure Log Analytics 작업 영역을 사용하여 자체 분석을 수행할 수도 있습니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사는 Azure Resource Manager 기반 관리되는 도메인에 대해서만 사용할 수 있습니다. 마이그레이션 방법에 대한 자세한 내용은 [Azure AD DS를 클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션][migrate-azure-adds]을 참조하세요.

## <a name="security-audit-destinations"></a>보안 감사 대상

Azure AD DS 보안 감사에 대한 대상 리소스로 Azure Storage, Azure Event Hubs 또는 Azure Log Analytics 작업 영역을 사용할 수 있습니다. 이러한 대상은 조합할 수 있습니다. 예를 들어 Azure Storage를 사용하여 보안 감사 이벤트를 보관할 수 있지만 Azure Log Analytics 작업 영역을 사용하여 단기로 정보를 분석 및 보고할 수 있습니다.

다음 표에서는 각 대상 리소스 종류에 대한 시나리오를 간략하게 설명합니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사를 사용하도록 설정하기 전에 대상 리소스를 만들어야 합니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 이러한 리소스를 만들 수 있습니다.

| 대상 리소스 | 시나리오 |
|:---|:---|
|Azure Storage| 이 대상은 보관 목적으로 보안 감사 이벤트를 저장해야 하는 경우에 사용해야 합니다. 다른 대상은 보관 목적으로 사용할 수 있지만 이러한 대상은 보관의 기본 요구를 벗어난 기능을 제공합니다. <br /><br />Azure AD DS 보안 감사 이벤트를 사용하도록 설정하기 전에 먼저 [Azure Storage 계정을 만듭니다](../storage/common/storage-account-create.md).|
|Azure Event Hubs| 이 대상은 데이터 분석 소프트웨어 또는 SIEM(보안 정보 및 이벤트 관리) 소프트웨어와 같은 추가 소프트웨어를 사용하여 보안 감사 이벤트를 공유해야 하는 경우에 사용해야 합니다.<br /><br />Azure AD DS 보안 감사 이벤트를 사용하도록 설정하기 전에 [Azure Portal을 사용하여 이벤트 허브를 만듭니다](../event-hubs/event-hubs-create.md).|
|Azure Log Analytics 작업 영역| 이 대상은 Azure Portal에서 직접 보안 감사를 분석하고 검토해야 하는 경우에 주로 사용해야 합니다.<br /><br />Azure AD DS 보안 감사 이벤트를 사용하도록 설정하기 전에 [Azure Portal에 Log Analytics 작업 영역을 만듭니다](../azure-monitor/logs/quick-create-workspace.md).|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Azure Portal를 사용하여 보안 감사 이벤트 사용

Azure Portal를 사용하여 Azure AD DS 보안 감사 이벤트를 사용하도록 설정하려면 다음 단계를 완료합니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사는 소급되지 않습니다. 이전의 이벤트를 검색하거나 재생할 수 없습니다. Azure AD DS는 보안 감사를 사용하도록 설정한 후에 발생하는 이벤트만 보낼 수 있습니다.

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. Azure AD DS 창의 왼쪽에서 **진단 설정** 을 선택합니다.
1. 기본적으로 구성된 진단이 없습니다. 시작하려면 **진단 설정 추가** 를 선택합니다.

    ![Azure AD Domain Services에 대한 진단 설정 추가](./media/security-audit-events/add-diagnostic-settings.png)

1. 진단 구성에 대한 이름(예: *aadds-auditing*)을 입력합니다.

    원하는 보안 감사 대상의 확인란을 선택합니다. Azure Storage 계정, Azure 이벤트 허브 또는 Log Analytics 작업 영역에서 선택할 수 있습니다. 이러한 대상 리소스는 Azure 구독에 이미 있어야 합니다. 이 마법사에서는 대상 리소스를 만들 수 없습니다.

    ![캡처할 감사 이벤트의 필수 대상과 종류를 사용하도록 설정합니다.](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure Storage**
        * **스토리지 계정에 보관** 을 선택한 다음 **구성** 을 선택합니다.
        * **구독** 을 선택하고 감사 이벤트 보관에 사용할 **스토리지 계정** 을 선택합니다.
        * 준비가 되면 **확인** 을 선택합니다.
    * **Azure 이벤트 허브**
        * **이벤트 허브로 스트림** 을 선택한 다음 **구성** 을 선택합니다.
        * **구독** 을 선택하고 **이벤트 허브 네임스페이스** 를 선택합니다. 필요한 경우 **이벤트 허브 이름** 및 **이벤트 허브 정책 이름** 을 선택합니다.
        * 준비가 되면 **확인** 을 선택합니다.
    * **Azure Log Analytics 작업 영역**
        * **Log Analytics로 보내기** 를 선택하고 보안 감사 이벤트를 저장하는 데 사용하려는 **구독** 및 **Log Analytics 작업 영역** 을 선택합니다.

1. 특정 대상 리소스에 포함하려는 로그 범주를 선택합니다. Azure Storage 계정에 감사 이벤트를 보내는 경우 데이터를 보존할 일 수를 정의하는 보존 정책을 구성할 수도 있습니다. 기본 설정인 0은 모든 데이터를 유지하고 일정 시간 후에 이벤트를 삭제하지 않습니다.

    단일 구성 내에서 각 대상 리소스에 대해 서로 다른 로그 범주를 선택할 수 있습니다. 이 기능을 사용하여 Log Analytics에 대해 유지할 로그 범주와 보관할 로그 범주를 선택할 수 있습니다.

1. 완료되면 **저장** 을 선택하여 변경 사항을 커밋합니다. 대상 리소스는 구성이 저장된 후 즉시 Azure AD DS 보안 감사 이벤트 수신을 시작합니다.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Azure PowerShell을 사용하여 보안 감사 이벤트 사용

Azure PowerShell을 사용하여 Azure AD DS 보안 감사 이벤트를 사용하도록 설정하려면 다음 단계를 완료합니다. 필요한 경우 우선 [Azure PowerShell 모듈을 설치하고 Azure 구독에 연결](/powershell/azure/install-az-ps)합니다.

> [!IMPORTANT]
> Azure AD DS 보안 감사는 소급되지 않습니다. 이전의 이벤트를 검색하거나 재생할 수 없습니다. Azure AD DS는 보안 감사를 사용하도록 설정한 후에 발생하는 이벤트만 보낼 수 있습니다.

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 구독에 인증합니다. 메시지가 표시되면 계정 자격 증명을 입력합니다.

    ```azurepowershell
    Connect-AzAccount
    ```

1. 보안 감사 이벤트에 대한 대상 리소스를 만듭니다.

    * **Azure Storage** - [Azure PowerShell을 사용하여 스토리지 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure 이벤트 허브** - [Azure PowerShell을 사용하여 이벤트 허브 만들기](../event-hubs/event-hubs-quickstart-powershell.md). [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) cmdlet을 사용하여 이벤트 허브 네임스페이스에 Azure AD DS 권한을 부여해야 할 수 있습니다. 권한 부여 규칙에는 **관리**, **수신** 및 **전송** 권한이 포함되어야 합니다.

        > [!IMPORTANT]
        > 이벤트 허브 자체가 아니라 이벤트 허브 네임스페이스에 대한 권한 부여 규칙을 설정했는지 확인합니다.

    * **Azure Log Analytic 작업 영역** - [Azure PowerShell을 사용하여 Log Analytics 작업 영역을 만듭니다](../azure-monitor/logs/powershell-workspace-configuration.md).

1. [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) cmdlet을 사용하여 Azure AD DS 관리되는 도메인에 대한 리소스 ID를 가져옵니다. *$aadds.ResourceId* 라는 변수를 만들어 값을 보유합니다.

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) cmdlet을 사용하여 Azure Diagnostic 설정을 구성하고 Azure AD Domain Services 보안 감사 이벤트에 대한 대상 리소스를 사용합니다. 다음 예제에서는 이전 단계의 *$aadds.ResourceId* 변수가 사용됩니다.

    * **Azure Storage** - *storageAccountId* 를 스토리지 계정 이름으로 바꿉니다.

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure 이벤트 허브** - *eventHubName* 을 이벤트 허브의 이름으로, *eventHubRuleId* 를 권한 부여 규칙 ID로 바꿉니다.

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure Log Analytic 작업 영역** - *workspaceId* 를 Log Analytics 작업 영역의 ID로 바꿉니다.

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Azure Monitor를 사용하여 보안 감사 이벤트 쿼리 및 보기

Log Analytic 작업 영역을 사용하면 Azure Monitor 및 Kusto 쿼리 언어를 사용하여 보안 감사 이벤트를 보고 분석할 수 있습니다. 이 쿼리 언어는 읽기 쉬운 구문으로 강력한 분석 기능을 지원하는 읽기 전용 용도로 설계되었습니다. Kusto 쿼리 언어를 시작하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Monitor 설명서](../azure-monitor/index.yml)
* [Azure Monitor에서 Log Analytics 시작](../azure-monitor/logs/log-analytics-tutorial.md)
* [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/logs/get-started-queries.md)
* [Log Analytics 데이터 대시보드 만들기 및 공유](../azure-monitor/visualize/tutorial-logs-dashboards.md)

다음 샘플 쿼리를 사용하여 Azure AD DS에서 보안 감사 이벤트 분석을 시작할 수 있습니다.

### <a name="sample-query-1"></a>샘플 쿼리 1

지난 7일 간의 모든 계정 잠금 이벤트를 봅니다.

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>샘플 쿼리 2

날짜와 시간이 오름차순으로 정렬된 2020년 6월 3일 오전 9시부터 2020년 6월 10일 자정 사이의 모든 계정 잠금 이벤트(4740)를 봅니다.

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>샘플 쿼리 3

User라는 계정에 대해 (지금으로부터) 7일 전의 계정 로그인 이벤트를 확인합니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>샘플 쿼리 4

잘못된 암호를 사용하여 로그인을 시도한 User라는 계정에 대해 지금으로부터 7일 전의 계정 로그인 이벤트를 확인합니다(*0xC0000006a*).

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>샘플 쿼리 5

계정이 잠긴 동안 로그인을 시도한 User라는 계정에 대해 지금으로부터 7일 전의 계정 로그인 이벤트를 확인합니다(*0xC0000234*).

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>샘플 쿼리 6

모든 잠긴 사용자에게 발생한 모든 로그인 시도에 대해 지금으로부터 7일 전의 계정 로그인 이벤트 수를 확인합니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>감사 이벤트 범주

Azure AD DS 보안 감사는 기존의 AD DS 도메인 컨트롤러에 대한 일반적인 감사와 연계되어 있습니다. 하이브리드 환경에서는 기존 감사 패턴을 다시 사용할 수 있으므로 이벤트를 분석할 때 동일한 논리를 사용할 수 있습니다. 문제를 해결하거나 분석해야 하는 시나리오에 따라 다른 감사 이벤트 범주를 대상으로 해야 합니다.

다음 감사 이벤트 범주를 사용할 수 있습니다.

| 감사 범주 이름 | 설명 |
|:---|:---|
| 계정 로그온|도메인 컨트롤러 또는 로컬 SAM(보안 계정 관리자)의 계정 데이터 인증 시도를 감사합니다.</p>로그온 및 로그오프 정책 설정 및 이벤트 트랙은 특정 컴퓨터에 대한 액세스를 시도합니다. 이 범주의 설정 및 이벤트는 사용되는 계정 데이터베이스에 중점을 둡니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[자격 증명 유효성 검사 감사](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos 인증 오류 감사](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Kerberos 서비스 티켓 작업 감사](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[기타 로그온/로그오프 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| 계정 관리|사용자 및 컴퓨터 계정 및 그룹에 대한 변경 내용을 감사합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[애플리케이션 그룹 관리 감사](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[컴퓨터 계정 관리 감사](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[메일 그룹 관리 감사](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[기타 계정 관리 감사](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[보안 그룹 관리 감사](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[사용자 계정 관리 감사](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 세부 정보 추적|해당 컴퓨터에 있는 개별 애플리케이션 및 사용자의 작업을 감사하고 컴퓨터가 사용되는 방식을 이해합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[DPAPI 활동 감사](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP 작업 감사](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[프로세스 만들기 감사](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[프로세스 종료 감사](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC 이벤트 감사](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| 디렉터리 서비스 액세스|Active Directory Domain Services(AD DS)에 있는 개체에 대한 액세스 및 수정 시도를 감사합니다. 이러한 감사 이벤트는 도메인 컨트롤러에만 로그됩니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[세부 디렉터리 서비스 복제 감사](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[디렉터리 서비스 액세스 감사](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[디렉터리 서비스 변경 감사](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[디렉터리 서비스 복제 감사](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| 로그온-로그오프|대화형으로 또는 네트워크를 통한 컴퓨터 로그온 시도를 감사합니다. 이러한 이벤트는 사용자 작업을 추적하고 네트워크 리소스에 대한 잠재적인 공격을 식별하는 데 유용합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[계정 잠금 감사](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[사용자/디바이스 클레임 감사](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec 확장 모드 감사](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[그룹 멤버 자격 감사](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec 주 모드 감사](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec 빠른 모드 감사](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[로그오프 감사](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[로그온 감사](/windows/security/threat-protection/auditing/audit-logon)</li><li>[네트워크 정책 서버 감사](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[기타 로그온/로그오프 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[특수 로그온 감사](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|개체 액세스| 네트워크 또는 컴퓨터에서 특정 개체 또는 개체 유형에 대한 액세스 시도를 감사합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[애플리케이션 생성됨 감사](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[인증 서비스 감사](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[세부 파일 공유 감사](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[파일 공유 감사](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[파일 시스템 감사](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[필터링 플랫폼 연결 감사](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[필터링 플랫폼 패킷 삭제 감사](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[핸들 조작 감사](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[커널 개체 감사](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[기타 개체 액세스 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[레지스트리 감사](/windows/security/threat-protection/auditing/audit-registry)</li><li>[이동식 스토리지 감사](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM 감사](/windows/security/threat-protection/auditing/audit-sam)</li><li>[중앙 액세스 정책 준비 감사](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|정책 변경|로컬 시스템 또는 네트워크에서 중요 보안 정책 변경을 감사합니다. 정책은 일반적으로 관리자가 네트워크 리소스의 보안을 유지하도록 설정됩니다. 이러한 정책의 변경 또는 변경 시도 모니터링은 네트워크의 보안 관리에 있어 중요한 요소입니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[감사 정책 변경 감사](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[인증 정책 변경 감사](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[권한 부여 정책 변경 감사](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[필터링 플랫폼 정책 변경 감사](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC 규칙 수준 정책 변경 감사](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[기타 정책 변경 감사](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|권한 사용| 하나 이상의 시스템에서 특정 사용 권한의 사용을 감사합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[중요하지 않은 권한 사용 감사](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[중요한 권한 사용 감사](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[기타 권한 사용 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|시스템| 다른 범주에 포함되지 않고 잠재적으로 보안에 영향을 미칠 수 있는 시스템 수준 변경 내용을 감사합니다. 이 범주에는 다음 하위 범주가 포함됩니다.<ul><li>[IPsec 드라이버 감사](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[기타 시스템 이벤트 감사](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[보안 상태 변경 감사](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[보안 시스템 확장 감사](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[시스템 무결성 감사](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>범주별 이벤트 ID

 Azure AD DS 보안 감사는 특정 작업에서 감사 가능한 이벤트를 트리거할 때 다음 이벤트 ID를 기록합니다.

| 이벤트 범주 이름 | 이벤트 ID |
|:---|:---|
|계정 로그온 보안|4767, 4774, 4775, 4776, 4777|
|계정 관리 보안|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|세부 정보 추적 보안|없음|
|DS 액세스 보안|5136, 5137, 5138, 5139, 5141|
|로그온-로그오프 보안|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|개체 액세스 보안|없음|
|정책 변경 보안|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|권한 사용 보안|4985|
|시스템 보안|4612, 4621|

## <a name="next-steps"></a>다음 단계

Kusto 관련 정보는 다음 문서를 참조하세요.

* Kusto 쿼리 언어의 [개요](/azure/kusto/query/).
* 쿼리 기본 사항을 숙지하기 위한 [Kusto 자습서](/azure/kusto/query/tutorial).
* 데이터를 볼 수 있는 새로운 방법을 알아보는 데 도움이 되는 [샘플 쿼리](/azure/kusto/query/samples).
* 쿼리 최적화를 위한 Kusto [모범 사례](/azure/kusto/query/best-practices).

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md