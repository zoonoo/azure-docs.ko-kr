---
title: "Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 구성하는 방법 | Microsoft Docs"
description: "Azure Active Directory에서 Windows 도메인 가입 장치를 자동으로 등록하도록 설정합니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 617599c7df6284e6319a1b3331d1e894e5d4b2d1
ms.openlocfilehash: 2ced7d0d4e2e653013c605932066c412c4202082


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 구성하는 방법

[Azure Active Directory 장치 기반 조건부 액세스](active-directory-conditional-access.md)를 사용하려면 컴퓨터를 Azure AD(Azure Active Directory)에 등록해야 합니다. 이 문서는 조직에서 Azure AD를 사용하여 Windows 도메인 가입 장치의 자동 등록을 구성하는 단계를 제공합니다.

> [!NOTE]
> Windows 10 11월 업데이트는 Azure AD에서 향상된 사용자 경험 중 일부를 지원하지만, Windows 10 1주년 업데이트는 장치 기반 조건부 액세스를 완벽하게 지원합니다. 조건부 액세스에 대한 자세한 내용은 [Azure Active Directory 장치 기반 조건부 액세스](active-directory-conditional-access.md)를 참조하세요. 작업 공간의 Windows 10 장치 및 Azure AD에 이 장치를 등록하는 방법에 대한 자세한 내용은 [엔터프라이즈용 Windows 10: 작업용 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)을 참조하세요.
> 
> 

Windows를 실행하는 장치의 경우 다음을 비롯한 이전 버전의 Windows를 등록할 수 있습니다.

- Windows 8.1
- Windows 7

Windows Server를 실행하는 장치의 경우 다음 플랫폼을 등록할 수 있습니다.

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2



## <a name="prerequisites"></a>필수 조건

Azure AD를 사용하여 도메인 가입 장치를 자동 등록하는 데 필요한 기본 요구 사항으로 최신 버전의 Azure AD Connect가 있어야 합니다.

빠른 설치, 사용자 지정 설치, 전체 업그레이드 중 어느 것에도 상관없이 Azure AD Connect를 배포한 방법에 따라 자동으로 구성되었을 수도 있는 필수 조건은 다음과 같습니다.

- **온-프레미스 Active Directory의 서비스 연결 지점** - Azure AD에 등록한 컴퓨터에서 Azure AD 테넌트 정보를 검색하기 위한 용도입니다.
 
- **AD FS(Active Directory Federation Services) 발급 변환 규칙** – 등록 시 컴퓨터 인증을 위한 용도입니다(페더레이션된 구성에 적용 가능).

조직의 일부 장치가 Windows 10 도메인 가입 장치가 아닌 경우 다음 단계를 수행합니다.

* 사용자가 장치를 등록할 수 있도록 Azure AD에 정책을 설정합니다.
* IWA(Windows 통합 인증)를 다단계 인증의 유효한 대안으로 AD FS에 설정합니다.

## <a name="step-1-configure-service-connection-point"></a>1단계: 서비스 연결 지점 구성 

서비스 연결 지점(SCP) 개체가 컴퓨터 도메인의 구성 명명 컨텍스트 파티션에 반드시 있어야 합니다. 서비스 연결점에는 컴퓨터를 등록하는 Azure AD 테넌트에 대한 검색 정보가 포함됩니다. 다중 포리스트 Active Directory 구성에서는 도메인 가입 컴퓨터를 포함한 모든 포리스트에 서비스 연결점이 있어야 합니다.

SCP는 다음 위치에 있습니다.  

**CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[구성 명명 컨텍스트]**

Active Directory 도메인 이름이 *example.com*인 포리스트의 경우 구성 명명 컨텍스트는 다음과 같습니다.  

**CN=Configuration,DC=example,DC=com**

다음 Windows PowerShell 스크립트를 사용하여 개체의 존재를 확인하고 검색 값을 검색할 수 있습니다. 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

**$scp.Keywords** 출력은 Azure AD 테넌트 정보를 보여줍니다. 예:

azureADName:microsoft.com  
azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

서비스 연결 지점이 없는 경우 Azure AD Connect 서버에서 다음 PowerShell 스크립트를 실행하여 만들 수 있습니다.

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;



**설명**

- **$aadAdminCred = Get-Credential**을 실행할 때 사용자 이름을 입력해야 합니다. 사용자 이름은 **user@example.com** 형식을 사용하세요. 


- **Initialize-ADSyncDomainJoinedComputerSync** cmdlet을 실행하는 경우 [*커넥터 계정 이름*]을 Active Directory 커넥터 계정에서 사용하는 도메인 계정으로 바꿉니다.
  
- cmdlet에는 도메인 컨트롤러의 Active Directory Web Services에 속한 Active Directory PowerShell 모듈이 사용됩니다. Active Directory Web Services는 Windows Server 2008 R2 이상의 도메인 컨트롤러에서 지원됩니다. Windows Server 2008 이하 버전의 도메인 컨트롤러에 대한 서비스 연결점을 만들려면 PowerShell을 통해 System.DirectoryServices API를 사용한 다음 키워드 값을 할당합니다.
 
 



##<a name="step-2-register-your-devices"></a>2단계: 장치 등록

장치를 등록하는 올바른 단계는 조직의 페더레이션 여부에 따라 다릅니다. 


### <a name="device-registration-in-non-federated-organizations"></a>페더레이션되지 않은 조직의 장치 등록

페더레이션되지 않은 조직의 장치 등록은 다음이 참인 경우에만 지원됩니다.

- 장치에서 Windows 10 또는 Windows Server 2016을 실행 중입니다.
- 장치가 도메인에 가입되어 있습니다.
- Azure AD Connect를 사용한 암호 동기화를 사용하도록 설정되어 있습니다.

이러한 요구 사항을 모두 충족하는 경우 장치를 등록하기 위해 아무것도 할 필요가 없습니다.  


### <a name="device-registration-in-federated-organizations"></a>페더레이션된 조직의 장치 등록

페더레이션된 Azure AD 구성에서 장치는 AD FS(또는 온-프레미스 페더레이션 서버)를 사용하여 Azure AD를 인증한 다음, Azure Active Directory Device Registration Service에 등록합니다.

Windows 10 및 Windows Server 2016 컴퓨터의 경우 Azure AD Connect는 Azure AD의 장치 개체를 온-프레미스 컴퓨터 계정 개체와 연결합니다. 등록을 완료하고 장치 개체를 만들기 위해 Azure AD Device Registration Service 인증 시 있어야 하는 클레임은 다음과 같습니다.

- **http://schemas.microsoft.com/ws/2012/01/accounttype** - 주체 인증자를 도메인 가입 컴퓨터로 식별하는 DJ 값을 포함합니다.

- **http://schemas.microsoft.com/identity/claims/onpremobjectguid** - 온-프레미스 컴퓨터 계정의 **objectGUID** 특성 값을 포함합니다.
 
- **http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid** - 온-프레미스 컴퓨터 계정의 **objectSid** 특성 값에 해당하는 컴퓨터의 기본 보안 식별자(SID)를 포함합니다.

- **http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid** - Azure AD가 AD FS 또는 온-프레미스 보안 토큰 서비스(STS)에서 발급된 토큰을 신뢰하는 데 사용하는 값을 포함합니다. Azure AD에 확인된 도메인이 여러 개 있는 경우 중요합니다. AD FS의 경우 **http://\<*domain-name*\>/adfs/services/trust/**를 사용하며, 여기서 **\<domain-name\>**은 Azure AD에서 확인된 도메인 이름입니다.

확인된 도메인 이름에 대한 자세한 내용은 [Azure Active Directory에 사용자 지정 도메인 이름 추가](active-directory-add-domain.md)를 참조하세요.  
확인된 회사 도메인 목록을 보려면 the [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain) cmdlet을 사용합니다. 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)


Windows 10 및 Windows Server 2016 도메인 가입 컴퓨터는 AD FS에서 호스팅된 활성 WS-Trust 끝점에 Windows 통합 인증을 사용하여 인증합니다. 이 끝점을 사용할 수 있어야 합니다. 또한 웹 인증 프록시를 사용하는 경우 이 끝점이 프록시를 통해 게시되어야 합니다. 끝점은 **adfs/services/trust/13/windowstransport**입니다. 

AD FS 관리 콘솔의 **서비스 > 끝점**에서 사용하도록 설정되어야 합니다. 온-프레미스 페더레이션 서버인 AD FS가 없는 경우 공급업체의 지침에 따라 해당 끝점을 사용하도록 설정해야 합니다. 



> [!NOTE]
> 온-프레미스 페더레이션 서버로 사용되는 AD FS가 없는 경우 공급업체의 지침에 따라 이러한 클레임을 발급하는 규칙을 만듭니다.
> 
> 




**AD FS에서 수동으로 규칙을 만들려면:**

- 다음 Windows PowerShell 스크립트 중 하나를 선택합니다. 
- 서버에 연결된 세션에서 Windows PowerShell 스크립트를 실행합니다. 
- 첫째 줄을 Azure AD에서 조직의 인증된 도메인 이름으로 바꿉니다.




#### <a name="setting-ad-fs-rules-in-a-single-domain-environment"></a>단일 도메인 환경에서 AD FS 규칙 설정

**인증된 도메인이 하나**만 있는 경우 다음 스크립트를 사용하여 AD FS 규칙을 추가합니다.


    <#----------------------------------------------------------------------
    |   Modify the Azure AD Relying Party to include the claims needed
    |   for DomainJoin++. The rules include:
    |   -ObjectGuid
    |   -AccountType
    |   -ObjectSid
    +---------------------------------------------------------------------#>

    $rule1 = '@RuleName = "Issue object GUID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(claim = c2);' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 


#### <a name="setting-ad-fs-rules-in-a-multi-domain-environment"></a>다중 도메인 환경에서 AD FS 규칙 설정

인증된 도메인이 둘 이상이면 다음 단계를 수행합니다.

1. Azure AD Connect에서 만든 기존 **IssuerID** 규칙을 제거합니다.  
이 규칙에 대한 예제: c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 


2. 다음 스크립트를 실행합니다. 

        <#----------------------------------------------------------------------  
        |   Modify the Azure AD Relying Party to include the claims needed  
        |   for DomainJoin++. The rules include:
        |   -ObjectGuid
        |   -AccountType
        |   -ObjectSid
        +---------------------------------------------------------------------#>

        $VerifiedDomain = 'example.com'      # Replace example.com with one of your verified domains

        $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

        $rule2 = '@RuleName = "Issue account type for domain joined computers" 

        c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

        $rule3 = '@RuleName = "Pass through primary SID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(claim = c2);' 

        $rule4 = '@RuleName = "Issue AccountType with the value User when its not a computer account" 

        NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

        => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

        $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

        c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

        $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

        c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$VerifiedDomain+'/adfs/services/trust/");' 

        $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

        $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4+ $rule5+  $rule6 

        $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

        Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 



## <a name="step-3-setup-ad-fs-for-authentication-of-device-registration"></a>3단계: 장치 등록 인증을 위한 AD FS 설정

AD FS에서 IWA가 장치 등록을 위한 Multi-Factor Authentication의 유효한 대안으로 설정되어 있는지 확인해야 합니다. 이렇게 하려면 인증 방법을 통해 전달하는 발급 변환 규칙이 있어야 합니다.

1. AD FS 관리 콘솔에서 **AD FS** > **트러스트 관계** > **신뢰 당사자 트러스트**로 이동합니다.
2. 마우스 오른쪽 단추로 Microsoft Office 365 ID 플랫폼 신뢰 당사자 트러스트 개체를 클릭하고 **클레임 규칙 편집**을 선택합니다.
3. **발급 변환 규칙** 탭에서 **규칙 추가**를 선택합니다.
4. **클레임 규칙** 템플릿 목록에서 **사용자 지정 규칙을 사용하여 클레임 보내기**를 선택합니다.
5. **다음**을 선택합니다.
6. **클레임 규칙 이름** 상자에 **인증 방법 클레임 규칙**을 입력합니다.
7. **클레임 규칙** 상자에 다음 규칙을 입력합니다.  
**c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);**
8. 페더레이션 서버에서 다음 PowerShell 명령을 입력합니다.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName\>**은 Azure AD 신뢰 당사자 트러스트 개체의 신뢰 당사자 개체 이름입니다. 일반적으로 이 개체의 이름은 **Microsoft Office 365 ID 플랫폼**입니다.



##<a name="step-4-deployment-and-rollout"></a>4단계: 배포 및 롤아웃

도메인 가입 컴퓨터에서 필수 조건을 충족하면 Azure AD에 등록할 준비가 된 것입니다.

다음에 장치를 다시 시작하거나 사용자가 Windows에 로그인할 때 Windows 10 1주년 업데이트 및 Windows Server 2016 도메인 가입 컴퓨터가 Azure AD에 자동으로 등록됩니다. 새 도메인 가입 컴퓨터는 도메인 가입 이후 다시 시작할 때 Azure AD에 등록됩니다.

> [!NOTE]
> Windows 10 11월 업데이트를 실행하는 Windows 10 도메인 가입 컴퓨터는 롤아웃 그룹 정책 개체를 설정한 경우에만 Azure AD에 자동으로 등록됩니다.
> 
> 

그룹 정책 개체를 사용하면 Windows 10 및 Windows Server 2016 도메인 가입 컴퓨터의 자동 등록 롤아웃을 제어할 수 있습니다. 

비 Windows 10 도메인 가입 컴퓨터의 자동 등록을 롤아웃하려면 선택한 컴퓨터에 Windows Installer 패키지를 배포하면 됩니다.

> [!NOTE]
> 모든 비 Windows 10/Windows Server 2016 컴퓨터의 경우 이 문서 아래쪽에서 설명하는 대로 Windows Installer 패키지를 사용하는 것이 좋습니다.
> 
> 

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>자동 등록 롤아웃을 제어하는 그룹 정책 개체 만들기

Azure AD를 사용하여 도메인 가입 컴퓨터의 자동 등록 롤아웃을 제어하려면 **도메인 가입 컴퓨터를 장치로 등록** 그룹 정책을 등록하려는 컴퓨터에 배포하면 됩니다. 예를 들어 조직 구성 단위 또는 보안 그룹에 정책을 배포할 수 있습니다.

**정책을 설정하려면:**

1. [서버 관리자]를 연 다음 **도구** > **그룹 정책 관리**로 이동합니다.
2. Windows 10 또는 Windows Server 2016 컴퓨터의 자동 등록을 사용하도록 설정하려는 도메인에 해당하는 도메인 노드로 이동합니다.
3. **그룹 정책 개체**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 선택합니다.
4. 그룹 정책 개체의 이름을 입력합니다. 예: *Azure AD에 자동 등록*. **확인**을 선택합니다.
5. 마우스 오른쪽 단추로 새 그룹 정책 개체를 클릭한 다음 **편집**을 선택합니다.
6. **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **장치 등록**으로 이동합니다. 마우스 오른쪽 단추로 **도메인 가입 컴퓨터를 장치로 등록**을 클릭한 다음 **편집**을 선택합니다.
   
   > [!NOTE]
   > 이 그룹 정책 템플릿 이름은 이전 버전의 그룹 정책 관리 콘솔에서 변경되었습니다. 이전 버전의 콘솔을 사용하는 경우 **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **작업 공간 연결** > **클라이언트 컴퓨터와 작업 공간 자동 연결**로 이동합니다.
   > 
   > 
7. **사용**, **적용**을 차례로 선택합니다.
8. **확인**을 선택합니다.
9. 그룹 정책 개체를 선택한 위치에 연결합니다. 예를 들어 특정 조직 구성 단위에 연결할 수 있습니다. 또한 Azure AD에 자동으로 등록되는 컴퓨터의 특정 보안 그룹에 연결할 수도 있습니다. 조직의 모든 Windows 10 및 Windows Server 2016 도메인 가입 컴퓨터에 대해 이 정책을 사용하도록 설정하려면 그룹 정책 개체를 해당 도메인에 연결합니다.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>비 Windows 10 컴퓨터용 Windows Installer 패키지
페더레이션된 환경에서 Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2를 실행하는 도메인 가입 컴퓨터를 등록하려면 다음과 같은 Windows Installer 패키지 파일(.msi)을 다운로드하여 설치하면 됩니다.

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

System Center Configuration Manager와 같은 소프트웨어 배포 시스템을 사용하여 패키지를 배포합니다. 이 패키지는 *quiet* 매개 변수를 사용하여 표준 자동 설치 옵션을 지원합니다. System Center Configuration Manager 2016을 사용하면 완료된 등록을 추적하는 기능과 같은 이점이 추가로 제공됩니다. 자세한 내용은 [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center)을 참조하세요.

설치 관리자에서는 사용자 컨텍스트에서 실행되도록 예약된 작업을 시스템에 만듭니다. 사용자가 Windows에 로그인할 때 이 작업이 트리거됩니다. 이 작업은 IWA를 통해 인증한 후 사용자 자격 증명으로 Azure AD에 장치를 자동으로 등록합니다. 예약된 작업을 보려면 **Microsoft** > **작업 공간 연결**, [작업 스케줄러] 라이브러리로 이동합니다.

## <a name="next-steps"></a>다음 단계

- 자동 장치 등록 중에 오류가 발생하면 다음 문제 해결 항목을 참조하세요.
    - [Windows 10 및 Windows Server 2016](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows.md)
    - [Windows 하위 수준 클라이언트](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy.md)
- 일반적인 질문에 대한 답변을 보려면 [자동 장치 등록 FAQ](active-directory-conditional-access-automatic-device-registration-faq.md)를 참조하세요.




<!--HONumber=Feb17_HO2-->


