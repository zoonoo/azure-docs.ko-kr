---
title: 웹 API를 공개하는 애플리케이션 구성 | Azure
description: 애플리케이션이 새 권한/범위와 역할을 공개하여 해당 애플리케이션을 클라이언트 애플리케이션이 사용할 수 있게 구성하는 방법을 살펴봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 589ad189a3a157d0116e3991f8df3d6b43afc167
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988777"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis-preview"></a>빠른 시작: 웹 API를 공개하는 애플리케이션 구성(미리 보기)

웹 API를 개발하고 [권한/범위](developer-glossary.md#scopes) 및 [역할](developer-glossary.md#roles)을 공개하여 클라이언트 애플리케이션에서 사용할 수 있게 만들 수 있습니다. 올바르게 구성된 웹 API는 Graph API 및 Office 365 API와 같은 다른 Microsoft 웹 API와 마찬가지로 사용 가능합니다.

이 빠른 시작에서는 클라이언트 애플리케이션이 사용할 수 있게 새 범위를 공개하도록 애플리케이션을 구성하는 방법을 살펴봅니다.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음과 같은 필수 구성을 완료했는지 확인합니다.

* 지원되는 [권한 및 동의](v2-permissions-and-consent.md)를 살펴봅니다. 여기서는 다른 사용자 또는 애플리케이션에서 사용해야 하는 애플리케이션을 빌드하는 경우를 이해하는 것이 중요합니다.
* 애플리케이션이 등록된 테넌트가 있습니다.
  * 앱이 등록되지 않았다면 [Microsoft ID 플랫폼에 애플리케이션을 등록하는 방법](quickstart-register-app.md)을 살펴봅니다.
* Azure Portal에서 앱 등록 미리 보기 환경을 옵트인합니다. 이 빠른 시작의 단계는 새 UI에 해당하며 미리 보기 환경을 옵트인한 경우에만 작동합니다.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Azure Portal에 로그인하고 앱 선택

앱을 구성하려면 먼저 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록(미리 보기)** 을 차례로 선택합니다.
1. 구성하려는 애플리케이션을 찾아 선택합니다. 앱을 선택하면 볼 **개요** 또는 기본 등록 페이지가 나타납니다.
1. 새 범위를 공개하는 데 사용할 방법, UI 또는 애플리케이션 매니페스트를 선택합니다.
    * [UI를 통해 새 범위 공개](#expose-a-new-scope-through-the-ui)
    * [응용 프로그램 매니페스트를 통해 새 범위 또는 역할 공개](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>UI를 통해 새 범위 공개

[![UI를 사용하여 API 공개](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

UI를 통해 새 범위를 공개하려면

1. 앱의 **개요** 페이지에서 **API 공개** 섹션을 선택합니다.

1. **범위 추가**를 선택합니다.

1. **응용 프로그램 ID URI**를 설정하지 않은 경우 입력하라는 메시지가 표시됩니다. 애플리케이션 ID URI를 입력하거나, 제공된 항목을 사용한 다음, **저장 및 계속**을 선택합니다.

1. **범위 추가** 페이지가 나타나면 범위 정보를 입력합니다.

    | 필드 | 설명 |
    |-------|-------------|
    | **범위 이름** | 의미 있는 범위 이름을 입력합니다.<br><br>예: `Employees.Read.All`. |
    | **동의할 수 있는 사람** | 범위를 사용자가 동의할 수 있는지 또는 관리자 동의가 필요한지 선택합니다. 높은 권한을 적용하려면 **관리자만**을 선택합니다. |
    | **관리자 동의 표시 이름** | 관리자에게 표시되는 의미 있는 범위 설명을 입력합니다.<br><br>예를 들어 `Read-only access to Employee records` |
    | **관리자 동의 설명** | 관리자에게 표시되는 의미 있는 범위 설명을 입력합니다.<br><br>예를 들어 `Allow the application to have read-only access to all Employee data.` |

    사용자가 범위에 동의할 수 있는 경우 다음 필드에 대한 값도 추가합니다.

    | 필드 | 설명 |
    |-------|-------------|
    | **사용자 동의 표시 이름** | 사용자에게 표시되는 의미 있는 범위 이름을 입력합니다.<br><br>예를 들어 `Read-only access to your Employee records` |
    | **사용자 동의 설명** | 사용자에게 표시되는 의미 있는 설명을 입력합니다.<br><br>예를 들어 `Allow the application to have read-only access to your Employee data.` |

1. **상태**를 설정하고 마치면 **범위 추가**를 선택합니다.

1. [웹 API가 다른 응용 프로그램에 공개되는지 확인](#verify-the-web-api-is-exposed-to-other-applications)하기 위한 단계를 따릅니다.

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>애플리케이션 매니페스트를 통해 새 범위 또는 역할 공개

[![매니페스트에서 oauth2Permissions 컬렉션을 사용하여 새 범위를 공개합니다.](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

애플리케이션 매니페스트를 통해 새 범위 또는 역할을 공개하려면

1. 앱의 **개요** 페이지에서 **매니페스트** 섹션을 선택합니다. 웹 기반 매니페스트 편집기가 열리면 포털 내에서 매니페스트를 **편집**할 수 있습니다. 필요에 따라 **다운로드**를 선택하고 로컬로 매니페스트를 편집하고 **업로드**를 사용하여 애플리케이션에 다시 적용할 수 있습니다.
    
    다음 예제에서는 `oauth2Permissions` 컬렉션에 다음 JSON 요소를 추가하여 리소스/API에서 `Employees.Read.All`이라는 새 범위를 공개합니다.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

  > [!NOTE]
  > `id` 값은 [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx)과 같은 GUID 생성 도구를 사용하거나 프로그래밍 방식으로 만들어야 합니다. `id`는 웹 API에 의해 노출되는 범위에서 고유한 식별자를 나타냅니다. 웹 API에 액세스할 수 있는 권한이 있는 클라이언트를 적절하게 구성하면 Azure AD에서 OAuth 2.0 액세스 토큰을 발급합니다. 클라이언트가 웹 API를 호출하면 해당 애플리케이션 등록에서 요청된 권한으로 설정된 범위(scp) 클레임을 갖는 액세스 토큰을 표시합니다.
  >
  > 추가 범위를 나중에 필요한 대로 노출할 수 있습니다. 웹 API에서 다양한 기능과 관련된 여러 범위를 공개할 수도 있음을 고려하세요. 리소스에서는 OAuth 2.0 액세스 토큰에서 수신된 범위(`scp`) 클레임을 평가하여 런타임 시 웹 API에 대한 액세스를 제어할 수 있습니다.

1. 완료되면 **저장**을 클릭합니다. 이제 Web API가 디렉토리에 있는 다른 애플리케이션에 의해 사용되도록 구성되었습니다.
1. [웹 API가 다른 응용 프로그램에 공개되는지 확인](#verify-the-web-api-is-exposed-to-other-applications)하기 위한 단계를 따릅니다.

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>웹 API가 다른 애플리케이션에 공개되는지 확인합니다.

1. Azure AD 테넌트로 돌아가서 **앱 등록**을 다시 선택한 다음, 구성하려는 클라이언트 애플리케이션을 찾아 선택합니다.
1. [웹 API에 액세스하는 클라이언트 응용 프로그램 구성](#configure-a-client-application-to-access-web-apis)에서 개요로 설명한 단계를 반복합니다.
1. **API 선택** 단계가 되면 리소스를 선택합니다. 클라이언트 권한 요청에 사용할 수 있는 새 범위가 표시되어야 합니다.

## <a name="more-on-the-application-manifest"></a>애플리케이션 매니페스트에 대한 추가 정보

애플리케이션 매니페스트는 Azure AD 애플리케이션의 ID 구성의 모든 특성을 정의하는 애플리케이션 엔터티를 업데이트하기 위한 메커니즘으로 사용됩니다. 애플리케이션 엔터티 및 해당 스키마에 대한 자세한 내용은 [Graph API 애플리케이션 엔터티 설명서](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)를 참조하세요. 이 문서에는 다음을 비롯하여 API에 대한 사용 권한을 지정하는 데 사용되는 애플리케이션 엔터티 멤버에 대한 전체 참조 정보가 포함됩니다.  

* 웹 API에 대한 [애플리케이션 권한](developer-glossary.md#permissions)을 정의하는 데 사용되는 [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) 엔터티의 컬렉션인 appRoles 멤버
* 웹 API에 대한 [위임된 권한](developer-glossary.md#permissions)을 정의하는 데 사용되는 [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) 엔터티의 컬렉션인 oauth2Permissions 멤버

일반적인 애플리케이션 매니페스트 개념에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 매니페스트 이해](reference-app-manifest.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

앱에 대한 다른 관련 앱 관리 빠른 시작에 대해 알아봅니다.

* [Microsoft ID 플랫폼을 사용하여 응용 프로그램 등록](quickstart-register-app.md)
* [웹 API에 액세스하는 클라이언트 응용 프로그램 구성](quickstart-configure-app-access-web-apis.md)
* [응용 프로그램에서 지원되는 계정 수정](quickstart-modify-supported-accounts.md)
* [Microsoft ID 플랫폼을 사용하여 등록된 응용 프로그램 제거](quickstart-remove-app.md)

등록된 애플리케이션 및 이들 간의 관계를 나타내는 두 개의 Azure AD 개체에 대한 자세한 내용은 [애플리케이션 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

Azure Active Directory를 사용해 애플리케이션을 개발할 때 사용해야 하는 브랜딩 지침에 대해 자세히 알아보려면 [애플리케이션에 대한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 참조하세요.