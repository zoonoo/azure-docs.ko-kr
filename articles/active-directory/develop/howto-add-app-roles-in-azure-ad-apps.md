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
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706016"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>방법: 애플리케이션에 앱 역할 추가 및 토큰으로 수신

RBAC(역할 기반 액세스 제어)는 애플리케이션에서 권한 부여를 적용하는 데 널리 사용되는 메커니즘입니다. RBAC를 사용하는 경우, 관리자는 개별 사용자나 그룹이 아닌 역할에 사용 권한을 부여합니다. 그런 다음 관리자는 다른 사용자 및 그룹에 역할을 할당하여 누가 어떤 컨텐츠와 기능에 액세스 권한을 갖는지를 제어할 수 있습니다.

응용 프로그램 역할 및 역할 클레임에서 RBAC를 사용 하 여 개발자는 자신의 앱에서 권한 부여를 안전 하 게 적용할 수 있습니다.

또 다른 방법은 GitHub의 [WebApp-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) 코드 샘플에 나와 있는 것 처럼 Azure AD 그룹 및 그룹 클레임을 사용 하는 것입니다. Azure AD 그룹 및 응용 프로그램 역할은 함께 사용할 수 없습니다. 이러한 기능을 함께 사용 하 여 보다 세분화 된 액세스 제어를 제공할 수 있습니다.

## <a name="declare-roles-for-an-application"></a>애플리케이션에 대한 역할 선언

응용 프로그램 역할은 [Azure Portal](https://portal.azure.com)에서 정의 됩니다.  사용자가 애플리케이션에 로그인하면 Azure AD는 사용자가 사용자에게 개별적으로 부여한 역할 및 자신의 그룹 멤버 자격에서 부여한 각각의 역할에 대해 `roles` 클레임을 발행합니다.  역할에 사용자 및 그룹을 할당하는 작업은 포털의 UI를 통해 수행하거나 [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview)를 사용하여 프로그래밍 방식으로 수행할 수 있습니다.

앱 역할을 만들려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 앱 역할을 추가 하려는 앱 등록을 포함 하는 Azure Active Directory 테 넌 트를 선택 합니다.
1. **Azure Active Directory**를 검색하고 선택합니다.
1. **관리**에서 **앱 등록**를 선택 하 고 응용 프로그램 역할을 정의 하려는 응용 프로그램을 선택 합니다.
1. **앱 역할 선택 | 미리 보기**를 선택한 다음, **앱 역할 만들기**를 선택 합니다.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure Portal의 앱 등록 앱 역할 창":::
1. **앱 역할 만들기** 창에서 역할에 대 한 설정을 입력 합니다. 이미지 다음 표는 각 설정 및 해당 매개 변수를 설명 합니다.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="앱 등록의 앱 역할 Azure Portal에 컨텍스트 창 만들기":::

    | 필드 | Description | 예 |
    |-------|-------------|---------|
    | **표시 이름** | 관리자 승인 및 앱 할당 환경에 표시 되는 앱 역할의 표시 이름입니다. 이 값에는 공백이 포함 될 수 있습니다.  | `Survey Writer` |
    | **허용 되는 멤버 유형** | 이 앱 역할을 사용자, 응용 프로그램 또는 둘 다에 할당할 수 있는지 여부를 지정 합니다. | `Users/Groups` |
    | **값** | 응용 프로그램이 토큰에서 필요로 하는 역할 클레임의 값을 지정 합니다. 값은 응용 프로그램 코드에서 참조 된 문자열과 정확 하 게 일치 해야 합니다. 값에는 공백을 사용할 수 없습니다. | `Survey.Create` |
    | **설명** | 관리 앱 할당 및 동의 환경에서 표시 되는 앱 역할에 대 한 자세한 설명입니다. | `Writers can create surveys.` |
    | **이 앱 역할을 사용 하도록 설정 하 시겠습니까?** | 앱 역할의 사용 여부를 지정 합니다. 앱 역할을 삭제 하려면이 확인란의 선택을 취소 하 고 삭제 작업을 시도 하기 전에 변경 내용을 적용 합니다. | *선택* |

1. **적용**을 선택하여 변경 내용을 저장합니다.

> [!NOTE]
> 추가한 역할의 수는 응용 프로그램 매니페스트에 대해 정의 된 한도에 대해 계산 됩니다. 이러한 제한에 대 한 자세한 내용은 [Azure Active Directory 앱 매니페스트 참조](reference-app-manifest.md)의 [매니페스트 제한](./reference-app-manifest.md#manifest-limits) 섹션을 참조 하세요.

## <a name="assign-users-and-groups-to-roles"></a>역할에 사용자 및 그룹 할당

응용 프로그램에 앱 역할을 추가 하면 사용자 및 그룹을 역할에 할당할 수 있습니다.

1. Azure Portal **Azure Active Directory** 의 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.
1. **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

     응용 프로그램이 목록에 표시 되지 않으면 **모든 응용 프로그램** 목록의 맨 위에 있는 필터를 사용 하 여 목록을 제한 하거나 목록을 아래로 스크롤하여 응용 프로그램을 찾습니다.

1. 역할에 사용자 또는 보안 그룹을 할당할 애플리케이션을 선택합니다.
1. **관리**에서 **사용자 및 그룹**을 선택합니다.
1. **사용자 추가** 를 선택 하 여 **할당 추가** 창을 엽니다.
1. **할당 추가** 창에서 **사용자 및 그룹** 선택기를 선택합니다.

     사용자 및 보안 그룹의 목록이 표시 됩니다. 특정 사용자 또는 그룹을 검색 하 고 목록에 표시 되는 여러 사용자 및 그룹을 선택할 수 있습니다.

1. 사용자 및 그룹을 선택한 후 **선택** 단추를 선택 하 여 계속 합니다.
1. **할당 추가** 창에서 **역할 선택** 을 선택 합니다. 응용 프로그램에 대해 정의한 모든 역할이 표시 됩니다.
1. 역할을 선택 하 고 **선택** 단추를 선택 합니다.
1. **할당** 단추를 선택 하 여 앱에 대 한 사용자 및 그룹 할당을 완료 합니다.
1. 추가한 사용자 및 그룹이 **사용자 및 그룹** 목록에 표시 되는지 확인 합니다.

## <a name="receive-roles-in-tokens"></a>토큰에서 역할 수신

다양 한 앱 역할에 할당 된 사용자가 응용 프로그램에 로그인 하면 해당 토큰에는 클레임에 할당 된 역할이 있습니다 `roles` .

## <a name="web-api-application-permissions"></a>Web API 응용 프로그램 사용 권한

**사용자/그룹**, **응용 프로그램**또는 **둘 다**를 대상으로 하는 앱 역할을 정의할 수 있습니다. **응용 프로그램** 또는 **둘 다**선택 하는 경우 앱 역할은 클라이언트 앱의 **API 권한**에서 응용 프로그램 권한으로 표시 됩니다.

**응용 프로그램** 을 대상으로 하거나 **둘 다**를 대상으로 하는 역할을 정의한 후 클라이언트 앱 등록에서 응용 프로그램 권한을 선택 하려면 다음을 수행 합니다.

1. Azure Portal **Azure Active Directory** 에서 **앱 등록**를 선택한 다음 클라이언트 앱의 등록을 선택 합니다.
1. **관리** 아래에서 **API 권한**을 선택합니다.
1. 내 **api 추가 권한 추가를**선택  >  **My APIs**합니다.
1. 응용 프로그램 역할을 추가한 앱을 선택 하 고 **응용 프로그램 사용 권한**을 선택 합니다.

## <a name="next-steps"></a>다음 단계

다음 리소스를 사용 하 여 앱 역할에 대해 자세히 알아보세요.

- 코드 샘플: [앱 역할을 사용 하 여 권한 부여 추가 & 역할 클레임 ASP.NET Core 웹 앱에 대 한 클레임](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (GitHub)
- 비디오: [Microsoft id 플랫폼 (1:01:15)을 사용 하 여 응용 프로그램에서 권한 부여 구현](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory 앱 매니페스트](./reference-app-manifest.md)
- [Azure AD 액세스 토큰](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
