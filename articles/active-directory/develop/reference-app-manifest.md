---
title: Azure Active Directory 앱 매니페스트 이해 | Microsoft Docs
description: Azure AD 테넌트의 응용 프로그램 ID 구성을 나타내고 OAuth 권한 부여, 승인 환경 등을 용이하게 하는 데 사용되는 Azure Active Directory 앱 매니페스트에 대한 자세한 정보를 다룹니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 372bff911c0925e05297872da66279e727149010
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086780"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 앱 매니페스트

응용 프로그램 매니페스트는 Microsoft ID 플랫폼에 있는 응용 프로그램 개체의 모든 특성 정의를 포함합니다. 또한 응용 프로그램 개체를 업데이트하기 위한 메커니즘으로도 사용됩니다. 응용 프로그램 엔터티 및 해당 스키마에 대한 자세한 내용은 [Graph API 응용 프로그램 엔터티 설명서](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)를 참조하세요.

Azure Portal을 통해 또는 프로그래밍 방식으로 Microsoft Graph를 사용하여 앱 특성을 구성할 수 있습니다. 그러나 앱 매니페스트를 편집하여 앱 특성을 구성해야 하는 경우도 있습니다. 이 시나리오에는 다음이 포함됩니다.

* Azure AD 다중 테넌트 및 개인 Microsoft 계정으로 앱을 등록한 경우 UI에서 지원되는 Microsoft 계정을 변경할 수 없습니다. 대신 응용 프로그램 매니페스트 편집기를 사용하여 지원되는 계정 유형을 변경해야 합니다.
* 앱이 지원하는 권한 및 역할을 정의해야 하는 경우 응용 프로그램 매니페스트를 수정해야 합니다.

## <a name="configure-the-app-manifest"></a>앱 매니페스트 구성

응용 프로그램 매니페스트를 구성하려면

1. [Azure Portal](https://portal.azure.com)을 로그인합니다.
1. **Azure Active Directory** 서비스, **앱 등록** 또는 **앱 등록(미리 보기)** 을 차례로 선택합니다.
1. 구성하려는 앱을 선택합니다.
1. 앱의 **개요** 페이지에서 **매니페스트** 섹션을 선택합니다. 웹 기반 매니페스트 편집기가 열리면 포털 내에서 매니페스트를 편집할 수 있습니다. 필요에 따라 **다운로드**를 선택하여 로컬로 매니페스트를 편집하고 **업로드**를 사용하여 응용 프로그램에 다시 적용할 수 있습니다.

## <a name="manifest-reference"></a>매니페스트 참조

> [!NOTE]
> **설명** 다음에 **예제 값** 열이 표시되지 않는 경우 브라우저 창을 최대화하고 **예제 값** 열이 표시될 때까지 스크롤/살짝 밉니다.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| 키  | 값 형식 | 설명  | 예제 값 |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Nullable Int32 | 현재 API 리소스에 대해 승인된 액세스 토큰 버전을 지정합니다. 가능한 값은 1, 2, Null입니다. 기본값은 Null이며, 2로 처리됩니다. | `2` |
| `allowPublicClient` | 부울 | 대체 응용 프로그램 유형을 지정합니다. Azure AD는 기본적으로 replyUrlsWithType에서 응용 프로그램 유형을 유추합니다. Azure AD에서 클라이언트 앱 유형을 확인할 수 없는 특정 시나리오(예: HTTP 요청이 URL 리디렉션 없이 발생하는 [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) 흐름)가 있습니다. 이러한 경우 Azure AD는 이 속성 값을 기반으로 해서 응용 프로그램 유형을 해석합니다. 이 값을 true로 설정하면 대체 애플리케이션 유형이 모바일 디바이스에서 실행 중인 설치된 애플리케이션과 같은 공용 클라이언트로 설정됩니다. 기본값은 false이며, 대체 응용 프로그램 유형이 웹앱과 같은 기밀 클라이언트임을 의미합니다. | `false` |
| `appId` | 식별자 문자열 | Azure AD가 할당한 앱의 고유 식별자를 지정합니다. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | 배열 형식 | 앱에서 선언할 수 있는 역할의 컬렉션을 지정합니다. 이러한 역할은 사용자, 그룹 또는 서비스 주체에게 할당될 수 있습니다. 더 많은 예제 및 정보는 [응용 프로그램에서 앱 역할을 추가하고 토큰에서 수신하기](howto-add-app-roles-in-azure-ad-apps.md)를 참조하세요. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `groupMembershipClaims` | string | 앱이 예상하는 사용자 또는 OAuth 2.0 액세스 토큰에서 발급되는 `groups` 클레임을 구성하는 비트 마스크입니다. 비트 마스크 값은 다음과 같습니다.<br>0: 없음<br>1: 보안 그룹 및 Azure AD 역할<br>2: 예약됨<br>4: 예약됨<br>비트 마스크를 7로 설정하면 로그인한 사용자가 속한 모든 보안 그룹, 배포 그룹, Azure AD 디렉터리 역할을 가져옵니다. | `1` |
| `optionalClaims` | string | 이 특정 앱에 대해 보안 토큰 서비스에서 토큰으로 반환한 선택적 클레임입니다.<br>현재 앱 등록 포털을 통해 등록된 Azure AD 및 개인 계정을 모두 지원하는 앱은 선택적 클레임을 사용할 수 없습니다. 그러나 v2.0 엔드포인트를 사용하여 Azure AD에만 등록된 앱은 매니페스트에서 요청한 선택적 클레임을 가져올 수 있습니다. 자세한 내용은 [선택적 클레임](active-directory-optional-claims.md)을 참조하세요. | `null` |
| `id` | 식별자 문자열 | 디렉터리의 앱에 대한 고유 식별자입니다. 이 ID는 프로토콜 트랜잭션에서 앱을 식별하는 데 사용되는 식별자가 아닙니다. 디렉터리 쿼리의 개체를 참조하는 데 사용됩니다. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | 문자열 배열 | Azure AD 테넌트 안에서 또는 앱이 멀티 테넌트인 경우 확인된 사용자 지정 도메인 안에서 웹앱을 고유하게 식별하는 사용자 정의 URI입니다. | <code>[<br>&nbsp;&nbsp;"https://MyRegistererdApp"<br>]</code> |
| `informationalUrls` | string | 앱의 서비스 약관 및 개인정보처리방침 링크를 지정합니다. 서비스 약관 및 개인정보처리방침은 사용자 동의 환경을 통해 사용자에게 표시됩니다. 자세한 내용은 [등록된 Azure AD 앱의 서비스 약관 및 개인정보처리방침을 추가하는 방법](howto-add-terms-of-service-privacy-statement.md)을 참조하세요. | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | 배열 형식 | 앱 할당 자격 증명, 문자열 기반 공유 비밀, X.509 인증서에 대한 참조가 들어 있습니다. 액세스 토큰을 요청할 때(앱이 리소스가 아닌 클라이언트 역할을 수행할 때) 이 자격 증명을 사용합니다. | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | 배열 형식 | 클라이언트 앱과 사용자 지정 웹 API 앱 등 두 부분을 포함하는 솔루션이 있을 때 동의를 묶는 데 사용됩니다. 클라이언트 앱의 appID를 이 값에 입력하면 사용자는 클라이언트 앱에 대해 한 번만 동의하면 됩니다. Azure AD는 클라이언트에 대한 동의가 웹 API에 대한 암시적 동의임을 인식하고, 동시에 클라이언트와 웹 API 모두의 서비스 주체를 자동으로 프로비전하게 됩니다. 클라이언트와 웹 API 앱은 모두 동일한 테넌트에 등록되어야 합니다. | `[GUID]` |
| `logoUrl` | string | 포털에서 업로드된 로고에 대한 CDN URL을 가리키는 읽기 전용 값입니다. | `https://MyRegisteredAppLogo` |
| `logoutUrl` | string | 앱을 로그아웃하는 URL입니다. | `https://MyRegisteredAppLogout` |
| `name` | string | 앱의 표시 이름입니다. | `MyRegisteredApp` |
| `oauth2AllowImplicitFlow` | 부울 | 이 웹앱이 OAuth2.0 암시적 흐름 액세스 토큰을 요청할 수 있는지 여부를 지정합니다. 기본값은 false입니다. 이 플래그는 Javascript 단일 페이지 앱과 같은 브라우저 기반 앱에 사용됩니다. 자세히 알아보려면 목차에서 `OAuth 2.0 implicit grant flow`을 입력하여 암시적 흐름에 대한 항목을 참조하세요. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | 부울 | 이 웹앱이 OAuth2.0 암시적 흐름 ID 토큰을 요청할 수 있는지 여부를 지정합니다. 기본값은 false입니다. 이 플래그는 Javascript 단일 페이지 앱과 같은 브라우저 기반 앱에 사용됩니다. | `false` |
| `oauth2Permissions` | 배열 형식 | 웹 API(리소스) 앱을 클라이언트 앱에 노출하는 OAuth 2.0 권한 범위 컬렉션을 지정합니다. 이러한 권한 범위를 동의를 통해 클라이언트 앱에 부여할 수 있습니다. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"u| 웹 API(리소스) 앱을 클라이언트 앱에 노출하는 OAuth 2.0 권한 범위 컬렉션을 지정합니다. 이러한 권한 범위를 동의를 통해 클라이언트 앱에 부여할 수 있습니다. ser_impersonation”<br>&nbsp;&nbsp;}<br>]</code> |
| `oauth2RequiredPostResponse` | 부울 | OAuth 2.0 토큰 요청의 일부로 GET 요청과는 반대로 Azure AD의 POST 요청 허용 여부를 지정합니다. 기본값은 false로, GET 요청만 허용하도록 지정합니다. | `false` |
| `parentalControlSettings` | string | `countriesBlockedForMinors`는 미성년자가 사용할 수 없도록 앱이 차단되는 국가를 지정합니다.<br>`legalAgeGroupRule`은 앱 사용자에게 적용되는 법적 연령 그룹 규칙을 지정합니다. 설정 가능한 값은 `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` 또는 `BlockMinors`입니다.  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | 배열 형식 | `keyCredentials`에 대한 설명을 참조하세요. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | 배열 형식 | 암시적 동의에 대해 요청된 사용 권한 및 응용 프로그램을 나열합니다. 관리자가 응용 프로그램에 대한 동의를 제공한 상태여야 합니다. preAuthorizedApplications는 사용자가 요청된 사용 권한에 동의하지 않아도 됩니다. preAuthorizedApplications에 나열된 사용 권한은 사용자 동의가 필요하지 않습니다. 그러나 preAuthorizedApplications에 나열되지 않은 요청된 추가 사용 권한에는 사용자 동의가 필요합니다. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `replyUrlsWithType` | 문자열 배열 | 이 다중값 속성은 Azure AD가 토큰을 반환할 때 대상으로 허용하는 등록된 redirect_uri 값 목록을 포함합니다. 각 URI 값에는 연결된 앱 유형 값이 포함되어야 합니다. 지원되는 유형 값은 `Web`, `InstalledClient`입니다. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | 배열 형식 | 동적 동의를 통해 `requiredResourceAccess`는 관리자 동의 환경 및 정적 동의를 사용하는 사용자 동의 환경을 구동합니다. 그러나 일반적인 경우에는 사용자 동의 환경을 구동하지 않습니다.<br>`resourceAppId`는 앱이 액세스를 요청하는 대상 리소스의 고유 식별자입니다. 이 값은 대상 리소스 앱에 대해 선언된 appId와 같아야 합니다.<br>`resourceAccess`는 앱이 지정된 리소스에서 요구하는 OAuth2.0 권한 범위 및 앱 역할을 나열하는 배열입니다. 지정된 리소스의 `id` 및 `type` 값을 포함합니다. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | string | 앱의 SAML 메타데이터 URL입니다. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | string | 앱의 홈페이지 URL을 지정합니다. | `https://MyRegisteredApp` |
| `signInAudience` | string | 현재 응용 프로그램에 대해 지원되는 Microsoft 계정을 지정합니다. 지원되는 값은 다음과 같습니다.<ul><li>**AzureADMyOrg** - 내 조직의 Azure AD 테넌트(즉, 단일 테넌트)에서 Microsoft 회사 또는 학교 계정이 있는 사용자</li><li>**AzureADMultipleOrgs** - 임의 조직의 Azure AD 테넌트(즉, 다중 테넌트)에서 Microsoft 회사 또는 학교 계정이 있는 사용자</li> <li>**AzureADandPersonalMicrosoftAccount** - 임의 조직의 Azure AD 테넌트에서 개인 Microsoft 계정이나 회사 또는 학교 계정이 있는 사용자</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | 문자열 배열 | 응용 프로그램을 범주화하고 식별하는 데 사용할 수 있는 사용자 지정 문자열입니다. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="next-steps"></a>다음 단계

* 앱의 응용 프로그램 개체와 서비스 주체 개체 간의 관계에 대한 자세한 내용은 [Azure AD의 응용 프로그램 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.
* 핵심 Azure AD(Active Directory) 개발자 개념 중 일부에 대한 정의는 [Azure Active Directory 개발자 용어집](developer-glossary.md)을 참조하세요.

다음 의견 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖는 데 도움을 줄 수 있습니다.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
