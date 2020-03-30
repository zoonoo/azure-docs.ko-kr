---
title: 비 갤러리 응용 프로그램 추가 - Microsoft ID 플랫폼 | 마이크로 소프트 문서
description: Azure AD 테넌트에 비갤러리 응용 프로그램을 추가합니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063614"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Azure AD 조직에 나열되지 않은(비갤러리) 응용 프로그램 추가

[Azure AD 응용 프로그램 갤러리의](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)선택 사항 외에도 **비갤러리 응용 프로그램을**추가할 수 있습니다. 조직에 이미 있는 모든 응용 프로그램 또는 Azure AD 갤러리에 아직 속해 있지 않은 공급업체의 타사 응용 프로그램을 추가할 수 있습니다. [사용권 계약에](https://azure.microsoft.com/pricing/details/active-directory/)따라 다음 기능을 사용할 수 있습니다.

- [SAML(보안 어설션 태그 언어) 2.0](https://wikipedia.org/wiki/SAML_2.0) ID 공급자(SP 시작 또는 IdP 시작)를 지원하는 모든 응용 프로그램의 셀프 서비스 통합
- [암호 기반 SSO](what-is-single-sign-on.md#password-based-sso)
- [사용자 프로비저닝을 위해 SCIM(도메인 간 ID 관리) 프로토콜을](../app-provisioning/use-scim-to-provision-users-and-groups.md) 사용하는 응용 프로그램의 셀프 서비스 연결
- [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](what-is-single-sign-on.md#linked-sign-on)에서 애플리케이션에 대한 링크를 추가하는 기능

이 문서에서는 코드를 작성하지 않고 Azure 포털의 엔터프라이즈 응용 프로그램에 비갤러리 응용 프로그램을 추가하는 방법을 **설명합니다.** 대신 사용자 지정 앱을 Azure AD와 통합하는 방법에 대한 개발자 지침을 찾고 있는 경우 [Azure AD에 대한 인증 시나리오를](../develop/authentication-scenarios.md)참조하세요. [OpenId Connect/OAuth와](../develop/active-directory-v2-protocols.md) 같은 최신 프로토콜을 사용하여 사용자를 인증하는 앱을 개발하는 경우 Azure 포털의 [앱 등록](../develop/quickstart-register-app.md) 환경을 사용하여 Microsoft ID 플랫폼에 등록할 수 있습니다.

## <a name="add-a-non-gallery-application"></a>비갤러리 애플리케이션 추가

1. Microsoft ID 플랫폼 관리자 계정을 사용하여 [Azure Active Directory 포털에](https://aad.portal.azure.com/) 로그인합니다.

2. 엔터프라이즈 응용 프로그램**새 응용 프로그램을** **선택합니다.** > 

3. (선택 사항이지만 권장) Azure **AD 갤러리 찾아보기** 검색 상자에 응용 프로그램의 표시 이름을 입력합니다. 

4. **사용자 고유의 응용 프로그램 만들기를 선택합니다.** **사용자 고유의 응용 프로그램 만들기** 페이지가 나타납니다.

   ![애플리케이션 추가](media/add-non-gallery-app/create-your-own-application.png)

5. 새 응용 프로그램의 표시 이름을 입력합니다. 이름이 비슷한 갤러리 응용 프로그램이 있으면 검색 결과 목록에 표시됩니다.

   > [!NOTE]
   > 가능하면 응용 프로그램의 갤러리 버전을 사용하는 것이 좋습니다. 추가하려는 응용 프로그램이 검색 결과에 나타나면 응용 프로그램을 선택하고 이 절차의 나머지 부분을 건너뜁니다.

6. 응용 프로그램과 함께 수행할 작업을 아래에서 **갤러리에서 찾을 수 없는 다른 응용 프로그램 통합을** **선택합니다.** 이 옵션은 일반적으로 SAML 및 WS-Fed 응용 프로그램에 사용됩니다.

   > [!NOTE]
   > 다른 두 가지 옵션은 다음 시나리오에서 사용됩니다.
   >* **온-프레미스 응용 프로그램에 대한 안전한 원격 액세스를 위해 응용 프로그램 프록시를 구성하면** Azure AD 응용 프로그램 프록시 및 커넥터에 대한 구성 페이지가 열립니다.
   >* **Azure AD와 통합하기 위해 작업 중인 응용 프로그램을 등록하면** **앱 등록 페이지가** 열립니다. 이 옵션은 일반적으로 OpenID Connect 응용 프로그램에 사용됩니다.

7. **만들기**를 선택합니다. 애플리케이션 **개요** 페이지가 열립니다.

## <a name="configure-user-sign-in-properties"></a>사용자 로그인 속성 구성

1. **속성**을 선택하여 편집할 속성 창을 엽니다.

    ![속성 창 블레이드](media/add-non-gallery-app/edit-properties.png)

2. 애플리케이션에 할당되거나 할당되지 않은 사용자가 애플리케이션에 로그인할 수 있는 방법과 사용자가 액세스 패널에서 애플리케이션을 볼 수 있는지 여부를 결정하려면 다음 옵션을 설정합니다.

    - **사용자가 로그인할 수 있도록 활성화하면** 응용 프로그램에 할당된 사용자가 로그인할 수 있는지 여부가 결정됩니다.
    - **사용자 할당 필요**는 애플리케이션에 할당되지 않은 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자에게 표시**는 앱에 할당된 사용자가 액세스 패널 및 O365 시작 관리자에서 앱을 볼 수 있는지 여부를 결정합니다.

      **할당된** 사용자에 대한 동작:

       | 애플리케이션 속성 설정 | | | 할당된 사용자 경험 | |
       |---|---|---|---|---|
       | 사용자가 로그인할 수 있습니까? | 사용자 할당이 필요합니까? | 사용자가 볼 수 있습니까? | 할당된 사용자가 로그인할 수 있습니까? | 할당된 사용자가 애플리케이션을 볼 수 있습니까?* |
       | 예 | 예 | 예 | 예 | 예  |
       | 예 | 예 | 아니요  | 예 | 아니요   |
       | 예 | 아니요  | 예 | 예 | 예  |
       | 예 | 아니요  | 아니요  | 예 | 아니요   |
       | 아니요  | 예 | 예 | 아니요  | 아니요   |
       | 아니요  | 예 | 아니요  | 아니요  | 아니요   |
       | 아니요  | 아니요  | 예 | 아니요  | 아니요   |
       | 아니요  | 아니요  | 아니요  | 아니요  | 아니요   |

      **할당되지 않은** 사용자에 대한 동작:

       | 애플리케이션 속성 설정 | | | 할당되지 않은 사용자 환경 | |
       |---|---|---|---|---|
       | 사용자가 로그인할 수 있습니까? | 사용자 할당이 필요합니까? | 사용자가 볼 수 있습니까? | 할당되지 않은 사용자가 로그인할 수 있습니까? | 할당되지 않은 사용자가 애플리케이션을 볼 수 있습니다? * |
       | 예 | 예 | 예 | 아니요  | 아니요   |
       | 예 | 예 | 아니요  | 아니요  | 아니요   |
       | 예 | 아니요  | 예 | 예 | 아니요   |
       | 예 | 아니요  | 아니요  | 예 | 아니요   |
       | 아니요  | 예 | 예 | 아니요  | 아니요   |
       | 아니요  | 예 | 아니요  | 아니요  | 아니요   |
       | 아니요  | 아니요  | 예 | 아니요  | 아니요   |
       | 아니요  | 아니요  | 아니요  | 아니요  | 아니요   |

     *사용자가 액세스 패널 및 Office 365 앱 시작 관리자에서 애플리케이션을 볼 수 있습니까?

3. 사용자 지정 로고를 사용하려면 215 x 215 픽셀 로고를 만들고, PNG 형식으로 저장합니다. 해당 로고를 찾아서 업로드합니다.

    ![로고 변경](media/add-non-gallery-app/change-logo.png)

4. 완료되면 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure AD 조직에 애플리케이션을 추가했으므로, 이제 사용할 [Single Sign-On 방법을 선택](what-is-single-sign-on.md#choosing-a-single-sign-on-method)하고 아래에서 적절한 문서를 참조합니다.

- [SAML 기반 Single Sign-On 구성](configure-single-sign-on-non-gallery-applications.md)
- [암호 Single Sign-On 구성](configure-password-single-sign-on-non-gallery-applications.md)
- [연결된 사인온 구성](configure-linked-sign-on.md)
