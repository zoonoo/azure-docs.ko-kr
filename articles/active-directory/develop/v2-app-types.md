---
title: Microsoft id 플랫폼용 응용 프로그램 종류 | Microsoft
description: V2.0 (Microsoft identity platform) 끝점에서 지원 되는 앱 및 시나리오의 유형입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: def92071496716f90b24158a50e4a5233e93c994
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677996"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Microsoft id 플랫폼용 응용 프로그램 종류

Microsoft ID 플랫폼(v2.0) 엔드포인트는 모두 업계 표준 프로토콜 [OAuth 2.0 또는 OpenID Connect](active-directory-v2-protocols.md)를 기반으로 하는 다양한 최신 앱 아키텍처에 대한 인증을 지원합니다. 이 문서에서는 기본 언어나 플랫폼에 관계 없이 Microsoft id 플랫폼을 사용 하 여 빌드할 수 있는 앱의 유형을 설명 합니다. 이 정보는 [코드 작업을 시작](v2-overview.md#getting-started)하기 전에 높은 수준의 시나리오를 이해 하는 데 도움이 되도록 설계 되었습니다.

## <a name="the-basics"></a>기본 사항

새 [앱 등록 포털](https://go.microsoft.com/fwlink/?linkid=2083908)에서 Microsoft identity platform 끝점을 사용 하는 각 앱을 등록 해야 합니다. 앱 등록 프로세스는 앱에 대한 다음 값을 수집하고 할당합니다.

* 앱을 고유 하 게 식별 하는 **응용 프로그램 (클라이언트) ID**
* 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI**
* 지원 되는 계정 유형과 같은 다른 몇 가지 시나리오 관련 값

자세한 내용은 [앱 등록](quickstart-register-app.md)방법을 참조하세요.

앱이 등록 되 면 앱은 끝점에 요청을 전송 하 여 Microsoft id 플랫폼과 통신 합니다. Microsoft에서는 이러한 요청에 대한 세부 정보를 처리하는 오픈 소스 프레임워크 및 라이브러리를 제공합니다. 또한 이러한 엔드포인트에 대한 요청을 만들어 인증 논리를 직접 구현할 수 있습니다.

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>단일 페이지 앱(JavaScript)

대부분의 최신 앱에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 프레임워크로도 작성되는 대개 각도, 반응, Vue 등의 프레임 워크를 사용 하 여 작성 됩니다. Microsoft id 플랫폼 끝점은 [OAuth 2.0 암시적 흐름](v2-oauth2-implicit-grant-flow.md)을 사용 하 여 이러한 앱을 지원 합니다.

이 흐름에서 앱은 서버 간 교환 없이 Microsoft id 플랫폼 권한 부여 끝점에서 직접 토큰을 받습니다. 모든 인증 논리 및 세션 처리가 추가 페이지 리디렉션 없이 전적으로 JavaScript 클라이언트에서 발생합니다.

![암시적 인증 흐름을 보여 줍니다.](./media/v2-app-types/convergence-scenarios-implicit.svg)

이 시나리오의 작동 방식을 확인 하려면 [Microsoft id 플랫폼 시작](v2-overview.md#getting-started) 섹션의 단일 페이지 앱 코드 샘플 중 하나를 사용해 보세요.

## <a name="web-apps"></a>웹 앱

사용자가 브라우저를 통해 액세스하는 웹앱(.NET, PHP, Java, Ruby, Python, Node)의 경우 사용자 로그인에 [OpenID Connect](active-directory-v2-protocols.md)를 사용할 수 있습니다. OpenID Connect에서는 웹앱이 ID 토큰을 받습니다. ID 토큰은 사용자 ID를 확인하고 클레임 형태로 사용자 정보를 제공하는 보안 토큰입니다.

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Microsoft id 플랫폼 끝점에서 사용 되는 다양 한 토큰 형식에 대 한 자세한 내용은 [액세스 토큰](access-tokens.md) 참조 및 [id_token 참조](id-tokens.md) 에서 제공 됩니다.

웹 서버 앱에서 로그인 인증 흐름은 다음과 같은 높은 수준의 단계를 수행합니다.

![웹 앱 인증 흐름을 표시 합니다.](./media/v2-app-types/convergence-scenarios-webapp.svg)

Microsoft id 플랫폼 끝점에서 받은 공개 서명 키를 사용 하 여 ID 토큰의 유효성을 검사 하 여 사용자의 id를 확인할 수 있습니다. 후속 페이지 요청에서 사용자를 식별하는 데 사용할 수 있는 세션 쿠키가 설정됩니다.

이 시나리오의 작동 방식을 확인 하려면 [Microsoft id 플랫폼 시작](v2-overview.md#getting-started) 섹션의 웹 앱 로그인 코드 샘플 중 하나를 사용해 보세요.

간단한 로그인뿐 아니라 웹 서버 앱은 REST API와 같은 다른 웹 서비스에 액세스해야 할 수도 있습니다. 이 경우 웹 서버 앱은 [OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols.md)을 사용하여 결합된 OpenID Connect 및 OAuth 2.0 흐름에 참여합니다. 이 시나리오에 대 한 자세한 내용은 [web apps 및 Web api 시작](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)하기를 참조 하세요.

## <a name="web-apis"></a>Web API

Microsoft id 플랫폼 끝점을 사용 하 여 앱의 RESTful web API와 같은 웹 서비스를 보호할 수 있습니다. 웹 Api는 다양 한 플랫폼과 언어로 구현할 수 있습니다. Azure Functions에서 HTTP 트리거를 사용 하 여 구현할 수도 있습니다. ID 토큰 및 세션 쿠키 대신 web API는 OAuth 2.0 액세스 토큰을 사용 하 여 해당 데이터를 보호 하 고 들어오는 요청을 인증 합니다. 웹 API 호출자는 다음과 같이 HTTP 요청의 인증 헤더에 액세스 토큰을 추가 합니다.

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API는 액세스 토큰을 사용 하 여 API 호출자의 id를 확인 하 고 액세스 토큰에서 인코딩된 클레임에서 호출자에 대 한 정보를 추출 합니다. Microsoft id 플랫폼 끝점에서 사용 되는 다양 한 토큰 형식에 대 한 자세한 내용은 [액세스 토큰](access-tokens.md) 참조 및 [id_token](id-tokens.md) 참조에서 제공 됩니다.

Web API는 [범위](v2-permissions-and-consent.md)라고도 하는 사용 권한을 노출 하 여 특정 기능 또는 데이터를 옵트인 또는 옵트아웃 하는 기능을 사용자에 게 제공할 수 있습니다. 호출 앱이 범위에 대한 사용 권한을 얻으려면 사용자가 흐름 중 범위에 동의해야 합니다. Microsoft id 플랫폼 끝점은 사용자에 게 권한을 요청 하 고 web API가 수신 하는 모든 액세스 토큰에 대 한 사용 권한을 기록 합니다. Web API는 각 호출에서 받은 액세스 토큰의 유효성을 검사 하 고 권한 부여 검사를 수행 합니다.

Web API는 웹 서버 앱, 데스크톱 및 모바일 앱, 단일 페이지 앱, 서버 쪽 디먼 및 다른 웹 Api를 비롯 한 모든 유형의 앱에서 액세스 토큰을 받을 수 있습니다. 웹 API에 대 한 개략적인 흐름은 다음과 같습니다.

![웹 API 인증 흐름을 표시 합니다.](./media/v2-app-types/convergence-scenarios-webapi.svg)

OAuth2 액세스 토큰을 사용 하 여 web API를 보호 하는 방법을 알아보려면 [Microsoft id 플랫폼 시작](v2-overview.md#getting-started) 섹션에서 web api 코드 샘플을 확인 하세요.

대부분의 경우 웹 Api는 Microsoft id 플랫폼에 의해 보호 되는 다른 다운스트림 웹 Api에 대 한 아웃 바운드 요청을 만들어야 합니다. 이렇게 하기 위해 web **api는 들어오는 흐름을** 활용할 수 있습니다 .이를 통해 웹 api는 들어오는 액세스 토큰을 사용 하 여 아웃 바운드 요청에 사용할 다른 액세스 토큰을 교환할 수 있습니다. 자세한 내용은 [Microsoft id 플랫폼 및 OAuth 2.0 On flow](v2-oauth2-on-behalf-of-flow.md)를 참조 하세요.

## <a name="mobile-and-native-apps"></a>모바일 및 네이티브 앱

모바일 및 데스크톱 앱과 같은 장치 설치 앱은 데이터를 저장 하 고 사용자를 대신 하 여 기능을 수행 하는 백 엔드 서비스 또는 웹 Api에 액세스 해야 하는 경우가 많습니다. 이러한 앱은 [OAuth 2.0 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)을 사용하여 백 엔드 서비스에 로그인 및 권한 부여를 추가할 수 있습니다.

이 흐름에서 앱은 사용자가 로그인 할 때 Microsoft id 플랫폼 끝점에서 권한 부여 코드를 수신 합니다. 권한 부여 코드는 현재 로그인한 사용자를 대신해 백 엔드 서비스를 호출할 앱의 사용 권한을 나타냅니다. 앱은 백그라운드에서 권한 부여 코드를 OAuth 2.0 액세스 토큰 및 새로 고침 토큰으로 교환할 수 있습니다. 앱은 액세스 토큰을 사용 하 여 HTTP 요청에서 web Api에 인증 하 고, 이전 액세스 토큰이 만료 되 면 새로 고침 토큰을 사용 하 여 새 액세스 토큰을 가져올 수 있습니다.

![네이티브 앱 인증 흐름을 표시 합니다.](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>디먼 및 서버 쪽 앱

장기 실행 프로세스를 포함 하거나 사용자와의 상호 작용 없이 작동 하는 앱은 웹 Api와 같은 보안 리소스에 액세스 하는 방법도 필요 합니다. 이러한 앱은 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 사용자의 위임된 ID 대신 앱 ID로 인증하고 토큰을 가져올 수 있습니다. 클라이언트 암호 또는 인증서를 사용하여 앱 ID를 증명할 수 있습니다. 자세한 내용은 [Microsoft id 플랫폼을 사용 하는 .Net Core 디먼 콘솔 응용 프로그램](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)을 참조 하세요.

이 흐름에서 앱은 `/token` 끝점을 직접 조작 하 여 액세스 권한을 얻습니다.

![디먼 앱 인증 흐름을 표시 합니다.](./media/v2-app-types/convergence-scenarios-daemon.svg)

디먼 앱을 빌드하려면 [클라이언트 자격 증명 설명서](v2-oauth2-client-creds-grant-flow.md)를 참조하거나 [.NET 샘플 앱](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)을 사용해 보세요.
