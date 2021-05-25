---
title: Microsoft ID 플랫폼의 모범 사례 | Azure
description: Microsoft ID 플랫폼과 통합 시 모범 사례, 권장 사항 및 일반적인 실수에 대해 알아보세요.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: a6a7bf24571660d8e728c1acba29af2504539a18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219963"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft ID 플랫폼 모범 사례 및 권장 사항

이 문서에서는 Microsoft ID 플랫폼과 통합 시 모범 사례, 권장 사항 및 일반적인 실수에 대해 중점적으로 살펴봅니다.  이 검사 목록은 고품질의 보안 통합을 안내합니다. 이 목록을 정기적으로 검토하여 ID 플랫폼과 앱의 통합 품질과 보안을 유지해야 합니다. 이 검사 목록은 전체 애플리케이션을 검토하기 위한 것이 아닙니다. 플랫폼이 개선됨에 따라 검사 목록의 내용이 변경될 수 있습니다.

방금 시작한 경우 [Microsoft ID 플랫폼 설명서](index.yml)를 확인하여 인증 기본 사항, Microsoft ID 플랫폼의 애플리케이션 시나리오 등에 대해 알아보세요.

다음 검사 목록을 사용하여 애플리케이션이 [Microsoft ID 플랫폼](./index.yml)과 효과적으로 통합되었는지 확인합니다.

> [!TIP]
> Azure Portal의 *통합 길잡이* 를 통해 이러한 모범 사례와 권장 사항을 대부분 적용할 수 있습니다. Azure Portal에서 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 중 하나를 선택한 다음, **통합 길잡이** 메뉴 항목을 선택하여 길잡이를 시작합니다.

## <a name="basics"></a>기본 사항

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) [Microsoft Platform 정책](/legal/microsoft-identity-platform/terms-of-use)을 읽고 이해합니다. 애플리케이션이 사용자 및 플랫폼을 보호하도록 설계된 계약조건을 준수하는지 확인합니다.

## <a name="ownership"></a>소유권

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 앱을 등록하고 관리하는 데 사용하는 계정에 연결된 정보가 최신 상태인지 확인합니다.

## <a name="branding"></a>브랜딩

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) [애플리케이션에 대한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 준수합니다.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 애플리케이션에 의미 있는 이름 및 로고를 지정합니다. 이 정보는 [애플리케이션의 동의 프롬프트](application-consent-experience.md)에 표시됩니다. 사용자가 합리적인 의사 결정을 내릴 수 있도록 이름 및 로고가 회사/제품을 대표하는지 확인합니다. 상표권을 위반하지 않는지 확인합니다.

## <a name="privacy"></a>개인 정보 취급 방침

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 앱의 서비스 약관 및 개인정보처리방침에 대한 링크를 제공합니다.

## <a name="security"></a>보안

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 리디렉션 URI를 관리합니다. <ul><li>모든 리디렉션 URI의 소유권을 유지하고 DNS 레코드를 최신 상태로 유지합니다.</li><li>URI에 와일드카드(*)를 사용하지 않습니다.</li><li>웹앱의 경우 모든 URI가 안전하고 암호화되어 있는지 확인합니다(예: https 체계 사용).</li><li>공용 클라이언트의 경우 플랫폼별 리디렉션 URI(주로 iOS 및 Android용)를 사용합니다. 그렇지 않으면 앱으로 다시 호출할 때 충돌을 방지하기 위해 임의성이 많은 리디렉션 URI를 사용합니다.</li><li>격리된 웹 에이전트에서 앱을 사용하는 경우 `https://login.microsoftonline.com/common/oauth2/nativeclient`를 사용할 수 있습니다.</li><li>사용되지 않거나 불필요한 리디렉션 URI를 정기적으로 검토하고 제거합니다.</li></ul>

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 앱이 디렉터리에 등록된 경우 앱 등록 소유자 목록을 최소화하고 수동으로 모니터링합니다.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) [OAuth2 암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)에 대한 지원은 명시적으로 필요한 경우가 아니면 사용하도록 설정하지 않습니다. [여기](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)에서 유효한 시나리오에 대해 알아보세요.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 사용자 이름/암호를 넘어 이동합니다. 사용자 암호를 직접 처리하는 [ROPC(리소스 소유자 암호 자격 증명 흐름)](v2-oauth-ropc.md)는 사용하지 마세요. 이 흐름에는 높은 수준의 신뢰와 사용자 공개가 필요하며, 더 안전한 다른 흐름을 사용할 수 없는 경우에만 이 흐름을 사용해야 합니다. 이 흐름은 DevOps와 같은 일부 시나리오에서는 여전히 필요하지만, 이 흐름을 사용하면 애플리케이션에 제약이 발생합니다.  최신 접근법은 [인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)를 참조하세요.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 웹앱, 웹 API 및 디먼 앱의 기밀 앱 자격 증명을 보호하고 관리합니다. 암호 자격 증명(클라이언트 암호) 대신 [인증서 자격 증명](active-directory-certificate-credentials.md)을 사용합니다. 암호 자격 증명을 사용해야 하는 경우 수동으로 설정하지 마세요. 자격 증명을 코드 또는 구성에 저장하거나 사람이 처리하도록 허용해서는 안 됩니다. 가능하면 [Azure 리소스에 대한 관리 ID](../managed-identities-azure-resources/overview.md) 또는 [Azure Key Vault](../../key-vault/general/basic-concepts.md)를 사용하여 자격 증명을 저장하고 정기적으로 순환시킵니다.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 애플리케이션에서 최소 권한 권한을 요청하는지 확인합니다. 애플리케이션에 절대적으로 필요한 권한을 필요할 때만 요청합니다. 다양한 [유형의 권한](v2-permissions-and-consent.md#permission-types)을 이해합니다. 필요한 경우 애플리케이션 권한만 사용합니다. 가능한 경우 위임된 권한을 사용합니다. Microsoft Graph 권한의 전체 목록은 이 [권한 참조](/graph/permissions-reference)를 확인하세요.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) Microsoft ID 플랫폼을 사용하여 API를 보호하는 경우 노출해야 하는 권한을 신중하게 생각해야 합니다. 솔루션의 올바른 세분성과 관리자 동의가 필요한 권한을 고려합니다. 권한 부여 결정을 내리기 전에 수신 토큰에서 필요한 권한을 확인합니다.

## <a name="implementation"></a>구현

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 최신 인증 솔루션(OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md))을 사용하여 사용자를 안전하게 로그인시킵니다.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) OAuth 2.0 및 Open ID와 같은 프로토콜에 대해 직접 프로그래밍하지 마세요. 대신, [MSAL(Microsoft 인증 라이브러리)](msal-overview.md)을 활용합니다. MSAL 라이브러리는 사용하기 쉬운 라이브러리에서 보안 프로토콜을 안전하게 래핑하고, [조건부 액세스](../conditional-access/overview.md)에 대한 기본 제공 지원, 디바이스 차원의 [SSO(Single Sign-On)](../manage-apps/what-is-single-sign-on.md) 및 기본 제공 토큰 캐싱 지원을 제공합니다. 자세한 내용은 Microsoft 지원 [클라이언트 라이브러리](reference-v2-libraries.md) 목록을 참조하세요. 인증 프로토콜에 대한 코드를 직접 작성해야 하는 경우 [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx) 또는 유사한 개발 방법론을 따라야 합니다. 각 프로토콜에 대한 표준 사양의 보안 고려 사항에 유의하세요.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) [ADAL(Azure Active Directory 인증 라이브러리)](../azuread-dev/active-directory-authentication-libraries.md)의 기존 앱을 [Microsoft 인증 라이브러리](msal-overview.md)로 마이그레이션합니다. MSAL은 Microsoft의 최신 ID 플랫폼 솔루션이며 ADAL에 권장됩니다. .NET, JavaScript, Android, iOS, macOS에서 사용할 수 있으며, Python 및 Java의 경우 공개 미리 보기 상태입니다. [ADAL.NET](msal-net-migration.md), [ADAL.js](msal-compare-msal-js-and-adal-js.md), [ADAL.NET 및 iOS broker](msal-net-migration-ios-broker.md) 앱 마이그레이션에 대해 자세히 알아보세요.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 모바일 앱의 경우 애플리케이션 등록 환경을 사용하여 각 플랫폼을 구성합니다. 애플리케이션에서 Single Sign-On을 위해 Microsoft Authenticator 또는 Microsoft 회사 포털을 활용하려면 앱에 "broker 리디렉션 URI"가 구성되어 있어야 합니다. 그래야 Microsoft에서 인증 후 애플리케이션에 대한 제어권을 반환할 수 있습니다. 각 플랫폼을 구성할 때 앱 등록 환경에서 이 프로세스를 안내합니다. 빠른 시작을 사용하여 작업 예제를 다운로드합니다. iOS에서 가능할 때마다 broker 및 시스템 웹 보기를 사용합니다.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 웹앱 또는 웹 API에서 계정당 하나의 토큰 캐시를 유지합니다.  웹앱의 경우 계정 ID로 토큰 캐시 키를 지정해야 합니다.  웹 API의 경우, API를 호출하는 데 사용되는 토큰 해시로 계정 키를 지정해야 합니다. MSAL.NET은 .NET Framework 및 .NET Core 하위 플랫폼에서 사용자 지정 토큰 캐시 직렬화를 제공합니다. 보안 및 성능상의 이유로 사용자당 캐시 하나를 직렬화하는 것이 좋습니다. 자세한 내용은 [토큰 캐시 직렬화](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)를 참조하세요.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 앱에서 필요한 데이터를 Microsoft Graph 엔드포인트를 통해 사용할 수 있는 경우 개별 API 대신 [Microsoft Graph](https://developer.microsoft.com/graph)를 사용하여 해당 데이터에 대한 권한을 요청하세요.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 액세스 토큰 값을 확인하거나 클라이언트로 구문 분석을 시도하지 마세요.  값, 형식이 변경되거나 경고 없이 암호화될 수도 있습니다. 클라이언트가 사용자에 대한 정보를 확인하거나 Microsoft Graph를 호출해야 하는 경우 항상 id_token를 사용합니다.  웹 API는 형식을 정의하고 암호화 키를 설정하는 API이므로 액세스 토큰을 구문 분석해야 합니다.

## <a name="end-user-experience"></a>최종 사용자 환경

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 최종 사용자와 관리자가 앱을 신뢰할 수 있는지 확인하는 데 충분한 정보를 확보할 수 있도록 [동의 환경을 이해](application-consent-experience.md)하고 앱의 동의 프롬프트 부분을 구성합니다.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 대화형 흐름 전에 자동 인증(자동 토큰 획득)을 시도하여 사용자가 앱을 사용하는 동안 로그인 자격 증명을 입력해야 하는 횟수를 최소화합니다.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 모든 로그인에 "prompt=consent"를 사용하지 마세요. 추가 권한에 대한 동의를 요청해야 한다고 결정한 경우에만 prompt=consent를 사용하세요(예: 앱의 필수 권한을 변경한 경우).

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 해당하는 경우 사용자 데이터를 사용하여 애플리케이션을 보강합니다. 이 작업을 수행하는 쉬운 방법은 [Microsoft Graph API](https://developer.microsoft.com/graph)를 사용하는 것입니다. 시작하는 데 도움이 되는 [Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer) 도구.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 관리자가 테넌트에 쉽게 동의할 수 있도록 앱에 필요한 전체 권한 집합을 등록합니다. 사용자가 앱을 처음 시작할 때 앱에서 사용자가 우려하거나 혼동할만한 권한을 요청하는 이유를 이해하는 데 도움이 되도록 런타임에 [증분 동의](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)를 사용합니다.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) [깔끔한 단일 로그아웃 환경](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)을 구현합니다. 이는 개인 정보 보호 및 보안 관련 요구 사항이며 원활한 사용자 환경을 만드는 데 도움이 됩니다.

## <a name="testing"></a>테스트

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 애플리케이션을 사용하는 사용자의 능력에 영향을 미칠 수 있는 [조건부 액세스 정책](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)을 테스트합니다.

![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) 지원하려는 모든 가능한 계정(예: 회사 또는 학교 계정, 개인 Microsoft 계정, 자녀 계정 및 소버린 계정)을 사용하여 애플리케이션을 테스트합니다.

## <a name="additional-resources"></a>추가 리소스

v2.0에 관한 자세한 정보를 살펴보세요.

* [Microsoft ID 플랫폼(개요)](v2-overview.md)
* [Microsoft ID 플랫폼 프로토콜 참조](active-directory-v2-protocols.md)
* [액세스 토큰 참조](access-tokens.md)
* [ID 토큰 참조](id-tokens.md)
* [인증 라이브러리 참조](reference-v2-libraries.md)
* [Microsoft ID 플랫폼의 권한 및 동의](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
