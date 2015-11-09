<properties 
	pageTitle="시작: Azure AD 암호 관리 | Microsoft Azure" 
	description="사용자가 자신의 암호를 재설정하고, 암호 재설정을 위한 사전 필수 구성 요소를 검색하고 Active Directory에서 온-프레미스 암호를 관리하는 암호 쓰기 저장을 사용할 수 있도록 합니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="asteen"/>

# 암호 관리 시작
사용자가 자신의 클라우드 Azure Active Directory 또는 온-프레미스 Active Directory 암호를 사용하려면 간단한 몇 단계를 거치면 됩니다. 몇가지 간단한 필수 구성 요소를 충족한 후, 전체 조직에 대해 암호 변경 및 재설정을 사용할 수 있도록 설정해야 합니다. 이 문서는 다음 개념을 설명합니다.

* [**사용자가 클라우드 Azure Active Directory 암호를 재설정할 수 있도록 하는 방법**](#enable-users-to-reset-their-azure-ad-passwords)
 - [셀프 서비스 암호 재설정 사전 요구 사항](#prerequisites)
 - [1단계: 암호 재설정 정책 구성](#step-1-configure-password-reset-policy)
 - [2단계: 테스트 사용자에 대한 연락처 데이터 추가](#step-2-add-contact-data-for-your-test-user)
 - [3단계: 사용자로 암호 재설정](#step-3-reset-your-azure-ad-password-as-a-user)
* [**사용자가 온-프레미스 Active Directory 암호를 재설정하거나 변경할 수 있도록 하는 방법**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [암호 쓰기 저장 필수 구성 요소](#writeback-prerequisites)
 - [1단계: 최신 버전의 Azure AD Connect 다운로드](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [2단계: UI 또는 powershell을 통해 Azure AD Connect에서 암호 쓰기 저장을 사용 하도록 설정 및 확인](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [3단계: 방화벽 구성](#step-3-configure-your-firewall)
 - [4단계: 적절한 사용 권한 설정](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [5단계: 사용자로 AD 암호 재설정 및 확인](#step-5-reset-your-ad-password-as-a-user)

## 사용자가 Azure AD 암호를 재설정할 수 있도록 설정
이 섹션에서는 AAD 클라우드 디렉터리에 대한 셀프 서비스 암호 재설정 사용, 셀프 서비스 암호 재설정을 위한 사용자 등록 및 마지막으로 사용자로 테스트 셀프 서비스 암호 재설정 수행을 안내합니다.

- [셀프 서비스 암호 재설정 사전 요구 사항](#prerequisites)
- [1단계: 암호 재설정 정책 구성](#step-1-configure-password-reset-policy)
- [2단계: 테스트 사용자에 대한 연락처 데이터 추가](#step-2-add-contact-data-for-your-test-user)
- [3단계: 사용자로 암호 재설정](#step-3-reset-your-azure-ad-password-as-a-user)


###  필수 조건
셀프 서비스 암호 재설정을 사용하도록 설정하고 사용하기 전에, 다음 필수 조건을 완료해야 합니다.

- AAD 테넌트를 만듭니다. 자세한 내용은 [Azure AD Premium 시작](https://azure.microsoft.com/trial/get-started-active-directory/)을 참조하세요.
- Azure 구독. 자세한 내용은 [Azure AD 테넌트 정의](active-directory-administer.md#what-is-an-azure-ad-tenant)를 참조하세요.
- AAD 테넌트를 Azure 구독과 연결합니다. 자세한 내용은 [Azure 구독과 Azure AD의 연관 관계](https://msdn.microsoft.com/library/azure/dn629581.aspx)를 참조하세요.
- Azure AD Premium, Basic으로 업그레이드합니다. 자세한 내용은 [Azure Active Directory 버전](http://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.

  >[AZURE.NOTE]셀프 서비스 암호 재설정을 사용하려면 Azure AD Premium 또는 Azure AD Basic으로 업그레이드해야 합니다. 자세한 내용은 Azure Active Directory 버전을 참조하세요. 이 정보에는 Azure AD Premium 또는 Basic을 등록하는 방법, 라이선스 계획을 활성화하고 Azure AD 액세스를 활성화하는 방법 및 관리자와 사용자 계정에 대한 액세스를 할당하는 방법에 대한 지침이 포함됩니다.
  
- AAD 디렉터리에 하나 이상의 관리자 계정 및 사용자 계정 하나를 만듭니다.
- 만든 관리자 및 사용자 계정에 AAD Premium 또는 Basic 라이선스를 할당합니다.

### 1단계: 암호 재설정 정책 구성
사용자 암호 재설정 정책을 구성하려면 다음 단계를 완료합니다.
 
1.	사용자가 선택한 브라우저를 열고 [Azure 관리 포털](https://manage.windowsazure.com)로 이동합니다.
2.	[Azure 관리 포털](https://manage.windowsazure.com)의 왼쪽 탐색 모음에서 **Active Directory 확장**을 찾습니다.

    ![][001]

3. **디렉터리** 탭에서 사용자 암호 재설정 정책을 구성할 디렉터리를 클릭합니다(예: Wingtip Toys).

    ![][002]

4.	**구성** 탭을 클릭합니다.

    ![][003]

5.	**구성** 탭에서 **사용자 암호 재설정 정책** 섹션으로 아래로 스크롤합니다. 지정된 디렉터리에 대한 사용자 암호 재설정 정책의 모든 측면을 구성하는 곳입니다.

    >[AZURE.NOTE]이 **정책은 관리자가 아닌 조직에서 최종 사용자에만 적용됩니다.** 보안상의 이유로 Microsoft는 관리자에 대 한 암호 재설정 정책을 제어합니다. 이 섹션이 표시되지 않는 경우, Azure Active Directory Premium 또는 Basic에 대 해 로그인하고 이 기능을 구성 중인 관리자 계정에 **라이선스를 할당**했는지 확인합니다.

    ![][004]

6.	사용자 암호 재설정 정책을 구성하려면, **암호 재설정을 위해 사용할 수 있는 사용자** 토글을 **예**로 밀어 설정합니다. 디렉터리에서 이 기능의 작동 방식을 구성할 수 있는 여러 제어를 표시합니다. 필요에 따라 자유롭게 암호 재설정을 사용자 지정할 수 있습니다. 각 암호 재설정 정책 컨트롤이 수행하는 내용을 자세히 알려면, [사용자 지정: Azure AD 암호 관리](active-directory-passwords-customize)를 참조하세요.

    ![][005]

7.	테넌트에 대해 원하는 대로 사용자 암호 재설정 정책을 구성한 후, 화면 아래에서 **저장** 단추를 클릭합니다.

  >[AZURE.NOTE]가장 복잡한 경우에 기능이 동작하는 방법을 볼 수 있도록 두 개의 인증 질문 사용자 암호 재설정 정책을 권장합니다.

  ![][006]

### 2단계: 테스트 사용자에 대한 연락처 데이터 추가
암호 재설정에 사용할 사용자의 조직에서 사용자에 대한 데이터를 지정하는 방법에 대한 몇가지 옵션이 있습니다.

-	[Azure 관리 포털](https://manage.windowsazure.com) 또는 [Office 365 관리자 포털](https://portal.microsoftonline.com)에서 사용자를 편집합니다.
-	AAD Connect를 사용하여 온-프레미스 Active Directory 도메인에서 Azure AD로 사용자 속성을 동기화합니다.
-	Windows PowerShell을 사용하여 사용자 속성을 편집합니다.
-	[http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)의 등록 포털로 안내하여 사용자는 사용자 자신의 데이터를 등록할 수 있습니다.
-	**사용자가 액세스 패널에 로그인할 때 등록해야 함** SSPR 구성 옵션을 **예**로 설정하여 [http://myapps.microsoft.com](http://myapps.microsoft.com)의 액세스 패널에 로그인할 때 암호 재설정을 위해 사용자를 등록해야 합니다.

암호 재설정으로 사용되는 데이터 및 이 데이터에 대한 형식 요구사항에 대해 자세히 알아보려면 [암호 재설정에서 사용되는 데이터](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)를 참조하세요.

#### 사용자 등록 포털을 통해 사용자 연락처 데이터를 추가하려면
1.	암호 재설정 등록 포털을 사용하려면, 이 페이지에([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) 대한 링크를 조직 내의 사용자에게 제공하거나 사용자를 자동으로 등록하는 옵션을 설정합니다. 이 링크를 클릭하면 자신의 조직 계정을 사용하여 로그인해야 합니다. 로그인한 후 다음 페이지를 참조합니다.

    ![][007]

2.	여기에서 사용자는 휴대폰, 대체 전자 메일 주소 또는 보안 질문을 제공하고 확인할 수 있습니다. 다음은 휴대폰을 확인하는 모습입니다.

    ![][008]

3.	사용자가 이 정보를 지정한 후, 페이지가 업데이트되어 정보가 유효함을 나타냅니다(아래에서는 애매했음). **마침** 또는 **취소** 단추를 클릭하면, 액세스 패널이 사용자에게 표시됩니다.

    ![][009]

4.	사용자가 이 정보를 확인하면, 프로필은 자신이 제공하는 데이터로 업데이트됩니다. 이 예제에서, **사무실 전화** 번호가 수동으로 지정되었으므로, 사용자는 자신의 암호를 재설정하기 위한 연락 방법으로 이를 사용할 수도 있습니다.

    ![][010]

### 3단계: 사용자로 Azure AD 암호 재설정
사용자 재설정 정책을 구성하고 사용자에 대한 연락처 정보를 지정하였으므로 이 사용자는 셀프 서비스 암호 재설정을 수행할 수 있습니다.

#### 셀프 서비스 암호 재설정을 수행하려면
1.	[**portal.microsoftonline.com**](http://portal.microsoftonline.com)과 같은 사이트로 이동하는 경우, 아래와 같은 로그인 화면이 표시됩니다. **계정에 액세스할 수 없습니까?** 링크를 클릭하여 암호 재설정 UI를 테스트합니다.

    ![][011]

2.	**계정에 액세스할 수 없습니까?**를 클릭하면, 암호를 재설정할 **사용자 ID**를 묻는 새 페이지로 이동합니다. 테스트 **사용자 ID**를 여기에 입력하고 captcha를 전달하고 **다음**을 클릭합니다.

    ![][012]

3.	사용자가 **사무실 전화**, **휴대폰** 및 **대체 전자 메일**을 지정했으므로 이 경우에 첫번째 인증을 통과하기 위해 제공한 모든 옵션을 표시합니다.

    ![][013]

4.	이 경우, 먼저 **사무실 전화**로 **통화**를 선택합니다. 전화 기반 방법을 선택하는 경우 사용자가 **전화번호 확인**을 완료해야 암호를 재설정할 수 있습니다. 이렇게 하면 악의적인 사람이 사용자 조직에서 사용자의 전화번호를 스팸 발송하지 못합니다.

    ![][014]

5.	사용자가 전화 번호를 확인한 후, 전화를 클릭하면 스피너가 표시되며 전화가 울립니다. 수화기를 들면 **사용자가 "#"를 눌러야 ** 계정을 확인할 수 있다는 메시지가 한 번 재생됩니다. 이 키를 누르면 사용자는 첫번째 질문을 소유하고 두번째 확인 단계로의 UI를 진행함을 자동으로 확인합니다.

    ![][015]

6.	첫 번째 질문을 통과하면, UI가 자동으로 업데이트되어 사용자가 소유한 선택 항목 목록에서 제거됩니다. 이 경우, 먼저 **사무실 전화**를 사용했기 때문에 **휴대폰** 및 **대체 전자 메일**은 두번째 확인 단계에 대한 질문으로 사용할 수 있는 유효한 옵션으로 유지됩니다. **내 대체 전자 메일로 메일 전송** 옵션을 클릭합니다. 완료한 후 전자 메일을 누르면 파일의 대체 전자 메일로 메일을 전송합니다.

    ![][016]

7.	다음은 사용자가 볼 전자 메일의 샘플, 테넌트 브랜드 공지입니다.

    ![][017]

8.	전자 메일이 도착하면 페이지가 업데이트되며, 아래에 표시된 입력 상자의 전자 메일에서 찾은 확인을 입력할 수 있습니다. 적절한 코드를 입력한 후 다음 단추가 켜지고 두번째 확인 단계를 통해 전달할 수 있습니다.

    ![][018]

9.	조직 정책의 요구를 충족한 후 새 암호를 선택할 수 있습니다. 암호는 이를 기반으로 유효성을 검사하며 AAD "강력한" 암호 요구 사항을 충족하고([Azure AD의 암호 정책](https://msdn.microsoft.com/library/azure/jj943764.aspx) 참조), 강도 유효성 검사기는 입력한 암호가 해당 정책을 충족하는지 여부를 나타냅니다.

    ![][019]

10.	조직 정책을 충족하는 일치하는 암호를 제공하면 사용자 암호가 재설정되고 새 암호를 사용하여 즉시 로그인할 수 있습니다.

    ![][020]


## 사용자가 AD 암호를 재설정하거나 변경할 수 있도록 설정

이 섹션에서는 암호를 온-프레미스 Active Directory에 쓰기 저장하도록 구성하는 과정을 안내합니다.

- [암호 쓰기 저장 필수 구성 요소](#writeback-prerequisites)
- [1단계: 최신 버전의 Azure AD Connect 다운로드](#step-1-download-the-latest-version-of-azure-ad-connect)
- [2단계: UI 또는 powershell을 통해 Azure AD Connect에서 암호 쓰기 저장을 사용 하도록 설정 및 확인](#step-2-enable-password-writeback-in-azure-ad-connect)
- [3단계: 방화벽 구성](#step-3-configure-your-firewall)
- [4단계: 적절한 사용 권한 설정](#step-4-set-up-the-appropriate-active-directory-permissions)
- [5단계: 사용자로 AD 암호 재설정 및 확인](#step-5-reset-your-ad-password-as-a-user)


### 쓰기 저장 필수 구성 요소
암호 쓰기 저장을 사용하도록 설정하고 사용하기 전에, 다음 필수 조건을 완료해야 합니다.

- Azure AD Premium을 사용하도록 설정한 Azure AD 테넌트가 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.
- 암호 재설정이 구성되고 테넌트에서 사용하도록 설정합니다. 자세한 정보는 [사용자가 Azure AD 암호를 재설정할 수 있도록 설정](#enable-users-to-reset-their-azure-ad-passwords)을 참조하세요.
- 하나 이상의 관리자 계정이 있고 이 기능을 테스트하는데 사용할 수 있는 Azure AD Premium 라이선스가 있는 테스트 사용자 계정이 하나 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

  >[AZURE.NOTE]암호 쓰기 저장을 설정하는 데 사용하는 관리자 계정이 페더레이션된 계정(온-프레미스 AD에서 만들고 Azure AD로 동기화된)이 아닌 클라우드 관리자 계정(Azure AD에서 만든)인지 확인합니다.
  
- Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 또는 최신 서비스 팩이 설치된 Windows Server 2012 R2를 실행 중인 단일 또는 다중 포리스트 AD 온-프레미스 배포가 있습니다.

  >[AZURE.NOTE]이전 버전의 Windows Server 2008 또는 2008 R2를 실행 중인 경우, 이 기능을 여전히 사용할 수 있지만 클라우드에서 로컬 AD 암호 정책을 적용할 수 있으려면 [KB 2386717를 다운로드하고 설치](https://support.microsoft.com/kb/2386717)해야 합니다.
  
- Azure AD Connect 도구가 설치되어 있고 클라우드로 동기화할 AD 환경을 준비합니다. 자세한 내용은 [클라우드에서 온-프레미스 ID 인프라 사용](active-directory-aadconnect.md)을 참조하세요.

  >[AZURE.NOTE]비밀번호 쓰기 저장을 테스트하기 전에 먼저 AD와 Azure AD에서 모두 전체 가져오기 및 전체 동기화 되어있는지 확인해야 합니다.

- Azure AD Sync 또는 Azure AD Connect를 사용하는 경우, **TCP 443** 아웃바운드(및 경우에 따라 **TCP 9350-9354**)가 열려 있어야 합니다. 자세한 내용은 [3단계: 방화벽 구성](#step-3-configure-your-firewall)을 참조하세요. 이 시나리오에서 DirSync 사용은 더 이상 지원되지 않습니다. DirSync를 아직 사용 중인 경우, 비밀번호 쓰기 저장을 배포하기 전에 최신 버전의 Azure AD Connect로 업그레이드하십시오.

  >[AZURE.NOTE]Azure AD Sync 또는 디렉터리 동기화 도구를 사용하는 사람은 최신 버전의 Azure AD Connect로 업그레이드하여 가능한 최상의 경험 및 릴리스될 때의 새로운 기능을 확인하는 것이 좋습니다.
  

### 1단계: 최신 버전의 Azure AD Connect 다운로드
암호 쓰기 저장은 Azure AD Connect 또는 **1.0.0419.0911** 이상의 버전이 있는 Azure AD Sync 도구의 릴리스에서 사용 가능합니다. 자동 계정 잠금 해제가 있는 암호 쓰기 저장은 Azure AD Connect 또는 **1.0.0485.0222** 이상의 버전이 있는 Azure AD Sync 도구의 릴리스에서 사용 가능합니다. 이전 버전을 실행하는 경우 진행하기 전에 적어도 이 버전으로 업그레이드합니다. [여기를 클릭하여 최신 버전의 Azure AD Connect를 다운로드합니다](active-directory-aadconnect.md#download-azure-ad-connect).

#### Azure AD Sync의 버전을 확인하려면
1.	**%ProgramFiles%\\Azure Active Directory Sync**로 이동합니다.
2.	**ConfigWizard.exe** 실행 파일을 찾습니다.
3.	실행 파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 옵션을 선택합니다.
4.	**세부 정보** 탭을 클릭합니다.
5.	**파일 버전** 필드를 찾습니다.

    ![][021]

이 버전 번호가 **1.0.0419.0911**보다 크거나 같거나, 또는 Azure AD Connect를 설치하면, [2단계: UI 또는 powershell을 통해 Azure AD Connect에서 암호 쓰기 저장을 사용하도록 설정 및 확인](#step-2-enable-password-writeback-in-azure-ad-connect)으로 건너뛸 수 있습니다.

 >[AZURE.NOTE]처음으로 Azure AD Connect 도구를 설치한 경우, 디렉터리 동기화를 위한 환경을 준비하는 몇가지 모범 사례를 따르는 것이 좋습니다. Azure AD Connect 도구를 설치하기 전에 [Office 365 관리자 포털](https://portal.microsoftonline.com) 또는 [Azure 관리 포털](https://manage.windowsazure.com)에서 디렉터리 동기화를 활성화해야 합니다. 자세한 내용은 [Azure AD Connect 관리](active-directory-aadconnect-whats-next.md)를 참조하세요.


### 2단계: Azure AD Connect에서 암호 쓰기 저장 사용
이제 Azure AD Connect 도구를 다운로드 했으므로 암호 쓰기 저장 사용 준비가 된 것입니다. 이 두 방법 중 하나를 수행할 수 있습니다. Azure AD Connect 설치 마법사의 선택적 기능 화면에서 암호 쓰기 저장을 사용하거나 Windows PowerShell을 통해 설정할 수 있습니다.

#### 구성 마법사에서 암호 쓰기 저장을 사용 하도록 설정하려면
1.	**디렉터리 동기화 컴퓨터**에서 **Azure AD Connect** 구성 마법사를 엽니다.
2.	**선택적 기능** 구성 화면에 도달할 때까지 단계를 클릭합니다.
3.	**암호 쓰기 저장** 옵션을 선택합니다.

    ![][022]

4.	마법사를 완료하면, 마지막 페이지는 변경 내용을 요약하고 암호 쓰기 저장 구성 변경 내용이 포함됩니다.

> [AZURE.NOTE]이 마법사를 다시 실행하고 해당 기능을 선택 취소하거나, 또는 [Azure 관리 포털](https://manage.windowsazure.com)에서 사용자 디렉터리의 **구성** 탭의 **사용자 암호 재설정 정책** 섹션에서 **온-프레미스 디렉터리로 암호 쓰기 저장** 설정을 **아니오**로 설정하여 언제든지 암호 쓰기 저장을 사용하지 않도록 설정할 수 있습니다. 사용자 암호 재설정 환경 사용자 지정에 관한 자세한 내용은 [사용자 지정: Azure AD 암호 관리](active-directory-passwords-customize.md)를 확인합니다.

#### Windows PowerShell을 사용하여 암호 쓰기 저장을 사용하도록 설정하려면
1.	사용자 **디렉터리 동기화 컴퓨터**에서 새 **관리자 권한 Windows PowerShell 창**을 엽니다.
2.	모듈이 아직 로드되지 않은 경우, `Import-Module ADSync` 명령에 입력하여 현재 세션으로 Azure AD Connect cmdlet을 로드합니다.
3.	`Get-ADSyncConnector` cmdlet을 실행하고 결과를 `$aadConnectorName`에 저장하여 시스템에서 AAD Connector의 목록을 얻습니다.
4.	다음 cmdlet을 실행하여 현재 커넥터에 대한 쓰기 저장의 현재 상태를 가져오려면: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.	이 cmdlet을 실행하여 암호 쓰기 저장을 활성화합니다. `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE]자격 증명을 입력하라는 메시지가 표시되면, AzureADCredential에 대해 지정되는 관리자 계정이 페더레이션된 계정(온-프레미스 AD에서 만들고 Azure AD로 동기화된)이 아닌 **클라우드 관리자 계정(Azure AD에서 만든)**입니다.[AZURE.NOTE]위의 동일한 지침을 반복하지만 단계에서 `$false`을 전달하거나, 또는 [Azure 관리 포털](https://manage.windowsazure.com)에서 사용자 디렉터리의 **구성** 탭의 **사용자 암호 재설정 정책 섹션**에서 **온-프레미스 디렉터리로 암호 쓰기 저장** 설정을 **아니오**로 설정하여 언제든지 암호 쓰기 저장을 사용하지 않도록 설정하여 PowerShell을 통해 암호 쓰기 저장을 사용하지 않도록 설정할 수 있습니다.

#### 구성이 성공되었는지 확인합니다.
구성이 성공하면 Windows PowerShell 창에서 암호 재설정 쓰기 저장을 사용하도록 설정되었다는 메시지를 보거나, 구성 UI에서 성공 메시지를 볼 수 있습니다.

이벤트 뷰어를 열고 응용 프로그램 이벤트 로그로 이동하고 **PasswordResetService**에서 이벤트 **31005 - OnboardingEventSuccess**를 찾아 서비스가 올바르게 설치되었는지 확인할 수도 있습니다.

  ![][023]

### 3단계: 방화벽 구성
Azure AD Connect 도구에서 암호 쓰기 저장을 사용하도록 설정한 후, 서비스가 클라우드에 연결할 수 있는지 확인해야 합니다.

1.	설치가 완료되면, 사용자 환경에서 알 수 없는 아웃바운드 연결을 차단한 경우 방화벽에 다음 규칙도 추가해야 합니다. 변경한 후 AAD Connect 컴퓨터를 다시 부팅해야 합니다.
   - 포트 443 TCP를 통한 아웃바운드 연결 허용
   - https://ssprsbprodncu-sb.accesscontrol.windows.net/에 아웃바운드 연결 허용 
   - 프록시를 사용하거나 일반적인 연결 문제가 있는 경우, 포트 9350-9354 TCP를 통한 아웃바운드 연결 허용

### 4단계: 적절한 Active Directory 사용 권한 설정
암호를 재설정한 사용자를 포함한 모든 포리스트의 경우, X가 구성 마법사에서 해당 포리스트에 대해 지정된 계정이면(초기 구성 중), X는 **암호 재설정**, **암호 변경**, `lockoutTime`에서 **쓰기 권한**, `pwdLastSet`에서 **쓰기 권한**를 제공하며 해당 포리스트에서 각 도메인의 루트 개체에 대한 권한을 확장합니다. 오른쪽은 모든 사용자 개체에서 상속된 것으로 표시되어야 합니다.

위에서 참조되는 계정을 모르는 경우, Azure Active Directory Connect 구성 UI를 열고 **솔루션 검토** 옵션을 클릭합니다. 권한을 추가해야 하는 계정은 아래 스크린샷에서 빨간색 밑줄이 그어집니다.

**<font color="red">시스템에 있는 각 포리스트의 도메인 각각에 이 사용 권한을 설정하고, 그렇지 않은 경우 비밀번호 쓰기 저장이 제대로 작동하지 않습니다.</font>**

  ![][032]

  이러한 사용 권한을 설정하면 각 포리스트에 대한 MA 서비스 계정이 해당 포리스트 내에서 사용자 계정을 대신하여 암호를 관리할 수 있습니다. 이러한 사용 권한을 할당하는 것을 잊은 경우, 쓰기 저장이 올바르게 구성된 것으로 표시되면, 클라우드에서 온-프레미스 암호 관리를 시도하면 오류가 발생합니다. 다음은 **Active Directory 사용자 및 컴퓨터** 관리 스냅인을 사용하여 수행할 수 있는 방법에 대한 자세한 단계입니다.

>[AZURE.NOTE]이 사용 권한이 디렉터리의 모든 개체를 복제하려면 한 시간이 걸릴 수 있습니다.

#### 쓰기 저장이 이루어지도록 올바른 권한을 설정하려면

1.	적절한 도메인 관리 권한이 있는 계정으로 **Active Directory 사용자 및 컴퓨터**를 엽니다.
2.	**보기 메뉴** 옵션에서 **고급 기능**이 켜져 있어야 합니다.
3.	왼쪽 패널에서, 도메인의 루트를 나타내는 개체를 마우스 오른쪽 단추로 클릭합니다.
4.	**보안** 탭을 클릭합니다.
5.	**고급**을 클릭합니다.

    ![][024]

6.	**권한** 탭에서 **추가**를 클릭합니다.

    ![][025]

7.	권한을 부여하려는 계정을 선택합니다(해당 포리스트에 대한 동기화를 설정하는 동안 지정된 동일한 계정임).
8.	위쪽의 드롭다운 목록에서 **하위 사용자 개체**를 선택합니다.
9.	나타나는 **권한 항목** 대화 상자에서, **암호 재설정**, **암호 변경**, `lockoutTime`에서 **쓰기 권한** 및 `pwdLastSet`에서 **쓰기 권한** 상자를 선택합니다.

    ![][026] ![][027] ![][028]

10.	열린 모든 대화 상자를 통해 **적용/확인**을 클릭합니다.

### 5단계: 사용자로 AD 암호 재설정
이제 암호 쓰기 저장을 사용하도록 설정했으므로, 클라우드 테넌트로 동기화된 계정자의 사용자 암호를 재설정하여 작동하는지 테스트할 수 있습니다.
 
#### 암호 쓰기 저장이 제대로 작동하는 지 확인하려면
1.	[https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)으로 이동하거나 임의의 조직 ID 로그인 화면으로 이동하고 **계정에 액세스할 수 없습니까?** 링크를 클릭합니다.

    ![][029]

2.	이제 암호를 재설정하려는 사용자 ID에 대해 묻는 새 페이지가 나타나야 합니다. 테스트 사용자 ID를 입력하고 암호 재설정 작업을 진행합니다.
3.	암호를 재설정한 후 다음과 비슷한 화면이 표시됩니다. 사용자가 온-프레미스 및/또는 클라우드 디렉터리에서 암호를 성공적으로 재설정했음을 의미합니다.

    ![][030]

4.	작업이 성공했음을 확인하거나 모든 오류를 진단하려면, **디렉터리 동기화 컴퓨터**로 이동하고 **이벤트 뷰어**를 열고 **응용 프로그램 이벤트 로그**로 이동하고 테스트 사용자의 소스 **PasswordResetService**에서 이벤트 **31002-PasswordResetSuccess**를 찾습니다.

    ![][031]


<br/> <br/> <br/>

**추가 리소스**


* [암호 관리 정의](active-directory-passwords.md)
* [암호 관리의 작동 원리](active-directory-passwords-how-it-works.md)
* [암호 관리 사용자 지정](active-directory-passwords-customize.md)
* [암호 관리 모범 사례](active-directory-passwords-best-practices.md)
* [암호 관리 보고서와 함께 Operational Insights를 얻는 방법](active-directory-passwords-get-insights.md)
* [암호 관리 FAQ](active-directory-passwords-faq.md)
* [암호 관리 문제 해결](active-directory-passwords-troubleshoot.md)
* [자세한 정보](active-directory-passwords-learn-more.md)
* [MSDN의 암호 관리](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

<!---HONumber=Nov15_HO1-->