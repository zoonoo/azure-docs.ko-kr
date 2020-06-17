---
title: Microsoft Graph API를 사용하여 SAML 기반 Single Sign-On 구성
titleSuffix: Azure Active Directory
description: 애플리케이션의 여러 인스턴스에 대해 SAML 기반 Single Sign-On을 설정해야 하나요? Microsoft Graph API로 SAML 기반 Single Sign-On의 구성을 자동화하여 시간을 절약하는 방법을 알아보세요.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.reviewer: luleon
ms.openlocfilehash: fd59dcdd566110d1df02333f5701c0c206442d5d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846463"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Microsoft Graph API를 사용하여 SAML 기반 SSO 앱 구성 자동화

이 문서에서는 Azure AD(Azure Active Directory) 갤러리에서 애플리케이션을 만들고 구성하는 방법을 알아봅니다. 이 문서에서는 AWS를 예로 사용하지만, Azure AD 갤러리에 있는 모든 SAML 기반 앱에서 이 문서의 단계를 사용할 수 있습니다.

**Microsoft Graph API를 사용하여 SAML 기반 Single Sign-On의 구성을 자동화하는 방법**

| 단계  | 세부 정보  |
|---------|---------|
| [1. 갤러리 애플리케이션 만들기](#step-1-create-the-gallery-application) | API 클라이언트에 로그인 <br> 갤러리 애플리케이션 검색 <br> 갤러리 애플리케이션 만들기|
| [2. Single Sign-on 구성](#step-2-configure-single-sign-on) | 앱 개체 ID 및 서비스 주체 개체 ID 검색 <br> Single Sign-On 모드 설정 <br> 식별자, 회신 URL, 로그온 URL과 같은 기본 SAML URL 설정 <br> 앱 역할 추가(선택 사항)|
| [3. 클레임 매핑 구성](#step-3-configure-claims-mapping) | 클레임 매핑 정책 만들기 <br> 서비스 주체에게 클레임 매핑 정책 할당|
| [4. 서명 인증서 구성](#step-4-configure-signing-certificate) | 인증서 만들기 <BR> 사용자 지정 서명 키 추가 <br> 사용자 지정 서명 키 활성화|
| [5. 사용자 할당](#step-5-assign-users) | 애플리케이션에 사용자 및 그룹 할당
| [6. 애플리케이션 쪽 구성](#step-6-configure-the-application-side)| Azure AD SAML 메타데이터 가져오기

**설명서에 사용되는 모든 API 목록**

다음 API를 호출할 수 있는 해당 권한이 있는지 확인합니다.

|리소스 유형 |방법 |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[applicationTemplate 나열](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[applicationTemplate 인스턴스화](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)|[servicePrincipal 업데이트](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-beta&tabs=http) <br> [appRoleAssignments 만들기](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-beta&tabs=http) <br> [claimsMappingPolicies 할당](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[애플리케이션](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[애플리케이션 업데이트](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-beta&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [claimsMappingPolicy 만들기](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>이 문서에서 제시하는 응답 개체는 가독성을 위해 짧게 표시될 수 있습니다. 모든 속성은 실제 호출에서 반환됩니다.

## <a name="step-1-create-the-gallery-application"></a>1단계: 갤러리 애플리케이션 만들기

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Microsoft Graph Explorer(권장), Postman 또는 그 밖의 API 클라이언트에 로그인

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 시작합니다.
2. **Microsoft로 로그인**을 선택하고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용하여 로그인합니다.
3. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

### <a name="retrieve-the-gallery-application-template-identifier"></a>갤러리 애플리케이션 템플릿 식별자 검색

Azure AD 애플리케이션 갤러리의 각 애플리케이션에는 해당 애플리케이션의 메타데이터를 설명하는 [애플리케이션 템플릿](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http)이 있습니다. 이 템플릿을 사용하여 테넌트에서 관리를 위해 애플리케이션 및 서비스 주체의 인스턴스를 만들 수 있습니다.

#### <a name="request"></a>요청

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>갤러리 애플리케이션 만들기

전 단계에서 애플리케이션에 대해 검색한 템플릿 ID를 사용하여 테넌트에서 애플리케이션 및 서비스 주체의 [인스턴스를 만듭니다](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http).

#### <a name="request"></a>요청

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>응답


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>2단계: Single Sign-On 구성

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>앱 개체 ID 및 서비스 주체 개체 ID 검색

이전 호출의 응답을 사용하여 애플리케이션 개체 ID 및 서비스 주체 개체 ID를 검색하고 저장합니다.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Single Sign-On 모드 설정

이 예제에서는 [servicePrincipal 리소스 종류](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)에서 `saml`을 Single Sign-On 모드로 설정합니다. 그 밖에도 `notificationEmailAddresses`, `loginUrl`, `samlSingleSignOnSettings.relayState`와 같은 SAML SSO 속성을 구성할 수 있습니다.

#### <a name="request"></a>요청

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml",
    "notificationEmailAddresses": [
        "john@contoso.com"
      ]
}
```

#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>식별자, 회신 URL, 로그온 URL과 같은 기본 SAML URL 설정

애플리케이션 개체에서 AWS에 대해 식별자 및 회신 URL을 설정합니다.

#### <a name="request"></a>요청

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>앱 역할 추가(선택 사항)

애플리케이션에서 토큰에 역할 정보가 필요한 경우, 애플리케이션 개체에서 역할 정의를 추가합니다. AWS의 경우 [사용자 프로비저닝을 사용하도록 설정](../app-provisioning/application-provisioning-configure-api.md)하여 해당 AWS 계정에서 모든 역할을 가져올 수 있습니다. 

자세한 내용은 [SAML 토큰에서 발행된 역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)을 참조하세요.

> [!NOTE] 
> 앱 역할을 추가할 때 msiam_access 기본 앱 역할은 수정하지 않습니다. 

#### <a name="request"></a>요청

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>3단계: 클레임 매핑 구성

### <a name="create-claims-mapping-policy"></a>클레임 매핑 정책 만들기

기본 클레임 외에도 SAML 토큰에서 Azure AD가 다음 클레임을 내보내도록 구성합니다.

| 클레임 이름 | 원본  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userprincipalname |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | “900” |
| roles | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userprincipalname |

#### <a name="request"></a>요청

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>서비스 주체에게 클레임 매핑 정책 할당

#### <a name="request"></a>요청

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/beta/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>4단계: 서명 인증서 구성

[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) API를 사용해도 기본적으로 서명 인증서가 생성되지 않습니다. 사용자 지정 서명 인증서를 만들어서 애플리케이션에 할당하세요. 

### <a name="create-a-custom-signing-certificate"></a>사용자 지정 서명 인증서 만들기

테스트하려면 다음 PowerShell 명령을 사용하여 자체 서명된 인증서를 가져올 수 있습니다. 회사에서 제공하는 최상의 보안 방법을 사용하여 프로덕션을 위한 서명 인증서를 만듭니다.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>사용자 지정 서명 키 추가

서비스 주체에게 다음 정보를 추가합니다.

* 프라이빗 키
* 암호
* 공개 키 

PFX 파일에서 Base64 인코딩된 프라이빗 및 공개 키를 추출합니다. 속성에 대해 자세히 알아보려면 [keyCredential 리소스 종류](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0)를 참조하세요.

“Sign”에 사용되는 keyCredential의 keyId가 passwordCredential의 keyId와 일치하는지 확인합니다.

인증서 지문의 해시를 가져와서 `customkeyIdentifier`를 생성할 수 있습니다.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>요청

> [!NOTE]
> keyCredentials 속성의 “key” 값은 가독성을 위해 짧게 표시되었습니다.

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>사용자 지정 서명 키 활성화

`preferredTokenSigningKeyThumbprint` 속성을 Azure AD에서 SAML 응답에 서명하는 데 사용하도록 할 인증서의 지문으로 설정해야 합니다. 

#### <a name="request"></a>요청

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>5단계: 사용자 할당

### <a name="assign-users-and-groups-to-the-application"></a>애플리케이션에 사용자 및 그룹 할당

다음 사용자를 서비스 주체에게 할당하고 AWS_Role1을 할당합니다. 

| 속성  | ID  |
|---------|---------|
| 사용자 ID(principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| 유형(principalType) | 사용자 |
| 앱 역할 ID(appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID(resourceId) | 515f62cb-d18a-4dca-bec3-bb0bf31deeea |

#### <a name="request"></a>요청

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```
#### <a name="response"></a>응답

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```

자세한 내용은 [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-beta) 리소스 유형을 참조하세요.

## <a name="step-6-configure-the-application-side"></a>6단계: 애플리케이션 쪽 구성

### <a name="get-azure-ad-saml-metadata"></a>Azure AD SAML 메타데이터 가져오기

다음 URL을 사용하여 구성된 애플리케이션에 대해 Azure AD SAML 메타데이터를 가져옵니다. 메타데이터에는 서명 인증서, Azure AD entityID, Azure AD SingleSignOnService와 같은 정보가 포함됩니다.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>다음 단계
- [Microsoft Graph API를 사용하여 사용자 프로비저닝 구성](../app-provisioning/application-provisioning-configure-api.md)
- [AD FS 애플리케이션 활동 보고서를 사용하여 애플리케이션을 Azure AD로 마이그레이션](migrate-adfs-application-activity.md)
