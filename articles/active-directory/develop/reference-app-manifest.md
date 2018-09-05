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
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127023"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 앱 매니페스트

Azure AD(Azure Active Directory)와 통합되는 앱은 Azure AD 테넌트에 등록되어야 합니다. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory**를 선택하고, 구성하려는 앱을 선택하고, **매니페스트**를 선택하여 앱을 구성할 수 있습니다.

## <a name="manifest-reference"></a>매니페스트 참조

> [!NOTE]
> 설명을 볼 수 없는 경우 브라우저 창을 최대화하거나 화면을 스크롤하여/넘겨서 설명을 볼 수 있습니다.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| 키  | 값 형식 | 예제 값 | 설명  |
|---------|---------|---------|---------|
| `appID` | 식별자 문자열 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Azure AD가 할당한 앱의 고유 식별자를 지정합니다. |
| `appRoles` | 배열 형식 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | 앱에서 선언할 수 있는 역할의 컬렉션을 지정합니다. 이러한 역할은 사용자, 그룹 또는 서비스 주체에게 할당될 수 있습니다. |
| `availableToOtherTenants`| 부울 | `true` | 이 값을 true로 설정하면 앱을 다른 테넌트에서 사용할 수 있습니다. false로 설정하면 등록된 테넌트에만 앱이 제공될 수 있습니다. 자세한 내용은 [다중 테넌트 앱 패턴을 사용하여 Azure AD 사용자를 로그인하는 방법](howto-convert-app-to-be-multi-tenant.md)을 참조하세요. |
| `displayName` | string | `MyRegisteredApp` | 앱의 표시 이름입니다. |
| `errorURL` | string | `http://MyRegisteredAppError` | 앱에서 발생한 오류의 URL입니다. |
| `groupMembershipClaims` | string | `1` | 앱이 예상하는 사용자 또는 OAuth 2.0 액세스 토큰에서 발급되는 `groups` 클레임을 구성하는 비트 마스크입니다. 비트 마스크 값은 다음과 같습니다.<br>0: 없음<br>1: 보안 그룹 및 Azure AD 역할<br>2: 예약됨<br>4: 예약됨<br>비트 마스크를 7로 설정하면 로그인한 사용자가 속한 모든 보안 그룹, 배포 그룹, Azure AD 디렉터리 역할을 가져옵니다. |
| `optionalClaims` | string | `null` | 이 특정 앱에 대해 보안 토큰 서비스에서 토큰으로 반환한 선택적 클레임입니다. 자세한 내용은 [선택적 클레임](active-directory-optional-claims.md)을 참조하세요. |
| `acceptMappedClaims` | 부울 | `true` | 이 값을 `true`로 설정하면 앱이 사용자 지정 서명 키 없이 클레임 매핑을 사용할 수 있습니다. |
| `homepage` | string | `http://MyRegisteredApp` | 앱의 홈페이지 URL을 지정합니다. |
| `informationalUrls` | string | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | 앱의 서비스 약관 및 개인정보처리방침 링크를 지정합니다. 서비스 약관 및 개인정보처리방침은 사용자 동의 환경을 통해 사용자에게 표시됩니다. 자세한 내용은 [등록된 Azure AD 앱의 서비스 약관 및 개인정보처리방침을 추가하는 방법](howto-add-terms-of-service-privacy-statement.md)을 참조하세요. |
| `identifierUris` | 문자열 배열 | `http://MyRegistererdApp` | Azure AD 테넌트 안에서 또는 앱이 멀티 테넌트인 경우 확인된 사용자 지정 도메인 안에서 웹앱을 고유하게 식별하는 사용자 정의 URI입니다. |
| `keyCredentials` | 배열 형식 | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | 앱 할당 자격 증명, 문자열 기반 공유 비밀, X.509 인증서에 대한 참조가 들어 있습니다. 액세스 토큰을 요청할 때(앱이 리소스가 아닌 클라이언트 역할을 수행할 때) 이 자격 증명을 사용합니다. |
| `knownClientApplications` | 배열 형식 | `[GUID]` | 클라이언트 앱과 사용자 지정 웹 API 앱 등 두 부분을 포함하는 솔루션이 있을 때 동의를 묶는 데 사용됩니다. 클라이언트 앱의 appID를 이 값에 입력하면 사용자는 클라이언트 앱에 대해 한 번만 동의하면 됩니다. Azure AD는 클라이언트에 대한 동의가 웹 API에 대한 암시적 동의임을 인식하고, 동시에 클라이언트와 웹 API 모두의 서비스 주체를 자동으로 프로비전하게 됩니다. 클라이언트와 웹 API 앱은 모두 동일한 테넌트에 등록되어야 합니다. |
| `logoutUrl` | string | `http://MyRegisteredAppLogout` | 앱을 로그아웃하는 URL입니다. |
| `oauth2AllowImplicitFlow` | 부울 | `false` | 이 웹앱이 OAuth2.0 암시적 흐름 토큰을 요청할 수 있는지 여부를 지정합니다. 기본값은 false입니다. Javascript 단일 페이지 앱과 같은 브라우저 기반 앱에서 이 플래그를 사용합니다. |
| `oauth2AllowUrlPathMatching` | 부울 | `false` | OAuth 2.0 토큰 요청의 일부로 Azure AD가 앱의 replyUrls에 대해 리디렉션 URI의 경로 일치를 허용하는지 여부를 지정합니다. 기본값은 false입니다. |
| `oauth2Permissions` | 배열 형식 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | 웹 API(리소스) 앱을 클라이언트 앱에 노출하는 OAuth 2.0 권한 범위 컬렉션을 지정합니다. 이러한 권한 범위를 동의를 통해 클라이언트 앱에 부여할 수 있습니다. |
| `oauth2RequiredPostResponse` | 부울 | `false` | OAuth 2.0 토큰 요청의 일부로 GET 요청과는 반대로 Azure AD의 POST 요청 허용 여부를 지정합니다. 기본값은 false로, GET 요청만 허용하도록 지정합니다. |
| `objectId` | 식별자 문자열 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | 디렉터리의 앱에 대한 고유 식별자입니다. 이 ID는 프로토콜 트랜잭션에서 앱을 식별하는 데 사용되는 식별자가 아닙니다. 디렉터리 쿼리의 개체를 참조하는 데 사용됩니다. |
| `parentalControlSettings` | string | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors`는 미성년자가 사용할 수 없도록 앱이 차단되는 국가를 지정합니다.<br>`legalAgeGroupRule`은 앱 사용자에게 적용되는 법적 연령 그룹 규칙을 지정합니다. 설정 가능한 값은 `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` 또는 `BlockMinors`입니다.  |
| `passwordCredentials` | 배열 형식 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | `keyCredentials`에 대한 설명을 참조하세요. |
| `publicClient` | 부울 | `false` | 앱이 모바일 장치에서 실행되는 설치된 앱처럼 공용 클라이언트인지 여부를 지정합니다. 기본값은 False입니다. |
| `replyUrls` | 문자열 배열 | `"http://localhost"` | 이 다중값 속성은 Azure AD가 토큰을 반환할 때 대상으로 허용하는 등록된 redirect_uri 값 목록을 포함합니다. |
| `requiredResourceAccess` | 배열 형식 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | 동적 동의를 통해 `requiredResourceAccess`는 관리자 동의 환경 및 정적 동의를 사용하는 사용자 동의 환경을 구동합니다. 그러나 일반적인 경우에는 사용자 동의 환경을 구동하지 않습니다.<br>`resourceAppId`는 앱이 액세스를 요청하는 대상 리소스의 고유 식별자입니다. 이 값은 대상 리소스 앱에 대해 선언된 appId와 같아야 합니다.<br>`resourceAccess`는 앱이 지정된 리소스에서 요구하는 OAuth2.0 권한 범위 및 앱 역할을 나열하는 배열입니다. 지정된 리소스의 `id` 및 `type` 값을 포함합니다. |
| `samlMetadataUrl` | string | `http://MyRegisteredAppSAMLMetadata` | 앱의 SAML 메타데이터 URL입니다. |

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

