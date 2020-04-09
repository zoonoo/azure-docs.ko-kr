---
title: Azure Active Directory 앱 매니페스트 이해
description: Azure AD 테넌트의 애플리케이션 ID 구성을 나타내고 OAuth 권한 부여, 승인 환경 등을 용이하게 하는 데 사용되는 Azure Active Directory 앱 매니페스트에 대한 자세한 정보를 다룹니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/23/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 487559aaf632a30b7efcda490f92a90d4f59b8f5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883477"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 앱 매니페스트

애플리케이션 매니페스트는 Microsoft ID 플랫폼에 있는 애플리케이션 개체의 모든 특성 정의를 포함합니다. 또한 애플리케이션 개체를 업데이트하기 위한 메커니즘으로도 사용됩니다. 응용 프로그램 엔터티 및 해당 스키마에 대한 자세한 내용은 [그래프 API 응용 프로그램 엔터티 설명서를](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)참조하십시오.

Azure 포털을 통해 또는 [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) 또는 [PowerShell을](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications)프로그래밍 방식으로 사용하여 앱의 특성을 구성할 수 있습니다. 그러나 앱 매니페스트를 편집하여 앱 특성을 구성해야 하는 경우도 있습니다. 이 시나리오에는 다음이 포함됩니다.

* 앱을 Azure AD 다중 테넌트 및 개인 Microsoft 계정으로 등록한 경우 UI에서 지원되는 Microsoft 계정을 변경할 수 없습니다. 대신 애플리케이션 매니페스트 편집기를 사용하여 지원되는 계정 유형을 변경해야 합니다.
* 앱이 지원하는 권한 및 역할을 정의해야 하는 경우 애플리케이션 매니페스트를 수정해야 합니다.

## <a name="configure-the-app-manifest"></a>앱 매니페스트 구성

애플리케이션 매니페스트를 구성하려면

1. [Azure 포털](https://portal.azure.com)로 이동합니다. **Azure Active Directory** 서비스를 검색하고 선택합니다.
1. **앱 등록을 선택합니다.**
1. 구성하려는 앱을 선택합니다.
1. 앱의 **개요** 페이지에서 **매니페스트** 섹션을 선택합니다. 웹 기반 매니페스트 편집기가 열리면 포털 내에서 매니페스트를 편집할 수 있습니다. 필요에 따라 **다운로드**를 선택하여 로컬로 매니페스트를 편집하고 **업로드**를 사용하여 애플리케이션에 다시 적용할 수 있습니다.

## <a name="manifest-reference"></a>매니페스트 참조

이 섹션에서는 응용 프로그램 매니페스트에 있는 특성에 대해 설명합니다.

### <a name="accesstokenacceptedversion-attribute"></a>액세스 토큰 수락 버전 특성

| 키 | 값 형식 |
| :--- | :--- |
| 액세스토큰허용버전 | Nullable Int32 |

리소스에서 필요한 액세스 토큰 버전을 지정합니다. 이 매개 변수는 액세스 토큰을 요청하는 데 사용되는 끝점 또는 클라이언트와 독립적으로 생성된 JWT의 버전과 형식을 변경합니다.

사용된 엔드포인트, v1.0 또는 v2.0은 클라이언트에서 선택되고 id_token의 버전에만 영향을 줍니다. 리소스는 `accesstokenAcceptedVersion`을 명시적으로 구성해서 지원되는 액세스 토큰 형식을 지정해야 합니다.

`accesstokenAcceptedVersion`에 가능한 값은 1, 2 또는 null입니다. 값이 null이면 이 매개 변수는 v1.0 끝점에 해당하는 1로 기본설정됩니다.

있는 `signInAudience` `AzureADandPersonalMicrosoftAccount`경우 값은 `2`이어야 합니다.

예제:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>추가 기능 속성

| 키 | 값 형식 |
| :--- | :--- |
| addIns | 컬렉션 |

사용하는 서비스가 특정 컨텍스트에서 앱을 호출하는 데 사용할 수 있는 사용자 지정 동작을 정의합니다. 예를 들어 파일 스트림을 렌더링할 수 `addIns` 있는 응용 프로그램은 "FileHandler" 기능에 대한 속성을 설정할 수 있습니다. 이 매개 변수를 사용하면 Office 365와 같은 서비스가 사용자가 작업 중인 문서의 컨텍스트에서 응용 프로그램을 호출할 수 있습니다.

예제:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient 특성

| 키 | 값 형식 |
| :--- | :--- |
| 허용공공 클라이언트 | 부울 |

대체 애플리케이션 유형을 지정합니다. Azure AD는 기본적으로 replyUrlsWithType에서 애플리케이션 유형을 유추합니다. Azure AD에서 클라이언트 앱 유형을 확인할 수 없는 특정 시나리오가 있습니다. 예를 들어 이러한 시나리오 중 하나는 HTTP 요청이 URL 리디렉션 없이 발생하는 [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) 흐름입니다. 이러한 경우 Azure AD는 이 속성의 값을 기반으로 응용 프로그램 형식을 해석합니다. 이 값을 true로 설정하면 대체 애플리케이션 유형이 모바일 디바이스에서 실행 중인 설치된 애플리케이션과 같은 공용 클라이언트로 설정됩니다. 기본값은 false이며, 대체 애플리케이션 유형이 웹앱과 같은 기밀 클라이언트임을 의미합니다.

예제:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>사용 가능한ToOtherTenants 특성

| 키 | 값 형식 |
| :--- | :--- |
| availableToOtherTenants | 부울 |

응용 프로그램이 다른 테넌과 공유되는 경우 true로 설정합니다. 그렇지 않으면 거짓입니다.

> [!NOTE]
> 이 특성은 앱 **등록(레거시)** 환경에서만 사용할 수 있습니다. `signInAudience` 앱 등록 환경에서 [대체됩니다.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="appid-attribute"></a>appId 특성

| 키 | 값 형식 |
| :--- | :--- |
| appId | String |

Azure AD가 할당한 앱의 고유 식별자를 지정합니다.

예제:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles 특성

| 키 | 값 형식 |
| :--- | :--- |
| appRoles | 컬렉션 |

앱에서 선언할 수 있는 역할의 컬렉션을 지정합니다. 이러한 역할은 사용자, 그룹 또는 서비스 주체에게 할당될 수 있습니다. 자세한 예제 및 정보는 [응용 프로그램에서 앱 역할 추가를 참조하고 토큰에서 수신합니다.](howto-add-app-roles-in-azure-ad-apps.md)

예제:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>표시이름 특성

| 키 | 값 형식 |
| :--- | :--- |
| displayName | String |

앱의 표시 이름입니다.

> [!NOTE]
> 이 특성은 앱 **등록(레거시)** 환경에서만 사용할 수 있습니다. `name` 앱 등록 환경에서 [대체됩니다.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="errorurl-attribute"></a>errorUrl 특성

| 키 | 값 형식 |
| :--- | :--- |
| errorUrl | String |

지원 안 됨

### <a name="groupmembershipclaims-attribute"></a>그룹멤버클레임 특성

| 키 | 값 형식 |
| :--- | :--- |
|groupMembershipClaims | String |

앱에 필요한, 사용자 또는 OAuth 2.0 액세스 토큰에 발급되는 `groups` 클레임을 구성합니다. 이 특성을 설정하려면 다음과 같은 유효한 문자열 값 중 하나를 사용합니다.

- `"None"`
- `"SecurityGroup"`(보안 그룹 및 Azure AD 역할의 경우)
- `"All"`(로그인한 사용자가 속한 모든 보안 그룹, 배포 그룹, Azure AD 디렉터리 역할을 가져옵니다.)

예제:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>홈페이지 속성

| 키 | 값 형식 |
| :--- | :--- |
| homepage |String |

애플리케이션 홈페이지 URL입니다.

> [!NOTE]
> 이 특성은 앱 **등록(레거시)** 환경에서만 사용할 수 있습니다. `signInUrl` 앱 등록 환경에서 [대체됩니다.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="objectid-attribute"></a>objectId 특성

| 키 | 값 형식 |
| :--- | :--- |
|objectId | String |

디렉터리의 앱에 대한 고유 식별자입니다.

앱 **등록(레거시)** 환경에서만 사용할 수 있습니다. `id` 앱 등록 환경에서 [대체됩니다.](https://go.microsoft.com/fwlink/?linkid=2083908)

예제:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>선택적클레임 특성

| 키 | 값 형식 |
| :--- | :--- |
| optionalClaims | String |

이 특정 앱에 대해 보안 토큰 서비스에서 토큰으로 반환한 선택적 클레임입니다.

현재 앱 등록 포털을 통해 등록된 Azure AD 및 개인 계정을 모두 지원하는 앱은 선택적 클레임을 사용할 수 없습니다. 그러나 v2.0 엔드포인트를 사용하여 Azure AD에만 등록된 앱은 매니페스트에서 요청한 선택적 클레임을 가져올 수 있습니다. 자세한 내용은 [선택적 클레임](active-directory-optional-claims.md)을 참조하십시오.

예제:

```json
    "optionalClaims": null,
```

### <a name="id-attribute"></a>id 특성

| 키 | 값 형식 |
| :--- | :--- |
| id | String |

디렉터리의 앱에 대한 고유 식별자입니다. 이 ID는 프로토콜 트랜잭션에서 앱을 식별하는 데 사용되는 식별자가 아닙니다. 디렉터리 쿼리의 개체를 참조하는 데 사용됩니다.

예제:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="identifieruris-attribute"></a>식별자Uris 특성

| 키 | 값 형식 |
| :--- | :--- |
| identifierUris | 문자열 배열 |

Azure AD 테넌트 안에서 또는 앱이 멀티 테넌트인 경우 확인된 사용자 지정 도메인 안에서 웹앱을 고유하게 식별하는 사용자 정의 URI입니다.

예제:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>정보 url 속성

| 키 | 값 형식 |
| :--- | :--- |
| 정보 urls | String |

앱의 서비스 약관 및 개인정보처리방침 링크를 지정합니다. 서비스 약관 및 개인정보처리방침은 사용자 동의 환경을 통해 사용자에게 표시됩니다. 자세한 내용은 [등록된 Azure AD 앱의 서비스 약관 및 개인정보처리방침을 추가하는 방법](howto-add-terms-of-service-privacy-statement.md)을 참조하세요.

예제:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>키 자격 증명 특성

| 키 | 값 형식 |
| :--- | :--- |
| keyCredentials | 컬렉션 |

앱 할당 자격 증명, 문자열 기반 공유 비밀, X.509 인증서에 대한 참조가 들어 있습니다. 이러한 자격 증명은 액세스 토큰을 요청할 때 사용됩니다(앱이 리소스로 클라이언트 역할을 하는 경우).

예제:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>알려진 클라이언트 응용 프로그램 특성

| 키 | 값 형식 |
| :--- | :--- |
| knownClientApplications | 문자열 배열 |

클라이언트 앱과 사용자 지정 웹 API 앱 등 두 부분을 포함하는 솔루션이 있을 때 동의를 묶는 데 사용됩니다. 클라이언트 앱의 appID를 이 값에 입력하면 사용자는 클라이언트 앱에 대해 한 번만 동의하면 됩니다. Azure AD는 클라이언트에 동의한다는 것은 웹 API에 암시적으로 동의한다는 것을 의미합니다. 클라이언트와 웹 API모두에 대한 서비스 주체를 동시에 프로비전합니다. 클라이언트와 웹 API 앱은 모두 동일한 테넌트에 등록되어야 합니다.

예제:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>로고Url 속성

| 키 | 값 형식 |
| :--- | :--- |
| 로고Url | String |

포털에서 업로드된 로고에 대한 CDN URL을 가리키는 읽기 전용 값입니다.

예제:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>로그아웃Url 특성

| 키 | 값 형식 |
| :--- | :--- |
| logoutUrl | String |

앱을 로그아웃하는 URL입니다.

예제:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>이름 속성

| 키 | 값 형식 |
| :--- | :--- |
| name | String |

앱의 표시 이름입니다.

예제:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2허용심플흐름 속성

| 키 | 값 형식 |
| :--- | :--- |
| oauth2AllowImplicitFlow | 부울 |

이 웹앱이 OAuth2.0 암시적 흐름 액세스 토큰을 요청할 수 있는지 여부를 지정합니다. 기본값은 false입니다. 이 플래그는 JavaScript 단일 페이지 앱과 같은 브라우저 기반 앱에 사용됩니다. 자세히 알아보려면 목차에서 `OAuth 2.0 implicit grant flow`을 입력하여 암시적 흐름에 대한 항목을 참조하세요.

예제:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowId토큰심암시플로우 속성

| 키 | 값 형식 |
| :--- | :--- |
| oauth2AllowId토큰심암시플로우 | 부울 |

이 웹앱이 OAuth2.0 암시적 흐름 ID 토큰을 요청할 수 있는지 여부를 지정합니다. 기본값은 false입니다. 이 플래그는 JavaScript 단일 페이지 앱과 같은 브라우저 기반 앱에 사용됩니다.

예제:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2권한 속성

| 키 | 값 형식 |
| :--- | :--- |
| oauth2Permissions | 컬렉션 |

웹 API(리소스) 앱을 클라이언트 앱에 노출하는 OAuth 2.0 권한 범위 컬렉션을 지정합니다. 이러한 권한 범위를 동의를 통해 클라이언트 앱에 부여할 수 있습니다.

예제:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse 특성

| 키 | 값 형식 |
| :--- | :--- |
| oauth2RequiredPostResponse | 부울 |

OAuth 2.0 토큰 요청의 일부로 GET 요청과는 반대로 Azure AD의 POST 요청 허용 여부를 지정합니다. 기본값은 false로, GET 요청만 허용하도록 지정합니다.

예제:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>보호자제어설정 속성

| 키 | 값 형식 |
| :--- | :--- |
| 보호자제어설정 | String |

- `countriesBlockedForMinors`는 미성년자가 사용할 수 없도록 앱이 차단되는 국가를 지정합니다.
- `legalAgeGroupRule`은 앱 사용자에게 적용되는 법적 연령 그룹 규칙을 지정합니다. 설정 가능한 값은 `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` 또는 `BlockMinors`입니다.  

예제:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>암호 자격 증명 특성

| 키 | 값 형식 |
| :--- | :--- |
| passwordCredentials | 컬렉션 |

`keyCredentials`에 대한 설명을 참조하세요.

예제:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>사전 승인된 응용 프로그램 특성

| 키 | 값 형식 |
| :--- | :--- |
| 사전 승인된 응용 프로그램 | 컬렉션 |

암시적 동의에 대해 요청된 사용 권한 및 애플리케이션을 나열합니다. 관리자가 애플리케이션에 대한 동의를 제공한 상태여야 합니다. preAuthorizedApplications는 사용자가 요청된 사용 권한에 동의하지 않아도 됩니다. preAuthorizedApplications에 나열된 사용 권한은 사용자 동의가 필요하지 않습니다. 그러나 preAuthorizedApplications에 나열되지 않은 요청된 추가 사용 권한에는 사용자 동의가 필요합니다.

예제:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>publicClient 특성

| 키 | 값 형식 |
| :--- | :--- |
| publicClient | 부울|

이 애플리케이션이 모바일 디바이스에 설치되어 실행되는 애플리케이션 등의 공용 클라이언트인지 여부를 지정합니다. 

이 속성은 앱 **등록(레거시)** 환경에서만 사용할 수 있습니다. `allowPublicClient` 앱 등록 환경에서 [대체됩니다.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="publisherdomain-attribute"></a>게시자도메인 속성

| 키 | 값 형식 |
| :--- | :--- |
| 퍼블리셔도메인 | String |

응용 프로그램에 대해 확인된 게시자 도메인입니다. 읽기 전용입니다.

예제:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>회신Url속성

| 키 | 값 형식 |
| :--- | :--- |
| replyUrls | 문자열 배열 |

이 다중값 속성은 Azure AD가 토큰을 반환할 때 대상으로 허용하는 등록된 redirect_uri 값 목록을 포함합니다.

이 속성은 앱 **등록(레거시)** 환경에서만 사용할 수 있습니다. `replyUrlsWithType` 앱 등록 환경에서 [대체됩니다.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="replyurlswithtype-attribute"></a>회신UrlsWithType 특성

| 키 | 값 형식 |
| :--- | :--- |
| 회신Urls유형 | 컬렉션 |

이 다중값 속성은 Azure AD가 토큰을 반환할 때 대상으로 허용하는 등록된 redirect_uri 값 목록을 포함합니다. 각 URI 값에는 연결된 앱 유형 값이 포함되어야 합니다. 지원되는 형식 값은 다음과 같습니다.

- `Web`
- `InstalledClient`

자세한 내용은 [replyUrl 제한 및 제한 사항을](https://docs.microsoft.com/azure/active-directory/develop/reply-url)참조하십시오.

예제:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>필수 리소스 액세스 특성

| 키 | 값 형식 |
| :--- | :--- |
| requiredResourceAccess | 컬렉션 |

동적 동의를 통해 `requiredResourceAccess`는 관리자 동의 환경 및 정적 동의를 사용하는 사용자 동의 환경을 구동합니다. 그러나 이 매개 변수는 일반적인 경우에 대한 사용자 동의 환경을 구동하지 않습니다.

- `resourceAppId`는 앱이 액세스를 요청하는 대상 리소스의 고유 식별자입니다. 이 값은 대상 리소스 앱에 대해 선언된 appId와 같아야 합니다.
- `resourceAccess`는 앱이 지정된 리소스에서 요구하는 OAuth2.0 권한 범위 및 앱 역할을 나열하는 배열입니다. 지정된 리소스의 `id` 및 `type` 값을 포함합니다.

예제:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>saml메타데이터Url 속성

| 키 | 값 형식 |
| :--- | :--- |
| samlMetadataUrl | String |

앱의 SAML 메타데이터 URL입니다.

예제:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl 속성

| 키 | 값 형식 |
| :--- | :--- |
| 사인인Url | String |

앱의 홈페이지 URL을 지정합니다.

예제:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>사인인타겟 속성

| 키 | 값 형식 |
| :--- | :--- |
| 사인인타겟 | String |

현재 애플리케이션에 대해 지원되는 Microsoft 계정을 지정합니다. 지원되는 값은 다음과 같습니다.
- `AzureADMyOrg`- 내 조직의 Azure AD 테넌트에 Microsoft 직장 또는 학교 계정이 있는 사용자(예: 단일 테넌트)
- `AzureADMultipleOrgs`- 조직의 Azure AD 테넌트에 Microsoft 직장 또는 학교 계정이 있는 사용자(예: 다중 테넌트)
- `AzureADandPersonalMicrosoftAccount`- 개인 Microsoft 계정 또는 조직의 Azure AD 테넌트에 있는 직장 또는 학교 계정이 있는 사용자
- `PersonalMicrosoftAccount`- Xbox 및 Skype와 같은 서비스에 로그인하는 데 사용되는 개인 계정입니다.

예제:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>태그 속성

| 키 | 값 형식 |
| :--- | :--- |
| tags | 문자열 배열  |

애플리케이션을 범주화하고 식별하는 데 사용할 수 있는 사용자 지정 문자열입니다.

예제:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>일반적인 문제

### <a name="manifest-limits"></a>매니페스트 제한

응용 프로그램 매니페스트에는 컬렉션이라고 하는 여러 특성이 있습니다. 예를 들어 appRoles, 키 자격 증명, 알려진 클라이언트 응용 프로그램, 식별자Uris, 리디렉션Uris, 필수ResourceAccess 및 oauth2권한. 모든 응용 프로그램에 대한 전체 응용 프로그램 매니페스트 내에서 결합된 모든 컬렉션의 총 항목 수는 1200개로 제한되었습니다. 이전에 응용 프로그램 매니페스트에 100개의 리디렉션 URI를 지정한 경우 매니페스트를 구성하는 다른 모든 컬렉션에서 사용할 1100개의 나머지 항목만 남게 됩니다.

> [!NOTE]
> 응용 프로그램 매니페스트에 1200 개 이상의 항목을 추가하려고하면 **"응용 프로그램 xxxxxx를 업데이트하지 못했습니다. 오류 세부 정보: 매니페스트의 크기가 제한을 초과했습니다. 값 의 수를 줄이고 요청을 다시 시도하십시오."**

### <a name="unsupported-attributes"></a>지원되지 않는 특성

응용 프로그램 매니페스트는 Azure AD의 기본 응용 프로그램 모델의 스키마를 나타냅니다. 기본 스키마가 발전함에 따라 매니페스트 편집기는 수시로 새 스키마를 반영하도록 업데이트됩니다. 따라서 응용 프로그램 매니페스트에 새 특성이 나타날 수 있습니다. 드문 경우지만 기존 특성에서 구문 또는 의미 체계 가 변경되거나 이전에 존재했던 특성이 더 이상 지원되지 않는 것을 발견할 수 있습니다. 예를 들어 앱 등록(레거시) 환경에서 다른 이름으로 알려진 앱 [등록에](https://go.microsoft.com/fwlink/?linkid=2083908)새 속성이 표시됩니다.

| 앱 등록(레거시)| 앱 등록           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

이러한 특성에 대한 설명은 [매니페스트 참조](#manifest-reference) 섹션을 참조하십시오.

이전에 다운로드한 매니페스트를 업로드하려고 하면 다음 오류 중 하나가 표시될 수 있습니다. 이 오류는 매니페스트 편집기에서 업로드하려는 스키마와 일치하지 않는 최신 버전의 스키마를 지원하기 때문일 수 있습니다.

* "xxxxxx 응용 프로그램을 업데이트하지 못했습니다. 오류 세부 정보: 잘못된 개체 식별자 '정의되지 않음'. []."
* "xxxxxx 응용 프로그램을 업데이트하지 못했습니다. 오류 세부 정보: 지정된 하나 이상의 속성 값이 잘못되었습니다. []."
* "xxxxxx 응용 프로그램을 업데이트하지 못했습니다. 오류 세부 정보: 업데이트를 위해 이 API 버전에서 사용 가능한ToOtherTenants를 설정할 수 없습니다. []."
* "xxxxxx 응용 프로그램을 업데이트하지 못했습니다. 오류 세부 정보: 'replyUrls' 속성에 대 한 업데이트는이 응용 프로그램에 대 한 허용 되지 않습니다. 대신 'replyUrlsWithType' 속성을 사용합니다. []."
* "xxxxxx 응용 프로그램을 업데이트하지 못했습니다. 오류 세부 정보: 형식 이름이 없는 값을 찾았으며 예상된 형식을 사용할 수 없습니다. 모델을 지정하면 페이로드의 각 값에는 페이로드에 명시적으로 지정하거나 호출자가 명시적으로 지정하거나 부모 값에서 암시적으로 유추할 수 있는 형식이 있어야 합니다. []"

이러한 오류 중 하나가 표시되면 다음 작업을 권장합니다.

1. 이전에 다운로드한 매니페스트를 업로드하는 대신 매니페스트 편집기에서 특성을 개별적으로 편집합니다. [매니페스트 참조](#manifest-reference) 테이블을 사용하여 이전 및 새 특성의 구문 및 의미 체계를 이해하여 관심 있는 특성을 성공적으로 편집할 수 있습니다. 
1. 워크플로에서 나중에 사용할 수 있도록 원본 리포지토리에 매니페스트를 저장해야 하는 경우 앱 등록 경험에 표시되는 매니페스트와 함께 리포지토리에 저장된 **매니페스트를** 리베이스하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 앱의 응용 프로그램 및 서비스 주체 개체 간의 관계에 대한 자세한 내용은 [Azure AD의 응용 프로그램 및 서비스 주체 개체를](app-objects-and-service-principals.md)참조하십시오.
* 일부 핵심 Microsoft ID 플랫폼 개발자 개념의 정의는 Microsoft ID 플랫폼 개발자 [용어집을](developer-glossary.md) 참조하십시오.

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
