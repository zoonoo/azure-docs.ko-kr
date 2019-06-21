---
title: 암호 SSO에 대해 구성 된 Azure AD 갤러리 응용 프로그램에 로그인 하는 문제 | Microsoft Docs
description: 암호 single sign-on 용으로 구성 된 Azure AD 갤러리 응용 프로그램을 사용 하 여 문제를 해결 하는 방법입니다.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190317"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>SSO에 대해 구성 된 Azure AD 갤러리 응용 프로그램을 사용 하 여 로그인 문제

액세스 패널은 웹 기반 포털입니다. 사용자를 게 Azure Active Directory (Azure AD) 직장 또는 학교 계정에 대 한 권한 있는 클라우드 기반 앱에 액세스할 수 있습니다. 셀프 서비스 그룹 및 앱 관리 기능 액세스 패널을 통해 Azure AD 버전을 권한이 있는 사용자를 이용할 수 있습니다.

액세스 패널은 Azure portal에서 분리 합니다. 사용자가 액세스 패널을 사용 하려면 Azure 구독이 필요 하지 않습니다.

암호 기반에서 single sign-on (SSO) 액세스 패널에서 사용 하려면 브라우저에서 액세스 패널 확장을 설치 합니다. 암호 기반 SSO 용으로 구성 된 앱을 선택 하면 확장을 자동으로 다운로드 합니다.

## <a name="browser-requirements-for-access-panel"></a>액세스 패널에 대 한 브라우저 요구 사항

액세스 패널에는 JavaScript를 지 원하는 브라우저가 필요 하 고 CSS를 활성화 합니다.

다음 브라우저는 암호 기반 SSO를 지원합니다.

- Internet Explorer 8, 9, 10 및 11 Windows 7 이상

- Windows 7 또는 나중에 또는 mac Os X 이상 chrome

- Firefox 26.0 이상 Windows XP sp2 또는 이후 또는 Mac OS X 10.6 이상

>[!NOTE]
>Windows 10의 Microsoft Edge에 대 한 암호 기반 SSO 확장을 사용할 때 Microsoft edge 브라우저 확장 된 추가 지원 합니다.

## <a name="install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장 설치

다음 단계를 수행하세요.

1. 오픈 [액세스 패널](https://myapps.microsoft.com) 지원 되는 브라우저 및 Azure AD의 사용자로 로그인 합니다.

2. 액세스 패널에서 암호-SSO가 설정 된 앱을 선택 합니다.

3. 메시지가 나타나면 선택 **지금 설치**합니다.

4. 브라우저에 따라 다운로드 링크로 이동 합니다. 선택 **추가** 브라우저 확장을 설치 합니다.

5. 메시지가 표시 되는 경우 선택 **을 사용 하도록 설정** 또는 **허용**합니다.

6. 설치 후 브라우저를 다시 시작 합니다.

7.  액세스 패널에 로그인 하 고 암호-SSO가 설정 된 앱을 시작할 수 있는지 확인 합니다.

이러한 링크를 통해 Chrome 및 Firefox에 대 한 확장을 직접 다운로드할 수 있습니다.

-   [Chrome 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Internet Explorer에 대한 그룹 정책 설정

원격 사용자의 컴퓨터에 Internet Explorer에 대 한 액세스 패널 확장을 설치할 수 있도록 그룹 정책을 설정할 수 있습니다.

다음은 필수 구성 요소입니다.

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) , 설정 및 사용자의 컴퓨터를 도메인에 가입 되어야 합니다.

-   "설정" 편집"권한이 그룹 정책 개체 (GPO)를 편집 해야 합니다. 기본적으로 다음 보안 그룹의 구성원에게는 이 권한이 있습니다: 도메인 관리자, 엔터프라이즈 관리자 및 그룹 정책 작성 소유자. [자세히 알아보기](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

그룹 정책을 구성 하 고 사용자에 게 배포, 참조 [그룹 정책을 사용 하 여 Internet Explorer 용 액세스 패널 확장을 배포 하는 방법을](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)합니다.

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Internet Explorer에서 액세스 패널 문제 해결

진단 도구 및 확장을 구성 하는 지침에 액세스 하려면 참조 [Internet Explorer 용 액세스 패널 확장 문제 해결](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)합니다.

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Azure AD 갤러리 응용 프로그램에 대 한 암호 SSO를 구성 합니다.

Azure AD 갤러리에서 앱을 구성 하려면 다음이을 수행 해야 합니다.

-   Azure AD 갤러리에서 앱 추가
-   [암호 single sign-on에 대 한 앱 구성](#configure-the-app-for-password-sso)
-   [앱에 사용자 할당](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 앱 추가

다음 단계를 수행하세요.

1. 엽니다는 [Azure portal](https://portal.azure.com) 전역 관리자 또는 공동 관리자 로그인

2. 선택 **모든 서비스** Azure AD 확장을 열고 왼쪽의 탐색 창 맨 위에 있는 합니다.

3. 형식 **Azure Active Directory** 에서 필터 검색 상자를 선택 합니다 **Azure Active Directory**합니다.

4. 선택 **엔터프라이즈 응용 프로그램** Azure AD 탐색 창에서.

5. 선택 **추가** 의 오른쪽 위 모서리에는 **Enterprise 응용 프로그램** 창입니다.

6. 에 **갤러리에서 추가** 섹션에서 앱의 이름을 입력 합니다 합니다 **이름을 입력** 상자입니다.

7. Sso를 구성 하려는 앱을 선택 합니다.

8. *선택 사항:* 앱에 추가 하기 전에에서 해당 이름을 변경할 수 있습니다 합니다 **이름을** 상자입니다.

9. 클릭 **추가** 앱을 추가 합니다.

   잠시 후 앱의 구성 창을 볼 수 있습니다.

### <a name="configure-the-app-for-password-sso"></a>암호 SSO에 대 한 앱 구성

다음 단계를 수행하세요.

1. 엽니다는 [Azure portal](https://portal.azure.com/) 전역 관리자 또는 공동 관리자 로그인

2. 선택 **모든 서비스** Azure AD 확장을 열고 왼쪽의 탐색 창 맨 위에 있는 합니다.

3. 형식 **Azure Active Directory** 에서 필터 검색 상자를 선택 합니다 **Azure Active Directory**합니다.

4. 선택 **엔터프라이즈 응용 프로그램** Azure AD 탐색 창에서.

5. 선택 **모든 응용 프로그램** 앱 목록을 볼 수 있습니다.

   > [!NOTE]
   > 사용 하 여 원하는 앱이 보이지 않으면 합니다 **필터** 맨 위에 있는 컨트롤을 **모든 응용 프로그램 목록**. 설정 된 **표시** "모든 응용 프로그램입니다." 옵션

6. Sso를 구성 하려는 앱을 선택 합니다.

7. 앱 로드 되 면 선택 **Single sign on** 앱의 왼쪽 창에서.

8. 선택 **암호 기반 로그온** 모드입니다.

9. 앱에 사용자를 할당 합니다.

10. 또한 사용자에 대 한 자격 증명을 제공할 수 있습니다. (그렇지 않은 경우 사용자에 게 수 메시지가 앱 시작 시 자격 증명을 입력 합니다.) 이 작업을 수행 하려면 사용자의 행을 선택 합니다. 선택한 **자격 증명 업데이트** 하 고 해당 사용자 이름과 암호를 입력 합니다.

### <a name="assign-users-to-the-app"></a>앱에 사용자 할당

앱에 사용자에 직접 할당 하려면 다음이 단계를 수행 합니다.

1. 엽니다는 [Azure portal](https://portal.azure.com/) 전역 관리자로 로그인 합니다.

2. 선택 **모든 서비스** Azure AD 확장을 열려면 왼쪽 탐색 창에서.

3. 형식 **Azure Active Directory** 에서 필터 검색 상자를 선택 합니다 **Azure Active Directory**합니다.

4. 선택 **엔터프라이즈 응용 프로그램** Azure AD 탐색 창에서.

5. 선택 **모든 응용 프로그램** 응용 프로그램의 목록을 볼 수 있습니다.

   > [!NOTE]
   > 사용 하 여 원하는 앱이 보이지 않으면 합니다 **필터** 맨 위에 있는 컨트롤을 **모든 응용 프로그램 목록**. 설정 된 **표시** "모든 응용 프로그램입니다." 옵션

6. 목록에서 사용자를 할당 하려는 앱을 선택 합니다.

7. 응용 프로그램이 로드 되 면 선택 **사용자 및 그룹** 앱의 왼쪽 탐색 창에서.

8. 선택 **추가** 맨 위에 있는 합니다 **사용자 및 그룹** 열려면 목록 합니다 **할당 추가** 창입니다.

9. 선택 **사용자 및 그룹** 에 **할당 추가** 창입니다.

10. 에 **이름 또는 전자 메일 주소로 검색** 상자, 전체 이름을 입력 하거나 할당 하려는 사용자의 전자 메일 주소입니다.

11. 사용자 목록에 올려 놓습니다. 사용자의 프로필 사진이 나 해당 사용자를 추가 하려면 로고 옆의 확인란을 선택 합니다 **선택한** 목록입니다.

12. *선택 사항:* 다른 사용자를 추가 하려면에 다른 이름이 나 전자 메일 주소를 입력 합니다 **이름 또는 전자 메일 주소로 검색** 상자를 선택한 다음 해당 사용자를 추가 하려면 확인란 합니다 **선택한** 목록입니다.

13. 사용자 선택이 끝나면 클릭 **선택** 사용자 및 앱에 할당 된 그룹의 목록에 추가 합니다.

14. *선택 사항:* 클릭 **역할 선택** 에 **할당 추가** 창에 선택한 사용자에 게 할당할 역할을 선택 합니다.

15. 선택 **할당** 선택한 사용자에 게 앱을 할당 합니다.

    잠시 후 사용자가 액세스 패널에서 해당 앱에 액세스할 수 있습니다.

## <a name="request-support"></a>지원 요청 
SSO를 설정 하 고 사용자를 할당 하는 경우 오류 메시지를 표시 하는 경우 지원 티켓을 엽니다. 다음 정보를 가능한 많이 포함:

-   상관 관계 오류 ID
-   UPN(사용자 전자 메일 주소)
-   TenantID
-   브라우저 종류
-   표준 시간대와 오류가 발생 한 시간/시간 프레임
-   Fiddler 추적

## <a name="next-steps"></a>다음 단계
[애플리케이션 프록시를 사용하여 앱에 Single Sign-On 제공](application-proxy-configure-single-sign-on-with-kcd.md)
