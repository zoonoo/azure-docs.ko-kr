---
title: Microsoft id 플랫폼에 대 한 응용 프로그램 종류 | Azure
description: 앱 및 Microsoft id 플랫폼 (v2.0) 끝점에서 지 원하는 시나리오의 형식입니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd7055794860398ecc6296d0174a691e7fac4450
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235245"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Microsoft id 플랫폼에 대 한 응용 프로그램 유형

Microsoft id 플랫폼 (v2.0) 끝점 모두 업계 표준 프로토콜을 기반으로 최신 앱 아키텍처의 다양 한 인증을 지원 [OAuth 2.0 또는 OpenID Connect](active-directory-v2-protocols.md)합니다. 이 문서에서는 사용자 기본 설정된 언어 또는 플랫폼에 관계 없이 Microsoft id 플랫폼을 사용 하 여 빌드할 수 있는 앱 유형을 설명 합니다. 정보는 하기 전에 개략적인 시나리오를 이해할 수 있도록 설계 되었습니다 [코드를 사용 하 여 작업 시작](v2-overview.md#getting-started)합니다.

> [!NOTE]
> Microsoft id 플랫폼 끝점은 모든 Azure Active Directory (Azure AD) 시나리오 및 기능 지원 하지 않습니다. 에 대 한 자세한 내용은 Microsoft id 플랫폼 끝점을 사용 해야 하는지 여부를 확인 합니다 [Microsoft identity 플랫폼 제한](active-directory-v2-limitations.md)합니다.

## <a name="the-basics"></a>기본 사항

새 Microsoft id 플랫폼 끝점을 사용 하는 각 앱을 등록 해야 합니다 [앱 등록 포털](https://go.microsoft.com/fwlink/?linkid=2083908)합니다. 앱 등록 프로세스는 앱에 대한 다음 값을 수집하고 할당합니다.

* **응용 프로그램 (클라이언트) ID** 고유 하 게 식별 하는 앱
* 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI**
* 다른 몇 가지 시나리오 관련 값과 같은 지원 되는 계정 유형

자세한 내용은 [앱 등록](quickstart-register-app.md)방법을 참조하세요.

앱 등록 되 면 앱 끝점에 요청을 전송 하 여 Microsoft id 플랫폼을 사용 하 여 통신 합니다. Microsoft에서는 이러한 요청에 대한 세부 정보를 처리하는 오픈 소스 프레임워크 및 라이브러리를 제공합니다. 또한 이러한 엔드포인트에 대한 요청을 만들어 인증 논리를 직접 구현할 수 있습니다.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>단일 페이지 앱(JavaScript)

대부분의 최신 앱에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 프레임워크로도 작성되는 종종 Angular, React, 또는 Vue 같은 프레임 워크를 사용 하 여 기록 됩니다. Microsoft id 플랫폼 끝점 사용 하 여 이러한 앱을 지원 합니다 [OAuth 2.0 암시적 흐름](v2-oauth2-implicit-grant-flow.md)합니다.

이 흐름에서 앱을 Microsoft id 플랫폼에서 직접 토큰을 받습니다 서버 간 교환 없이 끝점에 권한을 부여 합니다. 모든 인증 논리 및 세션 처리가 추가 페이지 리디렉션 없이 전적으로 JavaScript 클라이언트에서 발생합니다.

![암시적 인증 흐름](./media/v2-app-types/convergence-scenarios-implicit.svg)

이 시나리오의 작동이 방식을 보려면,의 단일 페이지 앱 코드 샘플 중 하나를 수행 합니다 [시작 하는 Microsoft id 플랫폼](v2-overview.md#getting-started) 섹션.

## <a name="web-apps"></a>웹 앱

사용자가 브라우저를 통해 액세스하는 웹앱(.NET, PHP, Java, Ruby, Python, Node)의 경우 사용자 로그인에 [OpenID Connect](active-directory-v2-protocols.md)를 사용할 수 있습니다. OpenID Connect에서는 웹앱이 ID 토큰을 받습니다. ID 토큰은 사용자 ID를 확인하고 클레임 형태로 사용자 정보를 제공하는 보안 토큰입니다.

```
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

사용할 수 있는 다양 한 Microsoft id 플랫폼 끝점에 사용 되는 토큰 형식에 대 한 자세한 내용은 합니다 [액세스 토큰](access-tokens.md) 참조 및 [id_token 참조](id-tokens.md)

웹 서버 앱에서 로그인 인증 흐름은 다음과 같은 높은 수준의 단계를 수행합니다.

![웹앱 인증 흐름](./media/v2-app-types/convergence-scenarios-webapp.svg)

Microsoft id 플랫폼 끝점에서 받은 공개 서명 키를 사용 하 여 ID 토큰 유효성을 검사 하 여 사용자의 id를 확인할 수 있습니다. 후속 페이지 요청에서 사용자를 식별하는 데 사용할 수 있는 세션 쿠키가 설정됩니다.

이 시나리오의 작동이 방식을 보려면 웹 앱 로그인 코드 샘플 중 하나를 수행 합니다 [시작 하는 Microsoft id 플랫폼](v2-overview.md#getting-started) 섹션입니다.

간단한 로그인뿐 아니라 웹 서버 앱은 REST API와 같은 다른 웹 서비스에 액세스해야 할 수도 있습니다. 이 경우 웹 서버 앱은 [OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols.md)을 사용하여 결합된 OpenID Connect 및 OAuth 2.0 흐름에 참여합니다. 이 시나리오에 대한 자세한 내용은 [웹앱 및 Web API 시작](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)을 참조하세요.

## <a name="web-apis"></a>Web API

앱의 RESTful 웹 API와 같은 웹 서비스를 보호 하는 Microsoft id 플랫폼 끝점을 사용할 수 있습니다. ID 토큰 및 세션 쿠키 대신 Web API는 OAuth 2.0 액세스 토큰을 사용하여 데이터 보안을 유지하고 들어오는 요청을 인증합니다. Web API 호출자는 다음과 같이 HTTP 요청의 인증 헤더에 액세스 토큰을 추가합니다.

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API는 액세스 토큰을 사용하여 API 호출자의 ID를 확인하고 액세스 토큰에 인코딩된 클레임에서 호출자에 대한 정보를 추출합니다. 사용할 수 있는 다양 한 Microsoft id 플랫폼 끝점에 사용 되는 토큰 형식에 대 한 자세한 내용은 합니다 [액세스 토큰](access-tokens.md) 참조 및 [id_token 참조](id-tokens.md)

Web API를 통해 사용자는 [범위](v2-permissions-and-consent.md)라고도 하는 사용 권한을 노출하여 특정 기능이나 데이터를 옵트인(opt-in)하거나 옵트아웃(opt-out)할 수 있습니다. 호출 앱이 범위에 대한 사용 권한을 얻으려면 사용자가 흐름 중 범위에 동의해야 합니다. Microsoft id 플랫폼 끝점 사용자 권한을 요청 하 고 웹 API를 수신 하는 모든 액세스 토큰에 사용 권한을 기록 합니다. Web API는 각 호출에서 받은 액세스 토큰의 유효성을 검사하고 권한 부여 검사를 수행합니다.

Web API는 웹 서버 앱, 데스크톱 및 모바일 앱, 단일 페이지 앱, 서버 쪽 데몬 및 다른 Web API까지 포함하여 모든 유형의 앱에서 액세스 토큰을 받을 수 있습니다. Web API에 대한 개략적인 흐름은 다음과 같습니다.

![Web API 인증 흐름](./media/v2-app-types/convergence-scenarios-webapi.svg)

OAuth2 액세스 토큰을 사용 하 여 Web API를 보호 하는 방법에 알아보려면 웹 API 코드 샘플에서는 확인 합니다 [시작 하는 Microsoft id 플랫폼](v2-overview.md#getting-started) 섹션입니다.

대부분의 경우에서 web Api도 다른 다운스트림 웹 Api는 Microsoft id 플랫폼에서 보호에 대 한 아웃 바운드 요청할 필요 합니다. 웹 Api를 활용할 수 있도록 작업을 수행 하는 **-대신-의** 아웃 바운드 요청에 사용할 다른 액세스 토큰에 대 한 들어오는 액세스 토큰을 교환 하는 웹 API를 허용 하는 흐름입니다. 자세한 내용은 참조 하세요. [Microsoft id 플랫폼 및 OAuth 2.0 On-Behalf-Of 흐름](v2-oauth2-on-behalf-of-flow.md)합니다.

## <a name="mobile-and-native-apps"></a>모바일 및 네이티브 앱

모바일 및 데스크톱 앱과 같은 디바이스 설치 앱은 데이터를 저장하고 사용자 대신 기능을 수행하는 백 엔드 서비스 또는 Web API에 액세스해야 하는 경우가 많습니다. 이러한 앱은 [OAuth 2.0 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)을 사용하여 백 엔드 서비스에 로그인 및 권한 부여를 추가할 수 있습니다.

이 흐름에서 앱 사용자가 로그인 할 때 Microsoft id 플랫폼 끝점에서 권한 부여 코드를 받습니다. 권한 부여 코드는 현재 로그인한 사용자를 대신해 백 엔드 서비스를 호출할 앱의 사용 권한을 나타냅니다. 앱은 백그라운드에서 권한 부여 코드를 OAuth 2.0 액세스 토큰 및 새로 고침 토큰으로 교환할 수 있습니다. 앱은 액세스 토큰을 사용하여 HTTP 요청 시 Web API에 인증할 수 있고 새로 고침 토큰을 사용하여 이전 액세스 토큰 만료 시 새 액세스 토큰을 가져올 수 있습니다.

![네이티브 앱 인증 흐름](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>디먼 및 서버 쪽 앱

장기 실행 프로세스가 있거나 사용자와의 상호 작용 없이 작동하는 앱은 Web API와 같은 보안 리소스에 액세스하는 방법도 필요로 합니다. 이러한 앱은 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 사용자의 위임된 ID 대신 앱 ID로 인증하고 토큰을 가져올 수 있습니다. 클라이언트 암호 또는 인증서를 사용하여 앱 ID를 증명할 수 있습니다. 자세한 내용은 참조 하세요. [인증 인증서로 디먼 앱에서 Microsoft id 플랫폼에](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/)입니다.

이 흐름에서 앱에 직접 상호 작용 하는 하는 `/token` 액세스 끝점:

![디먼 앱 인증 흐름](./media/v2-app-types/convergence-scenarios-daemon.svg)

디먼 앱을 빌드하려면 [클라이언트 자격 증명 설명서](v2-oauth2-client-creds-grant-flow.md)를 참조하거나 [.NET 샘플 앱](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)을 사용해 보세요.
