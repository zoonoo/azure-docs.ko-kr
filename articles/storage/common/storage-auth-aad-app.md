---
title: 응용 프로그램에서 Blob 및 큐 데이터에 액세스하도록 Azure Active Directory를 사용하여 인증(미리 보기) | Microsoft Docs
description: Azure Active Directory를 사용하여 응용 프로그램 내에서 인증한 다음, Azure Storage 리소스에 대한 요청 권한을 부여합니다(미리 보기).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: d065dd6db361c5c348713c6e1ceabe3a4c42c312
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577707"
---
# <a name="authenticate-with-azure-active-directory-from-an-azure-storage-application-preview"></a>Azure Storage 응용 프로그램에서 Azure Active Directory를 사용하여 인증(미리 보기)

Azure Storage에서 Azure AD(Azure Active Directory)를 사용하는 주요 이점은 사용자의 자격 증명을 코드에 저장할 필요가 없다는 점입니다. 대신 Azure AD에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD에서는 응용 프로그램을 실행하여 보안 주체(사용자, 그룹 또는 서비스 사용자)의 인증을 처리합니다. 인증이 성공하면 Azure AD는 응용 프로그램에 액세스 토큰을 반환하고, 응용 프로그램은 액세스 토큰을 사용하여 Azure Storage에 대한 요청 권한을 부여할 수 있습니다.

이 아티클에서는 Azure AD를 사용하여 인증하기 위해 응용 프로그램을 구성하는 방법을 보여줍니다. 코드 예제에서는 .NET을 사용하지만 다른 언어는 비슷한 접근 방식을 사용합니다.

Azure Storage 응용 프로그램에서 보안 주체를 인증할 수 있기 전에 해당 보안 주체에 대해 RBAC(역할 기반 액세스 제어) 설정을 구성합니다. Azure Storage에서는 컨테이너 및 큐에 대한 사용 권한을 포함하는 RBAC 역할을 정의합니다. RBAC 역할이 보안 주체에게 할당되면 해당 보안 주체는 해당 리소스에 대한 액세스 권한이 부여됩니다. 자세한 내용은 [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 응용 프로그램에 대한 액세스 권한 부여](../../active-directory/develop/v1-protocols-oauth-code.md)를 참조하세요.

> [!IMPORTANT]
> 이 미리 보기는 프로덕션 이외 용도로 사용해야 합니다. Azure Storage에 대한 Azure AD 통합이 일반 공급 버전으로 선언되어야만 프로덕션 SLA(서비스 수준 계약)를 사용할 수 있습니다. 사용자 시나리오에서 Azure AD 통합이 아직 지원되지 않는 경우, 응용 프로그램에서 공유 키 인증 또는 SAS 토큰을 계속 사용합니다. 미리 보기에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증(미리 보기)](storage-auth-aad.md)을 참조하세요.
>
> 미리 보기 동안 RBAC 역할 할당을 전파하는 데 최대 5분이 소요될 수 있습니다.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 응용 프로그램 등록

Azure AD를 사용하여 저장소 리소스에 대한 액세스 권한을 부여하는 첫 번째 단계는 Azure AD 테넌트에서 클라이언트 응용 프로그램을 등록하는 것입니다. 응용 프로그램을 등록하면 코드에서 ADAL(Azure [Active Directory 인증 라이브러리](../../active-directory/active-directory-authentication-libraries.md))을 호출할 수 있습니다. ADAL은 응용 프로그램에서 Azure AD로 인증하는 API를 제공합니다. 또한 응용 프로그램을 등록하면 액세스 토큰을 사용하여 해당 응용 프로그램에서 Azure Storage API로 호출하는 권한을 부여할 수 있습니다.

응용 프로그램을 등록할 때 응용 프로그램에 대한 정보를 Azure AD에 제공합니다. 그러면 Azure AD는 런타임 시 응용 프로그램을 Azure AD와 연결하는 데 사용하는 클라이언트 ID(*응용 프로그램 ID*라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 사용자 개체](../../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요.

Azure Storage 응용 프로그램을 등록하려면 [Azure Active Directory와 응용 프로그램 통합](../../active-directory/active-directory-integrating-applications.md)에서 [응용 프로그램 추가](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md#adding-an-application) 섹션의 단계를 따릅니다. 응용 프로그램을 네이티브 응용 프로그램으로 등록하는 경우 **리디렉션 URI**에 유효한 URI를 지정할 수 있습니다. 값이 실제 엔드포인트일 필요는 없습니다.

![Azure AD에서 저장소 응용 프로그램을 등록하는 방법을 보여주는 스크린샷](./media/storage-auth-aad-app/app-registration.png)

응용 프로그램을 등록한 후에 **설정** 아래에 응용 프로그램 ID(또는 클라이언트 ID)가 표시됩니다.

![클라이언트 ID를 보여주는 스크린샷](./media/storage-auth-aad-app/app-registration-client-id.png)

Azure AD에서 응용 프로그램을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 응용 프로그램 통합](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)을 참조하세요. 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Azure Storage에 등록된 앱 사용 권한 부여

다음으로 Azure Storage API를 호출하여 응용 프로그램 사용 권한을 부여해야 합니다. 이 단계를 통해 Azure AD를 사용하여 Azure Storage에 대해 호출하는 권한을 응용 프로그램에 부여할 수 있습니다.

1. Azure Portal의 왼쪽 탐색 창에서 **모든 서비스**를 선택하고, **앱 등록**을 검색합니다.
2. 이전 단계에서 만든 등록된 응용 프로그램의 이름을 검색합니다.
3. 등록된 앱을 선택하고, **설정**을 클릭합니다. **API 액세스** 섹션에서 **필요한 사용 권한**을 선택합니다.
4. **필요한 사용 권한** 블레이드에서 **추가** 단추를 클릭합니다.
5. **API 선택** 아래에서 "Azure Storage"를 검색하고, 결과 목록에서 **Azure Storage**를 선택합니다.

    ![저장소에 대한 사용 권한을 보여주는 스크린샷](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. **사용 권한 선택** 아래의 **Azure Storage 액세스** 옆에 있는 확인란을 선택하고, **선택**을 클릭합니다.
7. **Done**을 클릭합니다.

이제 **필수 권한** 창은 Azure AD 응용 프로그램에 Azure Active Directory 및 Azure Storage 모두에 대한 액세스 권한이 있음을 보여줍니다. Azure AD에 앱을 처음 등록할 때 자동으로 Azure AD에 대한 사용 권한이 부여됩니다.

![앱 사용 권한을 등록하는 방법을 보여주는 스크린샷](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

코드 예제에서는 Azure AD에서 액세스 토큰을 가져오는 방법을 보여줍니다. 지정한 사용자를 인증한 다음, 블록 Blob을 만드는 요청에 권한을 부여하는 데 액세스 토큰이 사용됩니다. 이 샘플을 사용하려면 먼저 이전 섹션에 설명된 단계를 따릅니다.

> [!NOTE]
> Azure Storage 계정 소유자인 경우 자동으로 데이터에 액세스할 수 있는 권한이 할당되지 않습니다. Azure Storage에 RBAC 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 저장소 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다. 
>
> 예를 들어 샘플 코드를 사용자가 소유자이거나 고유한 사용자 ID 하의 저장소 계정에서 실행하려면 Blob 데이터 기여자에 대한 RBAC 역할을 자신에게 할당해야 합니다. 그렇지 않은 경우 Blob을 만들라는 요청은 HTTP 상태 코드 403(금지됨) 메시지를 표시하며 실패합니다. 자세한 내용은 [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요.

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Azure AD를 사용하여 인증하기 위해 잘 알려진 값

Azure AD를 사용하여 보안 주체를 인증하려면 코드에서 잘 알려진 몇 가지 값을 포함해야 합니다.

#### <a name="azure-ad-oauth-endpoint"></a>Azure AD OAuth 엔드포인트

OAuth 2.0의 기본 Azure AD 권한 엔드포인트는 다음과 같습니다. 여기서 *tenant-id*는 Active Directory 테넌트 ID(또는 디렉터리 ID)입니다.

`https://login.microsoftonline.com/<tenant-id>/oauth2/token`

테넌트 ID는 인증에 사용할 Azure AD 테넌트를 식별합니다. 테넌트 ID를 검색하려면 **Azure Active Directory에 대한 테넌트 ID 가져오기**에서 설명하는 단계를 따릅니다.

#### <a name="storage-resource-id"></a>저장소 리소스 ID

Azure Storage 리소스 ID를 사용하여 Azure Storage에 대한 요청을 인증하기 위해 토큰을 획득합니다.

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Azure Active Directory에 대한 테넌트 ID 가져오기

테넌트 ID를 얻으려면 다음 단계를 수행합니다.

1. Azure Portal에서 Active Directory를 선택합니다.
2. **속성**을 클릭합니다.
3. **디렉터리 ID**에 제공된 GUID 값을 복사합니다. 이 값은 테넌트 ID라고도 합니다.

![테넌트 ID를 복사하는 방법을 보여주는 스크린샷](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>참조 추가 및 명령문 사용  

Visual Studio에서 Azure Storage 클라이언트 라이브러리의 미리 보기 버전을 설치합니다. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다. 콘솔에 다음 명령을 입력하여 .NET용 클라이언트 라이브러리의 최신 버전을 설치합니다.

```
Install-Package WindowsAzure.Storage
```

ADAL의 최신 버전도 설치합니다.

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

다음으로 다음 using 문을 코드에 추가합니다.

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Azure AD에서 OAuth 토큰 가져오기

다음으로 Azure AD에서 토큰을 요청하는 메서드를 추가합니다. 토큰을 요청하려면 [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) 메서드를 호출합니다. 이전에 수행한 단계에서 다음 값이 있는지 확인합니다.

- 테넌트(디렉터리) ID
- 클라이언트(응용 프로그램) ID
- 클라이언트 리디렉션 URI

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthEndpoint, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>블록 Blob 만들기

마지막으로 액세스 토큰을 사용하여 새 저장소 자격 증명을 만들고 해당 자격 증명을 사용하여 Blob을 만듭니다.

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
```

> [!NOTE]
> Azure Storage와 Azure AD를 통합하려면 Azure Storage 작업에 HTTPS를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

- Azure Storage의 RBAC 역할에 대한 자세한 내용은 [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요.
- Azure Storage에서 관리 서비스 ID를사용 하는 방법에 대한 자세한 내용은 [Azure 관리 서비스 ID에서 Azure AD로 인증(미리 보기)](storage-auth-aad-msi.md)을 참조하세요.
- Azure AD ID를 사용하여 Azure CLI 및 PowerShell에 로그인하는 방법을 알아보려면 [Azure AD ID를 사용하여 CLI 또는 PowerShell을 통해 Azure Storage에 액세스(미리 보기)](storage-auth-aad-script.md)를 참조하세요.
- Azure Blob 및 큐의 Azure AD 통합에 대한 자세한 내용은 Azure Storage 팀 블로그 게시물 [Azure Storage에 대한 Azure AD 인증 미리 보기 발표](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)를 참조하세요.



