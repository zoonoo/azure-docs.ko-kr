---
title: .NET SDK를 사용하여 Purview 계정 만들기
description: .NET SDK를 사용하여 Azure Purview 계정을 만듭니다.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: b3dc7bf8ac7650a7219c15a09a31d4dcf84a40bf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587802"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>빠른 시작: .NET SDK를 사용하여 Purview 계정 만들기

이 빠른 시작에서는 .NET SDK를 사용하여 Azure Purview 계정을 만드는 방법을 설명합니다. 

> [!IMPORTANT]
> Azure Purview는 현재 미리 보기에 있습니다. [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기에 있거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 사용자 고유의 [Azure Active Directory 테넌트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* 계정에는 구독에서 리소스를 만들 수 있는 권한이 있어야 합니다.

* 모든 애플리케이션에서 **Storage 계정** 및 **EventHub 네임스페이스** 를 만들지 못하도록 차단하는 **Azure Policy** 가 있는 경우 태그를 사용하여 정책 예외를 만들어야 합니다. 이 예외는 Purview 계정을 만드는 과정에서 입력할 수 있습니다. 가장 중요한 이유는 만든 각 Purview 계정에 대해 관리되는 리소스 그룹을 만들고 이 리소스 그룹 내에 Storage 계정 및 EventHub 네임스페이스를 만들어야 하기 때문입니다. 자세한 내용은 [카탈로그 포털 만들기](create-catalog-portal.md)를 참조하세요.

### <a name="visual-studio"></a>Visual Studio

이 문서의 연습에서는 Visual Studio 2019를 사용합니다. Visual Studio 2013, 2015 또는 2017에 대한 절차는 약간 다릅니다.

### <a name="azure-net-sdk"></a>Azure .NET SDK

[Azure .NET SDK](https://azure.microsoft.com/downloads/)를 컴퓨터에 다운로드하여 설치합니다.

## <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션 만들기

*방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기* 의 섹션에서 다음 작업을 수행하기 위한 지침을 따릅니다.

1. [Azure Active Directory 애플리케이션 만들기](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)에서 이 자습서에서 만드는 .NET 애플리케이션을 나타내는 애플리케이션을 만듭니다. sign-on URL의 경우 (`https://contoso.org/exampleapp`)에 보이는 더미 URL을 제공할 수 있습니다.
2. [로그인을 위한 값 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)에서 **애플리케이션 ID** 및 **테넌트 ID** 를 가져온 후 이 자습서의 뒷부분에서 사용하게 되므로 이러한 값을 적어둡니다. 
3. [인증서 및 비밀](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)에서 **인증 키** 를 가져오고 이 자습서의 뒷부분에서 사용하게 되므로 이 값을 적어둡니다.
4. [역할에 애플리케이션 할당](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)에서 애플리케이션이 구독에 데이터 팩터리를 생성할 수 있도록 구독 수준에서 애플리케이션을 **참여자** 역할에 할당합니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

다음으로, Visual Studio에서 C# .NET 콘솔 애플리케이션을 만듭니다.

1. **Visual Studio** 를 시작합니다.
2. 시작 창에서 **새 프로젝트 만들기** > **콘솔 앱(.NET Framework)** 을 선택합니다. .NET 버전 4.5.2 이상이 필요합니다.
3. **프로젝트 이름** 에 **PurviewQuickStart** 를 입력합니다.
4. **만들기** 를 선택하여 프로젝트를 만듭니다.

## <a name="install-nuget-packages"></a>NuGet 패키지 설치

1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.
2. **패키지 관리자 콘솔** 페이지에서 다음 명령을 실행하여 패키지를 설치합니다. 자세한 내용은 [Microsoft.Azure.Management.Purview NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/)를 참조하세요.

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Purview 클라이언트 만들기

1. **Program.cs** 를 열고 다음 문을 포함하여 네임스페이스에 대한 참조를 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. 변수를 설정하는 **Main** 메서드에 다음 코드를 추가합니다. 자리 표시자를 고유한 값으로 바꿉니다. Purview를 현재 사용할 수 있는 Azure 지역 목록을 보려면 **Azure Purview** 를 검색하고 다음 페이지: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)에서 관심 있는 지역을 선택합니다.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. **Main** 메서드에 **PurviewManagementClient** 클래스의 인스턴스를 만드는 다음 코드를 추가합니다. 이 개체를 사용하여 Purview 계정을 만듭니다.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Purview 계정 만들기

**Main** 메서드에 **Purview 계정** 을 만드는 다음 코드를 추가합니다. 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>코드 실행

애플리케이션을 빌드하고 시작한 다음, 실행을 확인합니다.

콘솔에서 Purview 계정을 만드는 과정을 출력합니다.

### <a name="sample-output"></a>샘플 출력

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>출력 확인

[Azure Portal](https://portal.azure.com)에서 **Purview 계정** 페이지로 이동하고 위의 코드를 사용하여 만든 계정을 확인합니다. 

## <a name="delete-purview-account"></a>Purview 계정 삭제

Purview 계정을 프로그래밍 방식으로 삭제하려면 프로그램에 다음 코드 줄을 추가합니다. 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Purview 계정 이름을 사용할 수 있는지 확인합니다.

purview 계정의 가용성을 확인하려면 다음 코드를 사용합니다. 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

위의 코드는 이름을 사용할 수 있는 경우 'True'를 인쇄하고, 이름을 사용할 수 없으면 'False'를 인쇄합니다.


## <a name="next-steps"></a>다음 단계

이 자습서의 코드는 purview 계정을 만들고, purview 계정을 삭제하고, purview 계정의 이름 사용 가능 여부를 확인합니다. 이제 .NET SDK를 다운로드하고 Purview 계정에 대해 수행할 수 있는 다른 리소스 공급자 작업에 대해 알아볼 수 있습니다.

다음 문서로 계속 진행하여 사용자가 Azure Purview 계정에 액세스할 수 있도록 허용하는 방법을 알아보세요. 

> [!div class="nextstepaction"]
> [Azure Purview 계정에 사용자 추가](catalog-permissions.md)
