---
title: 갤러리 앱 추가 - Azure Active Directory | Microsoft Docs
description: Azure AD 갤러리의 앱을 Azure 엔터프라이즈 애플리케이션에 추가하는 방법을 알아봅니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bdf275bbafa9c46cfc4577ac2843da0be74c7ef
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477277"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Azure AD 조직에 갤러리 앱 추가

Azure AD(Azure Active Directory)에는 Enterprise Single Sign-On이 설정된 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 이 문서에서는 갤러리의 앱을 Azure AD 조직에 추가하는 일반적인 단계를 설명합니다.

> [!IMPORTANT]
> 먼저 [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)에서 앱을 확인합니다. 추가하려는 갤러리 앱을 추가하고 구성하는 방법에 대한 단계별 지침을 찾을 수 있습니다.

## <a name="add-a-gallery-application"></a>갤러리 애플리케이션 추가

1. [Azure Portal](https://portal.azure.com)에 Azure AD 테넌트의 글로벌 관리자, 클라우드 애플리케이션 관리자 또는 애플리케이션 관리자로 로그인합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.

1. **Azure Active Directory** 창에서 **엔터프라이즈 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 열기](media/add-application-portal/open-enterprise-apps.png)

1. **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션](media/add-application-portal/new-application.png)

1. **갤러리에서 추가** 아래의 검색 상자에 추가하려는 애플리케이션 이름을 입력합니다. 

    ![이름 또는 범주로 검색](media/add-application-portal/categories.png)

1. 결과에서 애플리케이션을 선택합니다.

1. (선택 사항) 애플리케이션별 양식에서 조직의 요구에 맞게 애플리케이션 이름을 편집할 수 있습니다.

1. **추가**를 선택합니다. 애플리케이션 **개요** 페이지가 열립니다.

## <a name="configure-user-sign-in-properties"></a>사용자 로그인 속성 구성

1. **속성**을 선택하여 편집할 속성 창을 엽니다.

    ![속성 창 블레이드](media/add-application-portal/edit-properties.png)

1. 애플리케이션에 할당되거나 할당되지 않은 사용자가 애플리케이션에 로그인할 수 있는 방법과 사용자가 액세스 패널에서 애플리케이션을 볼 수 있는지 여부를 결정하려면 다음 옵션을 설정합니다.

    - **사용자가 로그인할 수 있습니까**는 애플리케이션에 할당된 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자 할당 필요**는 애플리케이션에 할당되지 않은 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자에게 표시**는 앱에 할당된 사용자가 액세스 패널 및 O365 시작 관리자에서 앱을 볼 수 있는지 여부를 결정합니다.

      **할당된** 사용자에 대한 동작:

       | 애플리케이션 속성 설정 | | | 할당된 사용자 경험 | |
       |---|---|---|---|---|
       | 사용자가 로그인할 수 있습니까? | 사용자 할당이 필요합니까? | 사용자가 볼 수 있습니까? | 할당된 사용자가 로그인할 수 있습니까? | 할당된 사용자가 애플리케이션을 볼 수 있습니까?* |
       | 예 | 예 | 예 | 예 | 예  |
       | 예 | 예 | no  | 예 | no   |
       | 예 | no  | 예 | 예 | 예  |
       | 예 | no  | no  | 예 | no   |
       | no  | 예 | 예 | no  | no   |
       | no  | 예 | no  | no  | no   |
       | no  | no  | 예 | no  | no   |
       | no  | no  | no  | no  | no   |

      **할당되지 않은** 사용자에 대한 동작:

       | 애플리케이션 속성 설정 | | | 할당되지 않은 사용자 환경 | |
       |---|---|---|---|---|
       | 사용자가 로그인할 수 있습니까? | 사용자 할당이 필요합니까? | 사용자가 볼 수 있습니까? | 할당되지 않은 사용자가 로그인할 수 있습니까? | 할당되지 않은 사용자가 애플리케이션을 볼 수 있습니다? * |
       | 예 | 예 | 예 | no  | no   |
       | 예 | 예 | no  | no  | no   |
       | 예 | no  | 예 | 예 | no   |
       | 예 | no  | no  | 예 | no   |
       | no  | 예 | 예 | no  | no   |
       | no  | 예 | no  | no  | no   |
       | no  | no  | 예 | no  | no   |
       | no  | no  | no  | no  | no   |

     *사용자가 액세스 패널 및 Office 365 앱 시작 관리자에서 애플리케이션을 볼 수 있습니까?

1. 사용자 지정 로고를 사용하려면 215 x 215 픽셀 로고를 만들고, PNG 형식으로 저장합니다. 해당 로고를 찾아서 업로드합니다.

    ![로고 변경](media/add-application-portal/change-logo.png)

1. 완료되면 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure AD 조직에 애플리케이션을 추가했으므로, 이제 사용할 [Single Sign-On 방법을 선택](what-is-single-sign-on.md#choosing-a-single-sign-on-method)하고 아래에서 적절한 문서를 참조합니다.

- [SAML 기반 Single Sign-On 구성](configure-single-sign-on-non-gallery-applications.md)
- [암호 Single Sign-On 구성](configure-password-single-sign-on-non-gallery-applications.md)
- [연결된 로그온 구성](configure-linked-sign-on.md)

