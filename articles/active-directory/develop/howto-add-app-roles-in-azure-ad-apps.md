---
title: 앱 역할 추가 및 토큰에서 가져오기 | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory에 등록 된 응용 프로그램에 앱 역할을 추가 하 고, 사용자 및 그룹을 이러한 역할에 할당 하 고, 토큰의 ' roles ' 클레임에서 수신 하는 방법에 대해 알아봅니다.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 96c52c46a75d6d5810dfddf91439c275d14e85f1
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616140"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>방법: 응용 프로그램에 앱 역할을 추가 하 고 토큰에서 수신

RBAC(역할 기반 액세스 제어)는 애플리케이션에서 권한 부여를 적용하는 데 널리 사용되는 메커니즘입니다. RBAC를 사용하는 경우, 관리자는 개별 사용자나 그룹이 아닌 역할에 사용 권한을 부여합니다. 그런 다음 관리자는 다른 사용자 및 그룹에 역할을 할당하여 누가 어떤 컨텐츠와 기능에 액세스 권한을 갖는지를 제어할 수 있습니다.

응용 프로그램 역할 및 역할 클레임에서 RBAC를 사용 하면 개발자는 더 짧은 노력으로 앱에서 권한 부여를 안전 하 게 적용할 수 있습니다.

또 다른 방법은 GitHub의 [active directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) 코드 샘플에 표시 된 대로 Azure AD 그룹 및 그룹 클레임을 사용 하는 것입니다. Azure AD 그룹 및 응용 프로그램 역할은 함께 사용할 수 없습니다. 이러한 기능을 함께 사용 하 여 훨씬 세부적인 액세스 제어를 제공할 수 있습니다.

## <a name="declare-roles-for-an-application"></a>애플리케이션에 대한 역할 선언

[Azure Portal](https://portal.azure.com)를 사용 하 여 앱 역할을 정의 합니다. 사용자가 응용 프로그램에 로그인 하면 Azure AD는 사용자에 `roles` 게 개별적으로 부여 된 각 역할에 대 한 클레임을 사용자와 그룹 구성원 자격으로 내보냅니다.

Azure Portal를 사용 하 여 앱 역할을 선언 하는 방법에는 두 가지가 있습니다.

* [앱 역할 UI](#app-roles-ui--preview) | 미리 보기
* [응용 프로그램 매니페스트 편집기](#app-manifest-editor)

추가 하는 역할 수는 Azure Active Directory에 의해 적용 되는 응용 프로그램 매니페스트 제한에 따라 계산 됩니다. 이러한 제한에 대 한 자세한 내용은 [Azure Active Directory 앱 매니페스트 참조](reference-app-manifest.md)의 [매니페스트 제한](./reference-app-manifest.md#manifest-limits) 섹션을 참조 하세요.

### <a name="app-roles-ui--preview"></a>앱 역할 UI | 미리 보기

> [!IMPORTANT]
> 앱 역할 포털 UI 기능 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Azure Portal의 사용자 인터페이스를 사용 하 여 앱 역할을 만들려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 앱 역할을 추가 하려는 앱 등록을 포함 하는 Azure Active Directory 테 넌 트를 선택 합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 에서 **앱 등록** 를 선택 하 고 응용 프로그램 역할을 정의 하려는 응용 프로그램을 선택 합니다.
1. **앱 역할 선택 | 미리 보기** 를 선택한 다음, **앱 역할 만들기** 를 선택 합니다.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure Portal의 앱 등록 앱 역할 창":::
1. **앱 역할 만들기** 창에서 역할에 대 한 설정을 입력 합니다. 이미지 다음 표는 각 설정 및 해당 매개 변수를 설명 합니다.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="앱 등록의 앱 역할 Azure Portal에 컨텍스트 창 만들기":::

    | 필드 | Description | 예제 |
    |-------|-------------|---------|
    | **표시 이름** | 관리자 승인 및 앱 할당 환경에 표시 되는 앱 역할의 표시 이름입니다. 이 값에는 공백이 포함 될 수 있습니다. | `Survey Writer` |
    | **허용 되는 멤버 유형** | 이 앱 역할을 사용자, 응용 프로그램 또는 둘 다에 할당할 수 있는지 여부를 지정 합니다.<br/><br/>에서 사용할 수 있는 경우 `applications` 앱 역할은 api 권한 > 앱 등록의 **관리** 섹션에서 응용 프로그램 권한으로 표시 **> Api > 사용 권한을 추가 > api > 응용 프로그램 사용 권한을 선택할** 수 있습니다. | `Users/Groups` |
    | **값** | 응용 프로그램이 토큰에서 필요로 하는 역할 클레임의 값을 지정 합니다. 값은 응용 프로그램 코드에서 참조 된 문자열과 정확 하 게 일치 해야 합니다. 값에는 공백을 사용할 수 없습니다. | `Survey.Create` |
    | **설명** | 관리 앱 할당 및 동의 환경에서 표시 되는 앱 역할에 대 한 자세한 설명입니다. | `Writers can create surveys.` |
    | **이 앱 역할을 사용 하도록 설정 하 시겠습니까?** | 앱 역할을 사용 하도록 설정할지 여부를 지정 합니다. 앱 역할을 삭제 하려면이 확인란의 선택을 취소 하 고 삭제 작업을 시도 하기 전에 변경 내용을 적용 합니다. | *검사할* |

1. **적용** 을 선택하여 변경 내용을 저장합니다.

### <a name="app-manifest-editor"></a>응용 프로그램 매니페스트 편집기

매니페스트를 직접 편집 하 여 역할을 추가 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 앱 역할을 추가 하려는 앱 등록을 포함 하는 Azure Active Directory 테 넌 트를 선택 합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 에서 **앱 등록** 를 선택 하 고 응용 프로그램 역할을 정의 하려는 응용 프로그램을 선택 합니다.
1. **관리** 아래에서 **매니페스트** 를 선택 합니다.
1. `appRoles`설정을 찾고 응용 프로그램 역할을 추가 하 여 응용 프로그램 매니페스트를 편집 합니다. `users`, 또는 둘 다를 대상으로 하는 앱 역할을 정의할 수 있습니다 `applications` . 다음 JSON 코드 조각에서는 둘 다의 예제를 보여 줍니다.
1. 매니페스트를 저장합니다.

매니페스트의 각 앱 역할 정의에는 해당 값에 대 한 고유 GUID가 있어야 합니다 `id` .

각 앱 역할 정의의 `value` 속성은 애플리케이션의 코드에 사용되는 문자열과 정확히 일치해야 합니다. `value` 속성은 공백을 포함할 수 없습니다. 이 경우 매니페스트를 저장하면 오류가 표시됩니다.

#### <a name="example-user-app-role"></a>예: 사용자 앱 역할

이 예제에서는에 할당할 수 있는 라는 앱 역할을 정의 합니다 `Writer` `User` .

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>예: 응용 프로그램 응용 프로그램 역할

에서 사용할 수 있는 경우 `applications` 앱 역할은 api 권한 > 앱 등록의 **관리** 섹션에서 응용 프로그램 권한으로 표시 **> Api > 사용 권한을 추가 > api > 응용 프로그램 사용 권한을 선택할** 수 있습니다.

이 예제에서는를 대상으로 하는 앱 역할을 보여 줍니다 `Application` .

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>역할에 사용자 및 그룹 할당

응용 프로그램에 앱 역할을 추가 하면 사용자 및 그룹을 역할에 할당할 수 있습니다. 역할에 사용자 및 그룹을 할당하는 작업은 포털의 UI를 통해 수행하거나 [Microsoft Graph](/graph/api/user-post-approleassignments)를 사용하여 프로그래밍 방식으로 수행할 수 있습니다. 다양 한 앱 역할에 할당 된 사용자가 응용 프로그램에 로그인 하면 해당 토큰에는 클레임에 할당 된 역할이 있습니다 `roles` .

Azure Portal를 사용 하 여 사용자 및 그룹을 역할에 할당 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** 의 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.
1. **모든 애플리케이션** 을 선택하여 모든 애플리케이션 목록을 봅니다. 응용 프로그램이 목록에 표시 되지 않으면 **모든 응용 프로그램** 목록의 맨 위에 있는 필터를 사용 하 여 목록을 제한 하거나 목록을 아래로 스크롤하여 응용 프로그램을 찾습니다.
1. 역할에 사용자 또는 보안 그룹을 할당할 애플리케이션을 선택합니다.
1. **관리** 에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택 하 여 **할당 추가** 창을 엽니다.
1. **할당 추가** 창에서 **사용자 및 그룹** 선택기를 선택합니다. 사용자 및 보안 그룹의 목록이 표시 됩니다. 특정 사용자 또는 그룹을 검색 하 고 목록에 표시 되는 여러 사용자 및 그룹을 선택할 수 있습니다.
1. 사용자 및 그룹을 선택한 후 **선택** 단추를 선택 하 여 계속 합니다.
1. **할당 추가** 창에서 **역할 선택** 을 선택 합니다. 응용 프로그램에 대해 정의한 모든 역할이 표시 됩니다.
1. 역할을 선택 하 고 **선택** 단추를 선택 합니다.
1. **할당** 단추를 선택 하 여 앱에 대 한 사용자 및 그룹 할당을 완료 합니다.

추가한 사용자 및 그룹이 **사용자 및 그룹** 목록에 표시 되는지 확인 합니다.

## <a name="assign-app-roles-to-applications"></a>응용 프로그램에 앱 역할 할당

응용 프로그램에 앱 역할을 추가한 후에는 Azure Portal를 사용 하거나 [Microsoft Graph](/graph/api/user-post-approleassignments)를 사용 하 여 프로그래밍 방식으로 클라이언트 앱에 앱 역할을 할당할 수 있습니다.

응용 프로그램에 앱 역할을 할당 하는 경우 *응용 프로그램 사용 권한을* 만듭니다. 응용 프로그램 권한은 일반적으로 사용자의 상호 작용 없이 인증 하 고 권한 있는 API 호출을 수행 해야 하는 디먼 앱 또는 백 엔드 서비스에서 사용 됩니다.

Azure Portal를 사용 하 여 응용 프로그램에 앱 역할을 할당 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** 의 왼쪽 탐색 메뉴에서 **앱 등록** 를 선택 합니다.
1. **모든 애플리케이션** 을 선택하여 모든 애플리케이션 목록을 봅니다. 응용 프로그램이 목록에 표시 되지 않으면 **모든 응용 프로그램** 목록의 맨 위에 있는 필터를 사용 하 여 목록을 제한 하거나 목록을 아래로 스크롤하여 응용 프로그램을 찾습니다.
1. 앱 역할을 할당 하려는 응용 프로그램을 선택 합니다.
1. **API 권한** > **권한 추가** 를 선택합니다.
1. **내 api** 탭을 선택 하 고 앱 역할을 정의한 앱을 선택 합니다.
1. **애플리케이션 권한** 을 선택합니다.
1. 할당 하려는 역할을 선택 합니다.
1. **권한 추가** 단추를 선택 하 고 역할 추가를 완료 합니다.

새로 추가 된 역할이 앱 등록의 **API 권한** 창에 표시 됩니다.

#### <a name="grant-admin-consent"></a>관리자 동의 부여

위임 된 권한이 아닌 *응용 프로그램 권한* 이므로 관리자는 응용 프로그램에 할당 된 앱 역할을 사용 하는 데 동의를 부여 해야 합니다.

1. 앱 등록의 **API 권한** 창에서 **에 대 한 \<tenant name\> 관리자 동의 부여** 를 선택 합니다.
1. 요청 된 권한에 대 한 동의를 허용 하 라는 메시지가 표시 되 면 **예** 를 선택 합니다.

**상태** 열에는 **에 대 한 \<tenant name\> 동의가 부여** 되었음을 반영 해야 합니다.

## <a name="use-app-roles-in-your-web-api"></a>웹 API에서 앱 역할 사용

앱 역할을 정의 하 여 사용자, 그룹 또는 응용 프로그램에 할당 한 후에는 API가 호출 될 때 해당 역할을 확인 하는 코드를 웹 API에 추가 합니다. 즉, 클라이언트 앱이 권한 부여를 요구 하는 API 작업을 요청 하는 경우 API의 코드는 클라이언트 앱의 호출에 제공 된 액세스 토큰에 범위가 있는지 확인 해야 합니다.

웹 API에 권한 부여를 추가 하는 방법을 알아보려면 [보호 된 웹 api: 범위 및 앱 역할 확인](scenario-protected-web-api-verification-scope-app-roles.md)을 참조 하세요.

## <a name="app-roles-vs-groups"></a>앱 역할 및 그룹

권한 부여를 위해 앱 역할 또는 그룹을 사용할 수 있지만, 두 그룹의 주요 차이점은 시나리오에 사용할 수 있는 영향을 받을 수 있습니다.

| 앱 역할                                                                          | 그룹                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| 응용 프로그램에만 적용 되며 앱 등록에 정의 됩니다. 응용 프로그램과 함께 이동 합니다. | Azure AD 테 넌 트에만 적용 되는 것은 아닙니다. |
| 앱 역할은 앱 등록이 제거 될 때 제거 됩니다.                      | 앱이 제거 되는 경우에도 그룹은 그대로 유지 됩니다.            |
| 클레임에 제공 `roles` 됩니다.                                                     | 클레임에 제공 `groups` 됩니다.                                 |

개발자는 앱 역할을 사용 하 여 사용자가 앱에 로그인 할 수 있는지 여부를 제어할 수 있습니다. 또는 앱이 웹 API에 대 한 액세스 토큰을 가져올 수 있습니다. 이 보안 제어를 그룹으로 확장 하기 위해 개발자와 관리자는 앱 역할에 보안 그룹을 할당할 수도 있습니다.

응용 프로그램 역할은 응용 프로그램 자체에서 권한 부여의 매개 변수를 설명 하 고 제어 하려는 경우 개발자가 선호 합니다. 예를 들어 권한 부여를 위해 그룹을 사용 하는 앱은 그룹 ID와 이름이 다를 수 있으므로 다음 테 넌 트에서 중단 됩니다. 앱 역할을 사용 하는 앱은 안전 하 게 유지 됩니다. 실제로 앱 역할에 그룹을 할당 하는 것은 동일한 이유로 SaaS 앱에서 널리 사용 됩니다.

## <a name="next-steps"></a>다음 단계

다음 리소스를 사용 하 여 앱 역할에 대해 자세히 알아보세요.

* GitHub의 코드 샘플
  * [그룹 및 그룹 클레임을 사용 하 여 ASP.NET Core 웹 앱에 권한 부여 추가](https://aka.ms/groupssample)
  * [.NET Core web API를 호출 하 고 앱 역할 및 보안 그룹을 사용 하 여 단일 페이지 SPA (단일 페이지 응용 프로그램)](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* 참조 설명서
  * [Azure AD 응용 프로그램 매니페스트](./reference-app-manifest.md)
  * [Azure AD 액세스 토큰](access-tokens.md)
  * [Azure AD ID 토큰](id-tokens.md)
  * [앱에 선택적 클레임 제공](active-directory-optional-claims.md)
* 비디오: [Microsoft id 플랫폼 (1:01:15)을 사용 하 여 응용 프로그램에서 권한 부여 구현](https://www.youtube.com/watch?v=LRoc-na27l0)
