<properties
	pageTitle="Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정 | Microsoft Azure"
	description="Windows Server 2012 R2에서 AD FS(Active Directory Federation Service)를 사용하여 온-프레미스 응용 프로그램에 대한 조건부 액세스를 사용하도록 설정하는 단계별 가이드입니다."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="femila"/>

# Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정

사용자가 작업 공간 연결을 통해 Azure Active Directory Device Registration 서비스에 장치를 등록하도록 요구하여 사용자의 개인 소유 장치를 조직에 알려지도록 표시할 수 있습니다. 다음은 Windows Server 2012 R2에서 AD FS(Active Directory Federation Service)를 사용하여 온-프레미스 응용 프로그램에 대한 조건부 액세스를 사용하도록 설정하는 단계별 가이드입니다.

> [AZURE.NOTE]Azure Active Directory Device Registration 서비스 조건부 액세스 정책에 등록된 장치를 사용하는 경우 Office 365 라이선스 또는 Azure AD Premium 라이선스가 필요합니다. 여기에는 AD FS(Active Directory Federation Services)에 의해 온-프레미스 리소스에 적용되는 정책이 포함됩니다.

온-프레미스의 조건부 액세스 시나리오에 대한 자세한 내용은 [회사 응용 프로그램에 대한 SSO 및 원활한 두 번째 인증 단계를 위해 임의 장치에서 작업 공간 연결](https://technet.microsoft.com/library/dn280945.aspx)을 참조하세요. 이러한 기능은 Azure Active Directory Premium 라이선스를 구입한 고객에게 제공됩니다.

지원되는 장치
-------------------------------------------------------------------------
* Windows 7 도메인 가입 장치
* Windows 8.1 개인 및 도메인 가입 장치
* iOS 6 이상
* Android 4.0 이상, Samsung GS3 이상 휴대폰, Samsung Note2 이상 태블릿

시나리오 필수 조건
------------------------------------------------------------------------
* Windows Server Active Directory(Windows Server 2008 이상)
* Windows Server 2012 R2에서 업데이트된 스키마
* Azure Active Directory Premium 구독
* Azure AD에 대해 SSO로 구성된 Windows Server 2012 R2 페더레이션 서비스
* Windows Server 2012 R2 웹 응용 프로그램 프록시
* 장치 개체 쓰기 저장을 포함하는 디렉터리 동기화 도구(DirSync). [dirsync.exe의 최신 빌드를 다운로드하세요.](http://go.microsoft.com/fwlink/?LinkID=278924)

이 릴리스의 알려진 문제
-------------------------------------------------------------------------------
* 장치 기반 조건부 액세스 정책을 사용하려면 Azure Active Directory에서 Active Directory로 장치 개체를 쓰기 저장해야 합니다. Active Directory에 장치 개체를 다시 쓰기 저장하는 데 최대 3시간이 걸릴 수 있습니다.
* iOS7 장치는 항상 클라이언트 인증서 인증 중에 인증서를 선택하라는 메시지를 사용자에게 표시합니다. 
* 현재 iOS8 베타 장치는 작동하지 않습니다.


## Azure Active Directory Device Registration 서비스 배포
이 가이드를 사용하여 조직에 맞게 Azure Active Directory Device Registration 서비스를 배포하고 구성할 수 있습니다.

이 가이드에서는 Windows Server Active Directory를 구성했으며 Microsoft Azure Active Directory를 구독한다고 가정합니다. 위의 필수 조건을 참조하세요.

Azure Active Directory Device Registration 서비스 및 Azure Active Directory 테넌트를 배포하려면 다음 검사 목록의 작업을 순서대로 완료합니다. 참조 링크가 개념 항목으로 이동하는 경우 개념 항목을 검토한 후 이 검사 목록으로 돌아와서 이 검사 목록의 나머지 작업을 계속할 수 있습니다. 일부 작업에는 단계가 성공적으로 완료되었는지 확인하는 데 도움이 되는 시나리오 유효성 검사 단계가 포함됩니다.

**검사 목록: Azure Active Directory Device Registration 사용**

아래 검사 목록에 따라 Azure Active Directory Device Registration 서비스를 사용하도록 설정하고 구성합니다.

| 작업 | 참조 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Azure Active Directory 테넌트에서 장치 등록을 사용하도록 설정하여 장치가 작업 공간에 연결할 수 있게 합니다. 기본적으로 다단계 인증은 서비스에 대해 사용되지 않습니다. 그러나 장치를 등록하는 경우 다단계 인증을 사용하는 것이 좋습니다. ADRS에서 다단계 인증을 사용하도록 설정하기 전에 다단계 인증 공급자에 대해 AD FS가 구성되었는지 확인합니다. | [Azure Active Directory Device Registration 사용](active-directory-conditional-access-device-registration-overview.md) |
| 장치는 잘 알려진 DNS 레코드를 찾아 Azure Active Directory Device Registration 서비스를 검색합니다. 장치가 Azure Active Directory Device Registration 서비스를 검색할 수 있도록 회사 DNS를 구성해야 합니다. | [Azure Active Directory Device Registration 검색 구성](active-directory-conditional-access-device-registration-overview.md) |

**검사 목록: Windows Server 2012 R2 Active Directory Federation Services를 배포 및 구성하고 Azure Active Directory와 페더레이션 관계 설정** 다음 작업 집합을 사용하여 Windows Server 2012 R2 Federation Services와 Azure Active Directory를 통합합니다. 이렇게 하면 Azure Active Directory Device Registration 서비스에 등록된 장치를 AD FS의 조건부 액세스 정책에 사용할 수 있습니다.


| 작업 | 참조 |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Windows Server 2012 R2 스키마 확장을 사용하여 Active Directory 도메인 서비스 도메인을 배포합니다. 도메인 컨트롤러는 Windows Server 2012 R2로 업그레이드할 필요가 없습니다. 스키마 업그레이드만 수행하면 됩니다. | Active Directory 도메인 서비스 스키마를 업그레이드합니다. |
| 웹 응용 프로그램 프록시와 함께 Windows Server 2012 R2 Federation Services를 배포합니다. | 웹 응용 프로그램 프록시와 함께 Windows Server 2012 R2 Federation Services를 배포합니다. |
| 장치 개체를 지원하는 데 필요한 개체 및 컨테이너를 사용하여 Active Directory 도메인 서비스 포리스트를 구성해야 합니다. 또한 AD FS에서 장치 인증을 사용하도록 설정합니다. | 장치를 지원하도록 Active Directory 포리스트를 준비합니다. |
| 조직 및 Azure Active Directory와 페더레이션 관계를 설정합니다. 이 단계에서는 Windows Server 2012 R2 Federation Services를 사용하여 Azure Active Directory 테넌트를 구성하는 과정을 단계별로 안내합니다. | Azure Active Directory와 페더레이션 관계 구성 및 디렉터리 동기화 도구 구성 |
| 장치 개체 쓰기 저장을 허용하도록 디렉터리 동기화(DirSync)를 구성합니다. Azure Active Directory에 만든 장치가 Active Directory에 기록됩니다. | 장치 개체 쓰기 저장을 허용하도록 DirSync 구성 |
| Windows Server 2012 R2 Federation Services를 사용하여 다단계 인증 공급자를 구성하는 것이 좋습니다. 이렇게 하면 사용자가 다단계 인증을 사용하여 장치를 안전하게 등록할 수 있습니다. | 다단계 인증을 제공하도록 페더레이션 서비스를 구성합니다. |


### Active Directory 도메인 서비스 스키마 업그레이드
> [AZURE.NOTE]Active Directory 스키마 업그레이드는 취소할 수 없습니다. 테스트 환경에서 이 작업을 수행하는 것이 좋습니다.

1. Enterprise Admin 및 Schema Admin 권한이 둘 다 있는 계정으로 도메인 컨트롤러에 로그인합니다.
2. [media]\\support\\adprep 디렉터리와 하위 디렉터리를 Active Directory 도메인 컨트롤러 중 하나에 복사합니다. 
3. 여기서 [media]는 Windows Server 2012 R2 설치 미디어의 경로입니다.
4. 명령 프롬프트에서 adprep 디렉터리로 이동한 다음 adprep.exe /forestprep를 실행합니다. 화면의 지시에 따라 스키마 업그레이드를 완료합니다.

### 웹 응용 프로그램 프록시와 함께 Windows Server 2012 R2 Federation Services 배포
아래 두 가이드에 따라 웹 응용 프로그램 프록시와 함께 Windows Server 2012 R2 Federation Services를 배포한 다음 이 가이드로 돌아옵니다. Windows Server 2012 R2 페더레이션 서버 팜 배포 지침을 따르세요.

> [AZURE.NOTE]AD FS를 배포하는 경우 선택적 단계: DRS(장치 등록 서비스)를 사용하여 페더레이션 서버 구성이라는 선택적 단계를 건너뛰어야 합니다. Azure Active Directory Device Registration을 사용하는 경우에는 이 단계가 필요하지 않습니다. Windows Server 2012 R2 페더레이션 서버 프록시 배포 지침을 따르세요.

모든 Windows Server 2012 R2 페더레이션 및 프록시 서버에 Windows Server 2012 R2용 최신 업데이트를 설치합니다. 이 작업은 페더레이션 팜 구성 전이나 후에 수행할 수 있습니다. Windows Server 2012 R2 업데이트에서 업데이트를 찾을 수 있습니다.


### 장치를 지원하도록 Active Directory 포리스트 준비

> [AZURE.NOTE]장치를 지원하도록 Active Directory 포리스트를 준비하기 위해 실행해야 하는 일회성 작업입니다. 이 절차를 완료하려면 엔터프라이즈 관리자 권한으로 로그온해야 하며 Active Directory 포리스트에 Windows Server 2012 R2 스키마가 있어야 합니다.

1. 페더레이션 서버에서 Windows PowerShell 명령 창을 열고 *Initialize-addeviceregistration*을 입력합니다.

2. ServiceAccountName에 대한 메시지가 표시되면 AD FS에 대한 서비스 계정으로 선택한 서비스 계정의 이름을 입력합니다. gMSA 계정인 경우 domain\\accountname$ 형식으로 계정을 입력합니다. 도메인 계정의 경우 domain\\accountname 형식을 사용합니다.


### AD FS에서 장치 인증 사용

1. 페더레이션 서버에서 AD FS 관리 콘솔을 열고 **AD FS** > **인증 정책**으로 이동합니다.
2. **작업** 창에서 전역 기본 인증 편집...을 선택합니다.
3. **장치 인증 사용**을 선택한 다음 확인을 선택합니다.
4. 기본적으로 AD FS는 Active Directory에서 사용하지 않은 장치를 주기적으로 제거합니다. Azure Active Directory Device Registration을 사용하는 경우 이 작업을 사용하지 않도록 설정하여 Azure에서 장치를 관리할 수 있게 해야 합니다.


### 사용하지 않은 장치 정리 사용 안 함
1. 페더레이션 서버에서 Windows PowerShell 명령 창을 열고 다음을 입력합니다. `*Set-AdfsDeviceRegistration -MaximumInactiveDays 0*`


###Azure Active Directory와 페더레이션 관계 구성 및 디렉터리 동기화 도구 구성
다음 섹션은 Azure Active Directory와 AD FS 간에 페더레이션 트러스트를 설정하는 데 도움이 됩니다. 일부 단계에서는 이 페이지를 벗어날 수도 있습니다. 아래 지침에 따른 후 이 가이드로 돌아옵니다.

Azure Active Directory와 로컬 Active Directory 통합 및 Single Sign-On 구성
------------------------------------------------------------------------------------------------------

1. **관리자** 권한으로 Azure 포털에 로그온합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. **디렉터리** 탭에서 해당 디렉터리를 선택합니다.
4. **디렉터리 통합** 탭을 선택합니다.
5. 로컬 Active Directory와 통합 섹션 아래에서 디렉터리 동기화 옵션을 찾아 활성화됨을 선택합니다.
6. 1-4단계에 따라 Azure Active Directory와 로컬 디렉터리를 통합합니다.
  1. **도메인 추가**
  2. **Single Sign-On 구성 및 디렉터리 동기화 준비**. AD FS를 이미 배포한 경우 이러한 하위 섹션에 따라 AD FS와 Azure AD 간에 트러스트를 구성하고 유효성을 검사할 수 있습니다. 
      *  [AD FS를 사용한 Single Sign-On을 위해 Windows PowerShell 설치](https://msdn.microsoft.com/library/azure/jj151814.aspx)
      *  [AD FS와 Azure AD 간의 트러스트 설정](https://msdn.microsoft.com/library/azure/jj205461.aspx)
      *  [AD FS를 사용하여 Single Sign-On 확인 및 관리](https://msdn.microsoft.com/library/azure/jj151809.aspx)
  	  *  [추가 AD FS 참조](https://msdn.microsoft.com/library/azure/dn151321.aspx)

  3. 디렉터리 동기화 도구를 설치 및 실행합니다. DirSync를 이미 설치한 경우 최신 버전으로 업그레이드해야 합니다. 필요한 최소 버전은 6862.0000입니다.
  4. 디렉터리 동기화 확인 및 관리 
  5. Azure Active Directory 신뢰 당사자 트러스트 개체에 대한 클레임 규칙을 추가로 구성합니다. 일반적으로 이 신뢰 당사자 트러스트 개체의 이름은 Microsoft Office 365 ID 플랫폼입니다.

###Azure Active Directory 신뢰 당사자 트러스트 클레임 규칙 구성

1. AD FS 관리 콘솔을 열고 AD FS > 트러스트 관계 > **신뢰 당사자 트러스트**로 이동합니다. **Microsoft Office 365 ID 플랫폼 신뢰 당사자 트러스트 개체**를 마우스 오른쪽 단추로 클릭하고 클레임 규칙 편집...을 선택합니다.
2. **발급 변환 규칙** 탭에서 규칙 추가를 선택합니다.
3. **클레임** 규칙 템플릿 드롭다운 상자에서 **사용자 지정 규칙을 사용하여 클레임 보내기**를 선택합니다. **다음**을 선택합니다.
4. **클레임 규칙 이름:** 텍스트 상자에 인증 방법 클레임 규칙을 입력합니다.
5. 클레임 규칙: 텍스트 상자에 다음 클레임 규칙을 입력합니다.

    ` c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]` `=> issue(claim = c);`

6. **확인**을 두 번 클릭하여 대화 상자를 완료합니다.
7. Azure Active Directory 신뢰 당사자 트러스트 개체에 대한 인증 클래스 참조를 추가로 구성합니다. 일반적으로 이 신뢰 당사자 트러스트 개체의 이름은 Microsoft Office 365 ID 플랫폼입니다.


###Azure Active Directory 신뢰 당사자 트러스트 인증 클래스 참조 구성

페더레이션 서버에서 Windows PowerShell 명령 창을 열고 다음을 입력합니다.

*Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn*


여기서 <RPObjectName>은 Azure Active Directory 신뢰 당사자 트러스트 개체에 대한 신뢰 당사자 개체 이름입니다. 일반적으로 이 개체의 이름은 Microsoft Office 365 ID 플랫폼입니다.

### 장치 개체 쓰기 저장을 허용하도록 DirSync 구성
장치 개체 쓰기 저장을 허용하도록 디렉터리 동기화(DirSync)를 구성해야 합니다. Azure Active Directory에 만든 장치가 Active Directory에 기록됩니다. Azure Active Directory에 만든 장치가 Active Directory에 쓰기 저장되기까지 최대 3시간이 걸릴 수 있습니다.
>[Azure.Note]다음 절차를 완료하려면 엔터프라이즈 관리자 권한으로 로그온해야 합니다. 여기서 dirsync.exe의 최신 빌드를 다운로드하세요.

> 장치가 Active Directory에 쓰기 저장할 수 있도록 하려면 Office 365 라이선스 또는 Azure AD Premium 라이선스가 필요합니다.

> DirSync 설치 마법사를 방금 완료한 경우 계속하기 전에 로그아웃했다가 로그인합니다. 이렇게 하면 업데이트된 액세스 토큰을 보유하게 됩니다.

>Active Directory 도메인 서비스에 장치 개체를 다시 쓰기 저장하는 데 최대 3시간이 걸릴 수 있습니다.

1. 디렉터리 동기화 서버에서 Windows PowerShell 명령 창을 열고 다음 명령을 실행합니다.

    `Import-Module DirSync`
    
    `$AADCreds = Get-Credential`
    
    `$ADCreds = Get-Credential`

2. 자격 증명을 묻는 메시지가 표시되면 클라우드 서비스 관리자 계정 자격 증명 및 Active Directory 관리자 자격 증명을 입력합니다.

*Enable-MSOnlineObjectManagement –ObjectTypes Device –TargetCredential $AADCreds -Credential $ADCreds*

### 다단계 인증을 제공하도록 페더레이션 서비스 구성
조직에 등록된 장치를 원활한 두 번째 인증 단계로 사용할 수 있습니다. 따라서 장치를 등록할 때 강력한 인증을 요구하는 것이 좋습니다. 아래 가이드에 따라 Azure Multi-Factor Authentication을 사용하여 AD FS를 구성한 다음 이 가이드로 돌아올 수 있습니다. Azure Multi-Factor Authentication을 배포하기 위한 지침을 따르세요.

## Azure Active Directory Device Registration을 사용하여 작업 공간에 장치 연결

### Azure Active Directory Device Registration을 사용하여 iOS 장치 연결

Azure Active Directory Device Registration은 iOS 장치에 대해 무선 프로필 등록 프로세스를 사용합니다. 이 프로세스는 사용자가 Safari 웹 브라우저를 사용하여 프로필 등록 URL에 연결하는 작업으로 시작됩니다. URL 형식은 다음과 같습니다.

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

여기서 <yourdomainname>은 Azure Active Directory를 사용하여 구성한 도메인 이름입니다. 예를 들어 도메인 이름이 contoso.com인 경우 URL은 다음과 같습니다.

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

다양한 방법으로 이 URL을 사용자에게 알릴 수 있습니다. 한 가지 권장 방법은 AD FS의 사용자 지정 응용 프로그램 액세스 거부 메시지에 이 URL을 게시하는 것입니다. 이 내용은 이후 섹션: 응용 프로그램 액세스 정책 및 사용자 지정 액세스 거부 메시지 만들기에서 설명합니다.

###Azure Active Directory Device Registration을 사용하여 Windows 8.1 장치 연결

1. Windows 8.1 장치에서 **PC 설정** > **네트워크** > **작업 공간**으로 이동합니다.
2. 사용자 이름을 UPN 형식으로 입력합니다. 예: dan@contoso.com..
3. **연결**을 선택합니다.
4. 메시지가 표시되면 자격 증명을 사용하여 로그인합니다. 이제 장치가 연결되었습니다.

### Azure Active Directory Device Registration을 사용하여 Android 장치 연결

Android용 Azure Authenticator 항목에는 Android 장치에 Azure Authenticator 앱을 설치하고 회사 계정을 추가하는 방법에 대한 지침이 있습니다. Android 장치에 회사 계정이 성공적으로 생성되면 해당 장치가 작업 공간 연결을 통해 조직에 연결됩니다.

### 등록된 장치가 Active Directory에 쓰기 저장되는지 확인
LDP.exe 또는 ADSI 편집을 사용하여 장치 개체가 Active Directory에 쓰기 저장되었는지 보고 확인할 수 있습니다. 둘 다 Active Directory 관리자 도구에서 사용할 수 있습니다.

기본적으로 Azure Active Directory에서 쓰기 저장된 장치 개체는 AD FS 팜과 동일한 도메인에 배치됩니다.

`CN=RegisteredDevices,defaultNamingContext`

###응용 프로그램 액세스 정책 및 사용자 지정 액세스 거부 메시지 만들기
다음 시나리오를 고려합니다. AD FS에서 응용 프로그램 신뢰 당사자 트러스트를 만들고 등록된 장치만 허용하는 발급 권한 부여 규칙을 구성합니다. 이제 등록된 장치만 응용 프로그램에 액세스할 수 있습니다. 사용자가 응용 프로그램에 쉽게 액세스할 수 있게 하려면 장치를 연결하는 방법에 대한 지침을 포함하는 사용자 지정 액세스 거부 메시지를 구성합니다. 이제 사용자가 응용 프로그램에 액세스하기 위해 해당 장치를 원활하게 등록할 수 있습니다.

다음 단계는 이 시나리오를 구현하는 방법을 보여 줍니다.
>[AZURE.NOTE]이 섹션에서는 AD FS에서 응용 프로그램에 대한 신뢰 당사자 트러스트를 이미 구성했다고 가정합니다.

1. AD FS MMC 도구를 열고 AD FS > 트러스트 관계 > 신뢰 당사자 트러스트로 이동합니다.
2. 이 새로운 액세스 규칙을 적용할 응용 프로그램을 찾습니다. 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 클레임 규칙 편집...을 선택합니다.
3. **발급 권한 부여 규칙** 탭을 선택한 다음 **규칙 추가...**를 선택합니다.
4. **클레임 규칙** 템플릿 드롭다운 목록에서 **들어오는 클레임에 따라 사용자 허용 또는 거부**를 선택합니다. **다음**을 선택합니다.
5. 클레임 규칙 이름: 필드에 **등록된 장치에서 액세스 허용**을 입력합니다.
6. 들어오는 클레임 유형: 드롭다운 목록에서 **등록된 사용자**를 선택합니다.
7. 들어오는 클레임 값: 필드에 **true**를 입력합니다.
9. **마침**을 선택한 다음 **적용**을 선택합니다.
10. 방금 만든 규칙보다 더 허용되는 규칙을 모두 제거합니다. 예를 들어 기본 규칙인 모든 사용자에 대한 액세스 허용을 제거합니다. 
8. **이 들어오는 클레임의 사용자에 대한 액세스 허용** 라디오 단추를 선택합니다.

이제 응용 프로그램이 사용자가 작업 공간에 등록 및 연결한 장치에서 들어오는 경우에만 액세스를 허용하도록 구성되었습니다. 고급 액세스 정책의 경우 다단계 액세스 제어를 사용하여 위험 관리를 참조하세요.

다음에는 응용 프로그램에 대한 사용자 지정 오류 메시지를 구성합니다. 오류 메시지를 통해 사용자는 장치를 작업 공간에 연결해야 응용 프로그램에 액세스할 수 있음을 알게 됩니다. 사용자 지정 HTML 및 Windows PowerShell을 사용하여 사용자 지정 응용 프로그램 액세스 거부 메시지를 만들 수 있습니다.

페더레이션 서버에서 Windows PowerShell 명령 창을 열고 다음 명령을 입력합니다. 명령 부분을 시스템과 관련된 항목으로 바꿉니다.

`Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage ` 이 응용 프로그램에 액세스하려면 먼저 장치를 등록해야 합니다.

**iOS 장치를 사용하는 경우 다음 링크를 선택하여 장치를 연결합니다**.

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

이 iOS 장치를 작업 공간에 연결합니다.


**Windows 8.1 장치를 사용하는 경우** PC 설정 > **네트워크** > **작업 공간**으로 이동하여 장치를 연결할 수 있습니다.


여기서 "**신뢰 당사자 트러스트 이름**"은 AD FS에서 응용 프로그램 신뢰 당사자 트러스트 개체의 이름입니다. 여기서 yourdomain.com은 Azure Active Directory를 사용하여 구성한 도메인 이름입니다. 예를 들어 contoso.com입니다. Set-AdfsRelyingPartyWebContent Cmdlet에 전달하는 html 콘텐츠에서 줄 바꿈을 모두 제거해야 합니다(있는 경우).

<!---HONumber=August15_HO9-->