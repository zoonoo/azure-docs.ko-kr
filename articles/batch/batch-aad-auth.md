---
title: "Azure Active Directory를 사용하여 Azure Batch에서 인증 | Microsoft Docs"
description: "Batch는 Batch 서비스 및 Batch 리소스 공급자에서 인증을 위해 Azure AD를 지원합니다."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/16/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 28665625cde9c7b164986f7b2d9c845136543aba
ms.lasthandoff: 03/18/2017

---

# <a name="authenticate-from-batch-solutions-with-active-directory"></a>Active Directory를 사용하여 Batch 솔루션에서 인증

Azure Batch는 Batch 서비스 및 Batch 관리 서비스용 [Azure Active Directory][aad_about](Azure AD)를 사용하여 인증을 지원합니다. Azure AD는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure에서는 해당 고객, 서비스 관리자 및 조직 사용자의 인증을 위해 Azure AD를 자체적으로 사용합니다.

이 문서에서는 Batch 관리 .NET 라이브러리 또는 Batch .NET 라이브러리를 사용하는 응용 프로그램에서 인증을 위해 Azure AD를 사용하여 탐색합니다. Batch .NET API의 컨텍스트에서 통합된 인증을 사용하여 구독 관리자 또는 공동 관리자를 인증하도록 Azure AD를 사용하는 방법을 보여 줍니다. 인증된 사용자는 Azure Batch에 대한 요청을 발급할 수 있습니다.

Azure AD를 사용하여 무인 모드로 실행하는 응용 프로그램에 대한 액세스를 인증하는 것도 가능합니다. 여기에서는 Azure AD 통합된 인증을 사용하는 데 중점을 두고 무인 응용 프로그램 인증에 대해 알아보도록 다른 리소스를 설명합니다.

## <a name="use-azure-ad-with-batch-management-solutions"></a>Batch 관리 솔루션으로 Azure AD 사용

Batch 관리 .NET 라이브러리는 배치 계정, 계정 키, 응용 프로그램 및 응용 프로그램 패키지를 사용하기 위한 종류를 표시합니다. Batch 관리 .NET 라이브러리는 Azure 리소스 공급자 클라이언트이며 [Azure Resource Manager][resman_overview]와 함께 프로그래밍 방식으로 해당 리소스를 관리하는 데 사용됩니다. 

Azure AD는 Batch 관리 .NET 라이브러리를 비롯한 Azure 리소스 공급자 클라이언트 및 [Azure Resource Manager][resman_overview]를 통해 만들어지는 요청을 인증하는 데 필요합니다.

이 섹션에서는 GitHub에서 사용할 수 있는 [AccountManagment][acct_mgmt_sample] 샘플 프로젝트를 사용하여 Batch 관리 .NET 라이브러리와 함께 Azure AD 사용을 안내합니다. AccountManagement 샘플은 프로그래밍 방식으로 구독에 액세스하고 리소스 그룹 및 새 배치 계정을 만들고 계정에서 일부 작업을 수행하는 콘솔 응용 프로그램입니다. 

Batch 관리 .NET 라이브러리 및 AccountManagement 샘플을 사용하는 방법에 대한 자세한 내용은 [.NET용 Batch 관리 클라이언트 라이브러리를 사용하여 배치 계정 및 할당량 관리](batch-management-dotnet.md)를 참조하세요.

### <a name="register-your-application-with-azure-ad"></a>Azure AD에 응용 프로그램 등록

Azure [Active Directory 인증 라이브러리][aad_adal](ADAL)는 응용 프로그램 내에서 사용하기 위해 Azure AD에 프로그래밍 방식 인터페이스를 제공합니다. 응용 프로그램에서 ADAL을 호출하려면 Azure AD 테넌트에 응용 프로그램을 등록해야 합니다. 응용 프로그램을 등록할 때 Azure AD 테넌트 내에서 이름을 포함하여 응용 프로그램에 대한 Azure AD 정보를 제공합니다. 그런 다음 Azure AD는 런타임 시 응용 프로그램을 Azure AD와 연결하는 데 사용하는 응용 프로그램 ID를 제공합니다. 응용 프로그램 ID에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](../active-directory/develop/active-directory-application-objects.md)를 참조하세요.

AccountManagement 샘플 응용 프로그램을 등록하려면 [Azure Active Directory와 응용 프로그램 통합][aad_integrate]에서 [응용 프로그램 추가](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) 섹션의 단계를 따릅니다. 응용 프로그램 유형으로 **네이티브 클라이언트 응용 프로그램**을 지정합니다. **리디렉션 URI**의 경우 실제 끝점일 필요가 없으므로 모든 유효한 URI를 지정할 수 있습니다(예: `http://myaccountmanagementsample`).

![](./media/batch-aad-auth/app-registration-management-plane.png)

등록 프로세스를 완료한 후 응용 프로그램에 대해 나열된 응용 프로그램 ID 및 개체(서비스 주체) ID가 표시됩니다.  

![](./media/batch-aad-auth/app-registration-client-id.png)

### <a name="update-your-code-to-reference-your-application-id"></a>응용 프로그램 ID를 참조하도록 코드 업데이트 

클라이언트 응용 프로그램은 런타임 시 Azure AD에 액세스하기 위해 응용 프로그램 ID(클라이언트 ID라고도 함)를 사용합니다. Azure Portal에서 응용 프로그램을 등록한 후 등록된 응용 프로그램에 대해 Azure AD에서 제공하는 응용 프로그램 ID를 사용하도록 코드를 업데이트합니다. AccountManagement 샘플 응용 프로그램에서 Azure Portal의 응용 프로그램 ID를 적절한 상수에 복사합니다.

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
또한 등록 프로세스 중 지정한 리디렉션 URI를 복사합니다.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

### <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>응용 프로그램에 대한 Azure Resource Manager API 액세스 권한 부여

다음으로, Azure Resource Manager API에 응용 프로그램에 대한 액세스 권한을 위임해야 합니다. 리소스 관리자 API에 대한 Azure AD 식별자는 **Windows Azure Service Management API**입니다.

Azure Portal에서 다음 단계를 따릅니다.

1. Azure Portal의 왼쪽 탐색 창에서 **추가 서비스**를 선택하고 **앱 등록**을 클릭한 다음 **추가**를 클릭합니다.
2. 앱 등록의 목록에서 응용 프로그램의 이름을 검색합니다.

    ![응용 프로그램 이름 검색](./media/batch-aad-auth/search-app-registration.png)

3. **설정** 블레이드를 표시합니다. **API 액세스** 섹션에서 **필요한 사용 권한**을 선택합니다.
4. **추가**를 클릭하여 새로운 필요한 사용 권한을 추가합니다. 
5. 1단계에서 **Windows Azure Service Management API**를 입력하고 결과 목록에서 해당 API를 선택하고 **선택** 단추를 클릭합니다.
6. 2단계에서 **조직 사용자로 Azure 클래식 배포 모델에 액세스** 옆의 확인란을 선택하고 **선택** 단추를 클릭합니다.
7. **완료** 단추를 클릭합니다.

**필요한 사용 권한** 블레이드는 이제 응용 프로그램에 대한 권한이 ADAL과 리소스 관리자 API에 부여되었음을 표시합니다. Azure AD에 앱을 처음 등록할 때 사용 권한은 기본적으로 ADAL에 부여됩니다.

![Azure Resource Manager API에 대한 권한 위임](./media/batch-aad-auth/required-permissions-management-plane.png)


### <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 인증 토큰 획득

AccountManagement 샘플 응용 프로그램은 Azure AD 및 Azure Resource Manager에 대한 끝점을 제공하는 상수를 정의합니다. 샘플 응용 프로그램은 이러한 상수를 사용하여 구독 정보에 대해 Azure AD를 쿼리합니다. 해당 상수를 변경하지 않고 그대로 둡니다.

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure service management resource 
private const string ResourceUri = "https://management.core.windows.net/";
```

Azure AD 테넌트에 AccountManagement 샘플을 등록하고 샘플 소스 코드 내에서 필요한 값을 제공하면 샘플은 Azure AD를 사용하여 인증할 준비가 됩니다. 샘플을 실행할 때 ADAL은 인증 토큰을 획득하려고 시도합니다. 이 단계에서 Microsoft 자격 증명을 묻습니다. 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

자격 증명을 제공한 후 샘플 응용 프로그램은 Batch 관리 서비스에 인증된 요청을 발급하도록 진행할 수 있습니다. 

## <a name="use-azure-ad-with-batch-service-solutions"></a>Batch 서비스 솔루션으로 Azure AD 사용

Batch .NET 라이브러리는 Batch 서비스와 함께 병렬 처리 워크플로 작성을 위한 형식을 제공합니다. Batch 서비스는 [공유 키](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service) 인증 및 Azure AD를 통한 인증을 지원합니다. 이 섹션에서는 Azure AD를 통한 인증을 설명합니다.

>[!NOTE]
>배치 계정을 만들 때 풀을 Batch에서 관리하는 구독 또는 사용자 구독에 할당할지 여부를 지정할 수 있습니다. 계정이 사용자 구독에 풀을 할당하는 경우 Azure AD를 사용하여 해당 계정의 리소스에 대한 요청을 인증해야 합니다.
>
>

Azure AD를 통한 Batch .NET 응용 프로그램 인증은 Batch 관리 .NET 응용 프로그램 인증과 비슷합니다. 이 섹션에서는 몇 가지 차이점을 설명합니다.

### <a name="batch-service-endpoints"></a>Batch 서비스 끝점

Batch 서비스 끝점은 Batch 관리 .NET과 함께 사용하는 서비스 끝점과 다릅니다.

Batch 서비스에 대한 Azure AD 끝점은 다음과 같습니다.

`https://login.microsoftonline.com/common`

Batch 서비스에 대한 리소스 끝점은 다음과 같습니다.

`https://batch.core.windows.net/`

### <a name="grant-the-batch-service-api-access-to-your-application"></a>응용 프로그램에 대한 Batch 서비스 API 액세스 권한 부여

Batch 응용 프로그램에서 Azure AD를 통해 인증하려면 Azure AD로 응용 프로그램을 등록하고 Batch 서비스 API에 대한 액세스 권한을 부여해야 합니다. Batch 서비스 API에 대한 Azure AD 식별자는 **Microsoft Azure Batch(MicrosoftAzureBatch)**입니다.

1. Batch 응용 프로그램을 등록하려면 [Azure Active Directory와 응용 프로그램 통합][aad_integrate]에서 [응용 프로그램 추가](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) 섹션의 단계를 따릅니다. **리디렉션 URI**의 경우 모든 유효한 URI를 지정할 수 있습니다. 실제 끝점일 필요는 없습니다.

    응용 프로그램을 등록한 후 응용 프로그램 ID 및 개체 ID가 표시됩니다.

    ![Azure AD에 Batch 응용 프로그램 등록](./media/batch-aad-auth/app-registration-data-plane.png)

2. 다음으로 **설정** 블레이드를 표시합니다. **API 액세스** 섹션에서 **필요한 사용 권한**을 선택합니다.
3. **필요한 사용 권한** 블레이드에서 **추가** 단추를 클릭합니다.
4. 1단계에서 **MicrosoftAzureBatch**를 검색하고 **Microsoft Azure Batch(MicrosoftAzureBatch)**를 선택하고 **선택** 단추를 클릭합니다.
5. 2단계에서 **Azure Batch 서비스에 액세스** 옆의 확인란을 선택하고 **선택** 단추를 클릭합니다.
6. **완료** 단추를 클릭합니다.

**필요한 사용 권한** 블레이드는 이제 Azure AD 응용 프로그램이 Azure AD 및 Azure Batch API에 액세스 권한을 부여하는 것을 표시합니다. 

![API 사용 권한](./media/batch-aad-auth/required-permissions-data-plane.png)

### <a name="authentication-for-batch-accounts-in-a-user-subscription"></a>사용자 구독에서 배치 계정에 대한 인증

새 배치 계정을 만들 때 풀이 할당된 구독을 선택할 수 있습니다. 사용자의 선택은 해당 계정의 리소스에 대한 요청을 인증하는 방법에 영향을 줍니다.

기본적으로 Batch 풀은 Batch 서비스 구독에 할당됩니다. 이 옵션을 선택하는 경우 공유 키 또는 Azure AD로 해당 계정의 리소스에 대한 요청을 인증할 수 있습니다.

또한 Batch 풀을 지정된 사용자 구독에 할당되도록 지정할 수도 있습니다. 이 옵션을 선택하는 경우 Azure AD로 인증해야 합니다.

### <a name="best-practices-for-using-azure-ad-with-batch"></a>Batch로 Azure AD 사용 모범 사례

Azure AD 인증 토큰은 1시간 후 만료됩니다. 수명이 긴 **BatchClient** 개체를 사용하는 경우 항상 유효한 토큰을 갖도록 모든 요청에 대해 ADAL에서 토큰을 검색하는 것이 좋습니다. 

이를 위해 .NET에서 Azure AD에서 토큰을 검색하는 메서드를 작성하고 해당 메서드를 대리자로 **BatchTokenCredentials** 개체에 전달합니다. 대리자 메서드는 유효한 토큰이 제공되었는지 확인하도록 Batch 서비스에 대한 모든 요청에서 호출됩니다. 기본적으로 ADAL은 토큰을 캐시하므로 필요한 경우 Azure AD에서 새 토큰이 검색됩니다. 예제는 다음 섹션의 [코드 예제: Batch .NET과 함께 Azure AD 사용](#code-example-using-azure-ad-with-batch-net)을 참조하세요. Azure AD의 토큰에 대한 자세한 내용은 [Azure AD의 인증 시나리오][aad_auth_scenarios]를 참조하세요.

### <a name="code-example-using-azure-ad-with-batch-net"></a>코드 예제: Batch .NET과 함께 Azure AD 사용

Azure AD로 인증하는 Batch .NET 코드를 작성 하려면 [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) 패키지 및 [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 패키지를 참조하세요.

코드에 다음 `using` 문을 포함시킵니다.

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

코드에서 Batch 서비스에 대해 Azure AD 공통 끝점 및 Azure AD 끝점을 참조합니다.  

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/common";
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

배치 계정 끝점을 참조합니다.

```csharp
private const string BatchAccountEndpoint = "https://myaccount.westcentralus.batch.azure.com";
```

응용 프로그램에 대한 응용 프로그램 ID(클라이언트 ID)를 지정합니다. 응용 프로그램 ID는 Azure Portal의 앱 등록에 사용할 수 있습니다. 이를 검색하려면 [응용 프로그램에 대한 Batch 서비스 API 액세스 권한 부여](#grant-the-batch-service-api-access-to-your-application)라는 제목의 섹션을 참조하세요. 

```csharp
private const string ClientId = "<application-id>";
```

또한 모든 유효한 URI가 될 수 있는 리디렉션 URI를 지정합니다.

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Azure AD에서 인증 토큰을 획득하는 콜백 메서드를 작성합니다. **AcquireTokenAsync** 메서드는 사용자에게 해당 자격 증명에 대해 묻고 해당 자격 증명을 사용하여 새 토큰을 획득합니다.

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

매개 변수로 대리자를 사용자는 **BatchTokenCredentials** 개체를 생성합니다. 이러한 자격 증명을 사용하여 **BatchClient** 개체를 엽니다. 그런 다음 Batch 서비스에 대한 후속 작업에 대해 해당 **BatchClient** 개체를 사용할 수 있습니다.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountEndpoint, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

위에 표시된 **GetAuthenticationTokenAsync** 콜백 메서드는 응용 프로그램과 상호 작용하는 사용자의 통합된 인증을 위해 Azure AD를 사용합니다. **AcquireTokenAsync** 메서드에 대한 호출은 사용자에게 해당 자격 증명을 묻고 사용자가 자격 증명을 제공하면 응용 프로그램이 진행됩니다. 또한 Azure AD 서비스 주체를 사용하여 무인 응용 프로그램을 인증하는 데 Azure AD를 사용할 수도 있습니다. 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](../active-directory/develop/active-directory-application-objects.md) 및 [포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 및 서비스 주체 만들기](../resource-group-create-service-principal-portal.md)를 참조하세요.  
 

## <a name="next-steps"></a>다음 단계

[AccountManagement 샘플 응용 프로그램][acct_mgmt_sample] 실행에 대한 자세한 내용은 [.NET용 Batch 관리 클라이언트 라이브러리를 사용하여 배치 계정 및 할당량 관리](batch-management-dotnet.md)를 참조하세요.

Azure AD에 대한 자세한 내용은 [Azure Active Directory 설명서](https://docs.microsoft.com/azure/active-directory/)를 참조하세요. ADAL을 사용하는 방법을 보여 주는 자세한 예제는 [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=active-directory) 라이브러리에서 사용할 수 있습니다.


[aad_about]: ../active-directory/active-directory-whatis.md "Azure Active Directory란?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD의 인증 시나리오"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Azure Active Directory와 응용 프로그램 통합"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

