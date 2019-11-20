---
title: Azure Active Directory 테넌트에 앱 추가 | Microsoft Docs
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure AD(Azure Active Directory) 테넌트에 갤러리 애플리케이션을 추가합니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5ee40f6fcd77465b67a7c409493027fefa48ca9
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062900"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>빠른 시작: 애플리케이션을 Azure Active Directory 테넌트에 추가합니다.

Azure AD(Azure Active Directory)에는 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 조직에서 사용하는 애플리케이션 중 일부는 이 갤러리에 있을 것입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure AD(Azure Active Directory) 테넌트에 갤러리 애플리케이션을 추가합니다.

Azure AD 테넌트에 애플리케이션을 추가하면 다음과 같은 작업을 수행할 수 있습니다.

- 조건부 액세스 정책을 사용하여 애플리케이션에 대한 사용자 액세스를 관리합니다.
- Azure AD 계정을 사용하여 애플리케이션에 Single Sign-On 하도록 사용자를 구성합니다.

## <a name="before-you-begin"></a>시작하기 전에

테넌트에 애플리케이션을 추가하려면 다음이 필요합니다.

- Azure AD 구독
- Single Sign-on이 설정된 애플리케이션 구독

[Azure Portal](https://portal.azure.com)에 Azure AD 테넌트의 글로벌 관리자, 클라우드 애플리케이션 관리자 또는 애플리케이션 관리자로 로그인합니다.

이 자습서의 단계를 테스트하려면 비-프로덕션 환경을 사용하는 것이 좋습니다. Azure AD 비-프로덕션 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 받을 수 있습니다.

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 추가

Azure AD 테넌트에 갤러리 애플리케이션을 추가하려면:

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.

2. **Azure Active Directory** 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. **모든 애플리케이션** 창이 열리고 Azure AD 테넌트에 있는 임의의 애플리케이션 샘플이 표시됩니다.

3. 테넌트에 캘러리 앱을 추가하려면 **새 애플리케이션**을 선택합니다. 

    ![새 애플리케이션을 선택하여 갤러리 앱을 테넌트에 추가](media/add-application-portal/new-application.png)

 4. 새 갤러리 미리 보기 환경으로 전환합니다. **애플리케이션 페이지 추가** 맨 위에 있는 배너에서 **여기를 클릭하여 새롭고 향상된 앱 갤러리 사용해 보기** 링크를 선택합니다.

5. **Azure AD 갤러리 찾아보기(미리 보기)** 창이 열리고 클라우드 플랫폼, 온-프레미스 애플리케이션 및 추천 애플리케이션에 대한 타일이 표시됩니다. **추천 애플리케이션** 섹션에 나열된 애플리케이션에는 SSO(페더레이션된 Single Sign-On) 및 프로비저닝을 지원하는지 여부를 나타내는 아이콘이 있습니다.

    ![이름 또는 범주로 앱 검색](media/add-application-portal/browse-gallery.png)

6. 추가하려는 애플리케이션의 갤러리를 찾아보거나 검색 상자에 해당 이름을 입력하여 애플리케이션을 검색할 수 있습니다. 그런 다음, 결과에서 애플리케이션을 선택합니다. 양식에서 조직의 요구에 맞게 애플리케이션 이름을 편집할 수 있습니다. 이 예제에서는 이름을 **GitHub-test**로 변경했습니다.

    ![갤러리에서 애플리케이션을 추가하는 방법을 표시합니다.](media/add-application-portal/create-application.png)

7. **만들기**를 선택합니다. 시작 페이지가 열리고 조직의 애플리케이션을 구성하는 옵션이 제공됩니다.

애플리케이션 추가를 마쳤습니다. 그 다음 섹션에서는 애플리케이션의 로고를 변경하고 다른 속성을 편집하는 방법을 보여줍니다.

## <a name="find-your-azure-ad-tenant-application"></a>Azure AD 테넌트 애플리케이션 찾기

여러분이 잠시 자리를 비웠다가 애플리케이션을 계속 구성하기 위해 돌아왔다고 가정해 봅시다. 가장 먼저 할 일은 애플리케이션을 찾는 것입니다.

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 Azure **Active Directory**를 선택합니다.
1. **Azure Active Directory** 창에서 **엔터프라이즈 애플리케이션**을 선택합니다.
1. **애플리케이션 종류** 드롭다운 메뉴에서 **모든 애플리케이션**을 선택하고 **적용**을 선택합니다. 보기 옵션에 대한 자세한 내용은 [테넌트 애플리케이션 보기](view-applications-portal.md)를 참조하세요.
1. 이제 Azure AD 테넌트에서 모든 애플리케이션 목록을 볼 수 있습니다. 목록은 임의의 샘플입니다. 더 많은 애플리케이션을 보려면 **자세히 보기**를 한 번 이상 선택합니다.
1. 테넌트의 애플리케이션을 신속하게 찾으려면 검색 상자에 애플리케이션 이름을 입력하고 **적용**을 선택합니다. 이 예제는 우리가 앞에서 추가한 GitHub-test 애플리케이션을 찾습니다.

    ![검색 상자를 사용하여 애플리케이션을 찾는 방법을 보여 줍니다.](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>사용자 로그인 속성 구성

애플리케이션을 찾았으니, 애플리케이션을 열고 속성을 구성할 수 있습니다.

애플리케이션 속성을 편집하려면:

1. 애플리케이션을 선택하여 엽니다.
2. **속성**을 선택하여 편집할 속성 창을 엽니다.

    ![속성 화면과 편집할 수 있는 앱 속성을 보여 줍니다.](media/add-application-portal/edit-properties.png)

3. 잠시 로그인 옵션을 살펴보면서 이해합니다. 이 옵션은 애플리케이션에 할당된 또는 할당되지 않은 사용자가 애플리케이션에 로그인하는 방법을 결정합니다. 사용자가 액세스 패널에서 애플리케이션을 볼 수 있는지 여부도 결정합니다.

    - **사용자가 로그인할 수 있나요?** 는 애플리케이션에 할당된 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자 할당이 필요하나요?** 는 애플리케이션에 할당되지 않은 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자에게 표시되나요?** 는 앱에 할당된 사용자가 액세스 패널 및 O365 시작 관리자에서 앱을 볼 수 있는지 여부를 결정합니다.

4. 다음 표를 사용하여 요구 사항에 가장 적합한 옵션을 선택하세요.

   - **할당된** 사용자에 대한 동작:

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

   - **할당되지 않은** 사용자에 대한 동작:

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

## <a name="use-a-custom-logo"></a>사용자 지정 로고 사용

사용자 지정 로고를 사용하려면:

1. 215 x 215 픽셀 로고를 만들고, PNG 형식으로 저장합니다.
1. 이미 애플리케이션을 찾았으니, 해당 애플리케이션을 선택합니다.
1. 왼쪽 창에서 **속성**을 선택합니다.
1. 로고를 업로드합니다.
1. 완료되면 **저장**을 선택합니다. 

    ![앱의 속성 페이지에서 로고를 변경하는 방법을 보여 줍니다.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > 이 **속성** 창에 표시되는 썸네일은 즉시 업데이트되지 않습니다. 속성을 닫았다가 다시 열어야 업데이트된 아이콘을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD 조직에 애플리케이션을 추가했으므로, 이제 사용할 [Single Sign-On 방법을 선택](what-is-single-sign-on.md#choosing-a-single-sign-on-method)하고 아래에서 적절한 문서를 참조합니다.

- [SAML 기반 Single Sign-On 구성](configure-single-sign-on-non-gallery-applications.md)
- [암호 Single Sign-On 구성](configure-password-single-sign-on-non-gallery-applications.md)
- [연결된 로그온 구성](configure-linked-sign-on.md)
