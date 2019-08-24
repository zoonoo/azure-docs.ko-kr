---
title: 비 갤러리 응용 프로그램 추가-Microsoft identity platform | Microsoft Docs
description: 비 갤러리 응용 프로그램을 Azure AD 테 넌 트에 추가 합니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8d8d6df16c5df7e29d8bb870c5d5eda6d8a2d3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477254"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>나열 되지 않은 (비 갤러리) 응용 프로그램을 Azure AD 조직에 추가

[AZURE AD 응용 프로그램 갤러리](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)의 선택 사항 외에도 **비 갤러리 응용 프로그램**을 추가 하는 옵션이 있습니다. 조직에 이미 있는 응용 프로그램이 나 아직 Azure AD 갤러리에 속하지 않은 공급 업체의 타사 응용 프로그램을 추가할 수 있습니다. [사용권 계약](https://azure.microsoft.com/pricing/details/active-directory/)에 따라 다음과 같은 기능을 사용할 수 있습니다.

- [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) id 공급자를 지 원하는 모든 응용 프로그램의 셀프 서비스 통합 (SP 시작 또는 IdP 시작)
- [암호 기반 SSO](what-is-single-sign-on.md#password-based-sso)
- [사용자 프로 비전을 위해 SCIM (도메인 간 Id 관리) 프로토콜에 시스템](use-scim-to-provision-users-and-groups.md) 을 사용 하는 응용 프로그램의 셀프 서비스 연결
- [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](what-is-single-sign-on.md#linked-sign-on)에서 애플리케이션에 대한 링크를 추가하는 기능

이 문서에서는 코드를 작성 하지 않고 Azure Portal의 **엔터프라이즈 응용** 프로그램에 비 갤러리 응용 프로그램을 추가 하는 방법을 설명 합니다. 사용자 지정 앱을 Azure AD와 통합 하는 방법에 대 한 개발자 가이드를 찾고 있는 경우 [AZURE ad에 대 한 인증 시나리오](../develop/authentication-scenarios.md)를 참조 하세요. [Openid connect Connect/OAuth](../develop/active-directory-v2-protocols.md) 와 같은 최신 프로토콜을 사용 하 여 사용자를 인증 하는 앱을 개발 하는 경우 Azure Portal에서 [앱 등록](../develop/quickstart-register-app.md) 환경을 사용 하 여 Microsoft id 플랫폼에 등록할 수 있습니다.

## <a name="add-a-non-gallery-application"></a>비갤러리 애플리케이션 추가

1. Microsoft id 플랫폼 관리자 계정을 사용 하 여 [Azure Active Directory 포털](https://aad.portal.azure.com/) 에 로그인 합니다.
1. **엔터프라이즈 응용 프로그램** > **새 응용 프로그램**을 선택 합니다.
2. (선택 사항 이지만 권장 됨) 갤러리 검색 **에서 추가** 상자에 응용 프로그램의 표시 이름을 입력 합니다. 응용 프로그램이 검색 결과에 표시 되 면이를 선택 하 고이 절차의 나머지 부분을 건너뜁니다.
3. **비 갤러리 응용 프로그램**을 선택 합니다. **사용자 고유의 응용 프로그램 추가** 페이지가 나타납니다.

   ![애플리케이션 추가](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. 새 응용 프로그램의 표시 이름을 입력 합니다.
6. **추가**를 선택합니다. 응용 프로그램 **개요** 페이지가 열립니다.

## <a name="configure-user-sign-in-properties"></a>사용자 로그인 속성 구성

1. **속성**을 선택하여 편집할 속성 창을 엽니다.

    ![속성 창 블레이드](media/add-application-portal/edit-properties.png)

1. 응용 프로그램에 할당 되거나 할당 되지 않은 사용자가 응용 프로그램에 로그인 할 수 있는 방법과 사용자가 액세스 패널에서 응용 프로그램을 볼 수 있는지 여부를 결정 하려면 다음 옵션을 설정 합니다.

    - **사용자가 로그인할 수 있습니까**는 애플리케이션에 할당된 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자 할당 필요**는 애플리케이션에 할당되지 않은 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자에게 표시**는 앱에 할당된 사용자가 액세스 패널 및 O365 시작 관리자에서 앱을 볼 수 있는지 여부를 결정합니다.

      **할당된** 사용자에 대한 동작:

       | 애플리케이션 속성 설정 | | | 할당된 사용자 경험 | |
       |---|---|---|---|---|
       | 사용자가 로그인할 수 있습니까? | 사용자 할당이 필요합니까? | 사용자가 볼 수 있습니까? | 할당된 사용자가 로그인할 수 있습니까? | 할당된 사용자가 애플리케이션을 볼 수 있습니까?* |
       | 예 | 예 | 예 | 예 | 예  |
       | 예 | 예 | 아니요  | 예 | 아니요   |
       | 예 | 아니요  | 예 | 예 | 예  |
       | 예 | no  | 아니요  | 예 | no   |
       | 아니요  | 예 | 예 | no  | 아니요   |
       | 아니요  | 예 | no  | 아니요  | 아니요   |
       | 아니요  | 아니요  | 예 | no  | 아니요   |
       | 아니요  | 아니요  | 아니요  | 아니요  | no   |

      **할당되지 않은** 사용자에 대한 동작:

       | 애플리케이션 속성 설정 | | | 할당되지 않은 사용자 환경 | |
       |---|---|---|---|---|
       | 사용자가 로그인할 수 있습니까? | 사용자 할당 필요? | 사용자가 볼 수 있습니까? | 할당되지 않은 사용자가 로그인할 수 있습니까? | 할당되지 않은 사용자가 애플리케이션을 볼 수 있습니다? * |
       | 예 | 예 | 예 | no  | 아니요   |
       | 예 | 예 | no  | 아니요  | 아니요   |
       | 예 | 아니요  | 예 | 예 | 아니요   |
       | 예 | no  | 아니요  | 예 | no   |
       | 아니요  | 예 | 예 | no  | 아니요   |
       | 아니요  | 예 | no  | 아니요  | 아니요   |
       | 아니요  | 아니요  | 예 | no  | 아니요   |
       | 아니요  | 아니요  | 아니요  | 아니요  | no   |

     *사용자가 액세스 패널 및 Office 365 앱 시작 관리자에서 애플리케이션을 볼 수 있습니까?

1. 사용자 지정 로고를 사용 하려면 215 픽셀의 215 로고를 만들고 PNG 형식으로 저장 합니다. 그런 다음 로고로 이동 하 여 업로드 합니다.

    ![로고 변경](media/add-application-portal/change-logo.png)

1. 완료되면 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure AD 조직에 응용 프로그램을 추가 했으므로 사용할 [single sign-on 방법을 선택](what-is-single-sign-on.md#choosing-a-single-sign-on-method) 하 고 아래에서 해당 하는 문서를 참조 하세요.

- [SAML 기반 single sign-on 구성](configure-single-sign-on-non-gallery-applications.md)
- [암호 single sign-on 구성](configure-password-single-sign-on-non-gallery-applications.md)
- [연결 된 로그온 구성](configure-linked-sign-on.md)
