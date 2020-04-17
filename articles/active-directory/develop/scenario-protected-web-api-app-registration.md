---
title: 보호된 웹 API 앱 등록 | Azure
titleSuffix: Microsoft identity platform
description: 보호된 웹 API를 빌드하는 방법과 앱을 등록하는 데 필요한 정보를 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 997dd98d35b74effe5d195f9a781fa0935286ee9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537222"
---
# <a name="protected-web-api-app-registration"></a>보호된 웹 API: 앱 등록

이 문서에서는 보호된 웹 API에 대한 앱 등록의 세부 사항을 설명합니다.

앱을 등록하는 일반적인 단계는 [빠른 시작: Microsoft ID 플랫폼에 응용 프로그램 등록을](quickstart-register-app.md)참조하십시오.

## <a name="accepted-token-version"></a>수락된 토큰 버전

Microsoft ID 플랫폼 끝점에서v1.0 토큰및 v2.0 토큰을 발행할 수 있습니다. 이러한 토큰에 대한 자세한 내용은 [Access 토큰 을](access-tokens.md)참조하십시오.

허용되는 토큰 버전은 응용 프로그램을 만들 때 선택하는 **지원되는 계정 유형** 값에 따라 달라집니다.

- **지원되는 계정 유형의** 값이 **모든 조직 디렉터리 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)의 계정인**경우 허용된 토큰 버전은 v2.0입니다.
- 그렇지 않으면 허용되는 토큰 버전은 v1.0입니다.

응용 프로그램을 만든 후 다음 단계에 따라 허용된 토큰 버전을 확인하거나 변경할 수 있습니다.

1. Azure 포털에서 앱을 선택한 다음 **매니페스트**를 선택합니다.
1. 매니페스트에서 속성 **액세스토큰AcceptedVersion을** 찾습니다. 속성의 기본값은 2입니다.
1. 이 값은 웹 API가 허용하는 토큰 버전인 Azure Active Directory(Azure AD)로 지정합니다.
    - 값이 2이면 웹 API는 v2.0 토큰을 수락합니다.
    - 값이 **null이면**웹 API는 v1.0 토큰을 허용합니다.
1. 토큰 버전을 변경한 경우 **저장을**선택합니다.

> [!NOTE]
> 웹 API는 수락할 토큰 버전을 지정합니다. 클라이언트가 Microsoft ID 플랫폼(v2.0) 끝점에서 웹 API에 대한 토큰을 요청하면 클라이언트는 웹 API가 허용하는 토큰 버전을 나타내는 토큰을 받습니다.

## <a name="no-redirect-uri"></a>리디렉션 URI 없음

웹 API는 대화식으로 로그인한 사용자가 없기 때문에 리디렉션 URI를 등록할 필요가 없습니다.

## <a name="exposed-api"></a>노출된 API

웹 API와 관련된 다른 설정은 노출된 API및 노출된 범위입니다.

### <a name="application-id-uri-and-scopes"></a>응용 프로그램 ID URI 및 범위

범위에는 일반적으로 폼이 `resourceURI/scopeName`있습니다. Microsoft 그래프의 경우 범위에 바로 가기가 있습니다. 예를 들어 `User.Read` 에 대한 `https://graph.microsoft.com/user.read`바로 가기입니다.

앱 등록 중에 다음 매개 변수를 정의해야 합니다.

- 리소스 URI
- 하나 이상의 범위
- 하나 이상의 앱 역할

기본적으로 응용 프로그램 등록 포털에서는 리소스 URI `api://{clientId}`를 사용하는 것이 좋습니다. 이 URI는 고유하지만 사람이 읽을 수 없습니다. URI를 변경하는 경우 새 값이 고유한지 확인합니다.

클라이언트 응용 프로그램에 대 한 범위는 *위임 된 권한으로* 표시 하 고 응용 프로그램 역할은 웹 API에 대 한 *응용 프로그램 권한으로* 표시 됩니다.

범위는 앱 사용자에게 표시되는 동의 창에도 표시됩니다. 따라서 범위를 설명하는 해당 문자열을 제공해야 합니다.

- 사용자가 보았듯이.
- 테넌트 관리자에서 볼 수 있듯이 관리자 동의를 부여할 수 있습니다.

### <a name="exposing-delegated-permissions-scopes"></a>위임된 권한 노출(범위)

1. 응용 프로그램 등록에서 **API 노출을** 선택합니다.
1. **범위 추가를**선택합니다.
1. 메시지가 표시되면 **저장 및 계속을**선택하여 제안된 응용 프로그램 ID URI()를`api://{clientId}`수락합니다.
1. 다음 값을 지정합니다.
    - **범위 이름을** 선택하고 **access_as_user**를 입력합니다.
    - **동의할 수 있는 사용자를** 선택하고 **관리자와 사용자가** 선택되었는지 확인합니다.
    - **관리자 동의 표시 이름을** 선택하고 **사용자로 TodoListService 에 대한 액세스를 입력합니다.**
    - **관리자 동의 설명을** 선택하고 **사용자로 TodoListService 웹 API에 대한 액세스를 입력합니다.**
    - **사용자 동의 표시 이름을** 선택하고 **사용자로 TodoListService 에 대한 액세스를 입력합니다.**
    - **사용자 동의 설명을** 선택하고 **사용자로 TodoListService 웹 API에 대한 액세스를 입력합니다.**
    - **상태** 값을 사용 **설정으로**유지합니다.
 1. **범위 추가를**선택합니다.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>웹 API가 데몬 앱에서 호출되는 경우

이 섹션에서는 데몬 앱이 안전하게 호출할 수 있도록 보호된 웹 API를 등록하는 방법을 배웁니다.

- 데몬 앱이 사용자와 상호 작용하지 않기 때문에 응용 프로그램 권한만 선언하고 *노출합니다.* 위임된 사용 권한은 의미가 없습니다.
- 테넌트 관리자는 Azure AD가 API의 응용 프로그램 권한 중 하나에 액세스하도록 등록된 응용 프로그램에만 웹 API 토큰을 발급하도록 요구할 수 있습니다.

#### <a name="exposing-application-permissions-app-roles"></a>응용 프로그램 권한 노출(앱 역할)

응용 프로그램 권한을 노출하려면 매니페스트를 편집해야 합니다.

1. 응용 프로그램에 대한 응용 프로그램 등록에서 **매니페스트를**선택합니다.
1. 매니페스트를 편집하려면 `appRoles` 설정을 찾아 응용 프로그램 역할을 추가합니다. 역할 정의는 다음 샘플 JSON 블록에 제공됩니다.
1. 그대로 `allowedMemberTypes` 둡니다. `"Application"`
1. 고유한 `id` GUID인지 확인하십시오.
1. 공백을 `displayName` `value` 포함하지 않도록 합니다.
1. 매니페스트를 저장합니다.

다음 샘플에서는 `appRoles`의 값이 고유한 GUID일 `id` 수 있는 의 내용을 보여 주며, 이 의 내용은 다음과 같은 내용을 보여 주며, 이의 내용은 다음과 같은 내용을 보여 주며, 그 내용은 다음과 같은 것이다.

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Azure AD가 허용된 클라이언트에만 웹 API에 대한 토큰을 발급하도록 합니다.

웹 API는 앱 역할을 확인합니다. 이 역할은 소프트웨어 개발자가 응용 프로그램 권한을 노출하는 방법입니다. 또한 테넌트 관리자가 API 액세스를 승인하는 앱에만 API 토큰을 발급하도록 Azure AD를 구성할 수도 있습니다.

이 향상된 보안을 추가하려면 다음을 수행하십시오.

1. 앱 등록을 위한 앱 **개요** 페이지로 이동합니다.
1. **로컬 디렉터리에서 관리되는 응용 프로그램에서**앱 이름이 있는 링크를 선택합니다. 이 선택 영역의 레이블이 잘릴 수 있습니다. 예를 들어 **관리되는 응용 프로그램이 다음에서** 표시될 수 있습니다.

   > [!NOTE]
   >
   > 이 링크를 선택하면 엔터프라이즈 응용 **프로그램 개요** 페이지로 이동합니다. 이 페이지는 만든 테넌트에서 응용 프로그램의 서비스 주체와 연결됩니다. 브라우저의 뒤로 버튼을 사용하여 앱 등록 페이지로 이동하여 확인할 수 있습니다.

1. 엔터프라이즈 응용 프로그램 페이지의 **속성 관리** 섹션에서 **속성** 페이지를 선택합니다.
1. Azure AD가 특정 클라이언트에서만 웹 API에 대한 액세스를 허용하도록 하려면 **사용자 할당을 예로** 설정합니다. **Yes**

   > [!IMPORTANT]
   >
   > **사용자 할당을 요구하도록** 설정한 경우 **예,** Azure AD는 웹 API 액세스 토큰을 요청할 때 클라이언트의 앱 역할 할당을 확인합니다. 클라이언트가 앱 역할에 할당되지 않은 경우 Azure AD는 "invalid_client: AADSTS501051: \<응용\> 프로그램 응용 프로그램 이름이 \<웹\>API의 역할에 할당되지 않음"이라는 오류 메시지를 반환합니다.
   >
   > **사용자 할당을 필수로** 유지하면 **아니요로**설정하면 클라이언트가 웹 API에 대한 액세스 토큰을 요청할 때 Azure AD가 앱 역할 할당을 확인하지 않습니다. 클라이언트 자격 증명 흐름을 사용하는 모든 클라이언트를 의미하는 모든 데몬 클라이언트는 대상을 지정하는 것만으로 API에 대한 액세스 토큰을 얻을 수 있습니다. 모든 응용 프로그램은 사용 권한을 요청하지 않고도 API에 액세스할 수 있습니다.
   >
   > 그러나 이전 섹션에서 설명한 대로 웹 API는 항상 응용 프로그램에 테넌트 관리자가 승인한 올바른 역할이 있는지 확인할 수 있습니다. API는 액세스 토큰에 역할 클레임이 있고 이 클레임의 값이 올바른지 확인하여 이 확인을 수행합니다. 이전 JSON 샘플에서 값은 `access_as_application`.

1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 코드 구성](scenario-protected-web-api-app-configuration.md)
