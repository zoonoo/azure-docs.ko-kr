---
title: Microsoft ID 플랫폼을 사용하는 앱 로그인 흐름 | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼(v2.0)에서 웹, 데스크톱 및 모바일 앱의 로그인 흐름에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: af5b27dc85a276c731a61135ab59ab81f5aaf3c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83772202"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>Microsoft ID 플랫폼을 사용하는 앱 로그인 흐름

이 항목에서는 Microsoft ID 플랫폼을 사용하는 웹, 데스크톱 및 모바일 앱에 대한 기본 로그인 흐름에 대해 설명합니다. Microsoft ID 플랫폼에서 지원하는 로그인 시나리오에 대한 자세한 내용은 [인증 흐름 및 앱 시나리오](authentication-flows-app-scenarios.md)를 참조하세요.

## <a name="web-app-sign-in-flow"></a>웹앱 로그인 흐름

사용자가 브라우저에서 웹앱으로 이동하면 다음 작업이 수행됩니다.

* 웹앱은 사용자가 인증되었는지 여부를 확인합니다.
* 사용자가 인증되지 않은 경우 웹앱은 사용자를 로그인하도록 Azure AD에 위임합니다. 해당 로그인은 조직의 정책을 준수합니다. 따라서 사용자에게 자격 증명을 입력하도록 요구하거나, [다단계 인증](../authentication/concept-mfa-howitworks.md)(2단계 인증 또는 2FA라고도 함)을 사용하거나, 암호를 전혀 사용하지 않을 수 있습니다(예: Windows Hello 사용).
* 사용자는 클라이언트 앱에 필요한 액세스에 동의하라는 요청을 받습니다. 이때문에 클라이언트 앱을 Azure AD에 등록해야 하며, Microsoft ID 플랫폼은 사용자가 동의한 액세스를 나타내는 토큰을 제공할 수 있습니다.

사용자가 인증되면 다음 결과가 나타납니다.

* Microsoft ID 플랫폼이 웹앱에 토큰을 보냅니다.
* 쿠키가 저장되고, 브라우저의 쿠키 보관함에 있는 사용자의 ID를 포함하는 Azure AD의 도메인과 연결됩니다. 다음번에 앱이 브라우저를 사용하여 Microsoft ID 플랫폼 권한 부여 엔드포인트로 이동되면 브라우저는 사용자가 다시 로그인할 필요가 없도록 쿠키를 표시합니다. 이것은 SSO가 구현되는 방식이기도 합니다. 쿠키는 Azure AD에서 생성되며 Azure AD에서만 이해할 수 있습니다.
* 그런 후에 웹앱에서 토큰의 유효성을 검사합니다. 유효성 검사가 성공하면 웹앱은 보호된 페이지를 표시하고 세션 쿠키를 브라우저의 쿠키 보관함에 저장합니다. 사용자가 다른 페이지로 이동하면 웹앱은 세션 쿠키에 따라 사용자가 인증되었음을 알게 됩니다.

다음 시퀀스 다이어그램은 이러한 상호 작용을 요약해서 보여 줍니다.

![웹앱 인증 프로세스](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>웹앱은 사용자가 인증되었는지 여부를 확인하는 방법

웹앱 개발자는 모든 페이지에 인증이 필요할지 또는 특정 페이지에만 인증이 필요한지를 나타낼 수 있습니다. 예를 들어 ASP.NET/ASP.NET Core에서는 컨트롤러 작업에 `[Authorize]` 특성을 추가하여 이 작업을 수행합니다.

이 특성을 사용하면 ASP.NET은 사용자의 ID가 포함된 세션 쿠키의 존재 여부를 확인합니다. 쿠키가 없는 경우 ASP.NET은 인증을 지정된 ID 공급자로 리디렉션합니다. ID 공급자가 Azure AD인 경우 웹앱은 인증을 `https://login.microsoftonline.com`으로 리디렉션하고 로그인 대화 상자를 표시합니다.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>웹앱이 Microsoft ID 플랫폼에 로그인을 위임하고 토큰을 가져오는 방법

브라우저를 통해 사용자 인증이 수행됩니다. OpenID 프로토콜은 표준 HTTP 프로토콜 메시지를 사용합니다.

* 웹앱은 Microsoft ID 플랫폼을 사용하기 위해 HTTP 302(리디렉션)를 브라우저로 보냅니다.
* 사용자가 인증되면 Microsoft ID 플랫폼은 브라우저를 통해 리디렉션을 사용하여 웹앱에 토큰을 보냅니다.
* 리디렉션은 웹앱에서 리디렉션 URI 형식으로 제공됩니다. 이 리디렉션 URI는 Azure AD 애플리케이션 개체에 등록됩니다. 애플리케이션을 여러 URI에 배포할 수 있기 때문에 여러 리디렉션 URI가 있을 수 있습니다. 따라서 웹앱은 사용할 리디렉션 URI도 지정해야 합니다.
* Azure AD는 웹앱에서 보낸 리디렉션 URI가 앱에 대해 등록된 리디렉션 URI 중 하나인지 확인합니다.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>데스크톱 및 모바일 앱 로그인 흐름

위에서 설명한 흐름은 약간의 차이가 있지만 데스크톱 및 모바일 애플리케이션에 적용됩니다.

데스크톱 및 모바일 애플리케이션은 인증을 위해 포함된 웹 컨트롤 또는 시스템 브라우저를 사용할 수 있습니다. 다음 다이어그램에서는 데스크톱 또는 모바일 앱이 MSAL(Microsoft 인증 라이브러리)을 사용하여 액세스 토큰을 획득하고 Web API를 호출하는 방법을 보여 줍니다.

![데스크톱 앱이 표시되는 방식](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL은 브라우저를 사용하여 토큰을 가져옵니다. 웹앱과 마찬가지로 인증은 Microsoft ID 플랫폼에 위임됩니다.

Azure AD는 웹앱의 경우와 동일한 ID 쿠키를 브라우저에 저장하므로 네이티브 또는 모바일 앱이 시스템 브라우저를 사용하는 경우 해당 웹앱을 사용하여 SSO를 즉시 가져옵니다.

기본적으로 MSAL은 시스템 브라우저를 사용합니다. 단, 포함된 컨트롤을 사용하여 보다 강력하게 통합된 사용자 환경을 제공하는 .NET Framework 데스크톱 애플리케이션은 예외입니다.

## <a name="next-steps"></a>다음 단계

인증 및 권한 부여 기본 사항을 설명하는 기타 항목:

* Microsoft ID 플랫폼의 인증 및 권한 부여의 기본 사항에 대해서는 [인증과 권한 부여의 차이점](authentication-vs-authorization.md)을 참조하세요.
* 인증 및 권한 부여에 액세스 토큰, 새로 고침 토큰 및 ID 토큰을 사용하는 방법에 대한 자세한 내용은 [보안 토큰](security-tokens.md)을 참조하세요.
* Microsoft ID 플랫폼과 통합될 수 있도록 애플리케이션을 등록하는 프로세스에 대해 알아보려면 [애플리케이션 모델](application-model.md)을 참조하세요.

앱 로그인 흐름에 대한 자세한 내용은 다음을 확인하세요.

* Microsoft ID 플랫폼에서 지원하는 기타 사용자 인증 시나리오에 대해 자세히 알아보려면 [인증 흐름 및 앱 시나리오](authentication-flows-app-scenarios.md)를 참조하세요.
* Microsoft 계정, Azure AD 계정 및 Azure AD B2C 사용자를 모두 간소화된 단일 프로그래밍 모델에서 사용하는 애플리케이션을 개발하는 데 도움이 되는 Microsoft 라이브러리에 대해 알아보려면 [MSAL 라이브러리](msal-overview.md)를 참조하세요.
