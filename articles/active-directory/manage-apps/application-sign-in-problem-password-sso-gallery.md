---
title: SSO용으로 구성된 Azure AD 갤러리 앱에 로그인하는 문제 | 마이크로 소프트 문서
description: 암호 단일 사인온으로 구성된 Azure AD 갤러리 응용 프로그램의 문제를 해결하는 방법
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381302"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>SSO용으로 구성된 Azure AD 갤러리 앱의 로그인 문제

액세스 패널은 웹 기반 포털입니다. Azure Active Directory(Azure AD) 직장 또는 학교 계정이 있는 사용자는 사용 권한이 있는 클라우드 기반 앱에 액세스할 수 있습니다. Azure AD 버전을 사용하는 사용자는 액세스 패널을 통해 셀프 서비스 그룹 및 앱 관리 기능을 사용할 수도 있습니다.

액세스 패널은 Azure 포털과 는 별개입니다. 사용자는 액세스 패널을 사용하기 위해 Azure 구독이 필요하지 않습니다.

액세스 패널에서 암호 기반 단일 사인온(SSO)을 사용하려면 액세스 패널 확장프로그램을 브라우저에 설치해야 합니다. 암호 기반 SSO용으로 구성된 앱을 선택하면 확장 프로그램이 자동으로 다운로드됩니다.

## <a name="browser-requirements-for-access-panel"></a>액세스 패널에 대한 브라우저 요구 사항

액세스 패널에는 자바스크립트를 지원하는 브라우저가 필요하며 CSS가 활성화되어 있습니다.

다음 브라우저는 암호 기반 SSO를 지원합니다.

- 윈도우 7 이상에서 인터넷 익스플로러 8, 9, 10 및 11

- 윈도우에 크롬 7 이상 또는 맥 OS X 이상에

- 파이어 폭스 26.0 이상 윈도우 XP SP2 이상 또는 맥 OS X에 10.6 이상

>[!NOTE]
>암호 기반 SSO 확장 윈도우에서 마이크로소프트 가장자리에 대 한 사용할 수 있게 10 브라우저 확장에 대 한 지원 은 마이크로소프트 가장자리에 추가 된 때.

## <a name="install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장 설치

다음 단계를 수행하세요.

1. 지원되는 브라우저에서 [액세스 패널을](https://myapps.microsoft.com) 열고 Azure AD에서 사용자로 로그인합니다.

2. 액세스 패널에서 암호 SSO 지원 앱을 선택합니다.

3. 메시지가 표시되면 지금 **설치를**선택합니다.

4. 브라우저를 기반으로 다운로드 링크로 이동합니다. 브라우저 확장 프로그램을 설치하려면 **추가를** 선택합니다.

5. 메시지가 표시되면 **사용** 또는 **허용을**선택합니다.

6. 설치 후 브라우저를 다시 시작합니다.

7.  액세스 패널에 로그인하여 암호 SSO 지원 앱을 시작할 수 있는지 확인합니다.

또한 다음 링크를 통해 Chrome 및 Firefox의 확장 프로그램을 직접 다운로드할 수도 있습니다.

-   [크롬 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [파이어 폭스 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Internet Explorer에 대한 그룹 정책 설정

사용자의 컴퓨터에 Internet Explorer용 액세스 패널 확장을 원격으로 설치할 수 있는 그룹 정책을 설정할 수 있습니다.

다음은 필수 구성 조건입니다.

-   [Active Directory 도메인 서비스를](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) 설정해야 하며 사용자의 컴퓨터를 도메인에 가입해야 합니다.

-   그룹 정책 개체(GPO)를 편집할 수 있는 "설정 편집" 권한이 있습니다. 기본적으로 도메인 관리자, 엔터프라이즈 관리자 및 그룹 정책 작성자/소유자 보안 그룹의 멤버에게 이 권한이 있습니다. [자세히 알아봅니다](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

그룹 정책을 구성하고 사용자에게 배포하려면 [그룹 정책을 사용하여 Internet Explorer용 액세스 패널 확장을 배포하는 방법을](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)참조하세요.

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>인터넷 익스플로러의 문제 해결 액세스 패널

확장 프로그램을 구성하는 진단 도구 및 지침에 액세스하려면 [Internet Explorer의 액세스 패널 확장](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)문제를 해결합니다.

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Azure AD 갤러리 앱에 대한 암호 SSO 구성

Azure AD 갤러리에서 앱을 구성하려면 다음 작업을 수행해야 합니다.

-   Azure AD 갤러리에서 앱 추가
-   [암호 단일 사인온에 대한 앱 구성](#configure-the-app-for-password-sso)
-   [앱에 사용자 할당](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 앱 추가

다음 단계를 수행하세요.

1. Azure [포털을](https://portal.azure.com) 열고 글로벌 관리자 또는 공동 관리자로 로그인합니다.

2. 왼쪽 탐색 창 상단에 있는 **모든 서비스를** 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory를** 입력한 다음 **Azure Active Directory**를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다.

5. 엔터프라이즈 응용 프로그램 창의 오른쪽 위 모서리에 **추가를** **선택합니다.**

6. **갤러리에서 추가** 섹션에서 이름 입력 상자에 앱 이름을 **입력합니다.**

7. SSO에 대해 구성할 앱을 선택합니다.

8. *선택 사항:* 앱을 추가하기 전에 **이름** 상자에서 이름을 변경할 수 있습니다.

9. **추가**를 클릭하여 앱을 추가합니다.

   잠시 후 앱의 구성 창을 볼 수 있습니다.

### <a name="configure-the-app-for-password-sso"></a>암호 SSO에 대한 응용 프로그램 구성

다음 단계를 수행하세요.

1. Azure [포털을](https://portal.azure.com/) 열고 글로벌 관리자 또는 공동 관리자로 로그인합니다.

2. 왼쪽 탐색 창 상단에 있는 **모든 서비스를** 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory를** 입력한 다음 **Azure Active Directory**를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다.

5. **모든 응용 프로그램을** 선택하여 앱 목록을 봅니다.

   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 모든 응용 프로그램 목록 맨 위에 있는 **필터** **컨트롤을**사용합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정합니다.

6. SSO에 대해 구성할 앱을 선택합니다.

7. 앱이 로드된 후 앱 왼쪽의 창에서 **단일 사인온을** 선택합니다.

8. **암호 기반 사인온** 모드를 선택합니다.

9. 사용자를 앱에 할당합니다.

10. 사용자에게 자격 증명을 제공할 수도 있습니다. 그렇지 않으면 앱 시작 시 자격 증명을 입력하라는 메시지가 표시됩니다. 이렇게 하려면 사용자의 행을 선택합니다. 그런 다음 **자격 증명 업데이트를** 선택하고 사용자 이름과 암호를 입력합니다.

### <a name="assign-users-to-the-app"></a>앱에 사용자 할당

사용자를 앱에 직접 할당하려면 다음 단계를 따르세요.

1. Azure [포털을](https://portal.azure.com/) 열고 전역 관리자로 로그인합니다.

2. 왼쪽 탐색 통증에서 **모든 서비스를** 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory를** 입력한 다음 **Azure Active Directory**를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다.

5. **모든 응용 프로그램을** 선택하여 응용 프로그램 목록을 봅니다.

   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 모든 응용 프로그램 목록 맨 위에 있는 **필터** **컨트롤을**사용합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정합니다.

6. 목록에서 사용자를 할당할 앱을 선택합니다.

7. 응용 프로그램이 로드된 후 왼쪽에 있는 앱의 탐색 창에서 **사용자 및 그룹을** 선택합니다.

8. 사용자 및 그룹 목록 상단에서 **추가를** 선택하여 **과제 추가** 창을 **엽니다.**

9. **할당 추가** 창에서 사용자 **및 그룹을** 선택합니다.

10. 이름 **또는 전자 메일 주소** 상자에서 할당할 사용자의 전체 이름 또는 전자 메일 주소를 입력합니다.

11. 목록에서 사용자 위로 마우스를 가져갑니다. 사용자의 프로필 사진 또는 로고 옆에 있는 확인란을 선택하여 해당 사용자를 **선택한** 목록에 추가합니다.

12. *선택 사항:* 다른 사용자를 추가하려면 이름 또는 전자 메일 **주소로 검색에** 다른 이름 또는 전자 메일 주소를 입력한 다음 확인란을 선택하여 해당 사용자를 **선택한** 목록에 추가합니다.

13. 사용자 선택을 마치면 **선택을** 클릭하여 앱에 할당된 사용자 및 그룹 목록에 사용자를 추가합니다.

14. *선택 사항:* **과제 추가** 창에서 **역할 선택을** 클릭하여 선택한 사용자에게 할당할 역할을 선택합니다.

15. 선택한 사용자에게 앱을 할당하려면 **할당을** 선택합니다.

    잠시 후 사용자는 액세스 패널에서 해당 앱에 액세스할 수 있습니다.

## <a name="request-support"></a>지원 요청 
SSO를 설정하고 사용자를 할당할 때 오류 메시지가 나타나면 지원 티켓을 엽니다. 다음 정보를 가능한 한 많이 포함하십시오.

-   상관 관계 오류 ID
-   UPN(사용자 전자 메일 주소)
-   TenantID
-   브라우저 종류
-   표준 시간대 및 시간/시간 프레임 오류가 발생 했습니다.
-   Fiddler 추적

## <a name="next-steps"></a>다음 단계
[애플리케이션 프록시를 사용하여 앱에 Single Sign-On 제공](application-proxy-configure-single-sign-on-with-kcd.md)
