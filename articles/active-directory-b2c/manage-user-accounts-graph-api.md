---
title: 마이크로소프트 그래프 API로 사용자 관리
titleSuffix: Azure AD B2C
description: Microsoft 그래프 API를 호출하고 응용 프로그램 ID를 사용하여 프로세스를 자동화하여 Azure AD B2C 테넌트의 사용자를 관리하는 방법
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 42596ba5470c6062efba4fd1050c1c9745b76e80
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637339"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>마이크로 소프트 그래프와 Azure AD B2C 사용자 계정 관리

Microsoft 그래프를 사용하면 Microsoft 그래프 API에서 메서드를 만들고, 읽고, 업데이트하고, 삭제할 수 있으므로 Azure AD B2C 디렉터리에서 사용자 계정을 관리할 수 있습니다. 기존 사용자 저장소를 Azure AD B2C 테넌트로 마이그레이션하고 Microsoft 그래프 API를 호출하여 다른 사용자 계정 관리 작업을 수행할 수 있습니다.

다음 섹션에서는 Microsoft 그래프 API를 사용 하 여 Azure AD B2C 사용자 관리의 주요 측면을 표시 합니다. 여기에 표시된 Microsoft 그래프 API 작업, 형식 및 속성은 Microsoft 그래프 API 참조 설명서에 표시되는 부분 집합입니다.

## <a name="register-a-management-application"></a>관리 응용 프로그램 등록

작성하는 사용자 관리 응용 프로그램 이나 스크립트 Azure AD B2C 테넌트의 리소스와 상호 작용할 수 있습니다.

이 방법 문서의 단계를 수행하여 관리 응용 프로그램에서 사용할 수 있는 응용 프로그램 등록을 만듭니다.

[마이크로소프트 그래프로 Azure AD B2C 관리](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>사용자 관리 마이크로 소프트 그래프 작업

다음 사용자 관리 작업은 [Microsoft 그래프 API에서](https://docs.microsoft.com/graph/api/resources/user)사용할 수 있습니다.

- [사용자 목록 받기](https://docs.microsoft.com/graph/api/user-list)
- [사용자 만들기](https://docs.microsoft.com/graph/api/user-post-users)
- [사용자 받기](https://docs.microsoft.com/graph/api/user-get)
- [사용자 업데이트](https://docs.microsoft.com/graph/api/user-update)
- [사용자 삭제](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>사용자 속성

### <a name="display-name-property"></a>이름 속성 표시

은 `displayName` 사용자에 대한 Azure 포털 사용자 관리에 표시할 이름이며 액세스 토큰에 Azure AD B2C가 응용 프로그램에 반환됩니다. 이 속성은 필수입니다.

### <a name="identities-property"></a>ID 속성

소비자, 파트너 또는 시민이 될 수 있는 고객 계정은 다음과 같은 ID 유형과 연결할 수 있습니다.

- **로컬** ID - 사용자 이름과 암호는 Azure AD B2C 디렉터리에 로컬로 저장됩니다. 이러한 ID를 "로컬 계정"이라고 하는 경우가 많습니다.
- **페더레이션** ID - *소셜* 또는 *엔터프라이즈* 계정이라고도 하는 사용자의 ID는 Facebook, Microsoft, ADFS 또는 Salesforce와 같은 페더레이션된 ID 공급자에 의해 관리됩니다.

고객 계정이 있는 사용자는 여러 ID로 로그인할 수 있습니다. 예를 들어 사용자 이름, 이메일, 직원 ID, 정부 ID 등을 예로 들 수 있습니다. 단일 계정은 동일한 암호를 가진 로컬 및 소셜 ID를 모두 가질 수 있습니다.

Microsoft 그래프 API에서 로컬 ID와 페더레이션된 ID는 모두 `identities` 개체 [Identity][graph-objectIdentity]형식의 사용자 특성에 저장됩니다. 컬렉션은 `identities` 사용자 계정에 로그인하는 데 사용되는 ID 집합을 나타냅니다. 이 컬렉션을 사용하면 사용자가 연결된 ID를 사용하여 사용자 계정에 로그인할 수 있습니다.

| 속성   | Type |Description|
|:---------------|:--------|:----------|
|사인 인 타입|문자열| 디렉터리에서 사용자 로그인 유형을 지정합니다. 로컬 계정의 `emailAddress` `emailAddress1`경우: `userName`, " `emailAddress2` `emailAddress3`또는 당신이 좋아하는 다른 유형. 소셜 계정을 로 `federated`설정해야 합니다.|
|발급자|문자열|ID 발급자를 지정합니다. 로컬 계정(signInType이 **signInType** 없는 `federated`경우)의 경우 이 속성은 로컬 B2C 테넌트 기본 도메인 `contoso.onmicrosoft.com`이름입니다. 소셜 **ID(signInType이** `federated`있는 경우)의 경우 값은 발급자의 이름입니다.`facebook.com`|
|발급자할당된 Id|문자열|발급자가 사용자에게 할당한 고유 식별자를 지정합니다. **발급자와** **발급자의** 조합은 테넌트 내에서 고유해야 합니다. 로컬 계정의 경우 **signInType이** `emailAddress` `userName`설정되거나 에 의해 설정되면 사용자의 로그인 이름을 나타냅니다.<br>**signInType이** 다음으로 설정된 경우: <ul><li>`emailAddress`(또는 같은 `emailAddress` `emailAddress1`것으로 시작) **발급자AssignedId는** 유효한 이메일 주소여야 합니다.</li><li>`userName`(또는 다른 값), **발급자AssignedId는** [이메일 주소의](https://tools.ietf.org/html/rfc3696#section-3) 유효한 로컬 부분이어야 합니다.</li><li>`federated`에서 **issuerAssignedId는** 페더레이션된 계정 고유 식별자를 나타냅니다.</li></ul>|

로그인 이름이 있는 로컬 계정 ID, 로그인으로 전자 메일 주소 및 소셜 ID가 있는 다음 **Identity** 속성입니다. 

 ```JSON
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

페더레이션된 ID의 경우 ID 공급자에 따라 **발급자AssignedId는** 응용 프로그램 또는 개발 계정당 지정된 사용자에 대한 고유 값입니다. Azure AD B2C 정책을 소셜 공급자 또는 동일한 개발 계정 내의 다른 응용 프로그램에서 이전에 할당한 것과 동일한 응용 프로그램 ID로 구성합니다.

### <a name="password-profile-property"></a>암호 프로필 속성

로컬 ID의 경우 **passwordProfile** 속성이 필요하며 사용자의 암호가 포함되어 있습니다. 속성은 `forceChangePasswordNextSignIn` `false`로 설정해야 합니다.

페더레이션된(소셜) ID의 경우 **passwordProfile** 속성이 필요하지 않습니다.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>암호 정책 속성

로컬 계정의 Azure AD B2C 암호 정책은 Azure Active Directory [강력한 암호 강도](../active-directory/authentication/concept-sspr-policy.md) 정책을 기반으로 합니다. Azure AD B2C 등록 또는 로그인 및 암호 재설정 정책에는 이러한 강력한 암호 강도가 필요하며 암호가 만료되지 않습니다.

사용자 마이그레이션 시나리오에서 마이그레이션하려는 계정이 Azure AD B2C에서 적용하는 [강력한 암호 강도보다 암호](../active-directory/authentication/concept-sspr-policy.md) 강도가 약한 경우 강력한 암호 요구 사항을 사용하지 않도록 설정할 수 있습니다. 기본 암호 정책을 변경하려면 `passwordPolicies` 속성을 `DisableStrongPassword`로 설정합니다. 예를 들어 다음과 같이 사용자 만들기 요청을 수정할 수 있습니다.

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>확장 속성

모든 고객 대면 응용 프로그램에는 수집할 정보에 대한 고유한 요구 사항이 있습니다. Azure AD B2C 테넌트에는 지정된 이름, 성, 도시 및 우편 번호와 같은 속성에 저장된 기본 제공 정보 집합이 함께 제공됩니다. Azure AD B2C를 사용하면 각 고객 계정에 저장된 속성 집합을 확장할 수 있습니다. 사용자 지정 특성 정의에 대한 자세한 내용은 [사용자 지정 특성(사용자 흐름)](user-flow-custom-attributes.md) 및 [사용자 지정 특성(사용자 지정 정책)을](custom-policy-custom-attributes.md)참조하십시오.

Microsoft 그래프 API는 확장 특성을 가진 사용자 만들기 및 업데이트를 지원합니다. Graph API의 확장 특성은 `extension_ApplicationObjectID_attributename` 규칙을 사용하여 명명됩니다. 다음은 그 예입니다.

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>코드 샘플

이 코드 샘플은 [Microsoft 그래프 SDK를](https://docs.microsoft.com/graph/sdks/sdks-overview) 사용하여 Microsoft 그래프 API와 상호 작용하는 .NET Core 콘솔 응용 프로그램입니다. 해당 코드는 Azure AD B2C 테넌트의 사용자를 프로그래밍 방식으로 관리하기 위해 API를 호출하는 방법을 보여 줍니다.
샘플 아카이브(*.zip)를 [다운로드하거나](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) GitHub에서 [리포지토리를 탐색하거나](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) 리포지토리를 복제할 수 있습니다.

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

코드 샘플을 얻은 후 사용자 환경에 맞게 구성한 다음 프로젝트를 빌드합니다.

1. [비주얼 스튜디오](https://visualstudio.microsoft.com) 또는 비주얼 스튜디오 코드에서 프로젝트를 [엽니다.](https://code.visualstudio.com)
1. `src/appsettings.json`를 엽니다.
1. 섹션에서 `appSettings` 테넌트의 `your-b2c-tenant` `Application (client) ID` 이름으로 바꾸고 관리 응용 `Client secret` 프로그램 등록에 대한 값으로 바꿉니다(이 문서의 [관리 응용 프로그램 등록](#register-a-management-application) 섹션 참조).
1. 리포지토리의 로컬 복제내에서 콘솔 창을 열고 `src` 디렉터리로 전환한 다음 프로젝트를 빌드합니다.
    ```console
    cd src
    dotnet build
    ```
1. `dotnet` 명령을 사용하여 애플리케이션을 실행합니다.

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

응용 프로그램은 실행할 수 있는 명령 목록을 표시합니다. 예를 들어 모든 사용자를 가져오고, 단일 사용자를 가져오고, 사용자를 삭제하고, 사용자의 암호를 업데이트하고, 대량 가져오기를 수행합니다.

### <a name="code-discussion"></a>코드 토론

샘플 코드는 Microsoft Graph에 액세스하는 고품질의 효율적이고 복원력이 뛰어난 응용 프로그램 빌드를 간소화하도록 설계된 Microsoft 그래프 [SDK를](https://docs.microsoft.com/graph/sdks/sdks-overview)사용합니다.

Microsoft 그래프 API에 대한 모든 요청은 인증을 위한 액세스 토큰이 필요합니다. 이 솔루션은 Microsoft 그래프 SDK와 함께 사용하기 위해 MSAL(Microsoft 인증 라이브러리)의 인증 시나리오 기반 래퍼를 제공하는 [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet 패키지를 사용합니다.

Program.cs `RunAsync` 파일의 _Program.cs_ 메서드:

1. _appsettings.json_ 파일에서 응용 프로그램 설정 읽기
1. [OAuth 2.0 클라이언트 자격 증명 부여](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) 흐름을 사용하여 인증 공급자를 초기화합니다. 클라이언트 자격 증명 부여 흐름을 사용하면 앱에서 Microsoft Graph API를 호출하는 액세스 토큰을 얻을 수 있습니다.
1. 인증 공급자를 통해 Microsoft 그래프 서비스 클라이언트를 설정합니다.

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

그런 다음 초기화된 *GraphServiceClient는* 사용자 관리 작업을 수행하기 위해 _UserService.cs_ 사용됩니다. 예를 들어 테넌트의 사용자 계정 목록을 가져오는 경우를 예로 들 수 있습니다.

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

[Microsoft 그래프 SDK를 사용하여 API 호출을 만들면](https://docs.microsoft.com/graph/sdks/create-requests) Microsoft Graph에서 정보를 `$select` 읽고 쓰고, 반환된 속성을 제어하고, `$filter` 사용자 `$orderBy` 지정 쿼리 매개 변수를 제공하고, 및 쿼리 매개 변수를 사용하는 방법에 대한 정보가 포함됩니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2C 리소스에 대해 지원되는 Microsoft 그래프 API 작업의 전체 인덱스는 [Azure AD B2C에 사용할 수 있는 Microsoft 그래프 작업을](microsoft-graph-operations.md)참조하십시오.

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
