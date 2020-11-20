---
title: Microsoft Graph API를 사용 하 여 사용자 관리
titleSuffix: Azure AD B2C
description: Microsoft Graph API를 호출 하 고 프로세스를 자동화 하는 응용 프로그램 id를 사용 하 여 Azure AD B2C 테 넌 트의 사용자를 관리 하는 방법입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a5fcbf80850fd9de77e6f9a431afea6d48cb14d1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949414"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Microsoft Graph를 사용 하 여 Azure AD B2C 사용자 계정 관리

Microsoft Graph를 사용 하면 Microsoft Graph API에서 만들기, 읽기, 업데이트 및 삭제 메서드를 제공 하 여 Azure AD B2C 디렉터리에서 사용자 계정을 관리할 수 있습니다. Azure AD B2C 테 넌 트에 기존 사용자 저장소를 마이그레이션하고 Microsoft Graph API를 호출 하 여 다른 사용자 계정 관리 작업을 수행할 수 있습니다.

다음 섹션에서는 Microsoft Graph API를 사용 하 여 사용자 관리 Azure AD B2C의 주요 측면을 제공 합니다. 여기에 제공 된 Microsoft Graph API 작업, 형식 및 속성은 Microsoft Graph API 참조 설명서에 표시 되는의 하위 집합입니다.

## <a name="register-a-management-application"></a>관리 응용 프로그램 등록

작성 하는 사용자 관리 응용 프로그램 또는 스크립트가 Azure AD B2C 테 넌 트의 리소스와 상호 작용할 수 있도록 하기 전에이 작업을 수행할 수 있는 권한을 부여 하는 응용 프로그램 등록이 필요 합니다.

이 방법 문서의 단계에 따라 관리 응용 프로그램에서 사용할 수 있는 응용 프로그램 등록을 만듭니다.

[Microsoft Graph를 사용 하 여 Azure AD B2C 관리](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>사용자 관리 Microsoft Graph 작업

[MICROSOFT GRAPH API](/graph/api/resources/user)에서 사용할 수 있는 사용자 관리 작업은 다음과 같습니다.

- [사용자 목록 가져오기](/graph/api/user-list)
- [사용자 만들기](/graph/api/user-post-users)
- [사용자 가져오기](/graph/api/user-get)
- [사용자 업데이트](/graph/api/user-update)
- [사용자 삭제](/graph/api/user-delete)

## <a name="user-properties"></a>사용자 속성

### <a name="display-name-property"></a>표시 이름 속성

는 `displayName` 사용자에 대 한 Azure Portal 사용자 관리에 표시 되는 이름이 며, 액세스 토큰 Azure AD B2C 응용 프로그램으로 돌아갑니다. 이 속성은 필수입니다.

### <a name="identities-property"></a>Id 속성

소비자, 파트너 또는 시민 일 수 있는 고객 계정은 다음과 같은 id 형식에 연결할 수 있습니다.

- **로컬** id-사용자 이름 및 암호는 Azure AD B2C 디렉터리에 로컬로 저장 됩니다. 이러한 ID는 "로컬 계정"으로 참조하는 경우가 많습니다.
- **페더레이션된** id- *소셜* 계정 또는 *엔터프라이즈* 계정이 라고도 하며, 사용자의 ID는 Facebook, Microsoft, ADFS 또는 Salesforce와 같은 페더레이션된 id 공급자를 통해 관리 됩니다.

고객 계정을 가진 사용자는 여러 id를 사용 하 여 로그인 할 수 있습니다. 예: 사용자 이름, 전자 메일, 직원 ID, 정부 ID 및 기타. 단일 계정에는 동일한 암호를 사용 하 여 로컬 및 소셜의 id가 여러 개 있을 수 있습니다.

Microsoft Graph API에서 로컬 및 페더레이션 id는 모두 `identities` [objectIdentity][graph-objectIdentity]형식의 user 특성에 저장 됩니다. `identities`컬렉션은 사용자 계정에 로그인 하는 데 사용 되는 id 집합을 나타냅니다. 이 컬렉션을 통해 사용자는 연결 된 id를 사용 하 여 사용자 계정에 로그인 할 수 있습니다.

| 속성   | 유형 |Description|
|:---------------|:--------|:----------|
|signInType|문자열| 디렉터리에서 사용자 로그인 유형을 지정 합니다. 로컬 계정:,,,,  `emailAddress` `emailAddress1` 또는 원하는 `emailAddress2` `emailAddress3`  `userName` 다른 모든 형식 소셜 계정은로 설정 되어야 합니다  `federated` .|
|발급자|문자열|Id의 발급자를 지정 합니다. 로컬 계정 (여기서 **signInType** 가이 아닌 경우)의 경우 `federated` 이 속성은 로컬 B2C 테 넌 트 기본 도메인 이름입니다 (예:) `contoso.onmicrosoft.com` . 소셜 id (여기서 **signInType** 는)의 경우  `federated` 값은 발급자의 이름입니다 (예:). `facebook.com`|
|issuerAssignedId|문자열|발급자에 의해 사용자에 게 할당 된 고유 식별자를 지정 합니다. **발급자** 와 **issuerAssignedId** 의 조합은 테 넌 트 내에서 고유 해야 합니다. 로컬 계정의 경우 **signInType** 가 또는로 설정 되 `emailAddress` 면 `userName` 사용자의 로그인 이름을 나타냅니다.<br>**SignInType** 가로 설정 된 경우: <ul><li>`emailAddress` (또는 like로 `emailAddress` 시작 `emailAddress1` ) **issuerAssignedId** 는 유효한 전자 메일 주소 여야 합니다.</li><li>`userName`(또는 다른 값) **issuerAssignedId** 는 [전자 메일 주소의 유효한 로컬 부분](https://tools.ietf.org/html/rfc3696#section-3) 이어야 합니다.</li><li>`federated`, **issuerAssignedId** 는 페더레이션된 계정 고유 식별자를 나타냅니다.</li></ul>|

로그인 이름이 있는 로컬 계정 id, 로그인으로 전자 메일 주소, 소셜 id를 사용 하는 다음 **id** 속성 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

페더레이션 id의 경우 id 공급자에 따라 **issuerAssignedId** 는 응용 프로그램 또는 개발 계정 별로 지정 된 사용자에 대 한 고유한 값입니다. 이전에 소셜 공급자나 동일한 개발 계정 내의 다른 응용 프로그램에서 할당 한 것과 동일한 응용 프로그램 ID를 사용 하 여 Azure AD B2C 정책을 구성 합니다.

### <a name="password-profile-property"></a>암호 프로필 속성

로컬 id의 경우 **Passwordprofile** 속성이 필요 하며 사용자의 암호를 포함 합니다. `forceChangePasswordNextSignIn`속성은로 설정 해야 합니다 `false` .

페더레이션된 (소셜) id의 경우 **Passwordprofile** 속성이 필요 하지 않습니다.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>암호 정책 속성

로컬 계정에 대 한 Azure AD B2C 암호 정책은 Azure Active Directory [강력한 암호 수준](../active-directory/authentication/concept-sspr-policy.md) 정책을 기반으로 합니다. Azure AD B2C 등록 또는 로그인 및 암호 재설정 정책에는 강력한 암호 강도가 필요 하며 암호는 만료 되지 않습니다.

사용자 마이그레이션 시나리오에서 마이그레이션하려는 계정이 Azure AD B2C에 의해 적용 되는 [강력한 암호](../active-directory/authentication/concept-sspr-policy.md) 강도 보다 약한 암호 강도의 경우 강력한 암호 요구 사항을 사용 하지 않도록 설정할 수 있습니다. 기본 암호 정책을 변경하려면 `passwordPolicies` 속성을 `DisableStrongPassword`로 설정합니다. 예를 들어 다음과 같이 사용자 만들기 요청을 수정할 수 있습니다.

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>확장 속성

모든 고객 지향 응용 프로그램에는 수집할 정보에 대 한 고유한 요구 사항이 있습니다. Azure AD B2C 테 넌 트에는 지정 된 이름, 성, 도시 및 우편 번호와 같은 속성에 저장 되는 기본 제공 정보 집합이 제공 됩니다. Azure AD B2C를 사용 하 여 각 고객 계정에 저장 된 속성 집합을 확장할 수 있습니다. 사용자 지정 특성을 정의 하는 방법에 대 한 자세한 내용은 사용자 지정 [특성 (사용자 흐름)](user-flow-custom-attributes.md) 및 [사용자 지정 특성 (사용자 지정 정책)](custom-policy-custom-attributes.md)을 참조 하세요.

Microsoft Graph API는 확장 특성이 있는 사용자를 만들고 업데이트하는 것을 지원합니다. Graph API의 확장 특성은 규칙을 사용 하 여 이름이 지정 됩니다 `extension_ApplicationClientID_attributename` . 여기서는 응용 프로그램의 응용 프로그램 `ApplicationClientID` **(클라이언트) ID** 입니다 `b2c-extensions-app` ( **App registrations**  >  Azure Portal의 **모든 응용** 프로그램 앱 등록에 있음). 확장 특성 이름에 표시 되는 **응용 프로그램 (클라이언트) ID** 에는 하이픈이 포함 되지 않습니다. 예를 들면 다음과 같습니다.

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>코드 샘플: 프로그래밍 방식으로 사용자 계정을 관리 하는 방법

이 코드 샘플은 [MICROSOFT GRAPH SDK](/graph/sdks/sdks-overview) 를 사용 하 여 Microsoft Graph API와 상호 작용 하는 .net Core 콘솔 응용 프로그램입니다. 이 코드에서는 API를 호출 하 여 Azure AD B2C 테 넌 트에서 사용자를 프로그래밍 방식으로 관리 하는 방법을 보여 줍니다.
[샘플 아카이브](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (* .zip)를 다운로드 하거나 GitHub에서 [리포지토리를 찾아보거나](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) 리포지토리를 복제할 수 있습니다.

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

코드 샘플을 가져온 후 사용자 환경에 맞게 구성한 다음 프로젝트를 빌드합니다.

1. [Visual Studio](https://visualstudio.microsoft.com) 또는 [Visual Studio Code](https://code.visualstudio.com)에서 프로젝트를 엽니다.
1. `src/appsettings.json`를 엽니다.
1. 섹션에서 `appSettings` 을 `your-b2c-tenant` 테 넌 트의 이름으로 바꾸고,을 `Application (client) ID` `Client secret` 관리 응용 프로그램 등록 값으로 바꿉니다 (이 문서의 [관리 응용 프로그램 등록](#register-a-management-application) 섹션 참조).
1. 리포지토리의 로컬 클론 내에서 콘솔 창을 열고 `src` 디렉터리로 전환한 다음 프로젝트를 빌드합니다.
    ```console
    cd src
    dotnet build
    ```
1. `dotnet` 명령을 사용하여 애플리케이션을 실행합니다.

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

응용 프로그램은 실행할 수 있는 명령 목록을 표시 합니다. 예를 들어 모든 사용자를 가져오고, 단일 사용자를 가져오고, 사용자를 삭제 하 고, 사용자 암호를 업데이트 하 고, 대량 가져오기를 합니다.

### <a name="code-discussion"></a>코드 토론

이 샘플 코드는 Microsoft Graph에 액세스 하는 고품질의 효율적이 고 복원 력 있는 응용 프로그램을 간편 하 게 빌드할 수 있도록 설계 된 [MICROSOFT GRAPH SDK](/graph/sdks/sdks-overview)를 사용 합니다.

Microsoft Graph API에 대 한 모든 요청에는 인증을 위한 액세스 토큰이 필요 합니다. 이 솔루션은 Microsoft Graph SDK에 사용할 MSAL (Microsoft 인증 라이브러리)의 인증 시나리오 기반 래퍼를 제공 하는 [Microsoft Graph. Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet 패키지를 사용 합니다.

`RunAsync` _Program.cs_ 파일의 메서드는 다음과 같습니다.

1. 파일 _의appsettings.js_ 에서 응용 프로그램 설정을 읽습니다.
1. [OAuth 2.0 클라이언트 자격 증명 부여](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) 흐름을 사용 하 여 인증 공급자를 초기화 합니다. 클라이언트 자격 증명 부여 흐름을 사용 하 여 앱은 Microsoft Graph API를 호출 하는 액세스 토큰을 가져올 수 있습니다.
1. 인증 공급자를 사용 하 여 Microsoft Graph 서비스 클라이언트를 설정 합니다.

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

그러면 초기화 된 *GraphServiceClient* 가 _UserService.cs_ 에서 사용자 관리 작업을 수행 하는 데 사용 됩니다. 예를 들어 테 넌 트의 사용자 계정 목록을 가져오는 중입니다.

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Microsoft Graph sdk를 사용 하 여 API 호출을 수행](/graph/sdks/create-requests) 하면 Microsoft Graph에서 정보를 읽고 쓰고,를 사용 하 여 `$select` 반환 된 속성을 제어 하 고, 사용자 지정 쿼리 매개 변수를 제공 하 고, `$filter` `$orderBy` 쿼리 매개 변수를 사용 하는 방법에 대 한 정보가

## <a name="next-steps"></a>다음 단계

Azure AD B2C 리소스에 대해 지원 되는 Microsoft Graph API 작업의 전체 인덱스는 [Azure AD B2C에 사용할 수 있는 Microsoft Graph 작업](microsoft-graph-operations.md)을 참조 하세요.

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)