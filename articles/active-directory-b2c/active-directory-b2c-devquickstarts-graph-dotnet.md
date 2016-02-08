<properties
	pageTitle="Azure AD B2C 미리 보기: Graph API 사용 | Microsoft Azure"
	description="프로세스를 자동화하기 위해 응용 프로그램 ID를 사용하여 B2C 테넌트에 대해 Graph API를 호출하는 방법입니다."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Azure AD B2C 미리 보기: Graph API 사용

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Azure AD B2C 테넌트는 일반적으로 매우 큽니다. 즉, 많은 일반 테넌트 관리 작업을 프로그래밍 방식으로 수행해야 합니다. 주된 예는 사용자 관리입니다. B2C 테넌트에 기존 사용자 저장소를 마이그레이션하거나 배후에서 Azure AD에 사용자 계정을 만들어 고유의 페이지에서 사용자 등록을 호스트해야 할 수 있습니다. 이러한 형식의 작업은 사용자 계정을 만들고 읽고 업데이트 및 삭제해야 할 수 있고 이는 Azure AD Graph API를 사용하여 수행할 수 있습니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

B2C 테넌트의 경우 Graph API와 통신하는 두 가지 기본적인 모드가 있습니다.

- 대화형인 한 번 실행 작업의 경우 B2C 테넌트에서 관리자 계정으로 작동하는 관리 작업을 수행하려고 합니다. 이 모드는 관리자를 필요로 하여 Graph API에 호출을 수행하기 전에 자격 증명으로 로그인합니다.
- 자동화된 연속 작업의 경우 필요한 권한을 부여하는 일종의 서비스 계정을 사용하여 관리 작업을 수행하려 합니다. Azure AD에서 [OAuth 2.0 클라이언트 자격 증명 부여](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)를 사용하여 응용 프로그램을 등록하고 "응용 프로그램 ID"를 사용하여 Azure AD로 인증함으로써 이 작업을 수행할 수 있습니다. 이 경우에 응용 프로그램은 특정 사용자 보다 자체로 움직이는 Graph API를 호출합니다.  

이 기사에서는 후자의 자동화된 사용 사례를 수행하는 방법을 보여줍니다. 시연하려면 사용자 CRUD 작업을 수행하는 .NET 4.5 "B2CGraphClient"를 빌드합니다. 시도한다는 목적을 위해 클라이언트에는 다양한 메서드를 호출할 수 있도록 하는 Windows 명령줄 인터페이스가 있습니다. 그러나 코드를 비대화형이고 자동화된 방식으로 동작하도록 기록합니다. 이제 시작하겠습니다.

## Azure AD B2C 테넌트 가져오기

응용 프로그램, 사용자를 만들거나 Azure AD와 상호 작용하려면 Azure AD B2C 테넌트 및 해당 테넌트의 전역 관리자 계정이 있어야 합니다. 하나도 없다면 [Azure AD B2C 시작](active-directory-b2c-get-started.md) 가이드를 수행합니다.

## 테넌트에 서비스 응용 프로그램 등록

이제 B2C 테넌트가 있으므로 Azure AD Powershell Cmdlet을 사용하여 서비스 응용 프로그램을 만들어야 합니다. 우선 [Microsoft Online Services 로그인 도우미](http://go.microsoft.com/fwlink/?LinkID=286152)를 다운로드 및 설치합니다. 그런 다음 [Windows Powershell 용 64비트 Azure Active Directory 모듈](http://go.microsoft.com/fwlink/p/?linkid=236297)을 다운로드 및 설치합니다.

> [AZURE.NOTE]
B2C 테넌트에서 Graph API를 사용하려면 이러한 지침에 따라 Powershell을 사용하여 전용 응용 프로그램을 등록해야 합니다. Azure 포털에 등록한 기존 B2C 응용 프로그램을 다시 사용할 수 없습니다. 이는 조만간(이 문서를 업데이트하는 시점) 제거될 Azure AD B2C 미리 보기의 제한 사항입니다.

Powershell 모듈을 설치한 후에 Powershell을 열고 B2C 테넌트에 연결합니다. `Get-Credential`을 실행한 후에 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다. B2C 테넌트 관리자 계정의 해당 사항을 입력합니다.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

응용 프로그램을 만들기 전에 새 "클라이언트 암호"를 생성해야 합니다. 응용 프로그램이 클라이언트 암호를 사용하여 Azure AD에 인증하고 액세스 토큰을 획득합니다. Powershell에서 유효한 암호를 생성할 수 있습니다.

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

위의 마지막 명령은 새 클라이언트 암호를 인쇄해야 합니다. 안전한 곳에 복사합니다. 암호는 곧 다시 필요합니다. 이제 응용 프로그램을 만들 수 있으며 새 클라이언트 암호를 앱에 대한 자격 증명으로 제공합니다.

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

응용 프로그램을 만들기에 성공하면 위와 같은 응용 프로그램의 일부 속성을 인쇄해야 합니다. `ObjectId` 및 `AppPrincipalId` 둘 다 필요하므로 해당 값을 복사합니다.

B2C 테넌트에 응용 프로그램을 만들었으므로 사용자 CRUD 작업을 수행하는 데 필요한 권한을 응용 프로그램에 할당해야 합니다. 응용 프로그램에 디렉터리 읽기 권한자(사용자 읽기의 경우), 디렉터리 작성자(사용자 만들기 및 업데이트의 경우) 및 사용자 계정 관리자(사용자 삭제의 경우) 등의 세 가지 다른 역할을 할당해야 합니다. 이러한 역할에는 잘 알려진 식별자가 있으므로 아래 명령을 실행하여 `-RoleMemberObjectId` 매개 변수를 위의 `ObjectId`로 대체할 수 있습니다. 모든 디렉터리 역할의 목록을 보려면 `Get-MsolRole`을 실행합니다.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

이제 B2C 테넌트에서 사용자 만들기, 읽기, 업데이트 및 삭제 권한을 가진 응용 프로그램이 있습니다. 이를 사용하는 일부 코드를 작성해 보겠습니다.

## 샘플 코드를 다운로드, 구성 및 빌드합니다.

우선 샘플 코드를 다운로드하고 실행합니다. 그런 다음 배후에 진행되는 상황을 볼 수 있습니다. [샘플 코드를 .zip으로 다운로드](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)하거나 원하는 디렉터리에 복제할 수 있습니다.

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

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

이제 `B2CGraphClient` 솔루션을 마우스 오른쪽 단추로 클릭하고 샘플을 다시 빌드합니다. 성공하면 `B2CGraphClient\bin\Debug`에 있는 실행 가능한 `B2C.exe`를 찾을 수 있어야 합니다.

## Graph API를 사용하는 사용자 CRUD

B2CGraphClient를 사용하려면 cmd Windows 명령 프롬프트를 열고 `Debug` 디렉터리로 cd 명령을 수행합니다. `B2C Help` 명령을 실행합니다.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

각 명령에 대한 간략한 설명을 표시합니다. 이러한 명령 중 하나를 호출하면 B2CGraphClient가 Azure AD Graph API에 요청을 만듭니다.

### 액세스 토큰 가져오기

Graph API에 요청은 인증에 대한 액세스 토큰을 필요로 합니다. B2CGraphClient는 공개 소스 Active Directory 인증 라이브러리(ADAL)를 사용하여 액세스 토큰을 획득 도움을 줍니다. ADAL을 사용하여 토큰을 가져올 필요가 없습니다. 직접 HTTP 요청을 선별하여 토큰을 얻을 수 있습니다. ADAL을 사용하면 간단한 API를 제공하고 액세스 토큰의 캐싱과 같은 중요한 일부 세부 정보를 처리하여 토큰을 쉽게 얻을 수 있습니다.

> [AZURE.NOTE]
	이 코드 샘플은 의도적으로 ADAL v2, 즉 ADAL의 일반적으로 사용 가능한 버전을 사용합니다. Azure AD B2C와 작동하도록 설계된 미리 보기 버전인 ADAL v4을 사용하지 않습니다. Azure AD B2C 미리 보기의 경우 Graph API와 통신하는 데 ADAL v2를 사용해야 합니다. 시간이 지남에 따라 ADAL v4로 Graph API 액세스를 사용하도록 설정하므로 전체 Azure AD B2C 솔루션에서 ADAL의 다른 두 버전을 사용할 필요가 없습니다.

B2CGraphClient가 실행되면 `B2CGraphClient` 클래스의 인스턴스를 만듭니다. 이 클래스의 생성자는 ADAL의 인증 스캐폴딩을 설정합니다.

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
	// provided to Azure AD in order to receive an access_token using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

`B2C Get-User` 명령을 예로 사용해 보겠습니다. `Get-User`이 추가 입력 없이 호출되면 CLI가 `B2CGraphClient.GetAllUsers(...)` 메서드를 호출합니다. 이 메서드는 `B2CGraphClient.SendGraphGetRequest(...)`를 호출하며 이는 Graph API에 HTTP GET 요청을 제출합니다. 가져오기 요청을 보내기 전에 먼저 ADAL을 사용하여 액세스 토큰을 가져옵니다.

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

여기서 볼 수 있듯이 ADAL의 `AuthenticationContext.AcquireToken(...)` 메서드를 호출하여 Graph API에 대한 액세스 토큰을 가져올 수 있습니다. ADAL은 응용 프로그램의 ID를 나타내는 access\_token을 반환합니다.

### 사용자 읽기

Graph API에서 사용자의 목록을 가져오거나 특정 사용자를 가져오려는 경우 `/users` 끝점에 HTTP GET 요청을 보낼 수 있습니다. 테넌트의 모든 사용자에 대한 요청은 다음과 같습니다.

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

작업에서 이 요청을 보려면 다음을 실행합니다.

 ```
 > B2C Get-User
 ```

여기에 유의해야 할 두 가지 중요한 사항이 있습니다.

- ADAL을 통해 획득된 액세스 토큰은 `Bearer` 체계를 사용하여 `Authorization` 헤더에 추가됩니다.
- B2C 테넌트의 경우 쿼리 매개 변수 `api-version=beta`를 사용해야 합니다.


> [AZURE.NOTE]
	Azure AD Graph API 베타 버전은 미리 보기 기능을 제공합니다. 베타 버전에 대한 자세한 내용은 [이 Graph API 팀 블로그 게시물](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx)을 참조하세요.

이러한 세부 사항은 모두 `B2CGraphClient.SendGraphGetRequest(...)` 메서드에서 처리됩니다.

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user managment, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### 소비자 사용자 계정 만들기

B2C 테넌트에 사용자 계정을 만들 경우 `/users` 끝점에 HTTP POST 요청을 보낼 수 있습니다.

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// These properties are all required for creating consumer users.

	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for diplaying to the end-user
	"mailNickname": "joec",						// a mail alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

위의 요청에 포함된 각각의 속성은 소비자 사용자를 만들기 위해 필요합니다. 일러스트레이션에 `//` 주석이 포함되었습니다. 실제 요청에 포함하지 마세요.

작업에서 이 요청을 보려면 다음 명령 중 하나를 실행합니다.

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 명령은 `.json` 파일을 입력 매개 변수로 사용하며 이는 사용자 개체의 JSON 표현을 포함합니다. 샘플 코드에 포함된 두 예제 `.json` 파일인 `usertemplate-email.json` 및 `usertemplate-username.json`은 사용자의 요구에 맞게 수정할 수 있습니다. 위의 필수 필드 외에도 사용할 수 있는 이러한 파일에 포함된 선택적 필드는 여러 가지가 있습니다. 이러한 다른 필드에 대한 세부 정보는 [Azure AD Graph API 엔터티 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity)에서 찾을 수 있습니다.

이 POST 요청이 `B2CGraphClient.SendGraphPostRequest(...)`에서 어떻게 작성되는지 다음에서 확인할 수 있습니다.

- 액세스 토큰을 요청의 `Authorization` 헤더에 연결합니다.
- `api-version=beta`를 설정합니다.
- 요청 본문에 JSON 사용자 개체를 포함합니다.

### 소비자 사용자 계정 업데이트

사용자 개체를 업데이트하는 작업은 사용자 개체를 만드는 것과 매우 유사하지만 HTTP PATCH 동사를 사용합니다.

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request only updates the user's displayName
}
```

JSON 파일을 새 데이터로 업데이트하여 사용자를 업데이트하고 B2CGraphClient를 사용하여 다음 명령 중 하나를 실행해 볼 수 있습니다.

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

이 요청을 보내는 방법에 대한 세부 정보는 `B2CGraphClient.SendGraphPatchRequest(...)` 메서드를 검사합니다.

### 사용자 삭제

사용자를 삭제하는 작업은 간단합니다. 단순히 HTTP 삭제 동사를 사용하고 올바른 개체 ID로 URL를 구성합니다.

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

예제를 보려면 아래 명령을 시도하고 콘솔에 출력된 결과 삭제 요청을 봅니다.

```
> B2C Delete-User <object-id-of-user>
```

이 요청을 보내는 방법에 대한 세부 정보는 `B2CGraphClient.SendGraphDeleteRequest(...)` 메서드를 검사합니다.

사용자 관리 외에도 Azure AD Graph API를 사용하여 수행할 수 있는 다른 많은 작업이 있습니다. [Azure AD Graph API 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)는 샘플 요청과 함께 각 동작의 세부 정보를 제공합니다.


## 사용자 지정 특성 사용

거의 모든 소비자 응용 프로그램은 특정 유형의 사용자 지정 사용자 프로필 정보를 저장해야 합니다. 이 작업을 수행하는 한 가지 방법은 B2C 테넌트에 사용자 지정 특성을 정의하는 것입니다. 이 방법을 통해 사용자 개체에서 다른 속성과 마찬가지로 해당 특성을 처리할 수 있습니다. 특성을 업데이트 및 삭제하고 특성으로 쿼리하며 특성을 로그인 토큰에서 클레임으로 보낼 수 있습니다.

B2C 테넌트에서 사용자 지정 특성을 정의하려면 [B2C 미리 보기 사용자 지정 특성 참조](active-directory-b2c-reference-custom-attr.md)를 참조하세요.

B2CGraphClient를 사용하여 B2C 테넌트에 정의된 사용자 지정 특성을 볼 수 있습니다.

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

`extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`와 같은 전체 이름을 사용자 개체의 속성으로 사용할 수 있습니다. 단순히 `.json` 파일을 새 속성, 속성에 대한 값으로 업데이트하고 실행합니다.

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

지금까지 전반적인 내용을 알아보았습니다. 이제 B2CGraphClient를 사용하여 B2C 테넌트 사용자를 프로그래밍 방식으로 관리할 수 있는 서비스 응용 프로그램이 있습니다. 자체 응용 프로그램 ID를 사용하여 Azure AD Graph API에 인증하고 client\_secret를 사용하여 토큰을 획득합니다. 사용자 고유의 응용 프로그램에 이 기능을 통합하면 B2C 앱에 대한 몇 가지 주요 사항을 기억해야 합니다.

- 테넌트에서 응용 프로그램에 적절한 권한을 부여해야 합니다.
- 지금은 ADAL v2를 사용하여 액세스 토큰을 가져와야 합니다.(또는 라이브러리 없이 프로토콜 메시지를 직접 보낼 수 있음)
- Graph API를 호출할 때 [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx)를 사용합니다.
- 소비자 사용자를 만들고 업데이트하는 경우 위에서 설명한 몇 가지 필수 속성이 있습니다.

> [AZURE.IMPORTANT]
B2C 앱에서 Azure AD Graph API를 사용할 때 Azure AD B2C의 기반이 되는 디렉터리 서비스의 복제 특성을 설명해야 합니다(자세한 내용은 [이](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx) 문서 참조). 소비자가 **등록** 정책을 사용하여 B2C 앱에 등록한 후, 즉시 앱에서 Azure AD Graph API를 사용하여 사용자 개체를 읽으려고 하면 해당 개체를 사용할 수 없습니다. 복제 프로세스가 완료될 때까지 몇 초 동안 기다려야 합니다. 일반 공급 시에 Azure AD Graph API 및 디렉터리 서비스에서 제공하는 “읽기-쓰기 정합성 보장"에 대한 보다 구체적인 지침을 게시할 예정입니다.

B2C 테넌트에서 Graph API를 사용하여 수행하려는 작업에 대한 질문이나 요청이 있는 경우 언제든지 알려주세요. 문서에 의견을 남기거나 코드 샘플 GitHub 리포지토리에 문제를 제출하세요.

<!---HONumber=AcomDC_0128_2016-->