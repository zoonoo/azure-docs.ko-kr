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
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178877"
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


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>코드 샘플: 프로그래밍 방식으로 사용자 계정을 관리 하는 방법

이 코드 샘플은 [MICROSOFT GRAPH SDK](/graph/sdks/sdks-overview) 를 사용 하 여 Microsoft Graph API와 상호 작용 하는 .net Core 콘솔 응용 프로그램입니다. 이 코드에서는 API를 호출 하 여 Azure AD B2C 테 넌 트에서 사용자를 프로그래밍 방식으로 관리 하는 방법을 보여 줍니다.
[샘플 아카이브](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (* .zip)를 다운로드 하거나 GitHub에서 [리포지토리를 찾아보거나](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) 리포지토리를 복제할 수 있습니다.

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

코드 샘플을 가져온 후 사용자 환경에 맞게 구성한 다음 프로젝트를 빌드합니다.

1. [Visual Studio](https://visualstudio.microsoft.com) 또는 [Visual Studio Code](https://code.visualstudio.com)에서 프로젝트를 엽니다.
1. `src/appsettings.json` 엽니다.
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
