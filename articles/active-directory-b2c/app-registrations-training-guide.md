---
title: Azure AD B2C의 새로운 앱 등록 환경
description: Azure AD B2C의 새로운 앱 등록 환경에 대해 소개 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eed0527b69dcaacd3a8cd0cf7cd178aa2aca3468
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89433911"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대 한 새로운 앱 등록 환경

이제 Azure AD B2C (Azure Active Directory B2C)에 대 한 새로운 **[앱 등록](https://aka.ms/b2cappregistrations)** 환경이 출시 되었습니다. Azure AD B2C에 대 한 응용 프로그램을 등록 하는 **응용 프로그램** 환경에 좀 더 친숙 한 경우이 가이드에서 새로운 환경을 사용 하 여 시작 해 볼 수 있습니다.

## <a name="overview"></a>개요
이전에는 레거시 환경을 사용 하 여 응용 프로그램의 나머지 부분과 별도로 Azure AD B2C 소비자 지향 응용 프로그램을 관리 해야 했습니다. Azure의 여러 위치에서 다양 한 앱 만들기 환경을 의미 합니다.

새 환경은 모든 Azure AD B2C 앱 등록 및 Azure AD 앱 등록을 한 곳에서 표시 하 고 일관 된 방식으로 관리 하는 방법을 제공 합니다. 리소스 관리에 대 한 Microsoft Graph 권한을 사용 하 여 앱을 관리 하는 고객 관련 앱을 만드는 것에서 작업을 수행 하는 한 가지 방법만 배워야 합니다.

Azure Portal의 **Azure AD B2C** 또는 **Azure Active Directory** 서비스 모두에서 Azure AD B2C 테 넌 트의 **앱 등록** 로 이동 하 여 새 환경에 연결할 수 있습니다.

Azure AD B2C 앱 등록 환경은 모든 Azure AD 테 넌 트에 대 한 일반 [앱 등록 환경을](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 기반으로 하지만 Azure AD B2C 테 넌 트에 맞게 조정 됩니다.

## <a name="whats-not-changing"></a>변경 되지 않는 항목
- 응용 프로그램 및 관련 구성은 새 환경에서 있는 그대로 찾을 수 있습니다. 응용 프로그램을 다시 등록할 필요는 없으며, 응용 프로그램의 사용자는 다시 로그인 할 필요가 없습니다.

> [!NOTE]
> 이전에 만든 모든 응용 프로그램을 보려면 **앱 등록** 블레이드로 이동 하 여 **모든 응용 프로그램** 탭을 선택 합니다. 그러면 레거시 환경, 새 환경 및 Azure AD 서비스에서 만든 앱이 표시 됩니다.

## <a name="key-new-features"></a>주요 새로운 기능

-   **통합 된 앱 목록** 에는 하나의 편리한 장소에서 Azure AD B2C 및 Azure AD를 사용 하 여 인증 하는 모든 응용 프로그램이 표시 됩니다. 또한 **만든** 날짜, **인증서 & 암호** 상태, 검색 표시줄 등을 포함 하 여 Azure AD 응용 프로그램에 이미 사용할 수 있는 기능을 활용할 수 있습니다.

-   **결합 된 앱 등록** 을 사용 하면 고객 관련 앱 또는 앱이 Microsoft Graph에 액세스할 수 있는지 여부에 관계 없이 앱을 신속 하 게 등록할 수 있습니다.

- **끝점** 창에서는 openid connect connect 구성, SAML 메타 데이터, Microsoft Graph API 및 [OAuth 2.0 사용자 흐름 끝점](tokens-overview.md#endpoints)을 포함 하 여 시나리오에 대 한 관련 끝점을 신속 하 게 식별할 수 있습니다.

- **Api 권한** 및 **api 공개는** 보다 광범위 한 범위, 권한 및 동의 관리를 제공 합니다. 이제 응용 프로그램에 MS Graph 및 Azure AD Graph 권한을 할당할 수 있습니다.

-   이제 Azure AD B2C를 사용 하 여 인증 하는 앱에 **소유자** 및 **매니페스트** 를 사용할 수 있습니다. [매니페스트 편집기를 사용 하 여](../active-directory/develop/reference-app-manifest.md)등록에 대 한 소유자를 추가 하 고 응용 프로그램 속성을 직접 편집할 수 있습니다.


## <a name="new-supported-account-types"></a>새 지원 계정 유형

새 환경에서는 다음 옵션 중에서 지원 계정 유형을 선택 합니다.
- 이 조직 디렉터리의 계정에만 해당 됩니다.
- 모든 조직 디렉터리의 계정 (모든 Azure AD 디렉터리 – 다중 테 넌 트)
- 모든 조직 디렉터리 또는 모든 id 공급자의 계정 Azure AD B2C를 사용 하 여 사용자를 인증 합니다.

다른 계정 유형을 이해 하려면 만들기 환경에서 **도움말** 선택을 선택 합니다.

레거시 환경에서 앱은 항상 고객 지향 응용 프로그램으로 생성 되었습니다. 이러한 앱에 대해 계정 유형은 **모든 조직 디렉터리 또는 모든 id 공급자의 계정으로 설정 됩니다. Azure AD B2C를 사용 하 여 사용자를 인증**합니다.
> [!NOTE]
> 이 옵션은 Azure AD B2C 사용자 흐름을 실행 하 여이 응용 프로그램에 대 한 사용자를 인증 하는 데 필요 합니다. [사용자 흐름에 사용할 응용 프로그램을 등록 하는 방법](tutorial-register-applications.md) 에 대해 알아봅니다.

이 옵션을 사용 하 여 Azure AD B2C를 SAML 서비스 공급자로 사용할 수도 있습니다. [자세히 알아봅니다](identity-provider-adfs2016-custom.md).

## <a name="applications-for-devops-scenarios"></a>DevOps 시나리오용 응용 프로그램
다른 계정 유형을 사용 하 여 Id 경험 프레임 워크 정책을 업로드 하거나 사용자를 프로 비전 하는 Microsoft Graph 같은 DevOps 시나리오를 관리 하는 앱을 만들 수 있습니다. [Microsoft Graph 응용 프로그램을 등록 하 여 Azure AD B2C 리소스를 관리 하는 방법을](microsoft-graph-get-started.md)알아봅니다.

이러한 권한이 대부분 Azure B2C consumer 사용자에 게 적용 되지 않기 때문에 Microsoft Graph 권한이 모두 표시 되지 않을 수 있습니다. [Microsoft Graph를 사용 하 여 사용자 관리에 대해 자세히](manage-user-accounts-graph-api.md)알아보세요.

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>관리자 동의 및 offline_access + openid connect 범위
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

**Openid connect** 범위는 Azure AD B2C 사용자가 앱에 로그인 할 수 있도록 하는 데 필요 합니다. **Offline_access** 범위는 사용자에 대 한 새로 고침 토큰을 발급 하는 데 필요 합니다. 이러한 범위는 이전에 추가 되었으며 기본적으로 관리자 동의가 부여 되었습니다. 이제 **openid connect 및 offline_access 권한에 관리자 동의 부여** 옵션을 선택 하 여 만들기 프로세스 중에 이러한 범위에 대 한 권한을 쉽게 추가할 수 있습니다. 또는 기존 앱에 대 한 **API 권한** 설정에서 관리자 동의를 사용 하 여 Microsoft Graph 권한을 추가할 수 있습니다.

[권한 및 동의](../active-directory/develop/v2-permissions-and-consent.md)에 대해 자세히 알아보세요.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>플랫폼/인증: 회신 Url/리디렉션 Uri
레거시 환경에서 다양 한 플랫폼 형식은 **속성** 에서 웹 앱/a p i에 대 한 회신 Url 및 Native client에 대 한 리디렉션 URI로 관리 되었습니다. "Native clients"는 "공용 클라이언트" 라고도 하며 iOS, macOS, Android 및 기타 모바일 및 데스크톱 응용 프로그램 유형에 대 한 앱을 포함 합니다.

새 환경에서 회신 Url 및 리디렉션 Uri는 모두 리디렉션 Uri 라고 하며, 앱의 **인증** 섹션에서 찾을 수 있습니다. 앱 등록는 웹 앱 또는 네이티브 응용 프로그램으로 제한 되지 않습니다. 각 리디렉션 Uri를 등록 하 여 이러한 모든 플랫폼 유형에 동일한 앱 등록을 사용할 수 있습니다.

리디렉션 Uri는 웹 또는 공용 (모바일 및 데스크톱) 앱 유형과 연결 해야 합니다. [리디렉션 Uri에 대 한 자세한 정보](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

**IOS/macOS** 및 **Android** 플랫폼은 공용 클라이언트의 한 유형입니다. MSAL과 함께 사용 하기 위해 적절 한 리디렉션 Uri로 iOS/macOS 또는 Android 앱을 구성 하는 쉬운 방법을 제공 합니다. [응용 프로그램 구성 옵션](../active-directory/develop/msal-client-applications.md)에 대해 자세히 알아보세요.


## <a name="application-certificates--secrets"></a>응용 프로그램 인증서 & 비밀

**키**대신 새 환경에서 인증서 **& 비밀** 블레이드를 사용 하 여 인증서 및 비밀을 관리 합니다. 인증서 & 암호를 사용 하면 응용 프로그램은 HTTPS 체계를 사용 하 여 웹 주소 지정 가능한 위치에서 토큰을 받을 때 인증 서비스에서 자신을 식별할 수 있습니다. Azure AD에 대해 인증할 때 클라이언트 자격 증명 시나리오에 클라이언트 암호 대신 인증서를 사용 하는 것이 좋습니다. 인증서를 사용 하 여 Azure AD B2C에 대해 인증할 수 없습니다.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Azure AD B2C 테 넌 트에 해당 되지 않는 기능
다음 Azure AD 앱 등록 기능은 Azure AD B2C 테 넌 트에서 적용 되지 않거나 사용할 수 없습니다.
- **역할 및 관리자** -현재 Azure AD B2C에 사용할 수 없는 Azure AD Premium P1 또는 P2 라이선스가 필요 합니다.
- **브랜딩** -UI/UX 사용자 지정은 **회사 브랜딩** 환경 또는 사용자 흐름의 일부로 구성 됩니다. [Azure Active Directory B2C에서 사용자 인터페이스를 사용자 지정 하는](customize-ui-overview.md)방법을 알아봅니다.
- **게시자 도메인 확인** -앱이 확인 된 도메인이 아닌 onmicrosoft.com에 등록 되어 *있습니다*. 또한 게시자 도메인은 사용자 동의를 부여 하는 데 주로 사용 되며, 사용자 인증을 위해 Azure AD B2C 앱에는 적용 되지 않습니다. [게시자 도메인에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/develop/howto-configure-publisher-domain).
- **토큰 구성** -토큰이 앱이 아닌 사용자 흐름의 일부로 구성 됩니다.
- 현재 Azure AD B2C 테 넌 **트에 대해 빠른 시작 환경을 사용할** 수 없습니다.
- 현재 Azure AD B2C 테 넌 트에 대해 **Integration assistant** 블레이드를 사용할 수 없습니다.


## <a name="limitations"></a>제한 사항
새 환경에는 다음과 같은 제한 사항이 있습니다.
- 현재 암시적 흐름에 대 한 액세스 또는 ID 토큰을 발급할 수 있는지 여부를 구분 하지 Azure AD B2C. **인증** 블레이드에서 **ID 토큰** 옵션을 선택한 경우에는 두 가지 토큰 유형 모두 암시적 부여 흐름에 사용할 수 있습니다.
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- 지원 되는 계정에 대 한 값 변경은 UI에서 지원 되지 않습니다. Azure AD 단일 테 넌 트와 다중 테 넌 트 간을 전환 하는 경우가 아니면 앱 매니페스트를 사용 해야 합니다.

## <a name="next-steps"></a>다음 단계

새 앱 등록 환경을 시작 하려면 다음을 수행 하세요.
* [웹 응용 프로그램을 등록 하는 방법](tutorial-register-applications.md)에 대해 알아봅니다.
* [WEB API를 등록 하는 방법을](add-web-api-application.md)알아봅니다.
* [네이티브 클라이언트 응용 프로그램을 등록 하는 방법](add-native-application.md)에 대해 알아봅니다.
* [Microsoft Graph 응용 프로그램을 등록 하 여 Azure AD B2C 리소스를 관리 하는 방법을](microsoft-graph-get-started.md)알아봅니다.
* [Azure AD B2C를 SAML 서비스 공급자로 사용 하는 방법을 알아봅니다.](identity-provider-adfs2016-custom.md)
* [응용 프로그램 종류](application-types.md)에 대해 알아봅니다.
