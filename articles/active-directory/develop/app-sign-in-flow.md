---
title: Microsoft id 플랫폼을 사용 하는 앱 로그인 흐름 | Microsoft
titleSuffix: Microsoft identity platform
description: V2.0 (Microsoft identity platform)에서 웹, 데스크톱 및 모바일 앱의 로그인 흐름에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 7b326e17611b5f4b9520d8218a28a67afe9a851a
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584351"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>Microsoft id 플랫폼을 사용 하는 앱 로그인 흐름

이 항목에서는 Microsoft id 플랫폼을 사용 하는 웹, 데스크톱 및 모바일 앱에 대 한 기본 로그인 흐름에 대해 설명 합니다. Microsoft id 플랫폼에서 지 원하는 로그인 시나리오에 대해 알아보려면 [인증 흐름 및 앱 시나리오](authentication-flows-app-scenarios.md) 를 참조 하세요.

## <a name="web-app-sign-in-flow"></a>웹 앱 로그인 흐름

사용자가 브라우저에서 웹 앱으로 이동 하면 다음 작업이 수행 됩니다.

* 웹 앱은 사용자가 인증 되었는지 여부를 확인 합니다.
* 사용자가 인증 되지 않은 경우 웹 앱은 사용자를 로그인 하기 위해 Azure AD에 위임 합니다. 로그인은 조직의 정책을 준수 하 게 됩니다 .이는 사용자에 게 사용자에 게 자격 증명을 입력 하도록 요청 하거나 (예: Windows Hello 사용), 암호를 사용 하지 않고 multi-factor authentication을 사용 하 여 사용자에 게 요청 하는 것을 의미할 수 있습니다.
* 사용자에 게 클라이언트 앱에 필요한 액세스에 동의 하 라는 메시지가 표시 됩니다. 이 때문에 Microsoft id 플랫폼이 사용자가 동의한 액세스를 나타내는 토큰을 제공할 수 있도록 클라이언트 앱을 Azure AD에 등록 해야 합니다.

사용자가 성공적으로 인증 된 경우:

* Microsoft id 플랫폼은 웹 앱에 토큰을 보냅니다.
* 쿠키는 브라우저의 쿠키 jar에 있는 사용자의 id를 포함 하는 Azure AD의 도메인과 연결 되어 저장 됩니다. 다음에 앱이 브라우저를 사용 하 여 Microsoft id 플랫폼 권한 부여 끝점으로 이동 하면 브라우저는 사용자가 다시 로그인 할 필요가 없도록 쿠키를 표시 합니다. 이는 SSO를 구현 하는 방법 이기도 합니다. 쿠키는 Azure AD에서 생성 되며 Azure AD 에서만 이해할 수 있습니다.
* 그러면 웹 앱에서 토큰의 유효성을 검사 합니다. 유효성 검사가 성공 하면 웹 앱은 보호 된 페이지를 표시 하 고 세션 쿠키를 브라우저의 쿠키 jar에 저장 합니다. 사용자가 다른 페이지로 이동 하면 웹 앱은 세션 쿠키에 따라 사용자가 인증 되었음을 인식 합니다.

다음 시퀀스 다이어그램은 이러한 상호 작용을 요약 합니다.

![웹 앱 인증 프로세스](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>웹 앱에서 사용자 인증 여부를 결정 하는 방법

웹 앱 개발자는 모든 또는 특정 페이지에 인증이 필요한 지 여부를 나타낼 수 있습니다. 예를 들어 ASP.NET/ASP.NET Core에서는 컨트롤러 작업에 `[Authorize]` 특성을 추가 하 여이 작업을 수행 합니다.

이 특성을 사용 하면 ASP.NET는 사용자의 id가 포함 된 세션 쿠키의 존재 여부를 확인 합니다. 쿠키가 없는 경우 ASP.NET는 인증을 지정 된 id 공급자로 리디렉션합니다. Id 공급자가 Azure AD 인 경우 웹 앱은 로그인 대화 상자를 `https://login.microsoftonline.com`표시 하는 인증을로 리디렉션합니다.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>웹 앱이 Microsoft id 플랫폼에 로그인을 위임 하 고 토큰을 가져오는 방법

브라우저를 통해 사용자 인증이 수행 됩니다. Openid connect 프로토콜은 표준 HTTP 프로토콜 메시지를 사용 합니다.

* 웹 앱은 Microsoft id 플랫폼을 사용 하기 위해 HTTP 302 (리디렉션)을 브라우저로 보냅니다.
* 사용자가 인증 되 면 Microsoft id 플랫폼은 브라우저를 통해 리디렉션을 사용 하 여 웹 앱에 토큰을 보냅니다.
* 리디렉션은 웹 앱에서 리디렉션 URI 형식으로 제공 됩니다. 이 리디렉션 URI는 Azure AD 응용 프로그램 개체에 등록 됩니다. 응용 프로그램을 여러 Url에 배포할 수 있기 때문에 여러 리디렉션 Uri가 있을 수 있습니다. 따라서 웹 앱은 사용할 리디렉션 URI도 지정 해야 합니다.
* Azure AD는 웹 앱에서 보낸 리디렉션 URI가 앱에 대해 등록 된 리디렉션 uri 중 하나 인지 확인 합니다.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>데스크톱 및 모바일 앱 로그인 흐름

위에서 설명한 흐름은 데스크톱 및 모바일 응용 프로그램에 약간의 차이가 있습니다.

데스크톱 및 모바일 응용 프로그램은 인증을 위해 포함 된 웹 컨트롤 또는 시스템 브라우저를 사용할 수 있습니다. 다음 다이어그램에서는 데스크톱 또는 모바일 앱이 MSAL (Microsoft 인증 라이브러리)을 사용 하 여 액세스 토큰을 획득 하 고 web Api를 호출 하는 방법을 보여 줍니다.

![데스크톱 앱이 표시 되는 방식](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL은 브라우저를 사용 하 여 토큰을 가져옵니다. 웹 앱과 마찬가지로 인증은 Microsoft id 플랫폼에 위임 됩니다.

Azure AD는 웹 앱과 동일한 id 쿠키를 브라우저에 저장 하므로 네이티브 또는 모바일 앱이 시스템 브라우저를 사용 하는 경우 해당 웹 앱을 사용 하 여 SSO를 즉시 가져옵니다.

기본적으로 MSAL은 시스템 브라우저를 사용 합니다. 단, 포함 된 컨트롤을 사용 하 여 보다 통합 된 사용자 환경을 제공 하는 데스크톱 응용 프로그램 .NET Framework는 예외입니다.

## <a name="next-steps"></a>다음 단계

인증 및 권한 부여 기본 사항에 대해 설명 하는 다른 항목:

* Microsoft id 플랫폼의 인증 및 권한 부여에 대 한 기본 개념에 대해 알아보려면 [인증 및 권한 부여](authentication-vs-authorization.md) 를 참조 하세요.
* 인증 및 권한 부여에서 액세스 토큰, 새로 고침 토큰 및 ID 토큰을 사용 하는 방법을 알아보려면 [보안 토큰](security-tokens.md) 을 참조 하세요.
* Microsoft id 플랫폼과 통합 될 수 있도록 응용 프로그램을 등록 하는 프로세스에 대해 알아보려면 [응용 프로그램 모델](application-model.md) 을 참조 하세요.

앱 로그인 흐름에 대 한 자세한 내용은 다음을 확인 하세요.

* Microsoft id 플랫폼에서 지원 되는 사용자를 인증 하는 다른 시나리오에 대해 자세히 알아보려면 [인증 흐름 및 앱 시나리오](authentication-flows-app-scenarios.md) 를 참조 하세요.
* Microsoft 계정, Azure AD 계정 및 Azure AD B2C 사용자를 모두 간소화 된 단일 프로그래밍 모델로 사용 하는 응용 프로그램을 개발 하는 데 도움이 되는 Microsoft 라이브러리에 대해 알아보려면 [Msal 라이브러리](msal-overview.md) 를 참조 하세요.
