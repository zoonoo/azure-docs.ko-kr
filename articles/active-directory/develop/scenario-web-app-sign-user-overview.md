---
title: 웹 앱에서 사용자 로그인 - Microsoft ID 플랫폼 | Azure
description: 사용자에게 서명하는 웹 앱을 빌드하는 방법 알아보기(개요)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 403f589702fd7142f0515a3b6f19ee1b9bbb6420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701555"
---
# <a name="scenario-web-app-that-signs-in-users"></a>시나리오: 사용자에 서명하는 웹 앱

사용자를 로그인하기 위해 Microsoft ID 플랫폼을 사용하는 웹 앱을 빌드하는 데 필요한 모든 것을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>시작

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

사용자에게 로그인하는 최초의 휴대용(ASP.NET Core) 웹 앱을 만들려면 다음 빠른 시작을 따르십시오.

> [!div class="nextstepaction"]
> [빠른 시작: 사용자에 서명 하는 코어 웹 애플 리 케이 션을 ASP.NET](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

기존 ASP.NET 웹 응용 프로그램에 로그인을 추가하는 방법을 이해하려면 다음 빠른 시작을 시도해 보십시오.

> [!div class="nextstepaction"]
> [빠른 시작: 사용자에 서명하는 웹 앱 ASP.NET](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Java 개발자인 경우 다음 빠른 시작을 시도해 보십시오.

> [!div class="nextstepaction"]
> [빠른 시작: 자바 웹 앱에 Microsoft와 로그인 추가](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

파이썬으로 개발하는 경우 다음 빠른 시작을 시도하십시오.

> [!div class="nextstepaction"]
> [빠른 시작: 파이썬 웹 앱에 Microsoft와 로그인 추가](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>개요

사용자가 로그인할 수 있도록 웹 앱에 인증을 추가합니다. 인증을 추가하면 웹 앱이 제한된 프로필 정보에 액세스하여 사용자 환경을 사용자 지정할 수 있습니다. 

웹 앱은 웹 브라우저에서 사용자를 인증합니다. 이 시나리오에서 웹 앱은 사용자의 브라우저를 지시하여 Azure Active Directory(Azure AD)에 로그인합니다. Azure AD는 보안 토큰에 사용자에 대한 클레임을 포함하는 사용자의 브라우저를 통해 로그인 응답을 반환합니다. 로그인하면 미들웨어 [라이브러리를](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)사용하여 간소화된 [Open ID Connect](./v2-protocols-oidc.md) 표준 프로토콜을 활용할 수 있습니다.

![웹앱의 사용자 로그인](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

두 번째 단계에서는 응용 프로그램에서 로그인한 사용자를 대신하여 웹 API를 호출하도록 설정할 수 있습니다. 이 다음 단계는 [웹 API를 호출하는 웹 앱에서](scenario-web-app-call-api-overview.md)찾을 수 있는 다른 시나리오입니다.

> [!NOTE]
> 웹 앱에 로그인을 추가하는 것은 웹 앱을 보호하고 **미들웨어** 라이브러리가 수행하는 사용자 토큰의 유효성을 검사하는 것입니다. .NET의 경우 이 시나리오에서는 보호된 API를 호출하기 위해 토큰을 획득하는 것과 같은 MSAL(Microsoft 인증 라이브러리)이 아직 필요하지 않습니다. 인증 라이브러리는 웹 앱이 웹 API를 호출해야 하는 후속 시나리오에서 도입됩니다.

## <a name="specifics"></a>구체적인

- 응용 프로그램 등록 중에 앱을 여러 위치에 배포하는 경우 하나 또는 여러 개의 회신 URI를 제공해야 합니다. 경우에 따라(ASP.NET 및 ASP.NET 코어) ID 토큰을 사용하도록 설정해야 합니다. 마지막으로 응용 프로그램이 로그아웃하는 사용자에게 반응하도록 로그아웃 URI를 설정해야 합니다.
- 응용 프로그램의 코드에서 웹 앱이 로그인하는 권한을 제공해야 합니다. 토큰 유효성 검사(특히 파트너 시나리오)를 사용자 지정할 수 있습니다.
- 웹 응용 프로그램은 모든 계정 유형을 지원합니다. 자세한 내용은 [지원되는 계정 유형을](v2-supported-account-types.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
