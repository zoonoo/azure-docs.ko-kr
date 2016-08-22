<properties
	pageTitle="Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 설정하는 방법 | Microsoft Azure"
	description="IT 관리자는 도메인 가입 Windows 장치를 Azure AD(Azure Active Directory)에 자동으로 등록하도록 선택할 수 있습니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="markvi"/>



# Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 설정하는 방법 

[Azure Active Directory 장치 기반 조건부 액세스](active-directory-conditional-access.md)를 사용하도록 설정하려면 Windows 도메인 가입 컴퓨터를 Azure AD에 등록해야 합니다.

회사 또는 학교 계정의 사용을 허용하는 등의 업데이트를 통해 Azure AD 앱에 향상된 SSO 환경을 제공하고, 장치 간 엔터프라이즈 설정 로밍을 지원하며, 비즈니스용 Windows 스토어를 사용하고, Windows Hello로 보다 강력한 인증 및 편리한 로그인을 적용할 수 있습니다.

> [AZURE.NOTE] Windows 10 2015년 11월 업데이트는 향상된 사용자 경험 중 일부를 지원하지만 이는 장치 기반 조건부 액세스를 완전히 지원하는 1주년 업데이트입니다. 조건부 액세스에 대한 자세한 내용은 [Azure Active Directory 장치 기반 조건부 액세스](active-directory-conditional-access.md)를 참조하세요. 작업 공간의 Windows 10 장치 및 Azure AD에 등록할 경우 사용자가 얻게 되는 경험에 대한 자세한 내용은 [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)을 참조하세요.


등록은 다음을 비롯한 이전 버전의 Windows에서 지원됩니다.

- Windows 8.1

- Windows 8.0

- Windows 7

Windows Server 컴퓨터를 데스크톱으로 사용하는 경우(예: 개발자가 Windows Server를 주 컴퓨터로 사용하는 경우) 다음 플랫폼을 등록할 수 있습니다.

- Windows Server 2016

- Windows Server 2012 R2

- Windows Server 2012

- Windows Server 2008 R2

Windows 도메인 가입 장치를 조직의 Azure AD에 등록하기 위해 사용자 환경에서 수행해야 하는 작업은 다음과 같습니다.

1. 필수 조건

2. 배포 및 롤아웃

3. 문제 해결

4. FAQ

 

## 필수 조건 

도메인 가입 장치의 Azure AD 자동 등록을 사용하도록 설정하기 위한 기본 요구 사항은 Azure AD Connect의 최신 버전을 설치하는 것입니다.

빠른 설치든, 사용자 지정 설치든, 전체 업그레이드든 상관없이 Azure AD Connect를 배포한 방법에 따라 다음 필수 구성 요소가 자동으로 구성되었을 수 있습니다.

1. Azure AD에 등록하는 컴퓨터에서 Azure AD 테넌트 정보를 검색할 수 있도록 온-프레미스 Active Directory에 SCP(서비스 연결 지점) 구성

2. 등록 시 컴퓨터 인증을 위한 AD FS 발급 변환 규칙(페더레이션된 구성에 적용)

조직에 비 Windows 10 도메인 가입 컴퓨터가 있는 경우 다음이 필요합니다.

1. 사용자가 장치를 등록하도록 허용하는 정책이 Azure AD에 설정되어 있는지 확인합니다.

2. AD FS에서 WIA(Windows 통합 인증)가 MFA(다단계 인증)의 유효한 대안으로 설정되어 있는지 확인합니다.

 

## Azure AD 테넌트 검색을 위한 서비스 연결 지점 

컴퓨터를 등록할 Azure AD 테넌트에 대한 검색 정보를 유지하는 SCP 개체가 해당 컴퓨터가 가입된 도메인 포리스트의 구성 명명 컨텍스트 파티션에 있어야 합니다. 다중 포리스트 구성의 Active Directory에서는 컴퓨터가 가입된 모든 포리스트에 SCP가 있어야 합니다.

Active Directory의 다음 위치에 SCP가 있어야 합니다.

	CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context] 

> [AZURE.NOTE] Active Directory 도메인 이름이 example.com인 포리스트의 경우 구성 명명 컨텍스트는 CN=Configuration,DC=example,DC=com입니다.

다음 PowerShell 스크립트를 사용하여 개체의 존재 여부 및 Azure AD 테넌트의 검색 값을 확인할 수 있습니다(예제의 구성 명명 컨텍스트를 자신에게 맞게 대체).

	$scp = New-Object System.DirectoryServices.DirectoryEntry; 

	$scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com"; 

	$scp.Keywords; 

$scp.Keywords 출력은 다음과 같은 Azure AD 테넌트 정보를 표시합니다.

	azureADName:microsoft.com 

	azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47 

SCP가 존재하지 않는 경우 Azure AD Connect 서버에서 다음 PowerShell 스크립트를 실행하여 만들 수 있습니다.

	Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1"; 

	$aadAdminCred = Get-Credential; 

	Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred; 


> [AZURE.NOTE] $aadAdminCred = Get-Credential을 실행하는 경우 Get-Credential 팝업이 표시될 때 입력되는 자격 증명의 사용자 이름에 대한 형식 user@example.com을 사용합니다. cmdlet Initialize-ADSyncDomainJoinedComputerSync를 실행하는 경우 [커넥터 계정 이름]을 Active Directory Connector 계정으로 사용하는 도메인 계정으로 바꿉니다. 위 cmdlet에서는 DC(도메인 컨트롤러)의 ADWS(Active Directory 웹 서비스)에 의존하는 Active Directory PowerShell 모듈을 사용합니다. ADWS는 Windows Server 2008 R2 이상 DC에서 지원됩니다. Windows Server 2008 DC 이하만 있는 경우 PowerShell을 통해 System.DirectoryServices API를 사용하여 SCP를 만들고 적절한 키워드 값을 할당할 수 있습니다.

## 컴퓨터 즉시 등록에 대한 AD FS 규칙(페더레이션된 조직) 

Azure AD 페더레이션 구성의 경우 컴퓨터는 AD FS(또는 온-프레미스 페더레이션 서버)에 의존하여 Azure DRS(Azure Device Registration Service)에 대해 등록을 위한 Azure AD를 인증합니다.

> [AZURE.NOTE] 페더레이션되지 않은 구성(즉, 사용자 암호 해시가 Azure AD에 동기화됨)의 경우 Windows 10 및 Windows Server 2016 도메인 가입 컴퓨터는 온-프레미스 컴퓨터 계정에 작성하고 Azure AD Connect를 통해 Azure AD로 가져오는 자격 증명을 사용하여 Azure DRS에 대해 인증합니다. 페더레이션되지 않은 구성의 비 Windows 10/Windows Server 2016 컴퓨터의 경우 조직에서 장치 기반 CA를 사용하도록 설정할 수 있는 옵션은 이 문서 아래쪽의 배포 및 롤아웃에서 비 Windows 10/Windows Server 2016 도메인 가입 컴퓨터 등록용 Windows Installer 패키지 섹션을 참조하세요.

Windows 10 및 Windows Server 2016 컴퓨터의 경우 Azure AD Connect는 Azure AD의 장치 개체를 온-프레미스 컴퓨터 계정 개체와 연결합니다. 이 연결이 작동하려면 등록을 완료하고 장치 개체를 처음으로 만들기 위해 Azure DRS 인증하는 동안 다음 클레임이 존재해야 합니다.

- `http://schemas.microsoft.com/ws/2012/01/accounttype` - 도메인 가입 컴퓨터로 인증하는 주체를 식별하는 “DJ” 값을 포함합니다.
- `http://schemas.microsoft.com/identity/claims/onpremobjectguid` - 온-프레미스 컴퓨터 계정의 objectGUID 특성 값을 포함합니다.
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` - 온-프레미스 컴퓨터 계정의 objectSid 특성 값에 해당하는 컴퓨터 주 SID를 포함합니다.
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` - Azure AD가 AD FS 또는 온-프레미스 STS에서 발급된 토큰을 신뢰하도록 허용하는 적절한 값을 포함합니다. 이는 컴퓨터가 AD FS 또는 온-프레미스 STS가 있는 Azure AD에 연결하는 포리스트가 아닌 다른 포리스트에 가입될 수 있는 다중 포리스트 Active Directory 구성에 중요합니다. AD FS의 경우 값은 `http://<domain-name>/adfs/services/trust/`여야 합니다(여기서 “<domain-name>”은 유효성이 검사된 Azure AD의 도메인 이름).

AD FS에서 이러한 규칙을 수동으로 만들려면 서버에 연결된 세션에서 다음 PowerShell 스크립트를 사용하면 됩니다. 첫째 줄을 유효성이 검사된 Azure AD의 도메인 이름으로 바꿔야 합니다.

> [AZURE.NOTE] 온-프레미스 페더레이션 서버인 AD FS가 없는 경우 공급업체의 지침에 따라 이러한 클레임을 발급하는 적절한 규칙을 만듭니다.

	$validatedDomain = "example.com"      # Replace example.com with your validated domain name in Azure AD 

	$rule1 = '@RuleName = "Issue object GUID" 

		c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      	=> issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

	$rule2 = '@RuleName = "Issue account type for domain joined computers" 

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

	$rule3 = '@RuleName = "Pass through primary SID" 

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

      => issue(claim = c2);' 

	$rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account" 

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

	$rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

	$rule6 = '@RuleName = "Update issuer for DJ computer auth" 

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://$validatedDomain/adfs/services/trust/");' 

	$existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

	$updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6 

	$crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 
 
	Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

> [AZURE.NOTE] Windows 10 및 Windows Server 2016 도메인 가입 컴퓨터는 AD FS에서 호스팅된 활성 WS-Trust 끝점에 Windows 통합 인증을 사용하여 인증합니다. 이 끝점을 사용할 수 있어야 합니다. 또한 웹 인증 프록시를 사용하는 경우 이 끝점이 프록시를 통해 게시되어야 합니다. 끝점은 adfs/services/trust/13/windowstransport입니다. AD FS 관리 콘솔의 서비스 > 끝점에 사용하도록 설정된 것으로 표시되어야 합니다. 온-프레미스 페더레이션 서버인 AD FS가 없는 경우 공급업체의 지침에 따라 해당 끝점을 사용하도록 설정해야 합니다.

 

## AD FS가 등록을 위한 장치 인증을 지원하도록 설정되었는지 확인

AD FS에서 WIA(Windows 통합 인증)가 장치 등록을 위한 MFA(다단계 인증)의 유효한 대안으로 설정되어 있는지 확인해야 합니다.

그러려면 인증 방법을 전달하는 발급 변환 규칙이 있어야 합니다.

1. AD FS 관리 콘솔을 열고 **AD FS > 트러스트 관계 > 신뢰 당사자 트러스트**로 이동합니다.

2. Microsoft Office 365 ID 플랫폼 신뢰 당사자 트러스트 개체를 마우스 오른쪽 단추로 클릭하고 **클레임 규칙 편집**을 선택합니다.

2.	**발급 변환 규칙** 탭에서 **규칙 추가**를 선택합니다.

3.	**클레임 규칙** 템플릿 목록에서 **사용자 지정 규칙을 사용하여 클레임 보내기**를 선택합니다.

4.	**다음**을 선택합니다.

4.	**클레임 규칙 이름** 텍스트 상자에 **인증 방법 클레임 규칙**을 입력합니다.

5.	**클레임 규칙** 텍스트 상자에 `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
=> issue(claim = c);`를 입력합니다.

6. 페더레이션 서버에서 Windows PowerShell을 엽니다.

7. 다음 명령을 입력합니다.

	`Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**<RPObjectName>**은 Azure Active Directory 신뢰 당사자 트러스트 개체에 대한 신뢰 당사자 개체 이름입니다. 일반적으로 이 개체의 이름은 Microsoft Office 365 ID 플랫폼입니다.




 

## 배포 및 롤아웃 

필수 구성 요소가 완료되면 도메인 가입 컴퓨터를 Azure AD에 등록할 준비가 완료된 것입니다.

다음에 Windows를 다시 부팅하거나 사용자가 로그인할 때 Windows 10 1주년 업데이트 및 Windows Server 2016 도메인 가입 컴퓨터가 Azure AD에 자동으로 등록됩니다. 도메인에 가입된 새 컴퓨터는 도메인 가입 작업 이후의 다시 부팅 시 Azure AD에 등록됩니다.

> [AZURE.NOTE] Windows 10 2015년 11월 업데이트 도메인 가입 컴퓨터는 롤아웃 그룹 정책 개체가 설정된 경우에만 Azure AD에 자동으로 등록됩니다. 자세한 내용은 다음 섹션을 참조하세요.

Windows 10/Windows Server 2016 도메인 가입 컴퓨터의 자동 등록 롤아웃을 제어하기 위해 이러한 용도로 사용할 수 있는 그룹 정책 개체가 있습니다. 비 Windows 10 도메인 가입 컴퓨터의 자동 등록 롤아웃의 경우 선택한 컴퓨터에 배포할 수 있는 Windows Installer 패키지가 있습니다.

> [AZURE.NOTE] 롤아웃 제어에 대한 그룹 정책은 Windows 8.1 도메인 가입 컴퓨터의 등록도 트리거합니다. Windows 8.1 도메인 가입 컴퓨터의 등록에 대한 정책을 사용하도록 선택하거나, 아니면 7 또는 8.0을 포함하는 혼합 버전의 Windows 또는 Windows Server 버전을 사용하는 경우, Windows Installer 패키지를 사용하여 모든 비 Windows 10/Windows Server 2016 컴퓨터의 등록을 사용하도록 선택할 수 있습니다.

### 자동 등록 롤아웃을 제어하는 그룹 정책 개체 

도메인 가입 컴퓨터의 Azure AD 자동 등록에 대한 롤아웃을 제어하기 위해 그룹 정책 등록 도메인 가입 컴퓨터를 등록할 컴퓨터에 장치로 배포할 수 있습니다. 예를 들어 보안 그룹 또는 OU(조직 구성 단위)를 기반으로 정책을 배포할 수 있습니다.

정책을 설정하려면 다음 단계를 수행합니다.

1. 서버 관리자를 열고 **도구 > 그룹 정책 관리**로 이동합니다.

2. 그룹 정책 관리에서 Windows 10 또는 Windows Server 2016 컴퓨터의 자동 등록을 사용하도록 설정하려는 도메인에 해당하는 도메인 노드로 이동합니다.

3. **그룹 정책 개체**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 선택합니다.

4. 그룹 정책 개체 이름(예: *Azure AD 자동 등록*)을 입력합니다. 확인을 클릭합니다.

4. 새 그룹 정책 개체를 마우스 오른쪽 단추로 클릭하고 **편집**을 선택합니다.

5. **컴퓨터 구성 > 정책 > 관리 템플릿 > Windows 구성 요소 > 장치 등록**으로 이동하여 **도메인에 가입된 컴퓨터를 장치로 등록**을 마우스 오른쪽 단추로 클릭하고 **편집**을 선택합니다.

	> [AZURE.NOTE] 이 그룹 정책 템플릿은 이전 버전의 그룹 정책 관리 콘솔에서 이름이 변경되었습니다. 이전 버전의 콘솔을 실행 중인 경우 정책은 클라이언트 컴퓨터를 자동으로 작업 공간에 연결이라는 이름으로 컴퓨터 구성 > 정책 > 관리 템플릿 > Windows 구성 요소 > 작업 공간 연결 아래에 표시됩니다.

6. **사용** 옵션 단추를 선택하고 **적용**을 클릭합니다.

7. **확인**을 클릭합니다.

7. 그룹 정책 개체를 선택한 위치에 연결합니다. 예를 들어 컴퓨터가 있는 특정 OU(조직 구성 단위) 또는 Azure AD에 자동으로 등록되는 컴퓨터가 포함된 특정 보안 그룹이 여기에 해당합니다. 조직의 모든 Windows 10 및 Windows Server 2016 도메인 가입 컴퓨터에 대해 이 정책을 사용하도록 설정하려면 그룹 정책 개체를 도메인에 연결합니다.

## 비 Windows 10 컴퓨터용 MSI 패키지  

Windows 7, Windows 8.0, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 또는 Windows Server 2012 R2를 실행하는 도메인 가입 컴퓨터를 등록하기 위해 Windows Installer 패키지(.msi)를 다운로드할 수 있습니다.

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

System Center Configuration Manager와 같은 소프트웨어 배포 시스템을 사용하여 이 패키지를 배포해야 합니다. 이 패키지는 /quiet 매개 변수를 사용하여 표준 자동 설치 옵션을 지원합니다. System Center Configuration Manager 2016을 사용하는 경우 완료된 등록을 추적하는 기능과 같은 추가 혜택을 누릴 수 있습니다. 자세한 내용은 [System Center 2016](https://www.microsoft.com/cloud-platform/system-center-2016)을 참조하세요.

설치 관리자는 사용자 컨텍스트에서 실행되고 사용자의 Windows 로그인 시 트리거되는 예약된 작업을 시스템에 만듭니다. 이 작업은 Windows 통합 인증을 사용한 인증 후 사용자 자격 증명으로 장치를 Azure AD에 자동으로 등록합니다. **Microsoft > 작업 공간 연결**의 작업 스케줄러 라이브러리에서 예약된 작업을 찾을 수 있습니다.



## 추가 항목

- [Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0810_2016-->