---
title: 관리 ID를 사용하여 App Configuration 액세스
titleSuffix: Azure App Configuration
description: 관리 ID를 사용하여 Azure App Configuration에 인증
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: ff7c2b6ced87c8254283923a9163e51f06ae6ef6
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114298177"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>관리 ID를 사용하여 App Configuration 액세스

Azure Active Directory [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 클라우드 애플리케이션에 대한 비밀 관리를 간소화합니다. 관리 ID를 사용하면 실행되는 Azure 서비스에 대해 만들어진 서비스 주체를 코드가 사용할 수 있습니다. Azure Key Vault 또는 로컬 연결 문자열에 저장된 별도의 자격 증명 대신 관리 ID를 사용합니다.

Azure App Configuration과 해당 .NET Core, .NET Framework, Java Spring 클라이언트 라이브러리에는 관리 ID 지원이 기본적으로 제공됩니다. 관리 ID를 반드시 사용할 필요는 없지만 사용하는 경우 비밀이 포함된 액세스 토큰이 필요하지 않습니다. 코드는 서비스 엔드포인트만 사용하여 App Configuration 저장소에 액세스할 수 있습니다. 비밀을 노출하지 않고도 해당 URL을 코드에 직접 포함할 수 있습니다.

이 문서에서는 관리 ID를 활용하여 App Configuration에 액세스하는 방법을 보여 줍니다. 빠른 시작에 소개된 웹앱을 기반으로 합니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 ASP.NET Core 앱을 만듭니다](./quickstart-aspnet-core-app.md).

> [!IMPORTANT]
> 관리 ID는 로컬로 실행되는 애플리케이션을 인증하는 데 사용할 수 없습니다. 관리 ID를 지원하는 Azure 서비스에 애플리케이션을 배포해야 합니다. 이 문서에서는 Azure App Service를 예로 사용하지만, 관리 ID를 지원하는 다른 Azure 서비스(예: [Azure Kubernetes Service](../aks/use-azure-ad-pod-identity.md), [Azure Virtual Machine](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md), [Azure Container Instances](../container-instances/container-instances-managed-identity.md))에도 동일한 개념이 적용됩니다. 워크로드가 해당 서비스 중 하나에서 호스트되는 경우 서비스의 관리 ID 지원도 활용할 수 있습니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * App Configuration에 대한 관리 ID 액세스 권한을 부여합니다.
> * App Configuration에 연결할 때 관리 ID를 사용하도록 앱을 구성합니다.


## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 항목이 필요합니다.

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Azure Cloud Shell 구성](../cloud-shell/quickstart.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>관리형 ID 추가

포털에서 관리 ID를 설정하려면 먼저 애플리케이션을 만든 다음, 해당 기능을 사용합니다.

1. [Azure Portal](https://portal.azure.com)의 App Service 리소스에 액세스합니다. 작업할 기존 App Services 리소스가 없는 경우 리소스를 만듭니다. 

1. 왼쪽 창에서 **설정** 그룹까지 아래로 스크롤하고 **ID** 를 선택합니다.

1. **시스템 할당** 탭에서 **상태** 를 **켜기** 로 전환하고 **저장** 을 선택합니다.

1. 시스템이 할당한 관리 ID를 사용하라는 메시지가 표시되면 **예** 라고 대답합니다.

    ![App Service에서 관리형 ID 설정](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>App Configuration에 대한 액세스 권한 부여

1. [Azure Portal](https://portal.azure.com)에서 **모든 리소스** 를 선택하고, 빠른 시작에서 만든 App Configuration 저장소를 선택합니다.

1. **액세스 제어(IAM)** 를 선택합니다.

1. **액세스 권한 확인** 탭의 **역할 할당 추가** 카드 UI에서 **추가** 를 선택합니다.

1. **역할** 에서 **App Configuration 데이터 판독기** 를 선택합니다. **다음에 대한 액세스 할당** 의 **시스템이 할당한 관리 ID** 아래에서 **App Service** 를 선택합니다.

1. **구독** 아래에서 Azure 구독을 선택합니다. 앱의 App Service 리소스를 선택합니다.

1. **저장** 을 선택합니다.

    ![관리형 ID 추가](./media/add-managed-identity.png)


## <a name="use-a-managed-identity"></a>관리 ID 사용

1. *Azure.Identity* 패키지에 대한 참조를 추가합니다.

    ```bash
    dotnet add package Azure.Identity
    ```

1. App Configuration 저장소에 대한 엔드포인트를 찾습니다. 해당 URL은 Azure Portal의 저장소에 대한 **액세스 키** 탭에 나열됩니다.

1. *appsettings.json* 을 열고 다음 스크립트를 추가합니다. *\<service_endpoint>* (대괄호 포함)를 App Configuration 저장소의 URL로 바꿉니다.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. *Program.cs* 를 열고 `Azure.Identity` 및 `Microsoft.Azure.Services.AppAuthentication` 네임스페이스에 대한 참조를 추가합니다.

    ```csharp-interactive
    using Azure.Identity;
    ```

1. App Configuration에 직접 저장된 값에만 액세스하려는 경우 `config.AddAzureAppConfiguration()` 메서드(`Microsoft.Azure.AppConfiguration.AspNetCore` 패키지에 있음)를 대체하여 `CreateWebHostBuilder` 메서드를 업데이트합니다.

    > [!IMPORTANT]
    > .NET Core 3.0에서 `CreateHostBuilder`는 `CreateWebHostBuilder`를 대체합니다.  사용자 환경에 따라 올바른 구문을 선택합니다.

    ### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ---

    > [!NOTE]
    > **사용자가 할당한 관리 ID** 를 사용하려는 경우 [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential)을 만들 때 clientId를 지정해야 합니다.
    >```csharp
    >config.AddAzureAppConfiguration(options =>
    >       {
    >           options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential("<your_clientId>"))
    >        });
    >```
    >[Azure 리소스에 대한 관리 ID FAQ](../active-directory/managed-identities-azure-resources/known-issues.md)에 설명된 것처럼 사용되는 관리 ID를 확인하는 기본 방법이 있습니다. 이 경우 Azure ID 라이브러리는 예를 들어 나중에 새 사용자가 할당한 관리 ID가 추가되거나 시스템이 할당한 관리 ID를 사용하는 경우 발생할 수 있는 런타임 문제를 방지하기 위해 원하는 ID를 지정하도록 강제합니다. 따라서 하나의 사용자가 할당한 관리 ID만 정의되어 있고 시스템이 할당한 관리 ID가 없는 경우에도 clientId를 지정해야 합니다.
 
    

## <a name="deploy-your-application"></a>애플리케이션 배포

관리 ID를 사용하려면 Azure 서비스에 앱을 배포해야 합니다. 관리 ID는 로컬로 실행되는 앱의 인증에 사용할 수 없습니다. [앱 구성으로 ASP.NET Core 앱 만들기](./quickstart-aspnet-core-app.md) 빠른 시작에서 만들고 관리 ID를 사용하도록 수정한 .NET Core 앱을 배포하려면 [웹 앱 게시](../app-service/quickstart-dotnetcore.md?pivots=development-environment-vs&tabs=netcore31#publish-your-web-app)의 지침을 따르세요.

App Service 외에도 다른 많은 Azure 서비스는 관리 ID를 지원합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure 관리 ID를 추가하여 App Configuration에 대한 액세스 관리를 간소화하고 앱에 대한 자격 증명 관리를 개선했습니다. App Configuration을 사용하는 방법에 대해 자세히 알아보려면 Azure CLI 샘플로 계속 진행하세요.

> [!div class="nextstepaction"]
> [CLI 샘플](./cli-samples.md)