---
title: 보호 된 웹 API-앱 등록 | Microsoft
description: 보호 된 웹 API와 앱을 등록 하는 데 필요한 정보를 빌드하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbccfc38a4e5e4b31cb625c614e838a3c92e7429
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562311"
---
# <a name="protected-web-api-app-registration"></a>보호 된 웹 API: 앱 등록

이 문서에서는 보호 된 web API에 대 한 앱 등록의 세부 사항을 설명 합니다.

[빠른 시작: 앱을 등록 하는 일반적인 단계를](quickstart-register-app.md) 위해 Microsoft id 플랫폼에 응용 프로그램을 등록 합니다.

## <a name="accepted-token-version"></a>수락 된 토큰 버전

Microsoft id 플랫폼 끝점은 두 가지 유형의 토큰을 발급할 수 있습니다. v 1.0 토큰 및 v2.0 토큰 이러한 토큰에 대 한 자세한 내용은 [액세스 토큰](access-tokens.md)을 참조 하세요. 허용 되는 토큰 버전은 응용 프로그램을 만들 때 선택한 **지원 되는 계정 유형에** 따라 달라 집니다.

- **지원 되는 계정 유형** 값이 **모든 조직 디렉터리 및 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com)의 계정인**경우 수락 된 토큰 버전은 v 2.0이 됩니다.
- 그렇지 않으면 허용 된 토큰 버전이 v 1.0이 됩니다.

응용 프로그램을 만든 후 다음 단계를 수행 하 여 허용 된 토큰 버전을 확인 하거나 변경할 수 있습니다.

1. Azure Portal에서 앱을 선택 하 고 앱에 대 한 **매니페스트** 를 선택 합니다.
2. 매니페스트에서 **"accessTokenAcceptedVersion"** 를 검색 합니다. 값은 **2**입니다. 이 속성은 웹 API에서 v2.0 토큰을 허용 하는 Azure Active Directory (Azure AD)를 지정 합니다. 값이 **null**이면 허용 되는 토큰 버전은 v 1.0입니다.
3. 토큰 버전을 변경한 경우 **저장**을 선택 합니다.

> [!NOTE]
> Web API는 허용 되는 토큰 버전 (v 1.0 또는 v2.0)을 지정 합니다. 클라이언트가 v2.0 (Microsoft identity platform) 끝점에서 web API에 대 한 토큰을 요청 하는 경우 웹 API에서 허용 하는 버전을 나타내는 토큰을 받게 됩니다.

## <a name="no-redirect-uri"></a>리디렉션 URI 없음

사용자가 대화형으로 로그인 하지 않았으므로 웹 Api에서 리디렉션 URI를 등록할 필요가 없습니다.

## <a name="expose-an-api"></a>API 표시

웹 Api에 특정 한 다른 설정은 노출 된 API 및 노출 된 범위입니다.

### <a name="resource-uri-and-scopes"></a>리소스 URI 및 범위

범위는 일반적으로 형식 `resourceURI/scopeName`입니다. Microsoft Graph의 경우 범위에는와 같은 `User.Read`바로 가기가 있습니다. 이 문자열은의 `https://graph.microsoft.com/user.read`바로 가기입니다.

앱을 등록 하는 동안 다음 매개 변수를 정의 해야 합니다.

- 리소스 URI 기본적으로 응용 프로그램 등록 포털에서를 사용 `api://{clientId}`하는 것이 좋습니다. 이 리소스 URI는 고유 하지만 사람이 읽을 수는 없습니다. 이를 변경할 수 있지만 새 값이 고유한 지 확인 합니다.
- 하나 이상의 *범위*입니다. 클라이언트 응용 프로그램에는 web API에 대 한 *위임 된 권한* 으로 표시 됩니다.
- 하나 이상의 *앱 역할*. 클라이언트 응용 프로그램에는 web API에 대 한 *응용 프로그램 권한* 으로 표시 됩니다.

범위는 앱의 최종 사용자에 게 표시 되는 동의 화면에도 표시 됩니다. 따라서 범위를 설명 하는 해당 문자열을 제공 해야 합니다.

- 최종 사용자에 게 표시 됩니다.
- 테 넌 트 관리자에 게 표시 되는 대로 관리자 동의를 허용할 수 있습니다.

### <a name="exposing-delegated-permissions-scopes"></a>위임 된 권한 (범위) 노출

1. 응용 프로그램 등록에서 **API 노출** 섹션을 선택 합니다.
1. **범위 추가**를 선택합니다.
1. 메시지가 표시 되 면 **저장 후 계속**을 선택 하`api://{clientId}`여 제안 된 응용 프로그램 ID URI ()를 적용 합니다.
1. 다음 매개 변수를 입력 합니다.
      - **범위 이름**에 **access_as_user**를 사용 합니다.
      - **사용자가 동의할 수 있는 사용자**는 **관리자 및 사용자** 가 선택 되어 있는지 확인 합니다.
      - **관리자 승인 표시 이름**에 **액세스 TodoListService를 사용자로**입력 합니다.
      - **관리자 동의 설명**에서 enter 키를 **누르면 사용자로 TodoListService Web API에 액세스**합니다.
      - **사용자 승인 표시 이름**에 **액세스 TodoListService를 사용자로**입력 합니다.
      - **사용자 동의 설명**에서 enter 키를 **누르면 사용자로 TodoListService Web API에 액세스**합니다.
      - **상태** 를 **사용**으로 설정 된 상태로 유지 합니다.
      - **범위 추가**를 선택 합니다.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>웹 API가 디먼 앱에 의해 호출 되는 경우

이 섹션에서는 디먼 앱에서 안전 하 게 호출할 수 있도록 보호 된 웹 API를 등록 하는 방법에 대해 알아봅니다.

- *응용 프로그램 사용 권한을*노출 해야 합니다. 디먼 앱은 사용자와 상호 작용 하지 않으므로 위임 된 권한이 적절 하지 않기 때문에 응용 프로그램 권한만 선언 합니다.
- 테 넌 트 관리자는 web api의 응용 프로그램 사용 권한 중 하나에 액세스 하도록 등록 된 응용 프로그램에만 웹 API에 대 한 토큰을 발급 하는 Azure Active Directory (Azure AD)를 요구할 수 있습니다.

#### <a name="exposing-application-permissions-app-roles"></a>응용 프로그램 사용 권한 노출 (앱 역할)

응용 프로그램 사용 권한을 노출 하려면 매니페스트를 편집 해야 합니다.

1. 응용 프로그램에 대 한 응용 프로그램 등록에서 **매니페스트**를 선택 합니다.
1. `appRoles` 설정을 찾아 하나 이상의 응용 프로그램 역할을 추가 하 여 매니페스트를 편집 합니다. 다음 샘플 JSON 블록에서 역할 정의가 제공 됩니다. 만으로 `allowedMemberTypes` `"Application"` 설정 된 상태로 둡니다. `id` 이 고유한 GUID `displayName` 이며`value` 공백을 포함 하지 않는지 확인 합니다.
1. 매니페스트를 저장합니다.

다음 샘플에서는의 `appRoles`내용을 보여 줍니다. 는 `id` 임의의 고유 GUID 일 수 있습니다.

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Azure AD에서 허용 되는 클라이언트만 웹 API에 대 한 토큰을 발급 하도록 보장

웹 API는 앱 역할을 확인 합니다. 응용 프로그램 사용 권한을 노출 하는 개발자 방법입니다. 그러나 API에 액세스 하기 위해 테 넌 트 관리자가 승인한 앱에만 web API에 대 한 토큰을 발급 하도록 Azure AD를 구성할 수도 있습니다. 이 강화 된 보안을 추가 하려면 다음을 수행 합니다.

1. 앱 등록에 대 한 앱 **개요** 페이지에서 **로컬 디렉터리의 관리 되는 응용 프로그램**아래에 있는 앱의 이름과 링크를 선택 합니다. 이 필드의 제목이 잘릴 수 있습니다. 예를 들어 **에서 관리 되는 응용 프로그램** 을 볼 수 있습니다.

   > [!NOTE]
   >
   > 이 링크를 선택 하면 응용 프로그램을 만든 테 넌 트의 응용 프로그램에 대 한 서비스 사용자와 연결 된 **엔터프라이즈 응용 프로그램 개요** 페이지로 이동 합니다. 브라우저의 뒤로 단추를 사용 하 여 앱 등록 페이지로 돌아갈 수 있습니다.

1. 엔터프라이즈 응용 프로그램 페이지의 **관리** 섹션에서 **속성** 페이지를 선택 합니다.
1. Azure AD에서 특정 클라이언트만 웹 API에 대 한 액세스를 허용 하도록 하려면 **사용자 할당 필요?** 를 **예**로 설정 합니다.

   > [!IMPORTANT]
   >
   > **사용자 할당 필요 여부** 를 **예**로 설정 하는 경우 AZURE AD는 웹 API에 대 한 액세스 토큰을 요청할 때 클라이언트의 앱 역할 할당을 확인 합니다. 클라이언트가 앱 역할에 할당 되지 않은 경우 Azure AD에서 오류 `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`를 반환 합니다.
   >
   > **사용자 할당** 을 유지 해야 하는 경우 **아니요**로 설정 하면 *클라이언트가 웹 API에 대 한 액세스 토큰을 요청할 때 Azure AD에서 앱 역할 할당을 확인 하지 않습니다*. 모든 데몬 클라이언트 (즉, 클라이언트 자격 증명 흐름을 사용 하는 모든 클라이언트)는 대상 그룹을 지정 하는 것 만으로 API에 대 한 액세스 토큰을 가져올 수 있습니다. 모든 응용 프로그램은이에 대 한 권한을 요청 하지 않고도 API에 액세스할 수 있습니다. 그러나 이전 섹션에서 설명한 대로 웹 API는 항상 응용 프로그램에 올바른 역할이 있는지 확인 합니다 (테 넌 트 관리자에 의해 권한이 부여 됨). API는 액세스 토큰에 역할 클레임이 있는지 확인 하 고이 클레임에 대 한 값이 올바른지 확인 하 여이 확인을 수행 합니다. 이 `access_as_application`경우 값은입니다.

1.           **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-protected-web-api-app-configuration.md)
