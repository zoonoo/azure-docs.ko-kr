---
title: Microsoft Graph를 사용 하 여 리소스 관리
titleSuffix: Azure AD B2C
description: Microsoft Graph API를 호출 하 고 프로세스를 자동화 하는 응용 프로그램 id를 사용 하 여 Azure AD B2C 테 넌 트의 리소스를 관리 하는 방법입니다.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 96772020e70aeb32fa1a8ae18bf3818396887877
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805231"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Microsoft Graph를 사용 하 여 Azure AD B2C 관리

Microsoft Graph를 사용 하 여 Azure AD B2C 디렉터리에서 리소스를 관리할 수 있습니다. 다음 Microsoft Graph API 작업은 사용자, id 공급자, 사용자 흐름, 사용자 지정 정책 및 정책 키를 비롯 한 Azure AD B2C 리소스 관리에 대해 지원 됩니다. 다음 섹션의 각 링크는 해당 작업에 대 한 Microsoft Graph API 참조 내의 해당 페이지를 대상으로 합니다. 

## <a name="prerequisites"></a>필수 조건

MS Graph API를 사용 하 고 Azure AD B2C 테 넌 트의 리소스와 상호 작용 하려면 권한을 부여 하는 응용 프로그램 등록이 필요 합니다. 관리 응용 프로그램에서 사용할 수 있는 응용 프로그램 등록을 만들려면 [Microsoft Graph를 사용 하 여 Azure AD B2C 관리](microsoft-graph-get-started.md) 문서의 단계를 수행 합니다. 

## <a name="user-management"></a>사용자 관리

- [사용자 나열](/graph/api/user-list)
- [소비자 사용자 만들기](/graph/api/user-post-users)
- [사용자 가져오기](/graph/api/user-get)
- [사용자 업데이트](/graph/api/user-update)
- [사용자 삭제](/graph/api/user-delete)

## <a name="user-phone-number-management"></a>사용자 전화 번호 관리

- [추가](/graph/api/authentication-post-phonemethods)
- [가져오기](/graph/api/b2cauthenticationmethodspolicy-get)
- [업데이트](/graph/api/b2cauthenticationmethodspolicy-update)
- [Delete](/graph/api/phoneauthenticationmethod-delete)

사용자의 로그인 전화 번호를 관리 하는 방법에 대 한 자세한 내용은 [B2C 인증 방법](/graph/api/resources/b2cauthenticationmethodspolicy)을 참조 하세요.

## <a name="identity-providers-user-flow"></a>Id 공급자 (사용자 흐름)

Azure AD B2C 테 넌 트에서 사용자 흐름에서 사용할 수 있는 id 공급자를 관리 합니다.

- [Azure AD B2C 테 넌 트에 등록 된 id 공급자 나열](/graph/api/identityprovider-list)
- [Id 공급자 만들기](/graph/api/identityprovider-post-identityproviders)
- [Id 공급자 가져오기](/graph/api/identityprovider-get)
- [Id 공급자 업데이트](/graph/api/identityprovider-update)
- [Id 공급자 삭제](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>사용자 흐름

등록, 로그인, 결합 된 등록 및 로그인, 암호 재설정 및 프로필 업데이트에 대해 미리 작성 된 정책을 구성 합니다.

- [사용자 흐름 나열](/graph/api/identitycontainer-list-b2cuserflows)
- [사용자 흐름 만들기](/graph/api/identitycontainer-post-b2cuserflows)
- [사용자 흐름 가져오기](/graph/api/b2cidentityuserflow-get)
- [사용자 흐름 삭제](/graph/api/b2cidentityuserflow-delete)

## <a name="custom-policies"></a>사용자 지정 정책

다음 작업을 사용 하 여 [사용자 지정 정책](custom-policy-overview.md)이라고 하는 Azure AD B2C 신뢰 프레임 워크 정책을 관리할 수 있습니다.

- [테 넌 트에서 구성 된 모든 신뢰 프레임 워크 정책을 나열 합니다.](/graph/api/trustframework-list-trustframeworkpolicies)
- [신뢰 프레임 워크 정책 만들기](/graph/api/trustframework-post-trustframeworkpolicy)
- [기존 보안 프레임 워크 정책의 속성을 읽습니다.](/graph/api/trustframeworkpolicy-get)
- [신뢰 프레임 워크 정책을 업데이트 하거나 만듭니다.](/graph/api/trustframework-put-trustframeworkpolicy)
- [기존 보안 프레임 워크 정책 삭제](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>정책 키

Id 경험 프레임 워크는 사용자 지정 정책에서 참조 되는 암호를 저장 하 여 구성 요소 간에 트러스트를 설정 합니다. 이러한 비밀은 대칭 또는 비대칭 키/값이 될 수 있습니다. Azure Portal에서 이러한 엔터티는 **정책 키** 로 표시 됩니다.

Microsoft Graph API의 정책 키에 대 한 최상위 리소스는 [신뢰할 수 있는 프레임 워크 키 집합](/graph/api/resources/trustframeworkkeyset)입니다. 각 키 **집합** 에는 하나 이상의 **키** 가 포함 되어 있습니다. 키를 만들려면 먼저 빈 키 집합을 만든 다음 키 집합에 키를 생성 합니다. 수동 암호를 만들거나 인증서 또는 PKCS12 키를 업로드할 수 있습니다. 키는 생성 된 암호, 문자열 (예: Facebook 응용 프로그램 암호) 또는 업로드 한 인증서 일 수 있습니다. 키 집합에 여러 키가 있는 경우 키 중 하나만 활성화 됩니다.

### <a name="trust-framework-policy-keyset"></a>보안 프레임 워크 정책 키 집합

- [신뢰 프레임 워크 키 집합이 비동기적 나열](/graph/api/trustframework-list-keysets)
- [키 집합이 비동기적 보안 프레임 워크 만들기](/graph/api/trustframework-post-keysets)
- [키 집합 가져오기](/graph/api/trustframeworkkeyset-get)
- [보안 프레임 워크 키 집합이 비동기적 업데이트](/graph/api/trustframeworkkeyset-update)
- [보안 프레임 워크 키 집합이 비동기적 삭제](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>보안 프레임 워크 정책 키

- [키 집합에서 현재 활성 키 가져오기](/graph/api/trustframeworkkeyset-getactivekey)
- [키 집합에 키 생성](/graph/api/trustframeworkkeyset-generatekey)
- [문자열 기반 비밀 업로드](/graph/api/trustframeworkkeyset-uploadsecret)
- [X.509 인증서 업로드](/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 형식 인증서 업로드](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>애플리케이션

- [애플리케이션 나열](/graph/api/application-list)
- [애플리케이션 만들기](/graph/api/resources/application)
- [애플리케이션 업데이트](/graph/api/application-update)
- [ServicePrincipal 만들기](/graph/api/resources/serviceprincipal)
- [Oauth2Permission Grant 만들기](/graph/api/resources/oauth2permissiongrant)
- [응용 프로그램 삭제](/graph/api/application-delete)

## <a name="application-extension-properties"></a>응용 프로그램 확장 속성

- [확장 속성 나열](/graph/api/application-list-extensionproperty)

Azure AD B2C는 사용자당 100개의 사용자 지정 특성을 보유할 수 있는 디렉터리를 제공합니다. 사용자 흐름의 경우 이러한 확장 속성은 [Azure Portal를 사용 하 여 관리](user-flow-custom-attributes.md)됩니다. 사용자 지정 정책의 경우 정책에서 확장 속성에 값을 처음으로 쓸 때 Azure AD B2C에서 속성을 만듭니다.

## <a name="audit-logs"></a>감사 로그

- [감사 로그 나열](/graph/api/directoryaudit-list)

Azure AD B2C 감사 로그에 액세스 하는 방법에 대 한 자세한 내용은 [감사 로그 Azure AD B2C 액세스](view-audit-logs.md)를 참조 하세요.

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>코드 샘플: 프로그래밍 방식으로 사용자 계정을 관리 하는 방법

이 코드 샘플은 [MICROSOFT GRAPH SDK](/graph/sdks/sdks-overview) 를 사용 하 여 Microsoft Graph API와 상호 작용 하는 .net Core 콘솔 응용 프로그램입니다. 이 코드에서는 API를 호출 하 여 Azure AD B2C 테 넌 트에서 사용자를 프로그래밍 방식으로 관리 하는 방법을 보여 줍니다.
[샘플 아카이브](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (* .zip)를 다운로드 하거나 GitHub에서 [리포지토리를 찾아보거나](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) 리포지토리를 복제할 수 있습니다.

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

코드 샘플을 가져온 후 사용자 환경에 맞게 구성한 다음 프로젝트를 빌드합니다.

1. [Visual Studio](https://visualstudio.microsoft.com) 또는 [Visual Studio Code](https://code.visualstudio.com)에서 프로젝트를 엽니다.
1. `src/appsettings.json` 엽니다.
1. 섹션에서 `appSettings` 을 `your-b2c-tenant` 테 넌 트의 이름으로 바꾸고,을 `Application (client) ID` `Client secret` 관리 응용 프로그램 등록 값으로 바꿉니다. 자세한 내용은 [Microsoft Graph 응용 프로그램 등록](microsoft-graph-get-started.md)을 참조 하세요.
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

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
