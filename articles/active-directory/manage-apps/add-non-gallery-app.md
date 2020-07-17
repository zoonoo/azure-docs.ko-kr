---
title: 비갤러리 애플리케이션 추가 - Microsoft ID 플랫폼 | Microsoft Docs
description: Azure AD 테넌트에 비갤러리 애플리케이션을 추가합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5db8aed0a47e7d8d928ef3287010d60efbc5e5da
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200460"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>나열되지 않은(비갤러리) 애플리케이션을 Azure AD 조직에 추가

[Azure AD 애플리케이션 갤러리](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)에서 선택하는 것 외에도 **비갤러리 애플리케이션**를 추가하는 옵션이 있습니다. 조직에 이미 있는 애플리케이션 또는 Azure AD 애플리케이션 갤러리의 일부가 아닌 공급업체의 타사 애플리케이션을 추가할 수 있습니다. [사용권 계약](https://azure.microsoft.com/pricing/details/active-directory/)에 따라 다음과 같은 기능을 사용할 수 있습니다.

- [SAML(Security Assertion Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) ID 공급자를 지원하는 애플리케이션의 셀프 서비스 통합(SP 시작 또는 IdP 시작)
- [암호 기반 SSO](what-is-single-sign-on.md#password-based-sso)를 사용하여 HTML 기반 로그인 페이지가 있는 웹 애플리케이션의 셀프 서비스 통합
- [사용자 프로비저닝에 SCIM(System for Cross-Domain Identity Management) 프로토콜](../app-provisioning/use-scim-to-provision-users-and-groups.md)을 사용하는 애플리케이션의 셀프 서비스 연결
- [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](what-is-single-sign-on.md#linked-sign-on)에서 애플리케이션에 대한 링크를 추가하는 기능

이 문서에서는 코드를 작성하지 않고 Azure Portal에서 **엔터프라이즈 애플리케이션** 에 비갤러리 애플리케이션을 추가하는 방법을 설명합니다. 대신 사용자 지정 앱을 Azure AD와 통합하는 방법에 대한 개발자 참고 자료를 찾는다면 [Azure AD의 인증 시나리오](../develop/authentication-scenarios.md)를 참조하세요. [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)와 같은 최신 프로토콜을 사용하여 사용자를 인증하는 앱을 개발할 때 Azure Portal에서 [앱 등록](../develop/quickstart-register-app.md) 경험을 사용하여 Microsoft ID 플랫폼에 등록할 수 있습니다.

## <a name="add-a-non-gallery-application"></a>비갤러리 애플리케이션 추가

1. Microsoft ID 플랫폼 관리자 계정을 사용하여 [Azure Active Directory 포털](https://aad.portal.azure.com/)에 로그인합니다.

2. **엔터프라이즈 애플리케이션** > **새 애플리케이션**을 선택합니다.

3. (선택 사항이지만 권장됨) **Azure AD 갤러리 찾아보기** 검색 상자에서 애플리케이션의 표시 이름을 입력합니다. 

4. **자신만의 애플리케이션**을 선택하세요. **자신만의 애플리케이션** 페이지가 나타납니다.

   ![애플리케이션 추가](media/add-non-gallery-app/create-your-own-application.png)

5. 새 애플리케이션의 표시 이름 입력을 시작합니다. 비슷한 이름의 갤러리 애플리케이션이 있으면 검색 결과 목록에 나타납니다.

   > [!NOTE]
   > 가능하면 항상 갤러리 버전의 애플리케이션을 사용하는 것이 좋습니다. 추가하려는 애플리케이션이 검색 결과에 표시되면 애플리케이션을 선택하고 이 프로시저의 나머지 부분을 건너뜁니다.

6. **애플리케이션으로 어떤 작업을 하고 싶으십니까?** 에서 **갤러리에서 찾을 수 없는 다른 애플리케이션 통합**을 선택합니다. 이 옵션은 일반적으로 SAML 및 WS-Fed 애플리케이션에 사용됩니다.

   > [!NOTE]
   > 다른 두 옵션은 다음 시나리오에서 사용됩니다.
   >* **온-프레미스 애플리케이션에 보안 원격 액세스를 위한 애플리케이션 프록시 구성**을 선택하면 Azure AD 애플리케이션 프록시 및 커넥터의 구성 페이지가 열립니다.
   >* **작업 중인 애플리케이션을 등록하여 Azure AD와 통합**을 선택하면 **앱 등록** 페이지가 열립니다. 이 옵션은 일반적으로 OpenID Connect 애플리케이션에 사용됩니다.

7. **만들기**를 선택합니다. 애플리케이션 **개요** 페이지가 열립니다.

## <a name="configure-user-sign-in-properties"></a>사용자 로그인 속성 구성

1. **속성**을 선택하여 편집할 속성 창을 엽니다.

    ![속성 창 블레이드](media/add-non-gallery-app/edit-properties.png)

2. 애플리케이션에 할당되거나 할당되지 않은 사용자가 애플리케이션에 로그인할 수 있는 방법과 사용자가 액세스 패널에서 애플리케이션을 볼 수 있는지 여부를 결정하려면 다음 옵션을 설정합니다.

    - **사용자가 로그인할 수 있습니까**는 애플리케이션에 할당된 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자 할당 필요**는 애플리케이션에 할당되지 않은 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자에게 표시**는 앱에 할당된 사용자가 액세스 패널 및 O365 시작 관리자에서 앱을 볼 수 있는지 여부를 결정합니다.

      **할당된** 사용자에 대한 동작:

       | 애플리케이션 속성 | 애플리케이션 속성 | 애플리케이션 속성 | 할당된 사용자 경험 | 할당된 사용자 경험 |
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

       | 애플리케이션 속성 | 애플리케이션 속성 | 애플리케이션 속성 | 할당되지 않은 사용자 환경 | 할당되지 않은 사용자 환경 |
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
- [연결된 로그온 구성](configure-linked-sign-on.md)
