<properties 
	pageTitle="모범 사례: Azure AD 암호 관리 | Microsoft Azure" 
	description="배포 및 사용 모범 사례, 샘플 최종 사용자 설명서 및 Azure Active Directory에서 암호 관리에 대한 교육 가이드입니다." 
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

# 암호 관리 배포 및 사용자 교육
암호 재설정을 사용한 후, 수행해야하는 다음 단계는 서비스를 사용하여 사용자의 조직에서 사용자를 가져오는 것입니다. 이렇게 하려면 사용자가 서비스를 제대로 사용하도록 구성했는지 확인하고 사용자가 자신의 암호를 성공적으로 관리하는 데 필요한 교육을 받아야 합니다. 이 문서는 다음 개념을 설명합니다.

* [**암호 관리를 위해 사용자를 구성하는 방법**](#how-to-get-users-configured-for-password-reset)
  * [암호 재설정을 위해 계정을 구성하는 이유](#what-makes-an-account-configured)
  * [사용자가 직접 인증 데이터를 채울 수 있는 방법](#ways-to-populate-authentication-data)
* [**조직에 재설정된 암호를 롤아웃할 수 있는 가장 좋은 방법**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [전자 메일 롤아웃 + 샘플 전자 메일 통신](#email-based-rollout)
  * [사용자에 대한 고유의 사용자 지정 암호 관리 포털 만들기](#creating-your-own-password-portal)
  * [강제로 사용자가 로그인에 등록하도록 강제 등록을 사용하는 방법](#using-enforced-registration)
  * [사용자 계정에 대한 인증 데이터를 업로드하는 방법](#uploading-data-yourself)
* [**샘플 사용자 및 지원 교육 자료(서비스 예정!)**](#sample-training-materials)

## 암호 재설정을 위해 사용자를 구성하는 방법
이 섹션에서는 사용자가 암호를 잊어버린 경우 효과적으로 조직의 모든 사용자가 셀프 서비스 암호 재설정을 사용할 수 있는 여러 방법을 설명합니다.

### 계정을 구성하는 이유
사용자가 암호 재설정을 사용하려면 먼저 **모든** 다음 조건이 충족되어야 합니다.

1.	암호 재설정은 디렉터리에서 사용할 수 있어야 합니다. [사용자의 Azure AD 암호 재설정 허용](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) 또는 [사용자의 AD 암호 재설정 또는 변경 허용](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)을 읽어 암호를 재설정하는 방법에 대해 배웁니다.
2.	사용자는 사용 허가를 받아야 합니다.
 - 클라우드 사용자의 경우, **모든 유료 Office 365 라이선스** 또는 **AAD 기본** 또는 **AAD Premium 라이선스**가 사용자에게 할당되어야 합니다.
 - 온-프레미스 사용자의 경우(페더레이션 또는 해시 동기화된), 사용자에게 **AAD Premium 라이선스가 할당되어 있어야 합니다**.
3.	사용자는 현재 암호 재설정 정책에 따라 **정의된 인증 데이터의 최소 집합**을 설정해야 합니다.
 - 디렉터리의 해당 필드가 올바른 형식의 데이터를 포함하는 경우 인증 데이터는 정의된 것으로 간주됩니다.
 - 게이트 정책 하나가 구성된 경우 최소 인증 데이터 집합이 사용 가능한 인증 옵션 중 **적어도 하나**로 정의되거나, 또는 두 게이트 정책이 구성된 경우 사용 가능한 인증 옵션 중 **최소 두** 개로 정의됩니다.
4.	사용자가 온-프레미스 계정을 사용하는 경우 [암호 쓰기 저장](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)이 활성화되고 켜져 있어야 합니다.

### 인증 데이터를 채우는 방법
암호 재설정에 사용할 사용자의 조직에서 사용자에 대한 데이터를 지정하는 방법에 대한 몇가지 옵션이 있습니다.

- [Azure 관리 포털](https://manage.windowsazure.com) 또는 [Office 365 관리자 포털](https://portal.microsoftonline.com)에서 사용자를 편집합니다.
- AADSync를 사용하여 온-프레미스 Active Directory 도메인에서 Azure AD로 사용자 속성을 동기화합니다.
- Windows PowerShell을 사용하여 사용자 속성을 편집합니다.
- [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)의 등록 포털로 안내하여 사용자는 사용자 자신의 데이터를 등록할 수 있습니다.
- **사용자가 SSPR를 등록해야 함** 구성 옵션을 **예**로 설정하여 [http://myapps.microsoft.com](http://myapps.microsoft.com)의 액세스 패널에 로그인할 때 암호 재설정을 위해 사용자를 등록해야 합니다.

시스템이 작동하기 위해 사용자가 암호를 등록할 필요는 없습니다. 예를 들어, 기존 전화번호가 로컬 디렉터리에 있는 경우 Azure AD에서 동기화할 수 있으며 자동으로 암호 재설정을 사용합니다.

## 사용자에 재설정된 암호를 롤아웃할 수 있는 가장 좋은 방법은 무엇입니까?
다음은 암호 재설정을 위한 일반 롤아웃 단계입니다.

1.	[Azure 관리 포털](https://manage.windowsazure.com)의 **구성** 탭으로 이동하고 **암호 재설정에 사용할 수 있는 사용자** 옵션에 대해 **예**를 선택하여 사용자 디렉터리에서 암호 재설정을 사용합니다.
2.	[Azure 관리 포털](https://manage.windowsazure.com)의 **라이선스** 탭으로 이동하여 암호 재설정에 제공하려는 각 사용자에게 적절한 라이선스를 할당합니다.
3.	**암호 재설정에 대한 액세스 제한** 전환을 **예**로 설정하고 암호 재설정을 사용하도록 보안 그룹을 선택하여 느리게 시간에 따라 기능을 롤아웃하도록 선택적으로 암호 재설정을 사용자 그룹으로 제한합니다.
4.	사용자에게 등록하도록 안내하는 전자 메일을 보내고, 액세스 패널에서 강제 등록을 사용하거나, DirSync, PowerShell 또는 [Azure 관리 포털](https://manage.windowsazure.com)을 통해 사용자 자신에 대해 적절한 인증 데이터를 업로드하여 암호 재설정을 사용하도록 사용자에게 알립니다. 이에 대한 자세한 내용은 아래에 제공됩니다.
5.	시간이 지남에 따라 보고서 탭으로 이동하여 [**암호 재설정 등록 활동**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) 보고서를 보고 등록된 사용자를 검토합니다.
6.	대부분의 사용자를 등록한 후, 보고서 탭으로 이동하고 [**암호 재설정 활동**](active-directory-passwords-get-insights.md#view-password-reset-activity) 보고서를 보고 암호 재설정을 사용합니다.

여러가지 방법으로 조직에 등록하고 암호 재설정을 사용할 수 있음을 사용자에게 알립니다. 아래에서 자세히 설명합니다.

### 전자 메일 기반 롤아웃
등록이나 암호 재설정 사용에 대해 사용자에게 알리는 가장 단순한 방법은 이를 설명하는 전자 메일을 전송하는 것입니다. 다음 템플릿을 사용하면 이 작업을 수행할 수 있습니다. 사용자 필요에 맞게 사용자 지정하기 위해 사용자가 선택한 색/ 로고로 자유롭게 바꾸세요.

  ![][001]

[여기](http://1drv.ms/1xWFtQM)에서 전자 메일 템플릿을 다운로드할 수 있습니다.

### 고유의 암호 포털 만들기
암호 관리 기능을 배포하는 더 큰 고객에게 적합한 하나의 전략은 사용자가 한 위치에서 암호와 관련된 모든 것을 관리하는 데 사용할 수 있는 "암호 포털"를 생성하는 것입니다.

대부분의 가장 큰 고객이 Azure AD 암호 재설정 포털, 암호 재설정 등록 포털 및 암호 변경 페이지에 대한 링크가 있는 https://passwords.contoso.com과 같은 루트 DNS 항목을 만들도록 선택합니다. 이러한 방식으로 보낼 전자 메일 통신 또는 전단지에서 서비스를 시작할 시간이 있을 때 이동할 수 있는 하나의 인상적인 URL을 포함할 수 있습니다.

여기서 진행하려면 응답성이 뛰어난 최신 UI 설계 패러다임을 사용하는 간단한 페이지를 만들고 모든 브라우저 및 모바일 장치에서 작동합니다.

  ![][007]
  
[여기](https://github.com/kenhoff/password-reset-page)에서 웹 사이트 템플릿을 다운로드할 수 있습니다. 조직의 필요성에 따라 로고 및 색을 사용자 지정하는 것이 좋습니다.

### 강제 등록 사용
직접 암호 재설정에 등록하려면 [http://myapps.microsoft.com](http://myapps.microsoft.com)의 액세스 패널에 로그인할 때 강제로 등록하게 할 수도 있습니다. **액세스 패널에 로그인할 때 사용자가 등록해야 함** 옵션을 사용하여 사용자 디렉터리의 **구성** 탭에서 이 옵션을 사용할 수 있습니다.

**사용자가 연락 데이터를 확인하기 전까지 남은 일 수**를 영(0)이 아닌 값으로 수정하여 구성 가능한 시간 후에 다시 등록해야 하는지 여부를 선택적으로 정의할 수도 있습니다. 자세한 내용은 [사용자 암호 관리 동작 사용자 지정](active-directory-passwords-customize.md#password-management-behavior)을 참조하세요.

  ![][002]

이 옵션을 설정한 다음, 사용자가 액세스 패널에 로그인하면 관리자가 연락처 정보를 확인하기 위해 필요함을 알리는 팝업을 표시합니다. 자신의 계정에 액세스하지 못한 경우 이를 사용하여 암호를 재설정할 수 있습니다.

  ![][003]

**지금 확인**을 클릭하면 [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)의 **암호 재설정 등록 포털**로 이동되며 해당 사용자를 등록해야 합니다. **취소** 단추를 클릭하거나 창을 닫어 이 메서드를 통한 등록을 해제할 수 있지만 등록하지 않은 경우 로그인할 때마다 사용자에게 알려줍니다.

  ![][004]

### 데이터 업로드
직접 인증 데이터를 업로드하려는 경우, 사용자가 자신의 암호를 다시 설정하기 전에 암호 재설정에 등록하지 않아도 됩니다. 사용자가 정의한 암호 재설정 정책을 충족하는 인증 데이터가 계정에 정의되어 있는 한 해당 사용자는 암호를 재설정할 수 있습니다.

AAD Connect 또는 Windows PowerShell을 통해 설정할 수 있는 속성을 알아보려면 [암호 재설정으로 사용되는 데이터](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)를 참조하세요.

다음 단계를 수행하여 [Azure 관리 포털](https://manage.windowsazure.com)에서 인증 데이터를 업로드할 수 있습니다.

1.	[Azure 관리 포털](https://manage.windowsazure.com)의 **Active Directory 확장**에서 사용자 디렉터리로 이동합니다.
2.	**사용자** 탭을 클릭합니다.
3.	목록에서 관심 있는 사용자를 선택합니다.
4.	첫 번째 탭에서 암호 재설정을 사용하는 속성으로 사용될 수 있는 **대체 전자 메일**을 찾습니다. 

    ![][005]

5.	**작업 정보** 탭을 클릭합니다.
6.	이 페이지에서 **사무실 전화**, **휴대폰**, **인증 전화** 및 **인증 전자 메일**을 찾습니다. 사용자가 자신의 암호를 다시 설정할 수 있도록 이 속성을 설정할 수도 있습니다. 

    ![][006]

각 특성을 사용할 수 있는 방법은 [암호 재설정으로 사용되는 데이터](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)를 참조하세요.

## 샘플 교육 자료
암호 재설정을 배포하고 사용하는 방법에 대해 IT 조직 및 사용자가 신속하게 사용할 수 있는 샘플 교육 자료로 작업합니다. 기대하세요.


<br/> <br/> <br/>

**추가 리소스**


* [암호 관리 정의](active-directory-passwords.md)
* [암호 관리의 작동 원리](active-directory-passwords-how-it-works.md)
* [암호 관리 시작](active-directory-passwords-getting-started.md)
* [암호 관리 사용자 지정](active-directory-passwords-customize.md)
* [암호 관리 보고서와 함께 Operational Insights를 얻는 방법](active-directory-passwords-get-insights.md)
* [암호 관리 FAQ](active-directory-passwords-faq.md)
* [암호 관리 문제 해결](active-directory-passwords-troubleshoot.md)
* [자세한 정보](active-directory-passwords-learn-more.md)
* [MSDN의 암호 관리](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"

<!---HONumber=Oct15_HO4-->