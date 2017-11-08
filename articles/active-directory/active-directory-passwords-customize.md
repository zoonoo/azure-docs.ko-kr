---
title: "사용자 지정: Azure AD SSPR | Microsoft Docs"
description: "Azure AD 셀프 서비스 암호 재설정에 대한 사용자 지정 옵션"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 030fb1d87547a4fc78d54a855bca961202f28837
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>셀프 서비스 암호 재설정을 위한 Azure AD 기능 사용자 지정

셀프 서비스 암호 재설정을 배포하려고 하는 IT 전문가는 사용자에게 맞게 환경을 사용자 지정할 수 있습니다.

## <a name="customize-the-contact-your-administrator-link"></a>관리자에게 문의 링크 사용자 지정

SSPR을 사용하지 않는 경우라도 사용자는 암호 재설정 포털에서 "관리자에게 문의" 링크를 사용합니다.  이 링크를 클릭하면 사용자의 암호를 변경하는 데 도움을 요청하기 위해 관리자에게 전자 메일을 보내거나 지정한 URL로 사용자를 보냅니다. 사용자가 지원을 위해 사용하는 이메일 주소 또는 웹 사이트와 같은 값으로 설정하는 것이 좋습니다.

![연락처][Contact]

이 전자 메일은 다음 순서로 다음과 같은 받는 사람에게 전송됩니다.

1. **암호 관리자** 역할이 할당된 경우 이 역할을 가진 관리자에게 알림이 제공됩니다.
2. 암호 관리자가 할당되지 않은 경우 **사용자 관리자** 역할을 가진 관리자에게 알림이 제공됩니다.
3. 이전의 역할이 할당되지 않은 경우 **전역 관리자**가 표시됩니다

어떠한 경우에도 최대 100명의 받는 사람에게 알림이 제공됩니다.

다른 관리자 역할 및 할당하는 방법에 대해 자세히 알아보려면 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles.md) 문서를 참조하세요.

### <a name="disable-contact-your-administrator-emails"></a>관리자에게 문의 전자 메일 사용 안 함

조직에서 관리자에게 암호 재설정 요청을 알리지 않으려는 경우 다음 구성을 사용할 수 있습니다.

* 모든 최종 사용자에게 셀프 서비스 암호 재설정 사용 이 옵션은 **암호 재설정 > 속성** 아래에 있습니다.
    * 사용자가 자신의 암호를 재설정할 수 없게 하려는 경우 빈 그룹에 대한 액세스 범위를 지정하면 됩니다. **이 옵션은 사용하지 않는 것이 좋습니다**.
* 사용자가 도움을 받을 수 있는 웹 URL 또는 mailto: 주소를 제공하는 고객 지원 센터 링크를 사용자 지정합니다. 이 옵션은 **암호 재설정 > 사용자 지정 > 사용자 지정 기술 지원 팀 메일 또는 URL** 아래에 있습니다.

## <a name="customize-adfs-sign-in-page-for-sspr"></a>SSPR에 대한 ADFS 로그인 페이지 사용자 지정

ADFS 관리자는 [Add sign-in page description](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)(로그인 페이지 설정 추가) 문서의 지침에 따라 로그인 페이지 링크를 추가할 수 있습니다.

ADFS 서버에서 다음에 나오는 명령을 사용하면 사용자가 셀프 서비스 암호 재설정 워크플로를 직접 입력할 수 있는 ADFS 로그인 페이지의 링크가 추가됩니다.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>로그인 및 액세스 패널 모양과 느낌 사용자 지정

사용자가 로그인 페이지에 액세스할 때 로그인 페이지 이미지와 함께 표시되는 로고를 회사 브랜딩에 맞게 사용자 지정할 수 있습니다.

다음과 같은 경우에는 이러한 그래픽이 표시됩니다.

* 사용자가 사용자 이름을 입력한 후
* 사용자가 사용자 지정된 URL에 액세스
    * "Whr" 매개 변수를 "https://login.microsoftonline.com/?whr=contoso.com"과 같은 암호 재설정 페이지에 전달하는 방법
    * "username" 매개 변수를 "https://login.microsoftonline.com/?username=admin@contoso.com"과 같은 암호 재설정 페이지에 전달하는 방법

### <a name="graphics-details"></a>그래픽 정보

다음 설정을 사용하면 로그인 페이지의 시각적 특성을 변경할 수 있습니다. 이 설정은 **Azure Active Directory**, **회사 브랜딩**, **회사 브랜딩 편집**에서 확인할 수 있습니다.

* 로그인 페이지 이미지는 500KB 이하의 1420x1200픽셀인 JPG 또는 PNG 파일이어야 합니다. 최상의 결과를 위해 약 200KB 정도가 좋습니다.
* 로그인 페이지 배경색은 대기 시간이 긴 연결에서 작업하는 경우에 사용되고 RGB 16진수 형식이어야 합니다.
* 배너 이미지는 10KB 이하의 60x280픽셀인 JPG 또는 PNG 파일이어야 합니다.
* 정사각형 로고(일반 및 어두운 테마)는 10KB 이하의 240x240(크기 조정 가능)픽셀인 JPG 또는 PNG 파일입니다.

### <a name="sign-in-text-options"></a>로그인 텍스트 옵션

다음 설정을 사용하면 텍스트를 조직에 관련된 로그인 페이지에 추가할 수 있습니다. 이러한 설정은 **Azure Active Directory**, **회사 브랜딩**, **회사 브랜딩 편집**에서 찾을 수 있습니다.

* **사용자 이름 힌트**는 someone@example.com 텍스트의 예제를 사용자에게 보다 적합한 형태로 바꿉니다. 이 기능은 내부 및 외부 사용자를 지원할 때 기본으로 설정하는 것이 좋습니다.
* **로그인 페이지 텍스트**는 최대 256자입니다. 이 텍스트는 사용자가 온라인으로 로그인하는 경우 및 Windows 10의 Azure AD 조인 경험에서 표시됩니다. 사용 약관, 지침 및 사용자에 대한 팁에서 이 텍스트를 사용합니다. **누구든지 로그인 페이지를 볼 수 있으므로 여기에서 중요 정보를 제공하지 않도록 합니다.**

### <a name="keep-me-signed-in-disabled"></a>로그인 사용 안 함

“로그인 사용 안 함” 옵션을 사용하면 사용자가 해당 브라우저 창을 닫고 다시 열 때 로그인 상태를 유지할 수 있습니다. 이 옵션은 세션 수명에 영향을 주지 않습니다. 이 설정은 **Azure Active Directory > 회사 브랜딩 > 회사 브랜딩 편집** 아래에 있습니다.

SharePoint Online과 Office 2010의 일부 기능은 이 확인란을 선택할 수 있는 사용자에 대한 종속성이 있습니다. 이 옵션을 숨기면 사용자에게 추가 및 예기치 않은 로그인 프롬프트가 표시될 수 있습니다.

### <a name="directory-name"></a>디렉터리 이름

**Azure Active Directory > 속성** 아래에서 이름 특성을 변경하여 포털 및 자동화된 통신에서 친숙한 조직 이름을 표시할 수 있습니다. 이 옵션은 계속되는 양식에서 자동화된 전자 메일의 형태로 표시됩니다.

* "CONTOSO 데모를 대신하는 Microsoft" 전자 메일의 친숙한 이름
* "CONTOSO 데모 계정 전자 메일 확인 코드" 전자 메일의 제목 줄

## <a name="next-steps"></a>다음 단계

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](active-directory-passwords-best-practices.md)
* [암호 재설정 또는 변경](active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)
* [라이선스 관련 질문이 있습니까?](active-directory-passwords-licensing.md)
* [SSPR에서 사용하는 데이터는 무엇이며, 사용자에 대해 어떤 데이터를 채워야 합니까?](active-directory-passwords-data.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](active-directory-passwords-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](active-directory-passwords-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](active-directory-passwords-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](active-directory-passwords-how-it-works.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "암호 전자 메일 예제 재설정에 대한 도움이 필요하면 관리자에게 문의하십시오."