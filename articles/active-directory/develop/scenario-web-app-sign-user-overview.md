---
title: 웹앱에서 사용자 로그인 | Azure
titleSuffix: Microsoft identity platform
description: 사용자를 로그인하는 웹앱을 빌드하는 방법 알아보기(개요)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0bbc799f946d318c305a96d9cb8c6831d9242ff6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578297"
---
# <a name="scenario-web-app-that-signs-in-users"></a>시나리오: 사용자를 로그인하는 웹앱

Microsoft ID 플랫폼을 사용하여 사용자를 로그인하는 웹앱을 빌드하는 데 필요한 모든 것을 알아보세요.

## <a name="getting-started"></a>시작

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

사용자를 로그인하는 첫 번째 이식 가능(ASP.NET Core) 웹앱을 만들려면 다음 빠른 시작을 수행합니다.

[빠른 시작: 사용자를 로그인하는 ASP.NET Core 웹앱](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

기존 ASP.NET 웹 애플리케이션에 로그인을 추가하는 방법을 알아보려면 다음 빠른 시작을 시도합니다.

[빠른 시작: 사용자를 로그인하는 ASP.NET 웹앱](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Java 개발자인 경우 다음 빠른 시작을 시도합니다.

[빠른 시작: Java 웹앱에 Microsoft로 로그인 추가](quickstart-v2-java-webapp.md)

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Node.js 개발자인 경우 다음 빠른 시작을 시도합니다.

[빠른 시작: Node.js 웹앱에 Microsoft로 로그인 추가](quickstart-v2-nodejs-webapp-msal.md)

# <a name="python"></a>[Python](#tab/python)

Python을 사용하여 개발하는 경우 다음 빠른 시작을 시도합니다.

[빠른 시작: Python 웹앱에 Microsoft로 로그인 추가](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>개요

사용자가 로그인할 수 있도록 웹앱에 인증을 추가합니다. 인증을 추가하면 사용자의 환경을 사용자 지정하기 위해 웹앱에서 제한된 프로필 정보에 액세스할 수 있습니다.

웹앱은 웹 브라우저에서 사용자를 인증합니다. 이 시나리오에서는 웹앱이 사용자의 브라우저에서 Azure Active Directory(Azure AD)에 로그인하도록 지시합니다. Azure AD는 사용자의 브라우저를 통해 보안 토큰에 사용자에 대한 클레임이 포함된 로그인 응답을 반환합니다. 사용자 로그인은 미들웨어 [라이브러리](scenario-web-app-sign-user-app-configuration.md#microsoft-libraries-supporting-web-apps)를 사용하여 간소화된 [Open ID Connect](./v2-protocols-oidc.md) 표준 프로토콜을 활용합니다.

![웹앱의 사용자 로그인](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

두 번째 단계로 로그인한 사용자 대신 애플리케이션에서 웹 API를 호출하도록 설정할 수 있습니다. 이 다음 단계는 다른 시나리오이며, [웹 API를 호출하는 웹앱](scenario-web-app-call-api-overview.md)에서 찾을 수 있습니다.

> [!NOTE]
> 웹앱에서 로그인을 추가하는 작업은 웹앱을 보호하고 사용자 토큰의 유효성을 검사하는 것이며, 이는 **미들웨어** 라이브러리가 수행하는 작업입니다. .NET의 경우 이 시나리오에는 아직 보호된 API를 호출하기 위한 토큰을 획득하는 MSAL(Microsoft 인증 라이브러리)가 필요하지 않습니다. 후속 시나리오에서 웹앱이 웹 API를 호출해야 하는 경우에 대한 .NET용 인증 라이브러리가 도입될 예정입니다.

## <a name="specifics"></a>특수 적용 사항

- 애플리케이션을 등록하는 동안 여러 위치에 앱을 배포하는 경우 회신 URI를 하나 이상 제공해야 합니다. 경우에 따라(ASP.NET 및 ASP.NET Core) ID 토큰을 사용하도록 설정해야 합니다. 마지막으로 애플리케이션이 로그아웃하는 사용자에게 반응하도록 로그아웃 URI를 설정하는 것이 좋습니다.
- 애플리케이션에 대한 코드에서 웹앱이 로그인을 위임하는 권한을 제공해야 합니다. 토큰 유효성 검사를 사용자 지정하는 것이 좋습니다(특히 파트너 시나리오의 경우).
- 웹 애플리케이션은 모든 계정 유형을 지원합니다. 자세한 내용은 [지원되는 계정 유형](v2-supported-account-types.md)을 참조하세요.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이 시나리오의 다음 문서인 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore)으로 이동합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

이 시나리오의 다음 문서인 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet)으로 이동합니다.

# <a name="java"></a>[Java](#tab/java)

이 시나리오의 다음 문서인 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=java)으로 이동합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 시나리오의 다음 문서인 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=nodejs)으로 이동합니다.

# <a name="python"></a>[Python](#tab/python)

이 시나리오의 다음 문서인 [앱 등록](./scenario-web-app-sign-user-app-registration.md?tabs=python)으로 이동합니다.

---
