---
title: 클라이언트 응용 프로그램에서 요청을 승인하기 위해 Azure AD에서 토큰을 획득합니다.
titleSuffix: Azure Storage
description: Azure Active Directory를 사용하여 클라이언트 응용 프로그램 내에서 인증하고, OAuth 2.0 토큰을 획득하고, Azure Blob 저장소 및 큐 저장소에 대한 요청을 승인합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268484"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>클라이언트 응용 프로그램에서 요청을 승인하기 위해 Azure AD에서 토큰을 획득합니다.

Azure Blob 저장소 또는 큐 저장소와 함께 Azure Active Directory(Azure AD)를 사용하는 주요 이점은 자격 증명을 더 이상 코드에 저장할 필요가 없다는 것입니다. 대신 Microsoft ID 플랫폼(이전의 Azure AD)에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 응용 프로그램을 실행하는 보안 주체(사용자, 그룹 또는 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 액세스 토큰을 응용 프로그램에 반환하고 응용 프로그램은 액세스 토큰을 사용하여 Azure Blob 저장소 또는 큐 저장소에 대한 요청을 승인할 수 있습니다.

이 문서에서는 Microsoft ID 플랫폼 2.0을 사용하여 인증을 위해 네이티브 응용 프로그램 또는 웹 응용 프로그램을 구성하는 방법을 보여 줍니다. 코드 예제에서는 .NET을 사용하지만 다른 언어는 비슷한 접근 방식을 사용합니다. Microsoft ID 플랫폼 2.0에 대한 자세한 내용은 [Microsoft ID 플랫폼(v2.0) 개요를](../../active-directory/develop/v2-overview.md)참조하십시오.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Azure AD 보안 보안 주체에 역할 할당

Azure Storage 애플리케이션에서 보안 주체를 인증하려면 먼저 해당 보안 주체에 대해 RBAC(역할 기반 액세스 제어) 설정을 구성합니다. Azure Storage는 컨테이너 및 큐에 대한 사용 권한을 포함하는 기본 제공 RBAC 역할을 정의합니다. RBAC 역할이 보안 주체에게 할당되면 해당 보안 주체는 해당 리소스에 대한 액세스 권한이 부여됩니다. 자세한 내용은 [RBAC를 사용하여 Azure Blob 및 Queue 데이터에 대한 액세스 권한 관리를](storage-auth-aad-rbac.md)참조하십시오.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록

Azure AD를 사용하여 저장소 리소스에 대한 액세스 권한을 부여하는 첫 번째 단계는 [Azure 포털에서](https://portal.azure.com)Azure AD 테넌트를 사용하여 클라이언트 응용 프로그램을 등록하는 것입니다. 클라이언트 응용 프로그램을 등록할 때 Azure AD에 응용 프로그램에 대한 정보를 제공합니다. 그러면 Azure AD는 런타임 시 애플리케이션을 Azure AD와 연결하는 데 사용하는 클라이언트 ID(*애플리케이션 ID*라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요.

Azure Storage 응용 프로그램을 등록하려면 [빠른 시작: Microsoft ID 플랫폼에 응용 프로그램을 등록하는](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)단계에 따라. 다음 이미지는 웹 응용 프로그램을 등록하기 위한 일반적인 설정을 보여 주며 다음과 같은 것입니다.

![Azure AD로 저장소 응용 프로그램을 등록하는 방법을 보여주는 스크린샷](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> 응용 프로그램을 네이티브 응용 프로그램으로 등록하는 경우 **리디렉션 URI**에 대해 유효한 URI를 지정할 수 있습니다. 네이티브 응용 프로그램의 경우 이 값이 실제 URL일 필요는 없습니다. 웹 응용 프로그램의 경우 토큰이 제공되는 URL을 지정하므로 리디렉션 URI는 유효한 URI여야 합니다.

애플리케이션을 등록한 후에 **설정** 아래에 애플리케이션 ID(또는 클라이언트 ID)가 표시됩니다.

![클라이언트 ID를 보여주는 스크린샷](./media/storage-auth-aad-app/app-registration-client-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../../active-directory/develop/quickstart-v2-register-an-app.md)을 참조하세요.

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Azure Storage에 등록된 앱 사용 권한 부여

다음으로 응용 프로그램 권한을 Azure 저장소 API로 호출하도록 부여합니다. 이 단계를 통해 응용 프로그램에서 Azure AD를 사용하여 Azure 저장소에 대한 요청을 승인할 수 있습니다.

1. 등록된 응용 프로그램의 **개요** 페이지에서 **API 권한 보기를 선택합니다.**
1. API 사용 권한 섹션에서 **권한 추가를** 선택하고 **Microsoft API를** **선택합니다.**
1. 결과 목록에서 **Azure 저장소를** 선택하여 API 요청 권한 창을 **표시합니다.**
1. **응용 프로그램에 필요한 사용 권한 유형에서**사용 가능한 권한 유형이 **위임된 권한인지 확인합니다.** 이 옵션은 기본적으로 선택됩니다.
1. **API 사용 권한 요청** 창의 사용 권한 **선택** 섹션에서 **user_impersonation**옆에 있는 확인란을 선택한 다음 **권한 추가를 클릭합니다.**

    ![저장소에 대한 권한을 보여주는 스크린샷](media/storage-auth-aad-app/registered-app-permissions-1.png)

**API 권한** 창은 이제 등록된 Azure AD 응용 프로그램이 Microsoft 그래프와 Azure 저장소모두에 액세스할 수 있음을 보여 줍니다. Azure AD로 앱을 처음 등록할 때 Microsoft 그래프에 대한 사용 권한이 자동으로 부여됩니다.

![레지스터 앱 권한을 보여주는 스크린샷](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

응용 프로그램에는 토큰을 요청할 때 클라이언트 보안 이 필요합니다. 클라이언트 보안 을 추가하려면 다음 단계를 따르십시오.

1. Azure 포털에서 앱 등록으로 이동합니다.
1. 인증서 & 비밀 설정을 **선택합니다.**
1. **클라이언트 암호에서** **새 클라이언트 비밀을** 클릭하여 새 비밀을 만듭니다.
1. 비밀에 대한 설명을 제공하고 원하는 만료 간격을 선택합니다.
1. 새 비밀의 값을 즉시 안전한 위치에 복사합니다. 전체 값은 한 번만 표시됩니다.

    ![클라이언트 비밀을 보여주는 스크린샷](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>토큰 수집을 위한 클라이언트 라이브러리

응용 프로그램을 등록하고 Azure Blob 저장소 또는 Queue 저장소의 데이터에 액세스할 수 있는 권한을 부여한 후에는 응용 프로그램에 코드를 추가하여 보안 주체를 인증하고 OAuth 2.0 토큰을 획득할 수 있습니다. 토큰을 인증하고 획득하려면 [Microsoft ID 플랫폼 인증 라이브러리](../../active-directory/develop/reference-v2-libraries.md) 중 하나 또는 OpenID Connect 1.0을 지원하는 다른 오픈 소스 라이브러리중 하나를 사용할 수 있습니다. 그런 다음 응용 프로그램에서 액세스 토큰을 사용하여 Azure Blob 저장소 또는 Queue 저장소에 대한 요청을 승인할 수 있습니다.

토큰 획득이 지원되는 시나리오 목록은 [Microsoft 인증 라이브러리 콘텐츠의](/azure/active-directory/develop/msal-overview) [인증 흐름](/en-us/azure/active-directory/develop/msal-authentication-flows) 섹션을 참조하십시오.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Azure AD를 사용하여 인증하기 위해 잘 알려진 값

Azure AD를 사용하여 보안 주체를 인증하려면 코드에서 잘 알려진 몇 가지 값을 포함해야 합니다.

### <a name="azure-ad-authority"></a>Azure AD 권한

Microsoft 퍼블릭 클라우드의 경우 기본 Azure AD 권한은 다음과 같습니다. 여기서 *tenant-id*는 Active Directory 테넌트 ID(또는 디렉터리 ID)입니다.

`https://login.microsoftonline.com/<tenant-id>/`

테넌트 ID는 인증에 사용할 Azure AD 테넌트를 식별합니다. 디렉터리 ID라고도 합니다. 테넌트 ID를 검색하려면 Azure Portal에서 앱 등록을 위한 **개요** 페이지로 이동하여 거기에서 값을 복사합니다.

### <a name="azure-storage-resource-id"></a>Azure 저장소 리소스 ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

코드 예제에서는 Azure AD에서 액세스 토큰을 가져오는 방법을 보여줍니다. 지정한 사용자를 인증한 다음, 블록 Blob을 만드는 요청에 권한을 부여하는 데 액세스 토큰이 사용됩니다. 이 샘플을 사용하려면 먼저 이전 섹션에 설명된 단계를 따릅니다.

토큰을 요청하려면 앱 등록시 다음 값이 필요합니다.

- Azure AD 도메인의 이름입니다. Azure Active Directory의 **개요** 페이지에서 이 값을 검색합니다.
- 테넌트(또는 디렉터리) ID입니다. 앱 등록의 **개요** 페이지에서 이 값을 검색합니다.
- 클라이언트(또는 응용 프로그램) ID입니다. 앱 등록의 **개요** 페이지에서 이 값을 검색합니다.
- 클라이언트 리디렉션 URI입니다. 앱 등록에 대한 **인증** 설정에서 이 값을 검색합니다.
- 클라이언트 보안 의 값입니다. 이전에 복사한 위치에서 이 값을 검색합니다.

### <a name="create-a-storage-account-and-container"></a>스토리지 계정 및 컨테이너 만들기

코드 샘플을 실행하려면 Azure Active Directory와 동일한 구독 내에서 저장소 계정을 만듭니다. 그런 다음 해당 저장소 계정 내에서 컨테이너를 만듭니다. 샘플 코드는 이 컨테이너에 블록 Blob을 만듭니다.

그런 다음 샘플 코드를 실행할 사용자 계정에 **저장소 Blob 데이터 참여자** 역할을 명시적으로 할당합니다. Azure 포털에서 이 역할을 할당하는 방법에 대한 지침은 Azure 포털에서 [RBAC를 사용하여 Azure Blob 및 큐 데이터에](storage-auth-aad-rbac-portal.md)대한 권한 부여 액세스를 참조합니다.

> [!NOTE]
> Azure Storage 계정을 만들 때 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당되지 않습니다. Azure Storage에 RBAC 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Azure AD를 사용하여 Blob 저장소에 대한 액세스를 권한 부여하는 웹 응용 프로그램 만들기

응용 프로그램이 Azure Storage에 액세스하면 사용자를 대신하여 로그인한 사용자의 권한을 사용하여 Blob 또는 큐 리소스에 액세스합니다. 이 코드 예제를 사용하려면 Azure AD ID를 사용하여 로그인하라는 메시지를 표시하는 웹 응용 프로그램이 필요합니다. 직접 만들거나 Microsoft에서 제공하는 샘플 응용 프로그램을 사용할 수 있습니다.

토큰을 획득하고 이를 사용하여 Azure 저장소에서 Blob을 만드는 완료된 샘플 웹 응용 프로그램은 [GitHub](https://aka.ms/aadstorage)에서 사용할 수 있습니다. 완성된 샘플을 검토하고 실행하면 코드 예제를 이해하는 데 도움이 될 수 있습니다. 완성된 샘플을 실행하는 방법에 대한 지침은 [완료된 샘플 보기 및 실행이라는](#view-and-run-the-completed-sample)섹션을 참조하십시오.

#### <a name="add-references-and-using-statements"></a>참조 추가 및 명령문 사용  

Visual Studio에서 Azure 저장소 클라이언트 라이브러리를 설치합니다. **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다. .NET에 대한 Azure Storage 클라이언트 라이브러리에서 필요한 패키지를 설치하려면 콘솔 창에 다음 명령을 입력합니다.

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

다음으로 HomeController.cs 파일에 문을 사용하여 다음을 추가합니다.

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>블록 Blob 만들기

다음 코드 조각을 추가하여 블록 Blob을 만듭니다.

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> OAuth 2.0 토큰으로 Blob 및 큐 작업을 승인하려면 HTTPS를 사용해야 합니다.

위의 예제에서 .NET 클라이언트 라이브러리는 블록 Blob 만들기 요청의 권한 부여를 처리합니다. 다른 언어에 대한 Azure Storage 클라이언트 라이브러리도 요청의 권한 부여를 처리합니다. 그러나 REST API를 사용하여 OAuth 토큰을 통해 Azure Storage 작업을 호출하는 경우 OAuth 토큰을 사용하여 요청에 권한을 부여해야 합니다.

OAuth 액세스 토큰을 사용하여 Blob 및 큐 서비스 작업을 호출하려면, **Bearer**(전달자) 체계를 사용하여 **Authorization**(권한 부여) 헤더에 액세스 토큰을 전달하고 2017-11-09 이상의 서비스 버전을 지정합니다.

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Azure AD에서 OAuth 토큰 가져오기

그런 다음 사용자를 대신하여 Azure AD에서 토큰을 요청하는 메서드를 추가합니다. 이 메서드는 사용 권한을 부여할 범위를 정의합니다. 사용 권한 및 범위에 대한 자세한 내용은 [Microsoft ID 플랫폼 끝점에서 사용 권한 및 동의를](../../active-directory/develop/v2-permissions-and-consent.md)참조하세요.

리소스 ID를 사용하여 토큰을 획득할 범위를 구성합니다. 이 예제에서는 사용자를 대신하여 토큰이 요청되고 있음을 `user_impersonation` 나타내는 기본 제공 범위와 함께 리소스 ID를 사용하여 범위를 구성합니다.

사용자가 토큰을 대신 요청하는 데 동의할 수 있는 인터페이스를 사용자에게 제공해야 할 수 있습니다. 동의가 필요한 경우 이 예제에서는 **MsalUiRequiredException을** catch하고 동의 요청을 용이하게 하기 위해 다른 방법을 호출합니다.

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

동의란 사용자가 자신을 대신해 보호되는 리소스에 액세스하기 위해 애플리케이션에 권한을 부여하는 프로세스를 말합니다. Microsoft ID 플랫폼 2.0은 증분 동의를 지원하므로 보안 주체는 처음에 최소 사용 권한을 요청하고 필요에 따라 시간이 지남에 따라 권한을 추가할 수 있습니다. 코드에서 액세스 토큰을 요청하는 경우 `scope` 매개 변수에 따라 앱에 필요한 권한 범위를 지정합니다. 증분 동의에 대한 자세한 내용은 [Microsoft ID 플랫폼(v2.0)으로 업데이트하는 이유에서](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) **증분 및 동적 동의** 라는 섹션을 참조하십시오.

다음 메서드는 증분 동의를 요청 하기 위한 인증 속성을 구성 합니다.

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>완성된 샘플 보기 및 실행

샘플 응용 프로그램을 실행하려면 먼저 [GitHub에서](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)복제하거나 다운로드합니다. 그런 다음 다음 섹션에 설명된 대로 응용 프로그램을 업데이트합니다.

### <a name="provide-values-in-the-settings-file"></a>설정 파일에 값 제공

다음으로 다음과 같이 *appsettings.json* 파일을 사용자 고유의 값으로 업데이트합니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>저장소 계정 및 컨테이너 이름 업데이트

*HomeController.cs* 파일에서 블록 Blob을 참조하는 URI를 업데이트하여 저장소 계정 및 컨테이너의 이름을 사용합니다.

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>암시적 권한 부여 흐름 사용

샘플을 실행하려면 앱 등록에 대한 암시적 권한 부여 흐름을 구성해야 할 수 있습니다. 다음 단계를 수행하세요.

1. Azure 포털에서 앱 등록으로 이동합니다.
1. 관리 섹션에서 **인증** 설정을 선택합니다.
1. **고급 설정에서** **암시적 부여** 섹션에서 다음 이미지와 같이 액세스 토큰 및 ID 토큰을 사용하도록 설정하려면 확인란을 선택합니다.

    ![암시적 권한 부여 흐름에 대한 설정을 활성화하는 방법을 보여주는 스크린샷](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>localhost에서 사용하는 포트 업데이트

샘플을 실행할 때 런타임에 할당된 *localhost* 포트를 사용하려면 앱 등록에 지정된 리디렉션 URI를 업데이트해야 할 수 있습니다. 할당된 포트를 사용하도록 리디렉션 URI를 업데이트하려면 다음 단계를 따르십시오.

1. Azure 포털에서 앱 등록으로 이동합니다.
1. 관리 섹션에서 **인증** 설정을 선택합니다.
1. **리디렉션 URI에서**다음 이미지와 같이 샘플 응용 프로그램에서 사용하는 포트와 일치하도록 포트를 편집합니다.

    ![앱 등록을 위한 리디렉션 URI를 보여주는 스크린샷](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>다음 단계

- Microsoft ID 플랫폼에 대한 자세한 내용은 [Microsoft ID 플랫폼을](https://docs.microsoft.com/azure/active-directory/develop/)참조하십시오.
- Azure 저장소에 대한 RBAC 역할에 대해 자세히 알아보려면 [RBAC를 사용하여 저장소 데이터에](storage-auth-aad-rbac.md)대한 액세스 권한 관리를 참조하십시오.
- Azure Storage를 사용하여 Azure 리소스에 대해 관리되는 ID를 사용하는 방법에 대해 알아보려면 [Azure Active Directory 및 Azure 리소스에 대한 관리되는 ID를 사용하여 Blob 및 큐에 대한 액세스 인증을](storage-auth-aad-msi.md)참조하세요.
