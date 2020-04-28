---
title: SSO에 대해 구성 된 Azure AD 갤러리 앱에 로그인 하는 데 문제가 있습니다. | Microsoft Docs
description: 암호 Single Sign-On 대해 구성 된 Azure AD 갤러리 응용 프로그램의 문제를 해결 하는 방법입니다.
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
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381302"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>SSO에 대해 구성 된 Azure AD 갤러리 앱의 로그인 문제

액세스 패널은 웹 기반 포털입니다. 이를 통해 Azure AD (Azure Active Directory) 회사 또는 학교 계정이 있는 사용자가 권한이 있는 클라우드 기반 앱에 액세스할 수 있습니다. Azure AD 버전이 있는 사용자는 액세스 패널을 통해 셀프 서비스 그룹 및 앱 관리 기능을 사용할 수도 있습니다.

액세스 패널은 Azure Portal와 별개입니다. 사용자는 액세스 패널을 사용 하기 위해 Azure 구독이 필요 하지 않습니다.

액세스 패널에서 SSO (암호 기반 Single Sign-On)를 사용 하려면 브라우저에 액세스 패널 확장을 설치 해야 합니다. 암호 기반 SSO 용으로 구성 된 앱을 선택 하면 확장이 자동으로 다운로드 됩니다.

## <a name="browser-requirements-for-access-panel"></a>액세스 패널에 대 한 브라우저 요구 사항

액세스 패널에는 JavaScript를 지원 하 고 CSS를 사용 하도록 설정 된 브라우저가 필요 합니다.

다음 브라우저는 암호 기반 SSO를 지원 합니다.

- Windows 7 이상에서 Internet Explorer 8, 9, 10 및 11

- Windows 7 이상 또는 MacOS X 이상에 대 한 Chrome

- Windows XP SP2 이상 또는 Mac OS X 10.6 이상에서 Firefox 26.0 이상

>[!NOTE]
>Microsoft Edge에 브라우저 확장에 대 한 지원이 추가 되 면 Windows 10의 Microsoft Edge에 암호 기반 SSO 확장을 사용할 수 있게 됩니다.

## <a name="install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장 설치

다음 단계를 수행하세요.

1. 지원 되는 브라우저에서 [액세스 패널](https://myapps.microsoft.com) 을 열고 Azure AD에서 사용자로 로그인 합니다.

2. 액세스 패널에서 암호-SSO 지원 앱을 선택 합니다.

3. 메시지가 표시 되 면 **지금 설치**를 선택 합니다.

4. 브라우저에 따라 다운로드 링크로 이동 합니다. **추가** 를 선택 하 여 브라우저 확장을 설치 합니다.

5. 메시지가 표시 되 면 **사용** 또는 **허용**을 선택 합니다.

6. 설치 후 브라우저를 다시 시작 합니다.

7.  액세스 패널에 로그인 하 고 암호 SSO 지원 앱을 시작할 수 있는지 확인 합니다.

다음 링크를 통해 Chrome 및 Firefox에 대 한 확장을 직접 다운로드할 수도 있습니다.

-   [Chrome 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Internet Explorer에 대한 그룹 정책 설정

사용자의 컴퓨터에 Internet Explorer 용 액세스 패널 확장을 원격으로 설치할 수 있는 그룹 정책을 설정할 수 있습니다.

다음은 필수 구성 요소입니다.

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) 를 설정 해야 하 고 사용자의 컴퓨터가 도메인에 가입 되어 있어야 합니다.

-   GPO (그룹 정책 개체)를 편집할 수 있는 "설정 편집" 권한이 있습니다. 기본적으로 도메인 관리자, 엔터프라이즈 관리자 및 그룹 정책 작성자/소유자 보안 그룹의 멤버에게 이 권한이 있습니다. [자세히 알아보기](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

그룹 정책을 구성 하 고 사용자에 게 배포 하려면 [그룹 정책을 사용 하 여 Internet Explorer 용 액세스 패널 확장을 배포 하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)을 참조 하세요.

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Internet Explorer에서 액세스 패널 문제 해결

진단 도구 및 확장을 구성 하는 지침에 액세스 하려면 [Internet Explorer 용 액세스 패널 확장 문제 해결](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)을 참조 하세요.

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Azure AD 갤러리 앱에 대 한 암호 SSO 구성

Azure AD 갤러리에서 앱을 구성 하려면 다음 작업을 수행 해야 합니다.

-   Azure AD 갤러리에서 앱 추가
-   [암호 Single Sign-On 앱 구성](#configure-the-app-for-password-sso)
-   [앱에 사용자 할당](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 앱 추가

다음 단계를 수행하세요.

1. [Azure Portal](https://portal.azure.com) 을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.

2. 왼쪽의 탐색 창 맨 위에서 **모든 서비스** 를 선택 하 여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력 하 고 **Azure Active Directory**를 선택 합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. **엔터프라이즈 응용 프로그램** 창의 오른쪽 위 모퉁이에서 **추가** 를 선택 합니다.

6. **갤러리에서 추가** 섹션의 **이름 입력** 상자에 앱의 이름을 입력 합니다.

7. SSO에 대해 구성 하려는 앱을 선택 합니다.

8. *선택 사항:* 앱을 추가 하기 전에 **이름** 상자에서 이름을 변경할 수 있습니다.

9. **추가**를 클릭하여 앱을 추가합니다.

   잠시 후 응용 프로그램의 구성 창을 볼 수 있습니다.

### <a name="configure-the-app-for-password-sso"></a>암호 SSO에 대 한 앱 구성

다음 단계를 수행하세요.

1. [Azure Portal](https://portal.azure.com/) 을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.

2. 왼쪽의 탐색 창 맨 위에서 **모든 서비스** 를 선택 하 여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력 하 고 **Azure Active Directory**를 선택 합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. **모든 응용 프로그램** 을 선택 하 여 앱 목록을 봅니다.

   > [!NOTE]
   > 원하는 앱이 표시 되지 않으면 **모든 응용 프로그램 목록**의 맨 위에 있는 **필터** 컨트롤을 사용 합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정 합니다.

6. SSO에 대해 구성 하려는 앱을 선택 합니다.

7. 앱이 로드 되 면 앱의 왼쪽 창에서 **Single sign-on** 을 선택 합니다.

8. **암호 기반 로그온** 모드를 선택 합니다.

9. 사용자를 앱에 할당 합니다.

10. 사용자에 대 한 자격 증명을 제공할 수도 있습니다. 그렇지 않으면 사용자에 게 앱을 시작할 때 자격 증명을 입력 하 라는 메시지가 표시 됩니다. 이렇게 하려면 사용자의 행을 선택 합니다. 그런 다음 **자격 증명 업데이트** 를 선택 하 고 사용자 이름 및 암호를 입력 합니다.

### <a name="assign-users-to-the-app"></a>앱에 사용자 할당

사용자를 앱에 직접 할당 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 전역 관리자 권한으로 로그인 합니다.

2. 왼쪽의 탐색 불만에서 **모든 서비스** 를 선택 하 여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력 하 고 **Azure Active Directory**를 선택 합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. 응용 프로그램 목록을 보려면 **모든 응용 프로그램** 을 선택 합니다.

   > [!NOTE]
   > 원하는 앱이 표시 되지 않으면 **모든 응용 프로그램 목록**의 맨 위에 있는 **필터** 컨트롤을 사용 합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정 합니다.

6. 목록에서 사용자를 할당 하려는 앱을 선택 합니다.

7. 응용 프로그램이 로드 된 후 왼쪽에 있는 앱의 탐색 창에서 **사용자 및 그룹** 을 선택 합니다.

8. **사용자 및 그룹** 목록의 맨 위에 있는 **추가** 를 선택 하 여 **할당 추가** 창을 엽니다.

9. **할당 추가** 창에서 **사용자 및 그룹** 을 선택 합니다.

10. **이름 또는 전자 메일 주소로 검색** 상자에 할당 하려는 사용자의 전체 이름 또는 전자 메일 주소를 입력 합니다.

11. 목록에서 사용자를 마우스로 가리킵니다. 사용자의 프로필 사진 또는 로고 옆에 있는 확인란을 선택 하 여 해당 사용자를 **선택** 된 목록에 추가 합니다.

12. *선택 사항:* 다른 사용자를 추가 하려면 **이름 또는 전자 메일 주소로 검색** 상자에 다른 이름 또는 전자 메일 주소를 입력 한 다음 확인란을 선택 하 여 해당 사용자를 **선택** 된 목록에 추가 합니다.

13. 사용자를 선택 했으면 **선택** 을 클릭 하 여 앱에 할당 된 사용자 및 그룹의 목록에 추가 합니다.

14. *선택 사항:* **할당 추가** 창에서 **역할 선택** 을 클릭 하 여 선택한 사용자에 게 할당할 역할을 선택 합니다.

15. **할당** 을 선택 하 여 선택한 사용자에 게 앱을 할당 합니다.

    잠시 후 사용자는 액세스 패널에서 해당 앱에 액세스할 수 있습니다.

## <a name="request-support"></a>지원 요청 
SSO를 설정 하 고 사용자를 할당 하는 경우 오류 메시지가 표시 되 면 지원 티켓을 엽니다. 가능한 한 많은 다음 정보를 포함 합니다.

-   상관 관계 오류 ID
-   UPN(사용자 전자 메일 주소)
-   TenantID
-   브라우저 종류
-   오류가 발생 한 표준 시간대 및 시간/시간 프레임
-   Fiddler 추적

## <a name="next-steps"></a>다음 단계
[애플리케이션 프록시를 사용하여 앱에 Single Sign-On 제공](application-proxy-configure-single-sign-on-with-kcd.md)
