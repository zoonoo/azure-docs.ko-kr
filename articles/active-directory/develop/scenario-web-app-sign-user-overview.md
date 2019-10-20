---
title: 사용자를 로그인 하는 웹 앱 (개요)-Microsoft identity platform
description: 사용자를 로그인 하는 웹 앱을 빌드하는 방법 알아보기 (개요)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68d47d4233aec62ec5f1955e52025b0d55221af8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596708"
---
# <a name="scenario-web-app-that-signs-in-users"></a>시나리오: 사용자에 게 로그인 하는 웹 앱

Microsoft id 플랫폼을 사용 하 여 사용자를 로그인 하는 웹 앱을 빌드하는 데 필요한 모든 것을 알아보세요.

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>시작

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

사용자를 로그인 하는 첫 번째 휴대용 (ASP.NET Core) 웹 앱을 만들려면 다음 빠른 시작을 수행 합니다.

> [!div class="nextstepaction"]
> [빠른 시작: 사용자를 로그인 하는 웹 앱 ASP.NET Core](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

f 레거시 ASP.NET 웹 응용 프로그램에 로그인을 추가 하는 방법을 이해 하려면 다음 자습서를 사용해 보세요.

> [!div class="nextstepaction"]
> [빠른 시작: 사용자를 로그인 하는 ASP.NET 웹 앱](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Java 개발자 라면 다음 빠른 시작을 사용해 보세요.

> [!div class="nextstepaction"]
> [빠른 시작: Java 웹 앱에 Microsoft에 로그인 추가](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Python을 사용 하 여 개발 하는 경우 다음을 시도해 보세요.

> [!div class="nextstepaction"]
> [빠른 시작: Python 웹 앱에 Microsoft에 로그인 추가](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>개요

사용자가 로그인 할 수 있도록 웹 앱에 인증을 추가 합니다. 인증을 추가 하면 웹 앱에서 제한 된 프로필 정보에 액세스할 수 있으며, 예를 들어 사용자에 게 제공 하는 환경을 사용자 지정할 수 있습니다. 웹 앱은 웹 브라우저에서 사용자를 인증 합니다. 이 시나리오에서는 웹 애플리케이션이 사용자의 브라우저를 Azure AD에 로그인하도록 지시합니다. Azure AD는 보안 토큰에 사용자에 대한 클레임이 포함된 로그인 응답을 사용자의 브라우저를 통해 반환합니다. 로그인 사용자는 미들웨어 [라이브러리](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)를 사용 하 여 단순화 된 [Open ID Connect](./v2-protocols-oidc.md) 표준 프로토콜 자체를 활용 합니다.

![웹 앱 로그인 사용자](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

두 번째 단계로, 로그인 한 사용자를 대신 하 여 응용 프로그램이 Web Api를 호출 하도록 할 수도 있습니다. 다음 단계는 [웹 앱 호출 웹 api](scenario-web-app-call-api-overview.md) 에서 찾을 수 있는 다른 시나리오입니다.

> [!NOTE]
> 웹 앱에 로그인을 추가 하는 작업은 웹 앱을 보호 하 고 **미들웨어** 라이브러리인 사용자 토큰의 유효성을 검사 하는 것입니다. .NET의 경우이 시나리오는 아직 MSAL (Microsoft 인증 라이브러리)을 필요로 하지 않습니다 .이는 보호 된 Api를 호출 하는 토큰을 획득 하는 것입니다. 인증 라이브러리는 웹 앱이 web Api를 호출 해야 하는 경우에만 추가 작업 시나리오에서 도입 됩니다.

## <a name="specifics"></a>자세히

- 응용 프로그램을 등록 하는 동안 여러 위치에 앱을 배포 하는 경우 (여러 위치에 앱을 배포 하는 경우) 회신 Uri를 제공 해야 합니다. 경우에 따라 (ASP.NET/ASP.NET Core) ID 토큰을 사용 하도록 설정 해야 합니다. 마지막으로 응용 프로그램이 사용자 로그 아웃에 반응 하도록 로그 아웃 URI를 설정 하는 것이 좋습니다.
- 응용 프로그램에 대 한 코드에서 웹 앱이 로그인 하는 권한을 제공 해야 합니다. 토큰 유효성 검사를 사용자 지정 하는 것이 좋습니다 (특히 ISV 시나리오에서).
- 웹 응용 프로그램은 모든 계정 유형을 지원 합니다. 자세한 내용은 [지원 되는 계정 유형](v2-supported-account-types.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
