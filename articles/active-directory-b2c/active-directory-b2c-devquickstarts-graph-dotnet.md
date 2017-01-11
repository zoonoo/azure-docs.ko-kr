---
title: "Azure Active Directory B2C: Graph API 사용 | Microsoft Docs"
description: "프로세스를 자동화하기 위해 응용 프로그램 ID를 사용하여 B2C 테넌트에 Graph API를 호출하는 방법입니다."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: bryanla
ms.assetid: f9904516-d9f7-43b1-ae4f-e4d9eb1c67a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 271e2bd40fb605dc6be229bea8fb479effae4298


---
# <a name="azure-ad-b2c-use-the-graph-api"></a>Azure AD B2C: Graph API 사용
Azure Active Directory(Azure AD) B2C 테넌트는 매우 큰 경향이 있습니다. 즉, 많은 일반 테넌트 관리 작업을 프로그래밍 방식으로 수행해야 합니다. 주요 예제는 사용자 관리입니다. B2C 테넌트에 기존 사용자 저장소를 마이그레이션해야 할 수 있습니다. 고유한 페이지에서 사용자 등록을 호스팅하고 백그라운드에서 Azure AD에 사용자 계정을 만들려할 수 있습니다. 이러한 형식의 태스크는 사용자 계정을 만들고 읽고 업데이트 및 삭제하는 기능이 필요합니다. Azure AD Graph API를 사용하여 이 태스크를 수행할 수 있습니다.

B2C 테넌트의 경우 Graph API와 통신하는 두 가지 기본 모드가 있습니다.

* 대화형인 한 번 실행 작업의 경우 태스크를 수행할 때 B2C 테넌트에서 관리자 계정으로 작동해야 합니다. 이 모드에서는 관리자가 Graph API에 대한 호출을 수행할 수 있기 전에 해당 관리자가 자격 증명으로 로그인해야 합니다.
* 자동화된 연속 작업의 경우 필요한 권한을 제공하는 일종의 서비스 계정을 사용하여 관리 작업을 수행해야 합니다. Azure AD에서 응용 프로그램을 등록하고 Azure AD에 인증하여 이 작업을 수행할 수 있습니다. **OAuth 2.0 클라이언트 자격 증명 부여** 를 사용하는 [응용 프로그램 ID](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)를 사용하여 수행합니다. 이 경우에 응용 프로그램은 사용자로서가 아닌 자체로서 Graph API를 호출합니다.

이 기사에서는 자동화된 사용 사례를 수행하는 방법을 설명합니다. 이를 보여주려면 사용자 만들기, 읽기, 업데이트 및 삭제(CRUD) 작업을 수행하는 .NET 4.5 `B2CGraphClient`을 작성합니다. 클라이언트에는 다양한 메서드를 호출할 수 있도록 하는 Windows CLI(명령줄 인터페이스)가 있습니다. 그러나 코드를 비대화형이고 자동화된 방식으로 동작하도록 기록합니다.

## <a name="get-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 가져오기
응용 프로그램 또는 사용자를 만들거나 Azure AD와 상호 작용하려면 Azure AD B2C 테넌트 및 해당 테넌트의 전역 관리자 계정이 있어야 합니다. 테넌트가 없는 경우 [Azure AD B2C를 시작](active-directory-b2c-get-started.md)합니다.

## <a name="register-a-service-application-in-your-tenant"></a>테넌트에 서비스 응용 프로그램 등록
B2C 테넌트가 있으므로 Azure AD PowerShell Cmdlet을 사용하여 서비스 응용 프로그램을 만들어야 합니다.
우선 [Microsoft Online Services 로그인 도우미](http://go.microsoft.com/fwlink/?LinkID=286152)를 다운로드 및 설치합니다. 그런 다음 [Windows PowerShell 용 64비트 Azure Active Directory 모듈](http://go.microsoft.com/fwlink/p/?linkid=236297)을 다운로드하고 설치합니다.

> [!IMPORTANT]
> B2C 테넌트에서 Graph API를 사용하려면 PowerShell을 사용하여 전용 응용 프로그램을 등록해야 합니다. 이렇게 하려면 이 문서의 지침을 따릅니다. Azure 포털에 등록한 기존 B2C 응용 프로그램을 다시 사용할 수 없습니다.
> 
> 

PowerShell 모듈을 설치한 후에 PowerShell을 열고 B2C 테넌트에 연결합니다. `Get-Credential`을 실행한 후에 사용자 이름 및 암호를 묻는 메시지가 표시되면 B2C 테넌트 관리자 계정의 사용자 이름 및 암호를 입력합니다.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

응용 프로그램을 만들기 전에 새 **클라이언트 암호**를 생성해야 합니다.  응용 프로그램이 클라이언트 암호를 사용하여 Azure AD에 인증하고 액세스 토큰을 획득합니다. PowerShell에서 유효한 암호를 생성할 수 있습니다.

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

마지막 명령은 새 클라이언트 암호를 인쇄해야 합니다. 안전한 곳에 복사합니다. 나중에 필요합니다. 이제 새 클라이언트 암호를 앱에 대한 자격 증명으로 제공하여 응용 프로그램을 만들 수 있습니다.

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

성공적으로 응용 프로그램을 만들면 위와 같은 응용 프로그램의 일부 속성을 인쇄해야 합니다. 해당 값을 복사하려면 `ObjectId` 및 `AppPrincipalId` 둘 다 필요합니다.

B2C 테넌트에 응용 프로그램을 만든 후에 사용자 CRUD 작업을 수행하는 데 필요한 권한을 응용 프로그램에 할당해야 합니다. 응용 프로그램에 디렉터리 판독기(사용자 읽기), 디렉터리 작성자(사용자 만들기 및 업데이트) 및 사용자 계정 관리자(사용자 삭제) 등 세 가지 역할을 할당합니다. 이러한 역할에는 잘 알려진 식별자가 있으므로 `-RoleMemberObjectId` 매개 변수를 위의 `ObjectId`로 대체하여 다음 명령을 실행할 수 있습니다. 모든 디렉터리 역할의 목록을 보려면 `Get-MsolRole`을 실행합니다.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

이제 B2C 테넌트에서 사용자 만들기, 읽기, 업데이트 및 삭제 권한을 가진 응용 프로그램이 있습니다.

## <a name="download-configure-and-build-the-sample-code"></a>샘플 코드를 다운로드, 구성 및 작성합니다.
우선 샘플 코드를 다운로드하고 실행합니다. 그런 다음 자세히 살펴봅니다.  [샘플 코드를 .zip 파일로 다운로드](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)할 수 있습니다. 사용자가 선택한 디렉터리에 복제할 수 있습니다.

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Visual Studio에서 `B2CGraphClient\B2CGraphClient.sln` Visual Studio 솔루션을 엽니다. `B2CGraphClient` 프로젝트에서 `App.config` 파일을 엽니다. 세 개의 앱 설정을 다음과 같이 고유한 값으로 대체합니다.

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

이제 `B2CGraphClient` 솔루션을 마우스 오른쪽 단추로 클릭하고 샘플을 다시 작성합니다. 성공하는 경우 `B2C.exe` 실행 파일이 `B2CGraphClient\bin\Debug`에 있어야 합니다.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Graph API를 사용하여 사용자 CRUD 작업 작성
B2CGraphClient를 사용하려면 `cmd` Windows 명령 프롬프트를 열고 `Debug` 디렉터리로 디렉터리를 변경합니다. `B2C Help` 명령을 실행합니다.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

각 명령에 대한 간략한 설명을 표시합니다. 이 명령 중 하나를 호출할 때마다 `B2CGraphClient` 은 Azure AD Graph API에 요청합니다.

### <a name="get-an-access-token"></a>액세스 토큰 가져오기
Graph API에 대한 요청은 인증을 위한 액세스 토큰이 필요합니다. `B2CGraphClient` 은 오픈 소스 ADAL(Active Directory 인증 라이브러리)를 사용하여 액세스 토큰을 획득할 수 있도록 합니다. ADAL을 사용하면 간단한 API를 제공하고 액세스 토큰의 캐싱과 같은 중요한 일부 세부 정보를 처리하여 토큰을 쉽게 얻을 수 있습니다. 그러나 ADAL을 사용하여 토큰을 가져올 필요가 없습니다. HTTP 요청을 선별하여 토큰을 가져올 수도 있습니다.

> [!NOTE]
> 이 코드 샘플에서는 Graph API와 통신하기 위해 ADAL v2를 사용합니다.  Azure AD Graph API와 함께 사용할 수 있는 액세스 토큰을 가져오기 위해 ADAL v2 또는 v3를 사용해야 합니다.
> 
> 

`B2CGraphClient`가 실행되면 `B2CGraphClient` 클래스의 인스턴스를 만듭니다. 이 클래스의 생성자는 ADAL 인증 스캐폴딩을 설정합니다.

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

`B2C Get-User` 명령을 예로 사용합니다. `B2C Get-User`이 추가 입력 없이 호출되면 CLI가 `B2CGraphClient.GetAllUsers(...)` 메서드를 호출합니다. 이 메서드는 `B2CGraphClient.SendGraphGetRequest(...)`를 호출하며 이는 Graph API에 HTTP GET 요청을 제출합니다. `B2CGraphClient.SendGraphGetRequest(...)` 가 가져오기 요청을 보내기 전에 먼저 ADAL을 사용하여 액세스 토큰을 가져옵니다.

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

ADAL `AuthenticationContext.AcquireToken(...)` 메서드를 호출하여 Graph API에 대한 액세스 토큰을 가져올 수 있습니다. 그러면 ADAL은 응용 프로그램의 ID를 나타내는 `access_token` 를 반환합니다.

### <a name="read-users"></a>사용자 읽기
Graph API에서 사용자의 목록을 가져오거나 특정 사용자를 가져오려는 경우 `/users` 끝점에 HTTP `GET` 요청을 보낼 수 있습니다. 테넌트의 모든 사용자에 대한 요청은 다음과 같습니다.

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

이 요청을 보려면 다음을 실행합니다.

 ```
 > B2C Get-User
 ```

유의해야 할 두 가지 중요한 사항은 다음과 같습니다.

* ADAL을 통해 획득한 액세스 토큰은 `Bearer` 체계를 사용하여 `Authorization` 헤더에 추가됩니다.
* B2C 테넌트의 경우 쿼리 매개 변수 `api-version=1.6`를 사용해야 합니다.

이러한 세부 사항은 모두 `B2CGraphClient.SendGraphGetRequest(...)` 메서드에서 처리됩니다.

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>소비자 사용자 계정 만들기
B2C 테넌트에 사용자 계정을 만들 경우 `/users` 끝점에 HTTP `POST` 요청을 보낼 수 있습니다.

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

이 요청의 이러한 대부분의 속성은 소비자 사용자를 만드는 데 필요합니다. 자세히 알아보려면 [여기](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)를 클릭하세요. `//` 메모는 그림에 포함되었습니다. 실제 요청에 포함되지 않습니다.

요청을 확인하려면 다음 명령 중 하나를 실행합니다.

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 명령은 .json 파일을 입력 매개 변수로 사용합니다. 이는 사용자 개체의 JSON 표현을 포함합니다. 샘플 코드에는 두 개의 샘플 .json 파일인 `usertemplate-email.json`과 `usertemplate-username.json`이 있습니다. 필요에 따라 이러한 파일을 수정할 수 있습니다. 위의 필수 필드 외에도 사용할 수 있는 여러 가지 선택적 필드가 이러한 파일에 포함됩니다. 선택적 필드에 대한 세부 정보는 [Azure AD Graph API 엔터티 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity)에서 찾을 수 있습니다.

이 POST 요청이 `B2CGraphClient.SendGraphPostRequest(...)`에서 생성되는 방법을 확인할 수 있습니다.

* 액세스 토큰을 요청의 `Authorization` 헤더에 연결합니다.
* `api-version=1.6`을 설정합니다.
* 요청 본문에 JSON 사용자 개체를 포함합니다.

> [!NOTE]
> 기존 사용자 저장소에서 마이그레이션하려는 계정에 [Azure AD B2C에 의해 적용되는 강력한 암호 강도](https://msdn.microsoft.com/library/azure/jj943764.aspx)보다 낮은 강도의 암호가 지정되어 있으면 `passwordPolicies` 속성의 `DisableStrongPassword` 값을 사용하여 강력한 암호 요구 사항을 사용하지 않도록 설정할 수 있습니다. 예를 들어 다음과 같이 위에 제공된 사용자 만들기 요청을 수정할 수 있습니다. `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>소비자 사용자 계정 업데이트
사용자 개체를 업데이트할 때 사용자 개체를 만드는 작업과 프로세스가 비슷합니다. 하지만 이 프로세스는 HTTP `PATCH` 메서드를 사용합니다.

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

JSON 파일을 새 데이터로 업데이트하여 사용자를 업데이트하려고 합니다. `B2CGraphClient`을 사용하여 이러한 명령 중 하나를 실행할 수 있습니다.

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

이 요청을 보내는 방법에 대한 세부 정보는 `B2CGraphClient.SendGraphPatchRequest(...)` 메서드를 검사합니다.

### <a name="search-users"></a>사용자 검색
여러 가지 방법으로 B2C 테넌트에서 사용자를 검색할 수 있습니다. 하나는 사용자의 개체 ID를 사용하는 방법이고 다른 하나는 사용자의 로그인 식별자(예: `signInNames` 속성)를 사용하는 방법입니다.

특정 사용자를 검색하려면 다음 명령 중 하나를 실행합니다.

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

다음은 몇 가지 예입니다.

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>사용자 삭제
사용자를 삭제하는 과정은 간단합니다. HTTP `DELETE` 메서드를 사용하고 올바른 개체 ID로 URL을 생성합니다.

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

예제를 보려면 이 명령을 입력하고 콘솔에 출력된 삭제 요청을 봅니다.

```
> B2C Delete-User <object-id-of-user>
```

이 요청을 보내는 방법에 대한 세부 정보는 `B2CGraphClient.SendGraphDeleteRequest(...)` 메서드를 검사합니다.

사용자 관리 외에도 Azure AD Graph API를 사용하여 다른 많은 작업을 수행할 수 있습니다. [Azure AD Graph API 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) 는 샘플 요청과 함께 각 작업의 세부 정보를 제공합니다.

## <a name="use-custom-attributes"></a>사용자 지정 특성 사용
많은 소비자 응용 프로그램은 특정 유형의 사용자 지정 사용자 프로필 정보를 저장해야 합니다. 이렇게 할 수 있는 한 가지 방법은 B2C 테넌트의 사용자 지정 특성을 정의하는 것입니다. 그런 다음 해당 특성을 사용자 개체의 다른 속성을 다룰 경우와 동일한 방식으로 다룰 수 있습니다. 특성을 업데이트 및 삭제하고 특성으로 쿼리하며 특성을 로그인 토큰에서 클레임으로 보낼 수 있습니다.

B2C 테넌트에서 사용자 지정 특성을 정의하려면 [B2C 사용자 지정 특성 참조](active-directory-b2c-reference-custom-attr.md)를 참조하세요.

`B2CGraphClient`를 사용하여 B2C 테넌트에 정의된 사용자 지정 특성을 볼 수 있습니다.

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

이러한 함수의 출력은 각 사용자 지정 특성의 세부 정보를 다음과 같이 표시합니다.

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

`extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`와 같은 전체 이름을 사용자 개체의 속성으로 사용할 수 있습니다.  .json 파일을 새 속성 및 속성에 대한 값으로 업데이트한 다음 실행합니다.

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

`B2CGraphClient`를 사용하여 B2C 테넌트 사용자를 프로그래밍 방식으로 관리할 수 있는 서비스 응용 프로그램이 있습니다. `B2CGraphClient` 는 고유한 응용 프로그램 ID를 사용하여 Azure AD Graph API에 인증합니다. 또한 클라이언트 암호를 사용하여 토큰을 획득합니다. 응용 프로그램에 이 기능을 통합할 때 B2C 앱에 대한 몇 가지 주요 사항을 기억해야 합니다.

* 테넌트에서 응용 프로그램에 적절한 권한을 부여해야 합니다.
* 이제 ADAL v2를 사용하여 액세스 토큰을 가져와야 합니다. (또한 라이브러리를 사용하지 않고 직접 프로토콜 메시지를 보낼 수 있습니다.)
* Graph API를 호출할 때 `api-version=1.6`을 사용합니다.
* 소비자 사용자를 만들고 업데이트하는 경우 위에서 설명한 대로 필수적인 몇 가지 속성이 있습니다.

B2C 테넌트의 Graph API를 사용하여 수행하려는 작업에 대한 질문이나 요청이 있는 경우 이 문서 또는 파일에 GitHub 코드 샘플 리포지토리의 문제에 대한 의견을 남겨주세요.




<!--HONumber=Dec16_HO5-->


