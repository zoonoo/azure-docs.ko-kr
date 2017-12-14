---
title: "사용자 지정: Azure AD SSPR | Microsoft Docs"
description: "Azure AD 셀프 서비스 암호 재설정의 사용자 지정 옵션"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
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
ms.openlocfilehash: 36fc674ffcb8167a918f928d8821707d81198423
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Azure AD의 셀프 서비스 암호 재설정 기능 사용자 지정

Azure Active Directory(Azure AD)에서 셀프 서비스 암호 재설정(SSPR)을 배포하려는 IT 전문가는 사용자들의 필요에 맞게 환경을 사용자 지정할 수 있습니다.

## <a name="customize-the-contact-your-administrator-link"></a>“관리자에게 문의” 링크 사용자 지정

SSPR을 사용하지 않는 경우에도 사용자는 암호 재설정 포털에서 “관리자에게 문의” 링크를 사용할 수 있습니다. 사용자가 이 링크를 선택하면 다음 중 하나가 수행됩니다.
   * 관리자에게 사용자 암호 변경 지원을 요청하는 메일이 발송됩니다. 
   * 관리자가 지정한 지원 URL이 사용자에게 전송됩니다. 

이 링크는 사용자가 이미 지원 질문에 사용하고 있는 메일 주소 또는 웹 사이트로 설정하는 것이 좋습니다.

![연락처][Contact]

문의 메일은 다음과 같은 순서로 받는 사람에게 전송됩니다.

1. **암호 관리자** 역할이 할당된 경우 이 역할을 갖는 관리자에게 전송됩니다.
2. 암호 관리자가 할당되지 않은 경우 **사용자 관리자** 역할을 갖는 관리자에게 전송됩니다.
3. 암호 관리자와 사용자 관리자가 할당되지 않은 경우 **전역 관리자**에게 전송됩니다.

어떠한 경우에도 최대 100명의 받는 사람에게 알림이 제공됩니다.

다양한 관리자 역할과 각 역할을 할당하는 방법에 대해 자세히 알아보려면 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles-azure-portal.md)을 참조하세요.

### <a name="disable-contact-your-administrator-emails"></a>“관리자에게 문의” 메일 사용 안 함

관리자에게 암호 재설정 요청을 알리지 않으려는 경우 다음 구성을 사용할 수 있습니다.

* 모든 최종 사용자에게 셀프 서비스 암호 재설정 사용 이 옵션은 **암호 재설정** > **속성** 아래에 있습니다.
  
  사용자가 자신의 암호를 재설정할 수 없도록 하려면 빈 그룹에 대한 액세스 범위를 지정하면 됩니다. *이 옵션은 권장되지 않습니다.*
* 사용자가 도움을 받을 수 있는 웹 URL 또는 mailto: 주소를 제공하는 고객 지원 센터 링크를 사용자 지정합니다. 이 옵션은 **암호 재설정** > **사용자 지정** >  **사용자 지정 기술 지원 팀 메일 또는 URL** 아래에 있습니다.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>SSPR을 위해 AD FS 로그인 페이지 사용자 지정

AD FS(Active Directory Federation Services) 관리자는 [로그인 페이지 설명 추가](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) 문서의 지침에 따라 로그인 페이지에 링크를 추가할 수 있습니다.

AD FS 로그인 페이지에 링크를 추가하려면 AD FS 서버에서 아래 명령을 사용합니다. 이렇게 하면 사용자가 이 페이지에서 SSPR 워크플로우를 시작할 수 있습니다.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>로그인 페이지와 액세스 패널의 디자인 사용자 지정

로그인 페이지를 사용자 지정할 수 있습니다. 회사 브랜드에 맞는 이미지와 로고를 추가할 수 있습니다.

이미지는 다음과 같은 경우에 표시됩니다.

* 사용자가 사용자 이름을 입력한 뒤
* 사용자가 다음과 같은 방식으로 사용자 지정된 URL에 액세스하는 경우
    * 암호 재설정 페이지로 *whr* 매개 변수를 전달하는 경우(예: “https://login.microsoftonline.com/?whr=contoso.com”)
    * 암호 재설정 페이지로 *username* 매개 변수를 전달하는 경우(예: “https://login.microsoftonline.com/?username=admin@contoso.com”)

### <a name="graphics-details"></a>그래픽 정보

아래 설정을 사용하여 로그인 페이지의 시각적 특성을 변경합니다. **Azure Active Directory** > **회사 브랜딩** > **회사 브랜딩 편집**으로 이동합니다.

* 로그인 페이지 이미지는 1420 x 1200픽셀의 최대 500KB 크기의 .png 또는 .jpg 파일이어야 합니다. 최상의 결과를 위해 약 200KB가 가장 좋습니다.
* 로그인 페이지 배경색은 대기 시간이 긴 연결에서 사용되며, RGB 16진수 형식이어야 합니다.
* 배너 이미지는 60 x 280픽셀의 최대 10KB 크기의 .png 또는 .jpg 파일이어야 합니다.
* 정사각형 로고(일반 테마와 어두운 테마)는 (크기 조정 가능한) 240 x 240픽셀의 최대 10KB 크기의 .png 또는 .jpg 파일이어야 합니다.

### <a name="sign-in-text-options"></a>로그인 텍스트 옵션

아래 설정을 사용하여 로그인 페이지에 조직과 관련 있는 텍스트를 추가합니다. **Azure Active Directory** > **회사 브랜딩** > **회사 브랜딩 편집**으로 이동합니다.

* **사용자 이름 힌트**: *someone@example.com*이라는 예제 텍스트를 사용자에게 의미 있는 텍스트로 바꾸어 보세요. 단, 내부 사용자와 외부 사용자를 모두 지원하는 경우에는 기본값을 그대로 두는 것이 좋습니다.
* **로그인 페이지 텍스트**: 최대 256자까지 작성할 수 있습니다. 이 텍스트는 사용자가 온라인에서 로그인하는 모든 곳에 그리고 Windows 10의 Azure AD Workplace Join 환경에 표시됩니다. 사용 약관, 지침, 사용자를 위한 팁에 이 텍스트를 사용합니다. 

   >[!IMPORTANT]
   >로그인 페이지는 누구나 볼 수 있으므로 여기에 중요한 정보를 포함하지 않도록 합니다.
   >

### <a name="the-keep-me-signed-in-disabled-setting"></a>“로그인 사용 안 함” 설정

**로그인 사용 안 함** 옵션을 사용하면 사용자가 브라우저 창을 닫고 다시 열 때 로그인 상태가 유지됩니다. 이 옵션은 세션 수명에 영향을 주지 않습니다. **Azure Active Directory** > **회사 브랜딩** > **회사 브랜딩 편집**으로 이동합니다.

사용자가 이 확인란을 선택할 수 있는지 여부에 따라 SharePoint Online 및 Office 2010의 일부 기능이 영향을 받습니다. 이 옵션을 숨기면 사용자에게 로그인 프롬프트가 과도하게 많이 표시될 수 있습니다.

### <a name="directory-name"></a>디렉터리 이름

**Azure Active Directory** > **속성**에서 디렉터리 이름 속성을 변경할 수 있습니다. 여기에서 포털과 자동 커뮤니케이션에 사용할 조직 이름을 입력할 수 있습니다. 이 옵션은 다음과 같은 자동 메일에 표시됩니다.

* 메일의 식별 이름(예: “CONTOSO 데모를 대신하는 Microsoft”)
* 메일의 제목 줄(예: “CONTOSO 데모 계정 메일 확인 코드”)

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
