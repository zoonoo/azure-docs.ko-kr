---
title: Microsoft ID 플랫폼의 모범 사례 | Azure
description: Microsoft ID 플랫폼과 통합할 때 모범 사례, 권장 사항 및 일반적인 감독 사항에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050491"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft ID 플랫폼 모범 사례 및 권장 사항

이 문서에서는 Microsoft ID 플랫폼과 통합할 때 의 모범 사례, 권장 사항 및 일반적인 감독을 중점처리합니다.  이 체크리스트는 고품질의 안전한 통합을 안내합니다. 이 목록을 정기적으로 검토하여 앱과 ID 플랫폼의 통합의 품질과 보안을 유지했는지 확인합니다. 검사 목록은 전체 응용 프로그램을 검토하기 위한 것이 아닙니다. 체크리스트의 내용은 플랫폼을 개선할 때 변경될 수 있습니다.

이제 막 시작하는 경우 [Microsoft ID 플랫폼 설명서를](index.yml) 확인하여 인증 기본 사항, Microsoft ID 플랫폼의 응용 프로그램 시나리오 등에 대해 알아봅니다.

다음 검사 목록을 사용하여 응용 프로그램이 [Microsoft ID 플랫폼과](https://docs.microsoft.com/azure/active-directory/develop/)효과적으로 통합되었는지 확인합니다.

## <a name="basics"></a>기본 사항

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | Microsoft 플랫폼 [정책을](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409)읽고 이해합니다. 응용 프로그램이 사용자와 플랫폼을 보호하도록 설계된 대로 설명된 용어를 준수해야 합니다. |

## <a name="ownership"></a>소유권

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱을 등록하고 관리하는 데 사용한 계정과 연결된 정보가 최신 상태인지 확인합니다. |

## <a name="branding"></a>브랜딩

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | [응용 프로그램에 대한 브랜딩 지침을 준수합니다.](howto-add-branding-in-azure-ad-apps.md) |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 응용 프로그램에 의미 있는 이름과 로고를 제공합니다. 이 정보는 [응용 프로그램의 동의 프롬프트에](application-consent-experience.md)나타납니다. 사용자가 정보에 입각한 의사 결정을 내릴 수 있도록 이름과 로고가 회사/제품을 대표하는지 확인합니다. 상표를 위반하지 않는지 확인합니다. |

## <a name="privacy"></a>개인 정보 보호

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱의 서비스 약관 및 개인 정보 취급 방침에 대한 링크를 제공합니다. |

## <a name="security"></a>보안

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 리디렉션 URI 관리: <ul><li>모든 리디렉션 URI의 소유권을 유지하고 DNS 레코드를 최신 상태로 유지합니다.</li><li>URI에서 와일드카드(*)를 사용하지 마십시오.</li><li>웹 앱의 경우 모든 URI가 안전하고 암호화되어 있는지 확인합니다(예: https 구성표 사용).</li><li>공용 클라이언트의 경우 해당되는 경우 플랫폼별 리디렉션 URI를 사용합니다(주로 iOS 및 Android용). 그렇지 않으면 앱으로 다시 호출할 때 충돌을 방지하기 위해 임의성이 높은 리디렉션 URI를 사용합니다.</li><li>격리된 웹 에이전트에서 앱을 사용하는 경우 을 `https://login.microsoftonline.com/common/oauth2/nativeclient`사용할 수 있습니다.</li><li>사용하지 않거나 불필요한 리디렉션 URI를 정기적으로 검토하고 다듬습니다.</li></ul> |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱이 디렉터리에 등록된 경우 앱 등록 소유자 목록을 최소화하고 수동으로 모니터링합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 명시적으로 필요하지 않는 한 [OAuth2 암시적 권한 부여 흐름에](v2-oauth2-implicit-grant-flow.md) 대한 지원을 사용하도록 설정하지 마십시오. [여기에서](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)유효한 시나리오에 대해 알아봅니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 사용자 이름/암호를 넘어섭니다. 사용자의 암호를 직접 처리하는 [ROPC(리소스 소유자 암호 자격 증명 흐름)를](v2-oauth-ropc.md)사용하지 마십시오. 이 흐름은 높은 수준의 신뢰와 사용자 노출이 필요하며 다른 보다 안전한 흐름을 사용할 수 없는 경우에만 사용해야 합니다. 이 흐름은 DevOps와 같은 일부 시나리오에서는 여전히 필요하지만, 이 흐름을 사용하면 애플리케이션에 제약이 발생합니다.  보다 현대적인 접근 방식의 경우 [인증 흐름 및 응용 프로그램 시나리오를](authentication-flows-app-scenarios.md)읽어보십시오.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 웹 앱, 웹 API 및 데몬 앱에 대한 기밀 앱 자격 증명을 보호하고 관리합니다. 암호 [자격 증명(클라이언트 암호)이](active-directory-certificate-credentials.md)아닌 인증서 자격 증명을 사용합니다. 암호 자격 증명을 사용해야 하는 경우 수동으로 설정하지 마십시오. 자격 증명을 코드 또는 구성에 저장하지 말고 사용자가 처리할 수 있도록 허용하지 마십시오. 가능하면 Azure 리소스 또는 [Azure Key Vault에](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) [대해 관리되는 ID를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 사용하여 자격 증명을 저장하고 정기적으로 회전합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 응용 프로그램에서 최소 권한 권한을 요청해야 합니다. 응용 프로그램에 절대적으로 필요한 권한만 요청하고 필요할 때만 요청합니다. 다양한 [유형의 사용 권한에 대해 이해합니다.](v2-permissions-and-consent.md#permission-types) 필요한 경우에만 응용 프로그램 권한을 사용합니다. 가능한 경우 위임된 권한을 사용합니다. Microsoft 그래프 사용 권한의 전체 목록은 이 [사용 권한 참조를](https://docs.microsoft.com/graph/permissions-reference)참조하십시오. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | Microsoft ID 플랫폼을 사용하여 API를 보호하는 경우 노출해야 하는 권한을 신중하게 고려해야 합니다. 솔루션에 적합한 세분성요소와 관리자의 동의가 필요한 권한에 대해 고려합니다. 권한 부여를 결정하기 전에 들어오는 토큰에서 예상되는 사용 권한을 확인합니다. |

## <a name="implementation"></a>구현

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 최신 인증 솔루션(OAuth 2.0, [OpenID Connect)을](v2-protocols-oidc.md)사용하여 사용자를 안전하게 로그인할 수 있습니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |  OAuth 2.0 및 Open ID와 같은 프로토콜에 대해 직접 프로그래밍하지 마십시오. 대신 [MSAL(Microsoft 인증 라이브러리)을](msal-overview.md)활용합니다. MSAL 라이브러리는 사용하기 쉬운 라이브러리에서 보안 프로토콜을 안전하게 래핑하며 [조건부 액세스](/azure/active-directory/conditional-access/overview) 시나리오, 장치 전체 [단일 사인온(SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)및 기본 제공 토큰 캐싱 지원을 기본 제공합니다. 자세한 내용은 Microsoft 지원 클라이언트 [라이브러리](reference-v2-libraries.md#microsoft-supported-client-libraries) 및 [미들웨어 라이브러리](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) 목록과 [호환되는 타사 클라이언트 라이브러리](reference-v2-libraries.md#compatible-client-libraries)목록을 참조하십시오.<br/><br/>인증 프로토콜에 대한 코드를 전달해야 하는 경우 [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx)과 같은 방법을 따라야 합니다. 각 프로토콜의 표준 사양의 보안 고려 사항에 주의를 기울이십시오.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |  기존 앱을 [Azure Active 디렉터리 인증 라이브러리(ADAL)에서](../azuread-dev/active-directory-authentication-libraries.md) [Microsoft 인증 라이브러리로](msal-overview.md)마이그레이션합니다. MSAL은 마이크로소프트의 최신 ID 플랫폼 솔루션이며 ADAL이 선호합니다. .NET, 자바 스크립트, 안드로이드, iOS, macOS에서 사용할 수 있으며 파이썬과 자바에 대한 공개 미리보기에도 있습니다. [ADAL.NET,](msal-net-migration.md) [ADAL.js,](msal-compare-msal-js-and-adal-js.md)ADAL.NET 및 [iOS 브로커](msal-net-migration-ios-broker.md) 앱 마이그레이션에 대해 자세히 읽어보십시오.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |  모바일 앱의 경우 응용 프로그램 등록 환경을 사용하여 각 플랫폼을 구성합니다. 응용 프로그램이 단일 로그인을 위해 Microsoft 인증자 또는 Microsoft 회사 포털을 활용하려면 앱에 구성된 "브로커 리디렉션 URI"가 필요합니다. 이렇게 하면 Microsoft에서 인증 후 응용 프로그램에 대한 제어를 반환할 수 있습니다. 각 플랫폼을 구성할 때 앱 등록 환경이 프로세스를 안내합니다. 빠른 시작을 사용하여 작업 예제를 다운로드합니다. iOS에서는 가능하면 브로커와 시스템 웹뷰를 사용하십시오.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |  웹 앱 또는 웹 API에서 계정당 하나의 토큰 캐시를 유지합니다.  웹 앱의 경우 토큰 캐시는 계정 ID로 키를 만들어야 합니다.  웹 API의 경우 API를 호출하는 데 사용되는 토큰의 해시에 의해 계정에 키가 표시되어야 합니다. MSAL.NET .NET 프레임워크 및 .NET Core 하위 플랫폼에서 사용자 지정 토큰 캐시 직렬화를 제공합니다. 보안 및 성능상의 이유로 사용자당 하나의 캐시를 직렬화하는 것이 좋습니다. 자세한 내용은 토큰 [캐시 직렬화](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)에 대해 읽어보십시오.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱에 필요한 데이터를 Microsoft [Graph를](https://developer.microsoft.com/graph)통해 사용할 수 있는 경우 개별 API가 아닌 Microsoft 그래프 끝점을 사용하여 이 데이터에 대한 권한을 요청합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |액세스 토큰 값을 보거나 클라이언트로 구문 분석하지 마십시오.  클라이언트가 사용자에 대해 알아보거나 Microsoft Graph에 전화해야 하는 경우 항상 id_token 사용하므로 값, 형식을 변경하거나 경고 없이 암호화될 수 있습니다.  웹 API만 액세스 토큰을 구문 분석해야 합니다(형식을 정의하고 암호화 키를 설정하는 형식이기 때문에). |

## <a name="end-user-experience"></a>최종 사용자 환경

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | [최종](application-consent-experience.md) 사용자와 관리자가 앱을 신뢰할 수 있는지 확인할 수 있는 충분한 정보를 갖도록 동의 환경을 이해하고 앱동의 프롬프트부분을 구성합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 대화형 흐름 전에 자동 인증(자동 토큰 획득)을 시도하여 앱을 사용하는 동안 사용자가 로그인 자격 증명을 입력해야 하는 횟수를 최소화합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 로그인할 때마다 "프롬프트=동의"를 사용하지 마십시오. 추가 권한에 대한 동의를 요청해야 한다고 결정한 경우에만 프롬프트=동의를 사용합니다(예: 앱의 필수 권한을 변경한 경우). |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 해당하는 경우 사용자 데이터로 응용 프로그램을 보강합니다. Microsoft [그래프 API를](https://developer.microsoft.com/graph) 사용하면 이 작업을 쉽게 수행할 수 있습니다. 시작하는 데 도움이 되는 [그래프 탐색기](https://developer.microsoft.com/graph/graph-explorer) 도구입니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 관리자가 테넌트에 쉽게 동의할 수 있도록 앱에 필요한 전체 권한 집합을 등록합니다. 런타임에 [증분 동의를](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) 사용하여 앱이 처음 시작할 때 사용자를 염려하거나 혼동할 수 있는 권한을 요청하는 이유를 사용자가 이해할 수 있도록 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 깨끗한 [단일 로그아웃 환경을 구현합니다.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) 그것은 개인 정보 보호 및 보안 요구 사항, 그리고 좋은 사용자 경험을 위해. |

## <a name="testing"></a>테스트

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 사용자의 응용 프로그램 사용 능력에 영향을 줄 수 있는 [조건부 액세스 정책을](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) 테스트합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 지원할 수 있는 모든 계정(예: 직장 또는 학교 계정, 개인 Microsoft 계정, 자녀 계정 및 주권 계정)으로 응용 프로그램을 테스트합니다. |

## <a name="additional-resources"></a>추가 리소스

v2.0에 관한 자세한 정보를 살펴보세요.

* [마이크로소프트 아이덴티티 플랫폼(v2.0 개요)](v2-overview.md)
* [Microsoft ID 플랫폼 프로토콜 참조](active-directory-v2-protocols.md)
* [액세스 토큰 참조](access-tokens.md)
* [ID 토큰 참조](id-tokens.md)
* [인증 라이브러리 참조](reference-v2-libraries.md)
* [Microsoft ID 플랫폼의 권한 및 동의](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
