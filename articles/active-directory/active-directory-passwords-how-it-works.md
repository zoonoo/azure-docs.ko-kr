---
title: "작동 방법: Azure AD 암호 관리 | Microsoft Docs"
description: "사용자가 암호를 등록, 다시 설정 및 변경하는 위치와 관리자가 온-프레미스 Active Directory 암호의 관리를 구성, 보고 및 사용하도록 설정하는 위치를 포함하여 Azure AD 암호 관리의 다양한 구성 요소에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: b9f7c86415a2606cb969427a362b4d2feef183fb
ms.lasthandoff: 04/13/2017


---
# <a name="how-password-management-works-in-azure-active-directory"></a>Azure Active Directory에서 암호 관리의 작동 원리
> [!IMPORTANT]
> **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md#reset-my-password).

Azure AD(Azure Active Directory)의 암호 관리는 다음과 같은 논리 구성 요소로 구성됩니다.

* **암호 관리 구성 포털** – 관리자가 [Azure Portal](https://manage.windowsazure.com)에서 해당 디렉터리의 **구성** 탭으로 이동하여 자신의 테넌트에서 암호를 관리하는 방법을 다양하게 제어할 수 있습니다.
* **사용자 등록 포털** – 사용자가 이 웹 포털을 통해 암호 다시 설정을 자동 등록할 수 있습니다.
* **사용자 암호 다시 설정 포털** – 사용자가 관리자 제어 암호 다시 설정 정책에 따라 다양한 챌린지를 사용하여 자신의 암호를 다시 설정할 수 있습니다.
* **사용자 암호 변경 포털** – 사용자가 이 웹 포털을 통해 이전 암호를 입력하고 새 암호를 선택하여 언제든지 자신의 암호를 변경할 수 있습니다.
* **암호 관리 보고서** – 관리자가 [Azure Portal](https://manage.windowsazure.com)에서 해당 디렉터리의 **보고서** 탭에 있는 **활동 보고서** 섹션으로 이동하여 자신의 테넌트에서 암호 다시 설정 및 등록 활동을 살펴보고 분석할 수 있습니다.
* **Azure AD Connect의 비밀번호 쓰기 저장 구성 요소** – 관리자가 Azure AD Connect를 설치할 때 선택적으로 비밀번호 쓰기 저장 기능을 사용하도록 설정하여 클라우드에서 페더레이션되거나 암호가 동기화된 사용자의 암호를 관리할 수 있습니다.

## <a name="password-management-configuration-portal"></a>암호 관리 구성 포털
특정 디렉터리의 **구성** 탭에 있는 **사용자 암호 다시 설정 정책** 섹션으로 이동하여 [Azure Portal](https://manage.windowsazure.com)에서 해당 디렉터리에 대한 암호 관리 정책을 구성할 수 있습니다. 이 구성 페이지에서 다음을 포함하여 조직이 암호를 관리하는 방법의 다양한 측면을 제어할 수 있습니다.

* 디렉터리의 모든 사용자에 대해 암호 다시 설정 사용 여부 설정
* 사용자가 암호를 다시 설정하기 위해 통과해야 하는 챌린지 횟수(1회 또는 2회) 설정
* 아래 선택 항목 중에서 조직의 사용자에 대해 사용하려는 특정 챌린지 유형 설정
  * 휴대폰(문자 또는 음성 통화를 통한 확인 코드)
  * 사무실 전화(음성 통화)
  * 대체 전자 메일(전자 메일을 통한 확인 코드)
  * 보안 질문(지식 기반 인증)
* 보안 질문 인증 방법을 사용하기 위해 사용자가 등록해야 하는 질문의 수 설정(보안 질문을 사용하도록 설정된 경우에만 표시됨)
* 보안 질문 인증 방법을 사용하기 위해 사용자가 다시 설정하는 중에 제공해야 하는 질문의 수 설정(보안 질문을 사용하도록 설정된 경우에만 표시됨)
* 사용자가 암호 다시 설정을 등록할 때 선택하여 사용할 수 있는 미리 만든 지역화된 보안 질문 사용(보안 질문을 사용하도록 설정된 경우에만 표시됨)
* 사용자가 암호 다시 설정을 등록할 때 선택하여 사용할 수 있는 사용자 지정 보안 질문 정의(보안 질문을 사용하도록 설정된 경우에만 표시됨)
* 사용자가 응용 프로그램 [액세스 패널](http://myapps.microsoft.com)로 이동할 때 암호 다시 설정을 등록하도록 요구
* 구성 가능한 일수가 경과하면 사용자가 이전에 등록한 데이터를 다시 확인하도록 요구(강제 등록을 사용하도록 설정된 경우에만 표시됨)
* 사용자가 암호를 다시 설정하는 데 문제가 있는 경우 사용자에게 표시되는 사용자 지정 기술 지원팀 전자 메일 또는 URL 제공
* 비밀번호 쓰기 저장 기능 사용 여부 설정(Azure AD Connect를 사용하여 비밀번호 쓰기 저장을 배포한 경우)
* 비밀번호 쓰기 저장 에이전트의 상태 보기(Azure AD Connect를 사용하여 비밀번호 쓰기 저장을 배포한 경우)
* 사용자의 암호가 다시 설정되었을 때 해당 사용자에게 전자 메일 알림 사용 설정([Azure Portal](https://manage.windowsazure.com)의 **알림** 섹션에 있음)
* 다른 관리자가 관리자의 암호를 다시 설정한 경우 해당 관리자에게 전자 메일 알림 사용 설정([Azure Portal](https://manage.windowsazure.com)의 **알림** 섹션에 있음)
* 테넌트 브랜딩 사용자 지정 기능을 사용하여 조직의 로고와 이름으로 사용자 암호 다시 설정 포털 및 암호 다시 설정 전자 메일 브랜딩([Azure Portal](https://manage.windowsazure.com)의 **알림** 섹션에 있음)

조직에서 암호 관리를 구성하는 방법에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.

## <a name="user-registration-portal"></a>사용자 등록 포털
사용자가 암호 다시 설정을 사용하려면 먼저 클라우드 사용자 계정을 올바른 인증 데이터로 업데이트하여 관리자가 정의한 적절한 수의 암호 다시 설정 챌린지 횟수를 통과할 수 있도록 해야 합니다. 또한 Azure/Office 웹 포털, DirSync/Azure AD Connect 또는 Windows PowerShell을 사용하여 해당 사용자를 대신하여 이 인증 정보를 정의할 수도 있습니다.

그러나 사용자의 고유 데이터를 등록하려는 경우 사용자가 이동할 수 있는 웹 페이지를 제공하여 해당 정보를 채울 수 있도록 합니다. 이 페이지에서는 사용자가 조직에서 사용하도록 설정된 암호 다시 설정 정책에 따라 인증 정보를 지정할 수 있습니다. 이 데이터가 확인된 후에는 클라우드 사용자 계정에 저장되어 나중에 계정 복구에 사용할 수 있습니다.

등록 포털의 모양은 다음과 같습니다.

  ![][001]

자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md) 및 [모범 사례: Azure AD 암호 관리](active-directory-passwords-best-practices.md)를 참조하세요.

## <a name="user-password-reset-portal"></a>사용자 암호 다시 설정 포털
셀프 서비스 암호 다시 설정을 사용하도록 설정했고, 조직의 셀프 서비스 암호 다시 설정 정책을 설정했고, 사용자가 디렉터리에 적절한 연락처 데이터를 가지고 있는 경우, 조직의 사용자는 로그인할 때 회사 또는 학교 계정을 사용하는 웹 페이지(예: [portal.microsoftonline.com](https://portal.microsoftonline.com))에서 자신의 암호를 자동으로 다시 설정할 수 있습니다. 이러한 페이지에는 **계정에 액세스할 수 없습니까?** 링크가 표시됩니다.

  ![][002]

이 링크를 클릭하면 셀프 서비스 암호 다시 설정 포털이 열립니다.

  ![][003]

사용자가 자신의 암호를 다시 설정할 수 있는 방법에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.

## <a name="user-password-change-portal"></a>사용자 암호 변경 포털
사용자가 자신의 암호를 변경하려면 언제든지 암호 변경 포털을 사용하면 됩니다. 사용자는 [액세스 패널] 프로필 페이지를 사용하거나 Office 365 응용 프로그램 내에서 **암호 변경** 링크를 클릭하여 암호 변경 포털에 액세스할 수 있습니다. 자신의 암호가 만료된 경우 사용자는 로그인할 때 암호를 자동으로 변경하도록 요청받습니다.

  ![][004]

두 경우 모두 비밀번호 쓰기 저장을 사용하도록 설정되어 있고 사용자가 페더레이션되었거나 암호가 동기화된 경우 변경된 암호는 온-프레미스 Active Directory에 다시 기록됩니다.

암호 변경 포털의 모양은 다음과 같습니다.

  ![][005]

사용자가 자신의 온-프레미스 Active Directory 암호를 변경할 수 있는 방법에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.

## <a name="password-management-reports"></a>암호 관리 보고서
**보고서** 탭으로 이동하여 **활동 로그** 섹션 아래를 보면 두 가지 암호 관리 보고서, 즉 **암호 다시 설정 활동** 및 **암호 다시 설정 등록 활동** 보고서가 표시되어 있습니다. 이 두 가지 보고서를 사용하여 조직의 사용자가 암호 다시 설정을 등록하고 사용할 수 있는 보기를 가져올 수 있습니다.

[Azure Portal](https://manage.windowsazure.com)에서 보고서는 다음과 같이 표시됩니다.

  ![][006]

자세한 내용은 [정보 얻기: Azure AD 암호 관리 보고서](active-directory-passwords-get-insights.md)를 참조하세요.

## <a name="password-writeback-component-of-azure-ad-connect"></a>Azure AD Connect의 암호 쓰기 저장 구성 요소
조직 내 사용자의 암호를 온-프레미스 환경에서 생성하면(페더레이션 또는 암호 동기화 중 하나를 통해) Azure AD Connect의 최신 버전을 설치하여 클라우드에서 직접 이러한 암호를 업데이트할 수 있습니다. 즉 사용자가 Azure AD 암호를 잊었거나 수정하려고 할 때 웹에서 바로 수행할 수 있습니다. Azure AD Connect 설치 마법사에서 암호 쓰기 저장을 찾을 수 있는 위치는 다음과 같습니다.

  ![][007]

Azure AD Connect에 대한 자세한 내용은 [시작: Azure AD Connect](active-directory-aadconnect.md)를 참조하세요. 비밀번호 쓰기 저장에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 Azure AD 암호 다시 설정 페이지를 참조하세요.

* **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 다시 설정하는 방법](active-directory-passwords-update-your-own-password.md#reset-my-password)을 알아봅니다.
* [**시작**](active-directory-passwords-getting-started.md) - 사용자가 클라우드 또는 온-프레미스 암호를 다시 설정하고 변경할 수 있는 방법에 대해 알아봅니다.
* [**사용자 지정**](active-directory-passwords-customize.md) - 조직의 요구에 맞게 서비스의 모양, 느낌 및 동작을 사용자 지정하는 방법에 대해 알아봅니다.
* [**모범 사례**](active-directory-passwords-best-practices.md) - 조직에서 암호를 신속하게 배포하고 효과적으로 관리하는 방법에 대해 알아봅니다.
* [**정보 얻기**](active-directory-passwords-get-insights.md) - 통합된 보고 기능에 대해 알아봅니다.
* [**FAQ**](active-directory-passwords-faq.md) - 자주 묻는 질문에 대한 답변을 얻습니다.
* [**문제해결**](active-directory-passwords-troubleshoot.md) - 서비스 관련 문제를 신속하게 해결하는 방법에 대해 알아봅니다.
* [**자세한 정보**](active-directory-passwords-learn-more.md) - 서비스의 작동 방법에 대한 기술적 측면을 자세히 살펴봅니다.

[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

