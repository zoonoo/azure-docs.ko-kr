<properties 
	pageTitle="작동 방법: Azure AD 암호 관리 | Microsoft Azure" 
	description="사용자가 암호를 등록, 재설정 및 변경하는 위치, 관리자가 온-프레미스 Active Directory 암호의 관리를 구성, 보고 및 사용하도록 설정하는 위치를 포함하여 Azure AD 암호 관리의 다양 한 구성 요소에 대해 알아봅니다." 
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
	ms.date="11/16/2015" 
	ms.author="asteen"/>

# 암호 관리의 작동 원리
Azure Active Directory에서 암호 관리는 아래에 설명된 여러 논리 구성 요소로 구성됩니다. 해당 구성 요소에 대한 자세한 내용을 보려면 각 링크를 클릭하세요.

- [**암호 관리 구성 포털**](#password-management-configuration-portal) – 관리자가 [Azure 관리 포털](https://manage.windowsazure.com)에서 해당 디렉터리의 구성 탭으로 이동하여 해당 테넌트에서 암호를 관리하는 방법의 다양한 측면을 제어할 수 있습니다.
- [**사용자 등록 포털**](#user-registration-portal) – 사용자가 이 웹 포털을 통해 암호 재설정에 자동 등록할 수 있습니다.
- [**사용자 암호 재설정 포털**](#user-password-reset-portal) – 사용자가 관리자 제어 암호 재설정 정책에 따라 여러 가지 챌린지를 사용하여 암호를 다시 설정할 수 있습니다.
- [**사용자 암호 변경 포털**](#user-password-change-portal) – 사용자가 이 웹 포털을 사용하여 이전 암호를 입력하고 새 암호를 선택하면 언제든지 자신의 암호를 변경할 수 있습니다.
- [**암호 관리 보고서**](#password-management-reports) – 관리자가 [Azure 관리 포털](https://manage.windowsazure.com)에서 해당 디렉터리의 "보고서" 탭의 "작업 보고서" 섹션으로 이동하여 해당 테넌트에서의 암호 재설정 및 등록 동작을 보고 분석할 수 있습니다.
- [**Azure AD Connect의 암호 쓰기 저장 구성 요소**](#password-writeback-component-of-azure-ad-connect) – 페더레이션 또는 암호가 동기화된 사용자 암호를 클라우드에서 관리할 수 있도록 Azure AD Connect를 설치하는 경우 관리자가 암호 쓰기 저장 기능을 선택적으로 사용할 수 있습니다.

## 암호 관리 구성 포털
특정 디렉터리의 **구성** 탭에 있는 **사용자 암호 재설정 정책** 섹션으로 이동하면 [Azure 관리 포털](https://manage.windowsazure.com)을 사용하여 해당 디렉터리에 대한 암호 관리 정책을 구성할 수 있습니다. 이 구성 페이지에서 다음을 포함하여 조직이 암호를 관리하는 방법의 다양한 측면을 제어할 수 있습니다.

- 디렉터리의 모든 사용자에 대해 암호 재설정 사용 여부 설정
- 사용자가 암호를 다시 설정하기 위해 통과해야 하는 챌린지 횟수(1회 또는 2회) 설정
- 아래 선택 항목 중 조직의 사용자에 대해 사용하려는 특정 챌린지 유형 설정
 - 휴대폰(문자 또는 음성 통화를 통한 확인 코드)
 - 사무실 전화(음성 통화)
 - 대체 전자 메일(전자 메일을 통한 확인 코드)
 - 보안 질문(지식 기반 인증)
- 보안 질문 인증 방법을 사용하기 위해 사용자가 등록해야 하는 질문의 수 설정(보안 질문을 사용하는 경우만 표시)
- 보안 질문 인증 방법을 사용하기 위해 사용자가 재설정 중에 제공해야 하는 질문의 수 설정(보안 질문을 사용하는 경우만 표시)
- 사용자가 암호를 재설정하기 위해 등록할 때 사용하려고 선택한 미리 준비된 지역화된 보안 질문 사용(보안 질문을 사용하는 경우만 표시)
- 사용자가 암호를 재설정하기 위해 등록할 때 사용하려고 선택한 사용자 지정 보안 질문 정의(보안 질문을 사용하는 경우만 표시)
- 사용자가 응용 프로그램 액세스 패널([http://myapps.microsoft.com](http://myapps.microsoft.com))로 이동할 때 암호 재설정에 대한 등록 필요
- 구성 가능한 일수가 경과한 후 이전에 등록한 데이터에 대한 사용자의 재확인 필요(강제 등록을 사용하는 경우에만 표시)
- 사용자가 암호를 재설정하는 데 문제가 있는 경우 사용자에게 표시할 사용자 지정 기술 지원팀 전자 메일 또는 URL 제공
- 암호 쓰기 저장 기능 사용 여부 설정(AAD Connect를 사용하여 암호 쓰기 저장이 배포된 경우)
- 암호 쓰기 저장 에이전트의 상태 보기(AAD Connect를 사용하여 암호 쓰기 저장이 배포된 경우)
- 사용자가 암호를 다시 설정한 경우 사용자에게 전자 메일 알림 사용([Azure 관리 포털](https://manage.windowsazure.com)의 **알림** 섹션에 있음)
- 다른 관리자가 암호를 다시 설정한 경우 관리자에게 전자 메일 알림 사용([Azure 관리 포털](https://manage.windowsazure.com)의 **알림** 섹션에 있음)
- 테넌트 브랜딩 사용자 지정 기능을 사용하여 조직의 로고 및 이름으로 사용자 암호 재설정 포털과 암호 재설정 전자 메일에 브랜딩 추가([Azure 관리 포털](https://manage.windowsazure.com)의 **디렉터리 속성** 섹션에 있음)

조직에서 암호 관리를 구성하는 방법에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.

##사용자 등록 포털
암호 재설정을 사용하려면 먼저 클라우드 사용자 계정을 올바른 인증 데이터로 업데이트하여 관리자가 적절하게 정의한 암호 재설정 챌린지 횟수를 통과할 수 있도록 해야 합니다. 또한 Azure 또는 Office 웹 포털, DirSync/Azure AD Connect, Windows PowerShell을 사용하면 관리자가 해당 사용자를 대신하여 이 인증 정보를 정의할 수 있습니다.

그러나 사용자가 자신의 고유 데이터를 등록하려는 경우는 사용자가 이 정보를 제공하기 위해 이동할 수 있는 웹 페이지도 제공됩니다. 이 페이지에서 사용자는 해당 조직에 사용 설정된 암호 재설정 정책에 따라 인증 정보를 지정할 수 있습니다. 이 데이터가 확인되고 나면 클라우드 사용자 계정에 저장되므로 나중에 계정 복구에 사용할 수 있습니다. 등록 포털의 모양은 다음과 같습니다.

  ![][001]

자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md) 및 [모범 사례: Azure AD 암호 관리](active-directory-passwords-best-practices.md)를 참조하세요.

##사용자 암호 재설정 포털
셀프 서비스 암호 재설정을 사용하도록 설정했으면 조직의 셀프 서비스 암호 재설정 정책을 설정할 수 있으며, 사용자가 디렉터리에 적절한 연락처 데이터를 가지고 있으면 조직 내 사용자는 로그인을 위해 회사 또는 학교 계정을 사용하는 모든 웹 페이지(예: [portal.microsoftonline.com](https://portal.microsoftonline.com))에서 자동으로 암호를 재설정할 수 있게 됩니다. 이러한 페이지에는 **계정에 액세스할 수 없습니까?** 링크가 표시됩니다.

  ![][002]

이 링크를 클릭하면 셀프 서비스 암호 재설정 포털이 시작됩니다.

  ![][003]

사용자가 암호를 다시 설정할 수 있는 방법에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.

##사용자 암호 변경 포털
사용자가 암호를 변경하려면 언제든지 암호 변경 포털을 사용하여 변경할 수 있습니다. 사용자는 액세스 패널 프로필 페이지를 통하거나 Office 365 응용 프로그램 내에서 "암호 변경" 링크를 클릭하여 암호 변경 포털에 액세스할 수 있습니다. 해당 암호가 만료되는 경우는 로그인 시 사용자에게 자동으로 변경할 것인지 물어봅니다.

  ![][004]

두 경우 모두, 암호 쓰기 저장이 사용 설정되고 사용자가 페더레이션 또는 암호가 동기화된 경우 변경된 암호가 온-프레미스 Active Directory에 다시 기록됩니다. 암호 변경 포털의 모양은 다음과 같습니다.

  ![][005]

사용자가 온-프레미스 Active Directory 암호를 변경할 수 있는 방법에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.

##암호 관리 보고서
**보고서** 탭으로 이동하여 **활동 로그** 섹션 아래를 보면 두 개의 암호 관리 보고서 즉, **암호 재설정 활동** 및 **암호 재설정 등록 활동** 보고서가 표시됩니다. 이들 두 보고서를 사용하여 조직 내에서 사용자의 암호 재설정 등록 및 사용에 대한 뷰를 얻을 수 있습니다. [Azure 관리 포털](https://manage.windowsazure.com)에서 보고서는 다음과 같이 표시됩니다.

  ![][006]

자세한 내용은 [정보 활용: Azure AD 암호 관리 보고서](active-directory-passwords-get-insights.md)를 참조하세요.

##Azure AD Connect의 암호 쓰기 저장 구성 요소
조직 내 사용자의 암호를 온-프레미스 환경에서 생성하면(페더레이션 또는 암호 동기화 중 하나를 통해) Azure AD Connect의 최신 버전을 설치하여 클라우드에서 직접 이러한 암호를 업데이트할 수 있습니다. 따라서 사용자가 AD 암호를 잊었거나 수정하려고 할 때 웹에서 바로 수행할 수 있습니다. Azure AD Connect 설치 마법사에서 암호 쓰기 저장을 찾을 수 있는 위치는 다음과 같습니다.

  ![][007]

Azure AD Connect에 대한 자세한 내용은 [시작: Azure AD Connect](active-directory-aadconnect.md)를 참조하세요. 암호 쓰기 저장에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.


<br/> <br/> <br/>

## 암호 재설정 설명서에 대한 링크
다음은 모든 Azure AD 암호 재설정 설명서 페이지에 대한 링크입니다.

* [**자신의 암호 재설정**](active-directory-passwords-update-your-own-password) - 시스템 사용자로서 자신의 암호를 재설정하거나 변경하는 방법을 알아봅니다.
* [**시작**](active-directory-passwords-getting-started.md) -사용자가 클라우드 또는 온-프레미스 암호를 다시 설정하고 변경할 수 있는 방법에 대해 알아봅니다.
* [**사용자 지정**](active-directory-passwords-customize.md) - 모양과 느낌 및 조직의 요구에 맞게 서비스의 동작을 사용자 지정하는 방법에 대해 알아봅니다
* [**모범 사례**](active-directory-passwords-best-practices.md) - 사용자의 조직에서 신속하게 배포하고 효과적으로 암호를 관리하는 방법에 대해 알아봅니다.
* [**정보 활용**](active-directory-passwords-get-insights.md) -우리의 통합된 보고 기능에 대해 알아봅니다
* [**FAQ**](active-directory-passwords-faq.md) -자주 묻는 질문에 답변합니다.
* [**문제해결**](active-directory-passwords-troubleshoot.md) -신속하게 서비스와의 문제를 해결하는 방법에 대해 알아봅니다.
* [**자세히 알아보기**](active-directory-passwords-learn-more.md) -서비스의 작동 원리 방식의 기술적 측면을 자세히 알아봅니다.



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

<!---HONumber=Nov15_HO4-->