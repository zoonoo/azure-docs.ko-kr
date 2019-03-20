---
title: Azure Stack 데이터 센터 통합-Identity
description: AD FS 데이터 센터를 사용 하 여 Azure Stack AD FS를 통합 하는 방법에 알아봅니다.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/04/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 5174723e7c9566fe90eb8bf49a1f353a5d9ae9ab
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58083974"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Azure Stack 데이터 센터 통합-Identity
Id 공급자로 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)를 사용 하 여 Azure Stack을 배포할 수 있습니다. Azure Stack을 배포 하기 전에 선택을 해야 합니다. 연결 된 시나리오에서는 Azure를 선택할 수 있습니다 AD 또는 AD FS 합니다. 연결이 끊긴된 시나리오에서 AD FS만 지원 됩니다.

> [!IMPORTANT]
> 전체 Azure Stack 솔루션 다시 배포 하지 않고 id 공급자를 전환할 수 없습니다.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory Federation Services 및 그래프

AD FS를 사용 하 여 배포 id를 기존 Active Directory 포리스트를 Azure Stack에서 리소스를 사용 하 여 인증할 수 있습니다. 이 기존 Active Directory 포리스트의 AD FS 페더레이션 트러스트를 만들 수 있도록 AD FS 배포에 필요 합니다.

인증은 id의 한 부분입니다. RBAC 역할 기반 액세스 제어 () Azure Stack에서 관리 하는 그래프 구성 요소 구성 되어야 합니다. 리소스에 대 한 액세스 위임 되는 그래프 구성 요소 LDAP 프로토콜을 사용 하 여 기존 Active Directory 포리스트의 사용자 계정을 조회 합니다.

![Azure AD FS 스택 아키텍처](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

기존 AD FS 계정 보안 토큰 서비스 (STS) Azure Stack AD FS (리소스 STS)에 대 한 클레임을 전송 하는 경우 Azure Stack automation 기존 AD FS에 대 한 메타 데이터 끝점을 사용 하 여 클레임 공급자 트러스트를 만듭니다.

기존 AD FS에서 신뢰 당사자 트러스트를 구성 합니다. 이 단계는 자동화를 통해 수행 되지 않습니다 및 연산자로 구성 해야 합니다. 패턴을 사용 하 여 AD FS에 대 한 Azure Stack VIP 끝점을 만들 수 있습니다 `https://adfs.<Region>.<ExternalFQDN>/`합니다.

신뢰 당사자 트러스트 구성 해야 Microsoft에서 제공 하는 클레임 변환 규칙을 구성할 수 있습니다.

그래프 구성에 대 한 기존 Active Directory에서 읽기 권한이 있는 서비스 계정 이어야 합니다. 이 계정은 RBAC 시나리오를 사용 하도록 설정 하는 자동화에 대 한 입력으로 합니다.

마지막 단계는 기본 공급자 구독에 대 한 새 소유자를 구성 합니다. 이 계정에 Azure Stack 관리자 포털에 로그인 하는 경우 모든 리소스에 대 한 전체 액세스 합니다.

Requirements:

|구성 요소|요구 사항|
|---------|---------|
|그래프|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Graph 통합 설정

그래프는 단일 Active Directory 포리스트를 사용 하 여 통합만 지원합니다. 여러 포리스트가 있는 경우 사용자 및 그룹을 가져올 구성에 지정 된 포리스트만 사용 됩니다.

다음 정보는 automation 매개 변수에 대 한 입력으로 필요 합니다.

|매개 변수|설명|예|
|---------|---------|---------|
|CustomADGlobalCatalog|Active Directory 포리스트에 대상의 FQDN<br>와 통합|Contoso.com|
|CustomADAdminCredentials|LDAP 읽기 권한이 있는 사용자|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Active Directory 사이트를 구성 합니다.

Active Directory 배포의 여러 사이트가 있는 경우 Azure Stack 배포에 가장 가까운 Active Directory 사이트를 구성 합니다. 구성을 원격 사이트에서 글로벌 카탈로그 서버를 사용 하 여 쿼리를 해결 하는 Azure Stack Graph 서비스를 하지 않아도 됩니다.

추가 Azure Stack [공용 VIP 네트워크](azure-stack-network.md#public-vip-network) Azure Stack에 가장 가까운 Active Directory 사이트에는 서브넷입니다. 예를 들어, Active Directory에 시애틀 및 레드먼드 시애틀 사이트에 배포 된 Azure Stack을 사용 하 여 두 사이트를 추가할 때 Azure Stack에 대 한 공용 VIP 네트워크 서브넷에 Active Directory 사이트에 시애틀에 대 한 합니다.

Active Directory 사이트에 대 한 자세한 내용은 참조 하세요 [사이트 토폴로지 디자인](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)합니다.

> [!Note]  
> 단일 사이트의 Active Directory를 구성 하는 경우이 단계를 건너뛸 수 있습니다. 범용 서브넷 구성 된 경우에 Azure Stack에 대 한 공용 VIP 네트워크 서브넷의 일부가 아닌지 확인 합니다.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>기존 Active Directory에서 사용자 계정 (선택 사항) 만들기

필요에 따라 기존 Active Directory의 Graph 서비스의 계정을 만들 수 있습니다. 사용 하려는 계정이 아직 없는 경우이 단계를 수행 합니다.

1. 기존 Active Directory에서 사용자 계정 (권장)를 만듭니다.
   - **사용자 이름**: graphservice
   - **암호**: 강력한 암호를 사용 하 여<br>만료 되지 않도록 암호를 구성 합니다.

   특정 권한 또는 멤버 자격이 필요 하지 않습니다.

#### <a name="trigger-automation-to-configure-graph"></a>그래프를 구성 하는 트리거 자동화

이 절차에서는 Azure Stack에서 권한 있는 끝점과 통신할 수 있는 데이터 센터 네트워크에서 컴퓨터를 사용 합니다.

1. 관리자 권한 Windows PowerShell 세션 (관리자 권한으로 실행)를 열고 권한 있는 끝점의 IP 주소에 연결 합니다. 에 대 한 자격 증명을 사용 하 여 **CloudAdmin** 인증할 수 있습니다.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 권한 있는 끝점에 연결 했으므로 다음 명령을 실행 합니다. 

   ```PowerShell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   메시지가 표시 되 면 (예: graphservice) 그래프 서비스에 사용 하려는 사용자 계정의 자격 증명을 지정 합니다. 등록 DirectoryService cmdlet에 대 한 입력 포리스트 이름/포리스트의 도메인 대신 포리스트의 다른 도메인 루트 해야 합니다.

   > [!IMPORTANT]
   > 자격 증명 팝업 될 때까지 기다립니다 (Get-credential 권한 있는 끝점에는 지원 되지 않습니다) 그래프 서비스 계정 자격 증명을 입력 합니다.

#### <a name="graph-protocols-and-ports"></a>그래프 프로토콜 및 포트

Azure Stack에서 그래프 서비스 키 배포 센터 (KDC) Active Directory 포리스트에 대상에서 로그인 요청을 처리할 수 있는 쓰기 가능한 글로벌 카탈로그 서버 (GC)와 통신 하는 다음 프로토콜 및 포트를 사용 합니다.

Azure Stack에서 그래프 서비스 대상 Active Directory와 통신 하는 다음 프로토콜 및 포트를 사용 합니다.

|Type|포트|프로토콜|
|---------|---------|---------|
|LDAP|389|TCP & UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>페더레이션 메타 데이터를 다운로드 하 여 AD FS 통합 설정

다음 정보를 반드시 automation 매개 변수에 대 한 입력으로:

|매개 변수|설명|예|
|---------|---------|---------|
|CustomAdfsName|클레임 공급자의 이름입니다.<br>이런 방식으로 AD FS 방문 페이지에 표시 됩니다.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|페더레이션 메타 데이터 링크| https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Azure Stack에서 클레임 공급자 트러스트를 구성 하는 트리거 자동화

이 절차에서는 Azure Stack에서 권한 있는 끝점과 통신할 수 있는 컴퓨터를 사용 합니다. 인증서 계정에 사용 되는 것입니다 **AD STS FS** Azure Stack에서 신뢰할 수 있습니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 권한 있는 끝점에 연결 합니다.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 권한 있는 끝점에 연결 했으므로 환경에 적합 한 매개 변수를 사용 하 여 다음 명령을 실행 합니다.

   ```PowerShell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. 환경에 적합 한 매개 변수를 사용 하 여 기본 공급자 구독 소유자를 업데이트 하려면 다음 명령을 실행 합니다.

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>페더레이션 메타 데이터 파일을 제공 하 여 AD FS 통합 설정

버전 1807 사용 하 여를 사용 하 여이 메서드는 다음 조건 중 하나에 해당할 경우:

- Azure Stack의 다른 모든 끝점에 비교 하는 AD FS 인증서 체인이 다릅니다.
- Azure Stack의 AD FS 인스턴스에서 기존 AD FS 서버에 없는 네트워크로 연결 되어 있습니다.

다음 정보를 반드시 automation 매개 변수에 대 한 입력으로:


|매개 변수|설명|예|
|---------|---------|---------|
|CustomAdfsName|클레임 공급자의 이름입니다. 이런 방식으로 AD FS 방문 페이지에 표시 됩니다.|Contoso|
|CustomADFSFederationMetadataFileContent|메타 데이터 콘텐츠|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>페더레이션 메타 데이터 파일 만들기

다음 절차에 대 한 계정이 STS는 기존 AD FS 배포에 대 한 네트워크 연결 된 컴퓨터를 사용 해야 합니다. 또한 필요한 인증서를 설치 해야 합니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 환경에 적합 한 매개 변수를 사용 하 여 다음 명령을 실행 합니다.

   ```PowerShell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. 권한 있는 끝점과 통신할 수 있는 컴퓨터에 메타 데이터 파일을 복사 합니다.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Azure Stack에서 클레임 공급자 트러스트를 구성 하는 트리거 자동화

이 절차에서는 Azure Stack에서 권한 있는 끝점과 통신 하 고 이전 단계에서 만든 메타 데이터 파일에 대 한 액세스 권한이 있는 컴퓨터를 사용 합니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 권한 있는 끝점에 연결 합니다.

   ```PowerShell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 권한 있는 끝점에 연결 했으므로 환경에 적합 한 매개 변수를 사용 하 여 다음 명령을 실행 합니다.

    ```PowerShell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. 환경에 적합 한 매개 변수를 사용 하 여 기본 공급자 구독 소유자를 업데이트 하려면 다음 명령을 실행 합니다.

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > 기존 AD FS (계정 STS)에서 인증서를 회전할 때 AD FS 통합을 다시 설정 해야 합니다. 메타 데이터 끝점에 연결할 수 있거나 메타 데이터 파일을 제공 하 여 구성 된 경우에 통합 설정 해야 합니다.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>기존 AD FS 배포 (계정 STS)에서 신뢰 당사자 구성

Microsoft는 클레임 변환 규칙을 포함 하 여 신뢰 당사자 트러스트를 구성 하는 스크립트를 제공 합니다. 명령을 수동으로 실행 하는 대로 스크립트를 사용 하는 것은 선택 사항입니다.

도우미 스크립트를 다운로드할 수 있습니다 [Azure Stack 도구](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) github입니다.

명령을 수동으로 실행 하려는 경우 다음이 단계를 수행 합니다.

1. 데이터 센터의 AD FS 인스턴스 또는 팜의 구성원에 (예: c:\ClaimRules.txt으로 저장).txt 파일에 다음 콘텐츠를 복사 합니다.

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. 에 대 한 Windows Forms 기반 인증의 유효성을 검사 엑스트라넷 인트라넷을 사용 하도록 설정 됩니다. 먼저 있는지 유효성 검사는 다음 cmdlet을 실행 하 여 이미 사용 하도록 설정 합니다.

   ```PowerShell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Windows 통합 인증 WIA () 지원 되는 사용자 에이전트 문자열 수 있습니다. AD FS 배포에 대 한 오래 된 최신 클라이언트를 지원 하도록 업데이트 해야 합니다. 문서에 사용자 에이전트 문자열을 지원 합니다 WIA를 업데이트 하는 방법에 대 한 자세한 내용은 읽어보세요 [WIA를 지원 하지 않는 장치에 대 한 구성 인트라넷 폼 기반 인증](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia)합니다.<br>양식 기반 인증 정책을 사용 하도록 설정 하는 단계 문서에 설명 되어 있습니다 [인증 정책 구성](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies)합니다.

3. 신뢰 당사자 트러스트를 추가 하려면 AD FS 인스턴스 또는 팜의 구성원에 다음 Windows PowerShell 명령을 실행 합니다. AD FS 끝점을 업데이트 해야 하 고 1 단계에서에서 만든 파일을 가리킵니다.

   **AD FS 2016에 대 한**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **For AD FS 2012/2012 R2**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Windows Server 2012 또는 2012 R2 AD FS를 사용 하는 경우 발급 권한 부여 규칙을 구성 하려면 AD FS MMC 스냅인을 사용 해야 합니다.

4. Internet Explorer 또는 Microsoft Edge 브라우저를 사용 하 여 Azure Stack을 액세스할 때 토큰 바인딩을 무시 해야 합니다. 그렇지 않은 경우 로그인 시도 실패합니다. AD FS 인스턴스 또는 팜 구성원에서 다음 명령을 실행 합니다.

   > [!note]  
   > Windows Server 2012 또는 2012 R2 AD FS를 사용 하는 경우에이 단계가 적용 하지 않습니다. 이 명령은 건너뛰고 통합을 사용 하 여 계속 해도 됩니다.

   ```PowerShell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>SPN 만들기

인증에 대 한 서비스 주체 이름 (SPN) 사용 해야 하는 많은 시나리오가 있습니다. 다음은 몇 가지 예입니다.

- Azure Stack의 AD FS 배포를 사용 하 여 CLI 사용
- AD FS를 사용 하 여 배포 하는 경우 Azure Stack 용 system Center 관리 팩
- AD FS를 사용 하 여 배포 하는 경우 Azure Stack에서 리소스 공급자
- 다양 한 응용 프로그램
- 비 대화형 로그인 필요

> [!Important]  
> AD FS는만 대화형 로그온 세션을 지원합니다. 자동화 된 시나리오는 비 대화형 로그온 해야 하는 경우 SPN을 사용 해야 합니다.

SPN을 만드는 방법에 대 한 자세한 내용은 참조 하세요. [AD FS에 대 한 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)합니다.


## <a name="troubleshooting"></a>문제 해결

### <a name="configuration-rollback"></a>구성 롤백

오류가 발생 환경을 더 이상 인증할 수 없는 상태를 유지 하는 rollback 옵션을 사용할 수 없습니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 다음 명령을 실행 합니다.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 다음 cmdlet을 실행 합니다.

   ```PowerShell  
   Reset-DatacenterIntegrationConfiguration
   ```

   롤백 작업을 실행 한 후 모든 구성 변경 내용이 롤백됩니다. 기본 제공 인증만 **CloudAdmin** 사용자가 가능 합니다.

   > [!IMPORTANT]
   > 기본 공급자 구독의 원래 소유자를 구성 해야 합니다.

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>추가 로그를 수집합니다.

Cmdlet 중 하나라도 실패를 사용 하 여 추가 로그를 수집할 수 있습니다는 `Get-Azurestacklogs` cmdlet.

1. 관리자 권한 Windows PowerShell 세션을 열고 다음 명령을 실행 합니다.

   ```PowerShell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 그런 다음 다음 cmdlet을 실행 합니다.

   ```PowerShell  
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>다음 단계

[외부 모니터링 솔루션 통합](azure-stack-integrate-monitor.md)
