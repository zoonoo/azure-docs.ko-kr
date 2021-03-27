---
title: 인증 및 권한 부여
description: Azure App Service 및 Azure Functions에서 기본 제공 되는 인증 및 권한 부여 지원과, 무단 액세스 로부터 앱을 보호 하는 방법에 대해 알아봅니다.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 35513abdfb61d889abdbd4af7125b1fbb556d7b8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612758"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Azure App Service 및 Azure Functions의 인증 및 권한 부여

Azure App Service는 기본 제공 인증 및 권한 부여 기능 ("Easy Auth"이 라고도 함)을 제공 하므로 사용자를 로그인 하 고 웹 앱, RESTful API 및 모바일 백 엔드에서 코드를 최소한으로 작성 하 여 데이터에 액세스 하 고 [Azure Functions](../azure-functions/functions-overview.md)수도 있습니다. 이 문서는 App Service가 앱의 인증 및 권한 부여를 단순화하는 방법에 대해 설명합니다.

## <a name="why-use-the-built-in-authentication"></a>기본 제공 인증을 사용 하는 이유

인증 및 권한 부여에는이 기능을 사용할 필요가 없습니다. 선택한 웹 프레임 워크에서 번들로 제공 되는 보안 기능을 사용 하거나 사용자 고유의 유틸리티를 작성할 수 있습니다. 그러나 최신 보안, 프로토콜 및 브라우저 업데이트를 사용 하 여 솔루션이 최신 상태를 유지 하는지 확인 해야 합니다.

인증 (로그인 사용자) 및 권한 부여 (보안 데이터에 대 한 액세스 제공)에 대 한 보안 솔루션을 구현 하면 상당한 노력이 소요 될 수 있습니다. 업계 모범 사례와 표준을 따르고 구현을 최신 상태로 유지 해야 합니다. App Service 및 Azure Functions에 대 한 기본 제공 인증 기능은 페더레이션 id 공급자를 사용 하 여 기본 인증을 제공 하 여 시간과 노력을 절감할 수 있으므로 응용 프로그램의 나머지 부분에 집중할 수 있습니다.

- Azure App Service를 사용 하 여 다양 한 인증 기능을 직접 구현 하지 않고도 웹 앱 또는 API에 통합할 수 있습니다.
- 플랫폼에 직접 빌드되고 특정 언어, SDK, 보안 기술 또는 사용할 코드도 필요 하지 않습니다.
- 여러 로그인 공급자와 통합할 수 있습니다. 예: Azure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>ID 공급자

App Service는 [페더레이션 ID](https://en.wikipedia.org/wiki/Federated_identity)를 사용하며 여기서 타사 ID 공급자는 사용자 ID와 인증 흐름을 관리합니다. 기본적으로 다음 id 공급자를 사용할 수 있습니다.

| 공급자 | 로그인 엔드포인트 | How-To 지침 |
| - | - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [Azure AD 로그인 App Service](configure-authentication-provider-aad.md) |
| [Microsoft 계정](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` | [Microsoft 계정 로그인 App Service](configure-authentication-provider-microsoft.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [Facebook 로그인 App Service](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [Google 로그인 App Service](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [Twitter 로그인 App Service](configure-authentication-provider-twitter.md) |
| 모든 [Openid connect Connect](https://openid.net/connect/) 공급자 (미리 보기) | `/.auth/login/<providerName>` | [App Service Openid connect Connect 로그인](configure-authentication-provider-openid-connect.md) |

이러한 공급자중 하나를 사용하여 인증 및 권한 부여를 활성화하면 사용자 인증과 공급자의 인증 토큰 유효성 검사에 로그인 엔드포인트를 사용할 수 있습니다. 사용자에 게 원하는 수의 로그인 옵션을 제공할 수 있습니다.

## <a name="considerations-for-using-built-in-authentication"></a>기본 제공 인증 사용에 대 한 고려 사항

이 기능을 사용 하도록 설정 하면 HTTPS를 적용 하는 App Service 구성 설정에 관계 없이 응용 프로그램에 대 한 모든 요청이 HTTPS로 자동 리디렉션됩니다. V2 구성의 설정으로이 기능을 사용 하지 않도록 설정할 수 있습니다  `requireHttps` . 그러나 HTTPS를 사용 하는 것이 좋지만 보안 되지 않은 HTTP 연결을 통해 전송 되는 보안 토큰이 없도록 해야 합니다.

사이트 콘텐츠 및 Api에 대 한 액세스를 제한 하거나 사용 하지 않고 인증을 위해 App Service를 사용할 수 있습니다. 앱 액세스를 인증 된 사용자로만 제한 하려면 **요청이 인증 되지 않은 경우 수행할 작업** 을 구성 된 id 공급자 중 하나로 로그인 하도록 설정 합니다. 인증을 하지만 액세스를 제한 하지 않으려면 "익명 요청 허용 (작업 없음)"에 대 한 **요청이 인증 되지 않은 경우 수행할 작업** 을 설정 합니다.

> [!NOTE]
> 각 앱 등록에 고유한 권한 및 동의를 제공 해야 합니다. 환경 간에 권한을 공유하는 일이 없도록 배포 슬롯마다 별도의 앱 등록을 사용합니다. 이렇게 하면 새 코드를 테스트할 때 문제가 프로덕션 앱에 영향을 주는 것을 방지할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

[Windows의 기능 아키텍처 (컨테이너가 아닌 배포)](#feature-architecture-on-windows-non-container-deployment)

[Linux 및 컨테이너의 기능 아키텍처](#feature-architecture-on-linux-and-containers)

[인증 흐름](#authentication-flow)

[권한 부여 동작](#authorization-behavior)

[사용자 및 응용 프로그램 클레임](#user-and-application-claims)

[토큰 저장소](#token-store)

[로깅 및 추적](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Windows의 기능 아키텍처 (컨테이너가 아닌 배포)

인증 및 권한 부여 모듈은 애플리케이션 코드와 동일한 샌드박스에서 실행됩니다. 이 기능이 활성화되면 애플리케이션 코드에 의해 처리되기 전에 들어오는 모든 HTTP 요청이 여기를 통과합니다.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="배포 된 사이트에 대 한 트래픽을 허용 하기 전에 id 공급자와 상호 작용 하는 사이트 샌드박스에서 프로세스에서 가로채는 요청을 보여 주는 아키텍처 다이어그램" lightbox="media/app-service-authentication-overview/architecture.png":::

이 모듈은 다음과 같이 앱에 대한 몇 가지 사항을 처리합니다.

- 지정된 공급자를 사용하여 사용자 인증
- 토큰의 유효성 검사, 저장 및 새로 고침
- 인증된 세션 관리
- 요청 헤더에 ID 정보 삽입

모듈은 애플리케이션 코드와 별도로 실행되며 앱 설정을 사용하여 구성됩니다. SDK, 특정 언어 또는 애플리케이션 코드의 변경이 필요하지 않습니다. 

#### <a name="feature-architecture-on-linux-and-containers"></a>Linux 및 컨테이너의 기능 아키텍처

인증 및 권한 부여 모듈은 응용 프로그램 코드와 분리 된 별도의 컨테이너에서 실행 됩니다. [특사 패턴](/azure/architecture/patterns/ambassador)으로 알려진 기능을 사용 하면 들어오는 트래픽과 상호 작용 하 여 Windows에서와 비슷한 기능을 수행할 수 있습니다. In-process로 실행 되지 않으므로 특정 언어 프레임 워크와의 직접 통합이 가능 하지 않습니다. 그러나 앱에 필요한 관련 정보는 아래에 설명 된 대로 요청 헤더를 사용 하 여 전달 됩니다.

#### <a name="authentication-flow"></a>인증 흐름

인증 흐름은 모든 공급자에 대해 동일하지만 공급자의 SDK로 로그인하는지 여부에 따라 다릅니다.

- 공급자 SDK가 없는 경우: 애플리케이션은 페드레이션 로그인을 App Service에 위임합니다. 이는 일반적으로 공급자의 로그인 페이지를 사용자에게 제공할 수 있는 브라우저 앱을 사용하는 경우입니다. 서버 코드는 로그인 프로세스를 관리하므로 _서버 방향 흐름_ 또는 _서버 흐름_ 이라고도 합니다. 이 경우는 브라우저 앱에 적용됩니다. 또한 SDK가 App Service 인증을 사용하여 사용자를 로그인시키기 위해 웹 보기를 열기 때문에 Mobile Apps 클라이언트 SDK를 사용하여 사용자를 로그인시키는 네이티브 앱에도 적용됩니다.
- 공급자 SDK 사용: 애플리케이션은 사용자를 수동으로 공급자에 로그인시킨 다음, 유효성 검사를 위해 인증 토큰을 App Service에 제출합니다. 이는 일반적으로 공급자의 로그인 페이지를 사용자에게 제공할 수 없는 브라우저리스 앱을 사용하는 경우입니다. 애플리케이션 코드는 로그인 프로세스를 관리하므로 _클라이언트 방향 흐름_ 또는 _클라이언트 흐름_ 이라고도 합니다. 이러한 경우는 REST API, [Azure Functions](../azure-functions/functions-overview.md) 및 JavaScript 브라우저 클라이언트뿐만 아니라 로그인 프로세스에서 더 많은 유연성이 필요한 브라우저 앱에도 적용됩니다. 공급자의 SDK를 사용하여 사용자를 로그인시키는 네이티브 모바일 앱에도 적용됩니다.

App Service의 신뢰할 수 있는 브라우저 앱에서 App Service 또는 [Azure Functions](../azure-functions/functions-overview.md) 의 다른 REST API에 대 한 호출은 서버 지향 흐름을 사용 하 여 인증할 수 있습니다. 자세한 내용은 [App Service에서 인증 및 권한 부여 사용자 지정](app-service-authentication-how-to.md)을 참조하세요.

아래 표는 인증 흐름 단계를 보여줍니다.

| 단계 | SDK 공급자가 없는 경우 | SDK 공급자가 있는 경우 |
| - | - | - |
| 1. 사용자 로그인 | 클라이언트를 `/.auth/login/<provider>`로 리디렉션합니다. | 클라이언트 코드는 공급자의 SDK를 사용하여 사용자를 직접 로그인시키고 인증 토큰을 받습니다. 자세한 내용은 공급자 설명서를 참조하세요. |
| 2. 인증 후 | 공급자가 클라이언트를 `/.auth/login/<provider>/callback`으로 리디렉션합니다. | 클라이언트 코드는 유효성 검사를 위해 [공급자에서로 토큰을 게시](app-service-authentication-how-to.md#validate-tokens-from-providers) `/.auth/login/<provider>` 합니다. |
| 3. 인증 된 세션 설정 | App Service는 인증된 쿠키를 응답에 추가합니다. | App Service는 자체 인증 토큰을 클라이언트 코드로 반환합니다. |
| 4. 인증 된 콘텐츠 제공 | 클라이언트는 후속 요청에 인증 쿠키를 포함합니다(브라우저에 의해 자동 처리됨). | 클라이언트 코드는 `X-ZUMO-AUTH` 헤더에 인증 토큰을 제공합니다(Mobile Apps 클라이언트 SDK에 의해 자동 처리됨). |

클라이언트 브라우저의 경우 App Service는 인증되지 않은 모든 사용자를 자동으로 `/.auth/login/<provider>`로 보냅니다. 사용자 자신이 선택한 제공자를 사용하여 앱에 로그인할 수 있도록 하나 이상의 `/.auth/login/<provider>` 링크를 사용자에게 할 수도 있습니다.

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>권한 부여 동작

[Azure Portal](https://portal.azure.com)에서 들어오는 요청이 인증 되지 않은 경우 여러 동작을 사용 하 여 App Service 권한 부여를 구성할 수 있습니다.

!["요청이 인증 되지 않은 경우 수행할 작업" 드롭다운을 보여 주는 스크린샷](media/app-service-authentication-overview/authorization-flow.png)

다음 제목은 옵션을 설명합니다.

**익명 요청 허용 (작업 없음)**

이 옵션은 응용 프로그램 코드에 대 한 인증 되지 않은 트래픽의 권한 부여를 지연 시킵니다. 인증된 요청의 경우 App Service는 HTTP 헤더의 인증 정보도 전달합니다.

이 옵션은 익명 요청을 보다 유연하게 처리할 수 있습니다. 예를 들어 [여러 로그인 공급자](app-service-authentication-how-to.md#use-multiple-sign-in-providers)를 사용자에게 제공할 수 있습니다. 그러나 코드를 작성해야 합니다.

**인증된 요청만 허용**

옵션은 **로 \<provider> 로그인** 합니다. App Service는 사용자가 선택한 공급자에 대한 모든 익명 요청을 `/.auth/login/<provider>`로 리디렉션합니다. 익명의 요청이 네이티브 모바일 앱에서 오는 경우 반환된 응답은 `HTTP 401 Unauthorized`입니다.

이 옵션을 사용하면 앱에서 인증 코드를 작성할 필요가 없습니다. 역할별 권한 부여와 같이 보다 정교한 권한 부여는 사용자의 클레임을 검사하여 처리할 수 있습니다([사용자 클레임 액세스](app-service-authentication-how-to.md#access-user-claims) 참조).

> [!CAUTION]
> 이러한 방식으로 액세스를 제한 하는 것은 앱에 대 한 모든 호출에 적용 됩니다 .이는 여러 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지를 사용 하는 앱에는 바람직하지 않을 수 있습니다.

> [!NOTE]
> 기본적으로 Azure AD 테 넌 트의 모든 사용자는 Azure AD에서 응용 프로그램에 대 한 토큰을 요청할 수 있습니다. 앱에 대 한 액세스를 정의 된 사용자 집합으로 제한 하려는 경우 [AZURE AD에서 응용 프로그램을 구성할](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) 수 있습니다.


#### <a name="user-and-application-claims"></a>사용자 및 응용 프로그램 클레임

모든 언어 프레임 워크에 대해 App Service는 들어오는 토큰의 클레임 (인증 된 최종 사용자 또는 클라이언트 응용 프로그램에서 온 것이 든 관계 없이)을 요청 헤더에 삽입 하 여 코드에서 사용할 수 있도록 합니다. ASP.NET 4.6 응용 프로그램의 경우 App Service는 인증된 사용자의 클레임을 사용하여 [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current)를 채우기 때문에 `[Authorize]` 특성을 비롯한 표준 .NET 코드 패턴을 따를 수 있습니다. 마찬가지로 PHP 앱의 경우, App Service는 `_SERVER['REMOTE_USER']` 변수를 채웁니다. Java 앱의 경우 [Tomcat 서블릿에서 클레임에 액세스할 수](configure-language-java.md#authenticate-users-easy-auth)있습니다.

[Azure Functions](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` .net 코드에 대해 채워지지 않지만 요청 헤더에서 사용자 클레임을 찾거나 `ClaimsPrincipal` 요청 컨텍스트에서 또는 바인딩 매개 변수를 통해 개체를 가져올 수 있습니다. 자세한 내용은 [클라이언트 id 작업](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) 을 참조 하세요.

자세한 내용은 [사용자 클레임 액세스](app-service-authentication-how-to.md#access-user-claims)를 참조하세요.

현재 ASP.NET Core는 현재 사용자를 인증/권한 부여 기능으로 채우는 기능을 지원 하지 않습니다. 그러나 이러한 차이를 해결 하기 위해 일부 타사 [오픈 소스 미들웨어 구성 요소가](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) 존재 합니다.

#### <a name="token-store"></a>토큰 저장소

App Service는 웹앱, API 또는 기본 모바일 앱의 사용자와 연결된 토큰 리포지토리인 내장 토큰 저장소를 제공합니다. 공급자와 인증을 사용하도록 설정하면 이 토큰 저장소를 앱에서 즉시 사용할 수 있습니다. 다음과 같이 애플리케이션 코드가 사용자를 대신하여 이러한 공급자의 데이터에 액세스해야 하는 경우,

- 인증된 사용자의 Facebook 타임라인에 게시
- Microsoft Graph API를 사용 하 여 사용자의 회사 데이터 읽기

일반적으로 애플리케이션에서 이러한 토큰을 수집, 저장 및 새로 고치는 코드를 작성해야 합니다. 토큰 저장소를 사용하면 토큰이 필요할 때 [토큰을 가져오고](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) 토큰이 무효화되면 [App Service에 알려 이를 새로 고치도록](app-service-authentication-how-to.md#refresh-identity-provider-tokens) 해야 합니다. 

ID 토큰, 액세스 토큰 및 새로 고침 토큰은 인증 된 세션에 대해 캐시 되며 연결 된 사용자만 액세스할 수 있습니다.  

앱에서 토큰을 사용할 필요가 없는 경우 앱의 **인증/권한 부여** 페이지에서 토큰 저장소를 사용 하지 않도록 설정할 수 있습니다.

#### <a name="logging-and-tracing"></a>로깅 및 추적

[애플리케이션 로깅을 사용하도록 설정](troubleshoot-diagnostic-logs.md)하면 로그 파일에서 인증 및 권한 추적을 바로 볼 수 있습니다. 예상치 못한 인증 오류가 표시 되 면 기존 응용 프로그램 로그를 확인 하 여 모든 세부 정보를 편리 하 게 찾을 수 있습니다. [실패한 요청 추적](troubleshoot-diagnostic-logs.md)을 사용하면 실패한 요청에서 인증 및 권한 부여 모듈이 수행한 역할을 정확히 볼 수 있습니다. 추적 로그에서 `EasyAuthModule_32/64`라는 모듈에 대한 참조를 찾습니다.

## <a name="more-resources"></a>추가 리소스

- [방법: Azure AD 로그인을 사용 하도록 App Service 또는 Azure Functions 앱 구성](configure-authentication-provider-aad.md)
- [Azure App Service의 고급 인증 및 권한 부여 사용](app-service-authentication-how-to.md)

샘플:
- [자습서: Azure App Service에서 실행되는 웹앱에 인증 추가](scenario-secure-app-authentication-app-service.md)
- [자습서: Azure App Service (Windows 또는 Linux)에서 종단 간 사용자 인증 및 권한 부여](tutorial-auth-aad.md)
- [Azure AppService EasyAuth (타사)의 .NET Core 통합](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [.NET Core (타사)를 사용 하 여 Azure App Service 인증 가져오기](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)
