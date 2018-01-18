---
title: "Azure Active Directory 응용 프로그램 매니페스트 이해 | Microsoft Docs"
description: "Azure AD 테넌트의 응용 프로그램 ID 구성을 나타내고 OAuth 권한 부여, 승인 환경 등을 용이하게 하는 데 사용되는 Azure Active Directory 응용 프로그램 매니페스트에 대한 자세한 정보를 다룹니다."
services: active-directory
documentationcenter: 
author: sureshja
manager: mtillman
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: f3284d4cbb15f21522549c678410815b54344744
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-application-manifest"></a>Azure Active Directory 응용 프로그램 매니페스트
Azure AD와 통합되는 앱은 Azure AD 테넌트에 등록되어야 합니다. 이 앱은 [Azure Portal](https://portal.azure.com)에서 앱 매니페스트(Azure AD 블레이드 아래)를 사용하도록 구성할 수 있습니다.

## <a name="manifest-reference"></a>매니페스트 참조

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|키  |값 형식 |예제 값  |설명  |
|---------|---------|---------|---------|
|appID     |  식별자 문자열       |""|  Azure AD에서 등록한 앱에 할당된 응용 프로그램의 고유 식별자입니다.|
|appRoles     |    배열 형식     |[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"장치 정보에 대한 읽기 전용 액세스",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]|응용 프로그램에서 선언할 수 있는 역할의 컬렉션입니다. 이러한 역할은 사용자, 그룹 또는 서비스 사용자에게 할당할 수 있습니다.|
|availableToOtherTenants|부울|true|이 값을 true로 설정하면 응용 프로그램을 다른 테넌트에서 사용할 수 있습니다.  false로 설정하면 등록된 테넌트에만 앱이 제공될 수 있습니다.  자세한 내용은 [다중 테넌트 응용 프로그램 패턴을 사용하여 모든 Azure AD(Active Directory) 사용자를 로그인하는 방법](active-directory-devhowto-multi-tenant-overview.md)을 참조하세요. |
|displayName     |문자열         |MyRegisteredApp         |응용 프로그램의 표시 이름입니다. |
|errorURL     |문자열         |http:<i></i>//MyRegisteredAppError         |응용 프로그램에서 발생한 오류의 URL입니다. |
|groupMembershipClaims     |    문자열     |    1     |   응용 프로그램이 기대하는 사용자 또는 OAuth 2.0 액세스 토큰에서 발급되는 "그룹" 클레임을 구성하는 비트 마스크입니다. 비트 마스크 값은 0(없음). 1(보안 그룹 및 Azure AD 역할), 2(예약됨) 및 4(예약됨)입니다. 비트 마스크를 7로 설정하면 로그인한 사용자가 속한 모든 보안 그룹, 배포 그룹, Azure AD 디렉터리 역할을 가져옵니다.      |
|optionalClaims     |  문자열       |     null    |    이 특정 앱에 대해 보안 토큰 서비스에서 토큰으로 반환한 선택적 클레임입니다.     |
|acceptMappedClaims    |      부울   | true        |    이 값이 true이면 응용 프로그램이 사용자 지정 서명 키 없이 클레임 매핑을 사용할 수 있게 허용합니다.|
|homepage     |  문자열       |http:<i></i>//MyRegistererdApp         |    응용 프로그램 홈페이지의 URL입니다.     |
|identifierUris     |  문자열 배열       | http:<i></i>//MyRegistererdApp        |   해당 Azure AD 테넌트 안에서 또는 응용 프로그램이 멀티 테넌트인 경우 확인된 사용자 지정 도메인 안에서 웹 응용 프로그램을 고유하게 식별하는 사용자 정의 URI입니다.      |
|keyCredentials     |   배열 형식      |   [{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]      |   이 속성에는 응용 프로그램 할당 자격 증명, 문자열 기반 공유 비밀, X.509 인증서에 대한 참조가 들어 있습니다.   이 자격 증명은 액세스 토큰을 요청할 때(앱이 리소스가 아닌 클라이언트 역할을 수행할 때) 역할을 수행합니다.     |
|knownClientApplications     |     배열 형식    |    [guid]     |     이 값은 클라이언트 응용 프로그램과 사용자 지정 웹 API 응용 프로그램 등 두 부분을 포함하는 솔루션이 있을 때 동의를 묶는 데 사용됩니다. 클라이언트 응용 프로그램의 appID를 이 값에 입력하면 사용자는 클라이언트 응용 프로그램에 대해 한 번만 동의하면 됩니다. Azure AD는 클라이언트에 대한 동의가 웹 API에 대한 암시적 동의임을 인식하고, 클라이언트와 웹 API 모두 동시에 서비스 사용자를 자동으로 프로비전하게 됩니다(클라이언트와 웹 API 응용 프로그램 모두 동일한 테넌트에 등록되어야 함).|
|logoutUrl     |   문자열      |     http:<i></i>//MyRegisteredAppLogout    |   응용 프로그램 로그아웃 URL입니다.      |
|oauth2AllowImplicitFlow     |   부울      |  false       |       이 웹 응용 프로그램이 OAuth2.0 암시적 흐름 토큰을 요청할 수 있는지 여부를 지정합니다. 기본값은 false입니다. Javascript 단일 페이지 앱 같은 브라우저 기반 앱에서 사용됩니다. |
|oauth2AllowUrlPathMatching     |   부울      |  false       |   OAuth 2.0 토큰 요청의 일부로 Azure AD가 응용 프로그램의 replyUrls에 대해 리디렉션 URI의 경로 일치를 허용하는지 여부를 지정합니다.  기본값은 false입니다.      |
|oauth2Permissions     | 배열 형식         |      [{<br>"adminConsentDescription":"응용 프로그램이 로그인한 사용자를 대신하여 리소스에 액세스하도록 허용합니다.",<br>"adminConsentDisplayName":"resource1 액세스",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"응용 프로그램이 사용자를 대신하여 resource1에 액세스하게 허용합니다.",<br>"userConsentDisplayName":"리소스 액세스",<br>"value":"user_impersonation"<br>}]   |  웹 API(리소스) 응용 프로그램을 클라이언트 응용 프로그램에 노출하는 OAuth 2.0 권한 범위 컬렉션입니다. 이러한 권한 범위를 동의를 통해 클라이언트 응용 프로그램에 부여할 수 있습니다. |
|oauth2RequiredPostResponse     | 부울        |    false     |      OAuth 2.0 토큰 요청의 일부로 GET 요청과는 반대로 Azure AD의 POST 요청 허용 여부를 지정합니다. 기본값은 false로, GET 요청만 허용하도록 지정합니다.   
|objectId     | 식별자 문자열        |     ""    |    디렉터리의 응용 프로그램에 대한 고유 식별자입니다.  프로토콜 트랜잭션에서 앱을 식별하는 데 사용되는 식별자가 아닙니다.  디렉터리 쿼리의 개체를 참조하는 데 사용됩니다.|
|passwordCredentials     | 배열 형식        |   [{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]      |    KeyCredentials 속성에 대한 설명을 참조하세요.     |
|publicClient     |  부울       |      false   | 응용 프로그램이 공용 클라이언트(예: 모바일 장치에서 실행되는 설치된 응용 프로그램)인지 여부를 지정합니다.  기본값은 false입니다.        |
|supportsConvergence     |  부울       |   false      | 이 속성은 편집하면 안 됩니다.  기본값을 적용합니다.        |
|replyUrls     |  문자열 배열       |   http:<i></i>//localhost     |  이 다중값 속성은 Azure AD가 토큰을 반환할 때 대상으로 받아들이는 등록된 redirect_uri 값 목록을 포함합니다. |
|requiredResourceAccess     |     배열 형식    |    [{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}]     |   이 응용 프로그램이 액세스를 요청하는 대상 리소스와, OAuth 권한 범위 및 각 리소스에서 필요한 응용 프로그램 역할을 지정합니다. 이 필수 리소스 액세스 사전 구성은 승인 환경을 구동합니다.|
|resourceAppId     |    식별자 문자열     |  ""      |   응용 프로그램이 액세스를 요청하는 대상 리소스의 고유 식별자입니다. 이 값은 대상 리소스 응용 프로그램에 대해 선언된 appId와 같아야 합니다.     |
|resourceAccess     |  배열 형식       | requiredResourceAcces 속성의 예제 값을 참조합니다.        |   OAuth2.0 권한 범위 및 응용 프로그램이 지정된 리소스로부터 요청하는 앱 역할의 목록입니다(지정된 리소스의 ID 및 유형 값 포함).        |
|samlMetadataUrl|문자열|http:<i></i>//MyRegisteredAppSAMLMetadata|응용 프로그램의 SAML 메타데이터에 대한 URL입니다.| 

## <a name="next-steps"></a>다음 단계
* 응용 프로그램의 응용 프로그램 개체와 서비스 주체 개체 간의 관계에 대한 자세한 내용은 [Azure AD의 응용 프로그램 개체 및 서비스 주체 개체][AAD-APP-OBJECTS]를 참조하세요.
* 핵심 Azure AD(Active Directory) 개발자 개념 중 일부에 대한 정의는 [Azure Active Directory 개발자 용어집][AAD-DEVELOPER-GLOSSARY]을 참조하세요.

다음 의견 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖는 데 도움을 줄 수 있습니다.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

