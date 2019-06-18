---
title: 보호 된 웹 API-앱 등록 | Azure
description: 보호 된 Web API 및 앱을 등록 하는 데 필요한 정보를 빌드하는 방법에 알아봅니다.
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
ms.openlocfilehash: 22fe71c38678ae789a93ecbc956f24f0b0ebeb01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111122"
---
# <a name="protected-web-api---app-registration"></a>보호 된 web API-앱 등록

이 문서는 보호 된 web API에 대 한 앱 등록 세부 정보를 설명합니다.

[빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 등록](quickstart-register-app.md) 응용 프로그램을 등록 하는 방법에는 일반적인 단계입니다.

## <a name="accepted-token-version"></a>허용 되는 토큰 버전

Microsoft id 플랫폼 끝점 두 가지 유형의 토큰을 발급할 수 있습니다: 토큰 v1.0 및 v2.0 토큰입니다. 이러한 토큰에 대 한 자세히 알아볼 수 있습니다 [액세스 토큰을](access-tokens.md)입니다. 허용 되는 토큰 버전에 따라 달라 집니다 합니다 **지원 되는 계정 유형** 응용 프로그램을 만들 때 선택한:

- 경우 값 **지원 되는 계정 유형** 됩니다 **모든 조직 디렉터리에 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com) 계정을**, 허용 되는 토큰 버전 v2.0 됩니다.
- 그렇지 않은 경우 허용 되는 토큰 버전 v1.0 됩니다.

응용 프로그램을 만든 후에 다음이 단계를 수행 하 여 허용 되는 토큰 버전을 변경할 수 있습니다.

1. Azure portal에서 앱을 선택 하 고 다음을 선택 합니다 **매니페스트** 앱에 대 한 합니다.
2. 검색할 매니페스트에서 **"accessTokenAcceptedVersion"** , 및 해당 값은 확인할 **2**합니다. 이 속성을 통해 Azure AD는 web API v2.0 토큰을 허용 하는지 알고 있어야 합니다. 있으면 **null**, 허용 되는 토큰 버전 v1.0 됩니다.
3. **저장**을 선택합니다.

> [!NOTE]
> 에 달려 허용 (v1.0 또는 v2.0) 토큰 버전을 결정 하는 웹 API입니다. 클라이언트 웹 Microsoft id 플랫폼 v2.0 끝점을 사용 하 여 API에 대 한 토큰을 요청 하면 웹 API에 의해 허용 되는 버전을 나타내는 토큰을 하 게 합니다.

## <a name="no-redirect-uri"></a>없는 리디렉션 URI

Web Api는 대화형으로 로그인 없는 사용자가 리디렉션 URI를 등록 하지 않아도 됩니다.

## <a name="expose-an-api"></a>API를 노출 합니다.

다른 설정은 웹 Api에 특정에 노출 된 API 및 노출 된 범위입니다.

### <a name="resource-uri-and-scopes"></a>리소스 URI 및 범위

범위는 일반적으로 폼의 `resourceURI/scopeName`합니다. Microsoft Graph에 대 한 범위 바로 가기가 같은 `User.Read`,이 문자열에 대 한 바로 가기 일 이지만 `https://graph.microsoft.com/user.read`합니다.

앱 등록 중 다음 매개 변수를 정의 해야 합니다.

- 기본 응용 프로그램 등록 포털에서 리소스 URI를 권장 하는 사용 하 여 `api://{clientId}`입니다. 이 리소스 URI는 고유 하지만 사람이 아닙니다 읽을 수 있습니다. 변경할 수 있지만 고유한 지 확인 합니다.
- 하나 이상의 **범위** (클라이언트 응용 프로그램에 이러한로 표시 됩니다 **위임 된 권한** Web API에 대 한)
- 하나 이상의 **응용 프로그램 역할** (클라이언트 응용 프로그램에 이러한로 표시 됩니다 **응용 프로그램 사용 권한** Web API에 대 한)

범위를 응용 프로그램을 사용 하는 최종 사용자에 게 표시 되는 동의 화면에 표시 됩니다. 따라서 범위를 설명 하는 해당 문자열을 제공 해야 합니다.

- 최종 사용자가 볼 수 있듯이
- 관리자 동의 부여할 수 있는 테 넌 트 관리자가 볼 수 있듯이

### <a name="how-to-expose-delegated-permissions-scopes"></a>위임 된 권한 (범위)를 노출 하는 방법

1. 선택 된 **API를 노출** 응용 프로그램 등록에서 섹션 및:
   1. **범위 추가**를 선택합니다.
   1. 요청 하는 경우 제안 된 응용 프로그램 ID URI를 수락 (api:// {clientId})을 선택 하 여 **저장 및 계속**합니다.
   1. 다음 매개 변수를 입력합니다.
      - 에 대 한 **범위 이름**를 사용 하 여 `access_as_user`입니다.
      - 에 대 한 **동의할 수 있는 사람**에 있는지 확인 합니다 **관리자 및 사용자** 옵션을 선택 합니다.
      - **관리자 동의 표시 이름**, 형식 `Access TodoListService as a user`합니다.
      - **관리자 동의 설명**, 형식 `Accesses the TodoListService Web API as a user`합니다.
      - **사용자 동의 표시 이름**, 형식 `Access TodoListService as a user`합니다.
      - **사용자 동의 설명**, 형식 `Accesses the TodoListService Web API as a user`합니다.
      - 유지할 **상태** 로 설정 **Enabled**합니다.
      - 선택 **범위 추가**합니다.

### <a name="case-where-your-web-api-is-called-by-daemon-application"></a>디먼 응용 프로그램이 Web API는 호출한 경우

이 단락이 디먼 응용 프로그램에서 안전 하 게 호출할 수 있도록 보호 된 Web API를 등록 하는 방법에 알아봅니다.

- 노출 해야 **응용 프로그램 사용 권한**합니다. 디먼 응용 프로그램 사용자 상호 작용 하지 않으며와 따라서 위임 된 권한 의미가 없었습니다만 응용 프로그램 사용 권한을 선언 합니다.
- 테 넌 트 관리자는 웹 API 앱 사용 권한 중 하나에 액세스 하도록 등록 된 응용 프로그램만 웹 앱에 대 한 Azure AD 토큰 발급에 필요할 수 있습니다.

#### <a name="how-to-expose-application-permissions-app-roles"></a>응용 프로그램 사용 권한 (앱 역할)을 노출 하는 방법

응용 프로그램 사용 권한, 노출 하는 매니페스트를 편집 해야 합니다.

1. 응용 프로그램에 대 한 응용 프로그램 등록을 클릭 **매니페스트**합니다.
1. 배치 하 여 매니페스트를 편집 합니다 `appRoles` 설정 하 고 하나 이상의 응용 프로그램 역할을 추가 합니다. 역할 정의 샘플 JSON 블록 아래에서 제공 됩니다.  유지 된 `allowedMemberTypes` "application"만 합니다. 올바른지 확인 하세요 합니다 **id** 는 고유 guid 및 **displayName** 및 **값** 공백을 포함 하지 않습니다.
1. 매니페스트를 저장합니다.

내용의 `appRoles` 다음과 (의 `id` 모든 고유 GUID 일 수 있습니다)

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

#### <a name="how-to-ensure-that-azure-ad-issues-tokens-for-your-web-api-only-to-allowed-clients"></a>Azure AD 토큰을 발급 웹 api에 확인 하는 방법에는 클라이언트 수

Web API (즉, 그렇게 하는 개발자 방법은) 앱 역할을 검사 합니다. 하지만 API에 액세스 하려면 테 넌 트 관리자가 승인 된 응용 프로그램에만 웹 API에 대 한 토큰을 발급 하도록 Azure Active Directory도 구성할 수 있습니다. 이 추가 보안을 추가 합니다.

1. 앱에서 **개요** 앱 등록 페이지에서 응용 프로그램의 이름 사용 하 여 하이퍼링크를 선택 합니다 **로컬 디렉터리에서 응용 프로그램을 관리 되는**합니다. 이 필드에 대 한 제목을 자를 수 있습니다. 예를 들어, 다음을 확인할 수 있습니다. `Managed application in ...`

   > [!NOTE]
   >
   > 로 이동 하는이 링크를 선택 합니다 **엔터프라이즈 응용 프로그램 개요** 테 넌 트를 만든 위치에서 응용 프로그램의 서비스 주체와 연결 된 페이지. 브라우저의 뒤로 단추를 사용 하 여 앱 등록 페이지로 탐색할 수 있습니다.

1. 선택 된 **속성** 페이지에 **관리** 엔터프라이즈 응용 프로그램 페이지의 섹션
1. 만 특정 클라이언트에서 Web API에 대 한 액세스를 적용 하는 AAD를 하려는 경우 설정할 **사용자 할당 필요?** 하 **예**합니다.

   > [!IMPORTANT]
   >
   > 설정 하 여 **사용자 할당 필요?** 하 **예**, AAD는 Web API에 대 한 액세스 토큰을 요청할 때 클라이언트 앱 역할 할당을 확인 합니다. 모든 AppRoles에 클라이언트를 할당 하지 않은, 경우 AAD 다음 오류가 반환 됩니다. `invalid_client: AADSTS501051: Application xxxx is not assigned to a role for the xxxx`
   >
   > 유지 하는 경우 **사용자 할당 필요?** 하 **No**를 <span style='background-color:yellow; display:inline'>클라이언트가 웹 API에 대 한 액세스 토큰을 요청 하면 Azure AD 앱 역할 할당을 확인 하지 않습니다</span>합니다. 따라서 (즉, 클라이언트 자격 증명 흐름을 사용 하 여 모든 클라이언트) 모든 디먼 클라이언트 대상을 지정 하 여 API에 대 한 액세스 토큰을 가져올 수 중일 것입니다. 모든 응용 프로그램에 대 한 권한을 요청 하지 않고도 API에 액세스할 수 것입니다. 이제, 이것은 다음의 끝으로 웹 API 수 항상 다음 섹션에 설명 된 대로 있는지 확인 합니다. 응용 프로그램 (테 넌 트 관리자 권한이 부여 되었습니다)는 올바른 역할, 액세스 토큰에는 유효성을 검사 하 여는 `roles` 클레임과 t에 대 한 올바른 값 그의 클레임 (여기서에서 `access_as_application`).

1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-protected-web-api-app-configuration.md)
