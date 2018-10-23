---
title: 하이브리드 Azure Active Directory 조인 장치를 수동으로 구성 | Microsoft Docs
description: 하이브리드 Azure Active Directory 조인 장치를 수동으로 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/25/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 4e3b7aff97cbcebe34e6af4755900e8888c5e57d
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352804"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>자습서: 하이브리드 Azure Active Directory 조인 장치를 수동으로 구성 

Azure AD(Active Directory)의 장치 관리를 사용하면 보안 및 규정 준수에 대한 표준을 충족하는 장치에서 사용자 리소스에 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory의 장치 관리 소개](overview.md)를 참조하세요.


> [!TIP]
> Azure AD Connect를 사용하도록 선택한 경우, [시나리오 선택](hybrid-azuread-join-plan.md#select-your-scenario)을 참조하세요. Azure AD Connect를 사용하면 하이브리드 Azure AD 조인 구성을 크게 간소화 할 수 있습니다.



온-프레미스 Active Directory 환경을 사용하고, Azure AD에 도메인 가입 장치를 연결하려는 경우 하이브리드 Azure AD 가입 장치를 구성하여 이를 수행할 수 있습니다. 이 자습서에서는 장치에 하이브리드 Azure AD 조인을 수동으로 구성하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 필수 조건
> * 구성 단계
> * 서비스 연결 지점 구성
> * 클레임 발급 설정
> * Windows 하위 수준 장치 설정
> * 가입 장치 확인
> * 구현 문제 해결
 




## <a name="prerequisites"></a>필수 조건

이 자습서에서는 사용자가 다음 항목에 대해 잘 알고 있다고 가정합니다.
    
-  [Azure Active Directory의 장치 관리 소개](../device-management-introduction.md)
    
-  [하이브리드 Azure Active Directory 조인 구현을 계획하는 방법](hybrid-azuread-join-plan.md)

-  [장치의 하이브리드 Azure AD 조인을 제어하는 방법](hybrid-azuread-join-control.md)


조직에서 하이브리드 Azure AD 가입 장치를 사용하도록 설정하기 전에 다음을 확인해야 합니다.

- 최신 버전의 Azure AD Connect를 실행합니다.

- Azure AD Connect는 Azure AD에 가입된 하이브리드 Azure AD를 사용하려는 장치의 컴퓨터 개체를 동기화합니다. 컴퓨터 개체가 특정 OU(조직 구성 단위)에 속한 경우 Azure AD Connect에서 이러한 OU를 동기화하기 위해 구성해야 합니다.

  

Azure AD Connect:

- 온-프레미스 AD(Active Directory)의 컴퓨터 계정과 Azure AD의 장치 개체 간 연결을 유지합니다. 
- 비즈니스용 Windows Hello 같은 기타 장치 관련 기능을 지원합니다.

Azure AD에 컴퓨터를 등록하려면 조직 네트워크 내에 있는 컴퓨터에서 다음 URL에 액세스할 수 있는지 확인합니다.

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com 허용
- https://device.login.microsoftonline.com

- 조직의 STS(페더레이션된 도메인)

아직 액세스할 수 없는 경우 조직의 STS(페더레이션된 도메인 용)가 사용자의 로컬 인트라넷 설정에 포함되어야 합니다.

조직에서 Seamless SSO를 사용하려는 경우 다음 URL은 조직 내의 컴퓨터에서 연결할 수 있어야 하며 사용자의 로컬 인트라넷 영역에도 추가해야 합니다.

- https://autologon.microsoftazuread-sso.com

- 또한 사용자 인트라넷 영역에서 "스크립트를 통한 상태 표시줄 업데이트 허용" 설정을 사용하도록 설정해야 합니다.

조직이 온-프레미스 AD에서 자동 관리(페더레이션되지 않은) 설정을 사용하고, ADFS를 사용하여 Azure AD에 페더레이션하지 않는 경우 Windows 10의 하이브리드 Azure AD 연결은 Azure AD에 동기화할 AD의 컴퓨터 개체에 의존합니다. 따라서 하이브리드 Azure AD를 연결해야 하는 컴퓨터 개체가 포함된 OU(조직 구성 단위)는 Azure AD Connect 동기화 구성에서 동기화에 대해 활성화되어 있어야 합니다.

버전 1703 이하의 Windows 10 장치를 사용하며 조직에서 아웃바운드 프록시를 통해 인터넷에 액세스해야 하는 경우 Windows 10 컴퓨터에서 Azure AD에 등록할 수 있도록 WPAD(웹 프록시 자동 검색)를 구현해야 합니다. 

Windows 10 1803부터는 페더레이션된 도메인에서 AD FS를 사용한 장치의 하이브리드 Azure AD 조인 시도가 실패하더라도 Azure AD Connect가 컴퓨터/장치 개체를 Azure AD와 동기화하도록 구성되어 있으면 장치가 동기화된 컴퓨터/장치를 사용하여 하이브리드 Azure AD 조인을 완료하려고 시도합니다.

## <a name="configuration-steps"></a>구성 단계

다양한 형식의 Windows 장치 플랫폼에 대한 하이브리드 Azure AD 가입 장치를 구성할 수 있습니다. 이 토픽에는 모든 일반 구성 시나리오에 필요한 단계가 포함되어 있습니다.  

다음 테이블을 사용하여 본인의 시나리오에 필요한 단계의 개요를 알아보세요.  



| 단계                                      | Windows 현재 및 암호 해시 동기화 | Windows 현재 및 페더레이션 | Windows 하위 수준 |
| :--                                        | :-:                                    | :-:                            | :-:                |
| 서비스 연결 지점 구성 | ![확인][1]                            | ![확인][1]                    | ![확인][1]        |
| 클레임 발급 설정           |                                        | ![확인][1]                    | ![확인][1]        |
| 비Windows 10 장치 활성화      |                                        |                                | ![확인][1]        |
| 가입 장치 확인          | ![확인][1]                            | ![확인][1]                    | ![확인][1]        |



## <a name="configure-service-connection-point"></a>서비스 연결 지점 구성

SCP(서비스 연결 지점) 개체는 등록 중에 장치가 Azure AD 테넌트 정보를 검색하는 데 사용됩니다. 온-프레미스 AD(Active Directory)에서, 하이브리드 Azure AD 가입 장치에 대한 SCP 개체는 컴퓨터 포리스트의 구성 명명 컨텍스트 파티션에 있어야 합니다. 포리스트당 하나의 구성 명명 컨텍스트가 있습니다. 다중 포리스트 Active Directory 구성에서는 도메인에 가입된 컴퓨터를 포함하고 있는 모든 포리스트에 서비스 연결점이 있어야 합니다.

[**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) cmdlet을 사용하여 포리스트의 구성 명명 컨텍스트를 검색할 수 있습니다.  

Active Directory 도메인 이름이 *fabrikam.com*인 포리스트의 경우 구성 명명 컨텍스트는 다음과 같습니다.

`CN=Configuration,DC=fabrikam,DC=com`

포리스트에서 도메인에 가입된 장치의 자동 등록을 위한 SCP 개체는 다음 위치에 있습니다.  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Azure AD Connect를 배포한 방법에 따라 SCP 개체가 이미 구성되었을 수도 있습니다.
다음 Windows PowerShell 스크립트를 사용하여 개체의 존재를 확인하고 검색 값을 검색할 수 있습니다. 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$scp.Keywords** 출력은 Azure AD 테넌트 정보를 보여줍니다. 예:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

서비스 연결 지점이 없는 경우 Azure AD Connect 서버에서 `Initialize-ADSyncDomainJoinedComputerSync` cmdlet을 실행하여 서비스 연결 지점을 만들 수 있습니다. 이 cmdlet을 실행하려면 엔터프라이즈 관리자 자격 증명이 필요합니다.  
cmdlet:

- Azure AD Connect가 연결된 Active Directory 포리스트에 서비스 연결 지점을 만듭니다. 
- `AdConnectorAccount` 매개 변수를 지정해야 합니다. Azure AD Connect에서 Active Directory 커넥터 계정으로 구성되는 계정입니다. 


다음 스크립트는 cmdlet 사용에 대한 예를 보여줍니다. 이 스크립트에서 `$aadAdminCred = Get-Credential`에는 사용자 이름을 입력해야 합니다. UPN(사용자 계정 이름) 형식(`user@example.com`)에 사용자 이름을 입력해야 합니다. 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

`Initialize-ADSyncDomainJoinedComputerSync` cmdlet:

- 도메인 컨트롤러에서 실행되는 Active Directory Web Services를 사용하는 Active Directory PowerShell 모듈 및 AD DS 도구를 사용합니다. Active Directory Web Services는 Windows Server 2008 R2 이상을 실행하는 도메인 컨트롤러에서 지원됩니다.
- **MSOnline PowerShell 모듈 버전 1.1.166.0**에서만 지원됩니다. 이 모듈을 다운로드하려면 이 [링크](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/)를 사용합니다.   
- AD DS 도구가 설치되지 않은 경우 `Initialize-ADSyncDomainJoinedComputerSync`에 실패합니다.  기능 - 원격 서버 관리 도구 - 역할 관리 도구 아래에서 서버 관리자를 통해 AD DS 도구를 설치할 수 있습니다.

Windows Server 2008 이전 버전을 실행하는 도메인 컨트롤러의 경우 아래 스크립트를 사용하여 서비스 연결 지점을 만들 수 있습니다.

다중 포리스트 구성에서는 다음 스크립트를 사용하여 컴퓨터가 있는 각 포리스트에 서비스 연결 지점을 만들어야 합니다.
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

위의 스크립트에서

- `$verifiedDomain = "contoso.com"`은 Azure AD에서 확인된 도메인 이름 중 하나로 교체해야 하는 자리 표시자입니다. 사용하려면 먼저 도메인을 소유해야 합니다.

확인된 도메인 이름에 대한 자세한 내용은 [Azure Active Directory에 사용자 지정 도메인 이름 추가](../active-directory-domains-add-azure-portal.md)를 참조하세요.  
확인된 회사 도메인 목록을 보려면 the [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) cmdlet을 사용합니다. 

![Get-AzureADDomain](./media/hybrid-azuread-join-manual-steps/01.png)

## <a name="setup-issuance-of-claims"></a>클레임 발급 설정

페더레이션된 Azure AD 구성에서는 장치가 AD FS(Active Directory Federation Services) 또는 타사 온-프레미스 페더레이션 서비스를 사용하여 Azure AD를 인증합니다. 장치는 인증을 통해 Azure DRS(Azure Active Directory Device Registration Service)에 등록하는 액세스 토큰을 가져옵니다.

Windows 현재 장치는 Windows 통합 인증을 사용하여 온-프레미스 페더레이션 서비스에서 호스트하는 활성 WS-Trust 엔드포인트(1.3 또는 2005 버전)에 인증합니다.

> [!NOTE]
> AD FS를 사용하는 경우 **adfs/services/trust/13/windowstransport** 또는 **adfs/services/trust/2005/windowstransport**를 사용하도록 설정해야 합니다. 또한 웹 인증 프록시를 사용하는 경우 이 엔드포인트가 프록시를 통해 게시되어야 합니다. **서비스 &gt; 엔드포인트**에서 AD FS 관리 콘솔을 통해 어떤 엔드포인트가 사용하도록 설정되었는지 확인할 수 있습니다.
>
>온-프레미스 페더레이션 서비스로 사용되는 AD FS가 없으면 공급업체의 지침에 따라 없다면 WS-Trust 1.3 또는 2005 버전이 지원되는지 확인하고 메타데이터 교환 파일(MEX)을 통해 게시되는지 확인합니다.

Azure DRS가 수신한 토큰에 다음 클레임이 있어야 장치 등록이 완료됩니다. Azure DRS는 이 정보 중 일부를 사용하여 Azure AD에 장치 개체를 만듭니다. 장치 개체는 Azure AD Connect가 새로 만들어진 장치 개체를 컴퓨터 계정 온-프레미스와 연결하는 데 사용됩니다.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

확인된 도메인 이름이 두 개 이상인 경우 컴퓨터에 대한 다음 클레임을 제공해야 합니다.

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

ImmutableID 클레임(예: 대체 로그인 ID)을 이미 발급 중인 경우 컴퓨터에 대한 해당 클레임 하나를 제공해야 합니다.

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

다음 섹션에서는 다음 항목에 대한 정보를 찾습니다.
 
- 각 클레임에 필요한 값
- AD FS에서 정의의 형식

정의는 값의 존재 여부 또는 값을 만들어야 하는지 여부를 확인하는 데 도움이 됩니다.

> [!NOTE]
> 온-프레미스 페더레이션 서버에 사용되는 AD FS가 없는 경우 공급업체의 지침에 따라 이러한 클레임을 발급하는 적절한 구성을 만듭니다.

### <a name="issue-account-type-claim"></a>계정 유형 클레임 발급

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** - 이 클레임은 도메인에 가입된 컴퓨터로 장치를 식별하는 **DJ** 값을 포함해야 합니다. AD FS에서 다음과 같은 형식의 발급 변환 규칙을 추가할 수 있습니다.

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>컴퓨터 계정 온-프레미스의 objectGUID 발급

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** - 이 클레임은 온-프레미스 컴퓨터 계정의 **objectGUID** 값을 포함해야 합니다. AD FS에서 다음과 같은 형식의 발급 변환 규칙을 추가할 수 있습니다.

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>컴퓨터 계정 온-프레미스의 objectSID 발급

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** - 이 클레임은 온-프레미스 컴퓨터 계정의 **objectSid** 값을 포함해야 합니다. AD FS에서 다음과 같은 형식의 발급 변환 규칙을 추가할 수 있습니다.

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Azure AD에 확인된 도메인 이름이 여러 개 있는 경우 컴퓨터에 대한 issuerID 발급

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** - 이 클레임의 식별자는 토큰을 발급하는 온-프레미스 페더레이션 서비스(AD FS 또는 타사)와 연결하는 확인된 도메인 이름의 URI(Uniform Resource Identifier)를 포함해야 합니다. AD FS에서 위와 같은 발급 변환 규칙을 추가한 후 아래와 같은 발급 변환 규칙을 그 순서대로 추가할 수 있습니다. 사용자에 대한 규칙을 명시적으로 발급하는 규칙 하나가 필요합니다. 아래 규칙에서 사용자 및 컴퓨터 인증을 식별하는 첫 번째 규칙이 추가됩니다.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


위의 클레임에서

- `<verified-domain-name>`은 Azure AD에서 확인된 도메인 이름 중 하나로 교체해야 하는 자리 표시자입니다. 예: 값="http://contoso.com/adfs/services/trust/"



확인된 도메인 이름에 대한 자세한 내용은 [Azure Active Directory에 사용자 지정 도메인 이름 추가](../active-directory-domains-add-azure-portal.md)를 참조하세요.  

확인된 회사 도메인 목록을 보려면 the [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) cmdlet을 사용합니다. 

![Get-MsolDomain](./media/hybrid-azuread-join-manual-steps/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>사용자에 대한 ID가 있는 경우(예: 대체 로그인 ID가 설정된 경우) 컴퓨터에 대한 ImmutableID 발급

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** - 이 클레임은 컴퓨터에 대한 유효한 값을 포함해야 합니다. AD FS에서 다음과 같은 발급 변환 규칙을 만들 수 있습니다.

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>AD FS 발급 변환 규칙을 만드는 도우미 스크립트

다음 스크립트는 위에서 설명한 발급 변환 규칙을 만드는 데 도움이 됩니다.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>설명 

- 이 스크립트는 기존 규칙에 규칙을 추가합니다. 이 스크립트를 두 번 실행하면 규칙 집합이 두 번 추가되므로 두 번 실행하지 마세요. 스크립트를 다시 실행하기 전에 이러한 클레임에 해당하는 규칙이 없는지 확인(해당 조건에서)하세요.

- Azure AD 포털에서 또는 Get MsolDomains cmdlet을 통해 보여드린 것처럼 확인된 도메인 이름이 여러 개 있는 경우 스크립트에서 **$multipleVerifiedDomainNames** 값을 **$true**로 설정합니다. 또한 Azure AD Connect 또는 다른 방법을 통해 만들어졌을 수 있는 기존 issuerid 클레임을 제거해야 합니다. 다음은 이 규칙에 대한 예입니다.


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- 사용자 계정에 대한 **ImmutableID** 클레임을 이미 발급한 경우 스크립트에서 **$immutableIDAlreadyIssuedforUsers** 값을 **$true**로 설정합니다.

## <a name="enable-windows-down-level-devices"></a>Windows 하위 수준 장치 설정

도메인에 가입된 장치 중 일부가 Windows 하위 수준 장치인 경우 다음을 수행해야 합니다.

- 사용자가 장치를 등록할 수 있도록 Azure AD에 정책을 설정합니다.
 
- 장치 등록에 **IWA(통합 Windows 인증)** 를 지원하는 클레임을 발급하도록 온-프레미스 페더레이션 서비스를 구성합니다.
 
- 장치를 인증할 때 인증서 프롬프트가 나타나지 않도록 로컬 인트라넷 영역에 Azure AD 장치 인증 끝점을 추가합니다.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>사용자가 장치를 등록할 수 있도록 Azure AD에 정책 설정

Windows 하위 수준 장치를 등록하려면 사용자가 Azure AD에서 장치를 등록할 수 있도록 허용하는 설정을 선택해야 합니다. Azure Portal의 다음 위치에서 이러한 값을 확인할 수 있습니다.

`Azure Active Directory > Users and groups > Device settings`
    
**사용자가 장치를 Azure AD에 등록할 수 있습니다.** 정책이 **모두**로 설정되어야 합니다.

![장치 등록](./media/hybrid-azuread-join-manual-steps/23.png)


### <a name="configure-on-premises-federation-service"></a>온-프레미스 페더레이션 서비스 구성 

온-프레미스 페더레이션 서비스는 아래와 같이 인코딩된 값으로 resouce_params 매개 변수를 보유한 Azure AD 신뢰 당사자에 대한 인증 요청을 받으면 **authenticationmehod** 및 **wiaormultiauthn** 클레임을 발급하도록 지원해야 합니다.

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

이러한 요청이 오면 온-프레미스 페더레이션 서비스는 통합 Windows 인증을 사용하여 사용자를 인증해야 하며 인증이 완료되는 즉시 다음 두 클레임을 발급해야 합니다.

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

AD FS에서 인증 메서드를 통과하는 발급 변환 규칙을 추가해야 합니다.  

**이 규칙을 추가하려면:**

1. AD FS 관리 콘솔에서 `AD FS > Trust Relationships > Relying Party Trusts`로 이동합니다.
2. 마우스 오른쪽 단추로 Microsoft Office 365 ID 플랫폼 신뢰 당사자 트러스트 개체를 클릭하고 **클레임 규칙 편집**을 선택합니다.
3. **발급 변환 규칙** 탭에서 **규칙 추가**를 선택합니다.
4. **클레임 규칙** 템플릿 목록에서 **사용자 지정 규칙을 사용하여 클레임 보내기**를 선택합니다.
5. **다음**을 선택합니다.
6. **클레임 규칙 이름** 상자에 **인증 방법 클레임 규칙**을 입력합니다.
7. **클레임 규칙** 상자에 다음 규칙을 입력합니다.

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. 페더레이션 서버에서 **\<RPObjectName\>** 을 Azure AD 신뢰 당사자 트러스트 개체의 신뢰 당사자 개체 이름으로 바꾼 후 아래의 PowerShell 명령을 입력합니다. 일반적으로 이 개체의 이름은 **Microsoft Office 365 ID 플랫폼**입니다.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>로컬 인트라넷 영역에 Azure AD 장치 인증 끝점 추가

등록 장치의 사용자가 Azure AD에 인증할 때 인증서 프롬프트를 표시하지 않으려는 경우 Internet Explorer의 로컬 인트라넷 영역에 다음 URL을 추가하도록 도메인에 가입된 장치에 정책을 푸시하면 됩니다.

`https://device.login.microsoftonline.com`


## <a name="verify-joined-devices"></a>가입 장치 확인

[Azure Active Directory PowerShell 모듈](/powershell/azure/install-msonlinev1?view=azureadps-2.0)에서 [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet을 사용하여 조직에 성공적으로 가입된 장치를 확인할 수 있습니다.

이 cmdlet의 출력은 Azure AD로 등록 및 가입된 장치를 보여 줍니다. 모든 장치를 가져오려면 **-All** 매개 변수를 사용한 다음 **deviceTrustType** 속성을 사용하여 장치를 필터링합니다. 도메인에 가입된 장치는 **도메인 가입** 값을 갖습니다.



## <a name="troubleshoot-your-implementation"></a>구현 문제 해결

도메인 가입 Windows 장치에 대한 하이브리드 Azure AD 조인은 완료할 때 문제가 발생하는 경우 다음을 참조하세요.

- [Windows 최신 장치의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-current.md)
- [Windows 하위 수준 장치의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory의 장치 관리 소개](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual-steps/12.png
