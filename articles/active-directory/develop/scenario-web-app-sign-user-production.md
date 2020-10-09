---
title: 사용자를 로그인 하는 웹 앱을 프로덕션-Microsoft id 플랫폼으로 이동 | Microsoft
description: 사용자를 로그인 하는 웹 앱을 빌드하는 방법 알아보기 (프로덕션으로 이동)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a45b52ac10a44b6efd54c41b3fec1e61a47a35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82181633"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>사용자가 로그인 하는 웹 앱: 프로덕션으로 이동

웹 Api를 호출 하는 토큰을 가져오는 방법을 배웠으므로 이제 프로덕션으로 이동 하는 방법을 알아보세요.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

### <a name="troubleshooting"></a>문제 해결

> [!NOTE]
> 사용자가 처음으로 웹 응용 프로그램에 로그인 하는 경우 동의 해야 합니다. 그러나 일부 조직에서는 사용자가 다음과 같은 메시지를 볼 수 있습니다.
>
> *AppName에는 관리자만 부여할 수 있는 조직의 리소스에 액세스할 수 있는 권한이 필요 합니다. 이 앱을 사용 하려면 먼저 관리자에 게이 앱에 대 한 권한을 부여 하도록 요청 하세요.*
>
> 이는 테 넌 트 관리자가 사용자에 게 동의할 수 없는 기능을 **사용 하지 않도록 설정** 했기 때문입니다. 이 경우 테 넌 트 관리자에 게 응용 프로그램에서 요구 하는 범위에 대 한 관리자 동의를 수행 하도록 요청 해야 합니다.

### <a name="same-site"></a>같은 사이트

새 버전의 Chrome 브라우저에서 발생할 수 있는 문제를 이해 해야 합니다.

> [!div class="nextstepaction"]
> [Chrome 브라우저에서 SameSite 쿠키 변경을 처리 하는 방법](howto-handle-samesite-cookie-changes-chrome-browser.md)

SameSite NuGet 패키지는 가장 일반적인 문제를 처리 합니다.

### <a name="scenario-for-calling-web-apis"></a>웹 Api 호출에 대 한 시나리오

웹 앱이 로그인 한 후에는 로그인 한 사용자를 대신 하 여 web Api를 호출할 수 있습니다. 웹 앱에서 웹 Api를 호출 하는 것은 다음 시나리오의 개체입니다.

> [!div class="nextstepaction"]
> [Web API를 호출하는 웹앱](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>심층 살펴보기: ASP.NET Core 웹 앱 자습서

이 ASP.NET Core 자습서를 사용 하 여 사용자를 로그인 하는 다른 방법에 대해 알아봅니다. 

> [!div class="nextstepaction"]
> [개발자를 위한 Microsoft id 플랫폼을 사용 하 여 웹 앱에서 사용자를 로그인 하 고 Api를 호출할 수 있도록 설정](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

이 프로그레시브 자습서에는의 계정을 사용 하 여 로그인을 추가 하는 방법을 비롯 하 여 웹 앱에 대 한 프로덕션 준비 코드가 있습니다.

- 조직
- 여러 조직
- 회사 또는 학교 계정 또는 개인 Microsoft 계정
- [Azure AD B2C](https://aka.ms/aadb2c)
- 국가별 클라우드

## <a name="sample-code-java-web-app"></a>샘플 코드: Java 웹 앱

GitHub에서이 샘플의 Java 웹 앱에 대해 자세히 알아보세요. 

> [!div class="nextstepaction"]
> [Microsoft id 플랫폼을 사용 하 여 사용자를 로그인 하 고를 호출 하는 Java 웹 응용 프로그램 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
