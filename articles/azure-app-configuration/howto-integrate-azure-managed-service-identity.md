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
ms.openlocfilehash: 7a9eb992ff0cb98fdae2920da2beeda0bbd8941b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877537"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>관리 ID를 사용하여 App Configuration 액세스

Azure Active Directory [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 클라우드 애플리케이션에 대한 비밀 관리를 간소화합니다. 관리 ID를 사용하면 실행되는 Azure 서비스에 대해 만들어진 서비스 주체를 코드가 사용할 수 있습니다. Azure Key Vault 또는 로컬 연결 문자열에 저장된 별도의 자격 증명 대신 관리 ID를 사용합니다.

Azure App Configuration과 해당 .NET Core, .NET Framework, Java Spring 클라이언트 라이브러리에는 관리 ID 지원이 기본적으로 제공됩니다. 관리 ID를 반드시 사용할 필요는 없지만 사용하는 경우 비밀이 포함된 액세스 토큰이 필요하지 않습니다. 코드는 서비스 엔드포인트만 사용하여 App Configuration 저장소에 액세스할 수 있습니다. 비밀을 노출하지 않고도 해당 URL을 코드에 직접 포함할 수 있습니다.

이 문서에서는 관리 ID를 활용하여 App Configuration에 액세스하는 방법을 보여 줍니다. 빠른 시작에 소개된 웹앱을 기반으로 합니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 ASP.NET Core 앱을 만듭니다](./quickstart-aspnet-core-app.md).

> [!NOTE]
> 이 문서에서는 Azure App Service를 예로 사용하지만, 관리 ID를 지원하는 다른 Azure 서비스(예: [Azure Kubernetes Service](../aks/use-azure-ad-pod-identity.md), [Azure Virtual Machine](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md), [Azure Container Instances](../container-instances/container-instances-managed-identity.md))에도 동일한 개념이 적용됩니다. 워크로드가 해당 서비스 중 하나에서 호스트되는 경우 서비스의 관리 ID 지원도 활용할 수 있습니다.

이 문서에서는 관리 ID를 App Configuration의 Key Vault 참조와 함께 사용할 수 있는 방법도 보여 줍니다. 단일 관리 ID를 사용하여 Key Vault의 비밀과 App Configuration의 구성 값에 모두 원활하게 액세스할 수 있습니다. 해당 기능을 살펴보려면 먼저 [ASP.NET Core를 사용하여 Key Vault 참조 사용](./use-key-vault-references-dotnet-core.md)을 완료합니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * App Configuration에 대한 관리 ID 액세스 권한을 부여합니다.
> * App Configuration에 연결할 때 관리 ID를 사용하도록 앱을 구성합니다.
> * 필요에 따라 App Configuration Key Vault 참조를 통해 Key Vault에 연결할 때 관리 ID를 사용하도록 앱을 구성합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 항목이 필요합니다.

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Azure Cloud Shell 구성](../cloud-shell/quickstart.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>관리형 ID 추가

포털에서 관리 ID를 설정하려면 먼저 애플리케이션을 만든 다음, 해당 기능을 사용합니다.

1. 평소와 같이 [Azure Portal](https://portal.azure.com)에서 App Services 인스턴스를 만듭니다. 포털에서 이동합니다.

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

1. 선택 사항: Key Vault에 대한 액세스 권한도 부여하려는 경우 [Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)의 지침을 따릅니다.

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

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    > [!NOTE]
    > **사용자가 할당한 관리 ID** 를 사용하려는 경우 [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential)을 만들 때 clientId를 지정해야 합니다.
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >[Azure 리소스에 대한 관리 ID FAQ](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)에 설명된 것처럼 사용되는 관리 ID를 확인하는 기본 방법이 있습니다. 이 경우 Azure ID 라이브러리는 예를 들어 나중에 새 사용자가 할당한 관리 ID가 추가되거나 시스템이 할당한 관리 ID를 사용하는 경우 발생할 수 있는 런타임 문제를 방지하기 위해 원하는 ID를 지정하도록 강제합니다. 따라서 하나의 사용자가 할당한 관리 ID만 정의되어 있고 시스템이 할당한 관리 ID가 없는 경우에도 clientId를 지정해야 합니다.


1. App Configuration 값과 Key Vault 참조를 모두 사용하려면 아래와 같이 *Program.cs* 를 업데이트합니다. 해당 코드는 `ConfigureKeyVault`의 일부로 `SetCredential`을 호출하여 Key Vault에 인증할 때 사용할 자격 증명을 구성 공급자에게 알립니다.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   var credentials = new ManagedIdentityCredential();

                   config.AddAzureAppConfiguration(options =>
                   {
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                              .ConfigureKeyVault(kv =>
                              {
                                 kv.SetCredential(credentials);
                              });
                   });
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    var credentials = new ManagedIdentityCredential();

                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                               .ConfigureKeyVault(kv =>
                               {
                                   kv.SetCredential(credentials);
                               });
                    });
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    이제 다른 App Configuration 키와 마찬가지로 Key Vault 참조에 액세스할 수 있습니다. 구성 공급자는 `ManagedIdentityCredential`을 사용하여 Key Vault에 인증하고 값을 검색합니다.

    > [!NOTE]
    > `ManagedIdentityCredential`은 관리 ID 인증을 지원하는 서비스의 Azure 환경에서만 작동합니다. 로컬 환경에서는 작동하지 않습니다. 코드가 로컬 및 Azure 환경에서 모두 작동하도록 [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential)을 사용하면 관리 ID를 비롯한 몇 가지 인증 옵션으로 대체됩니다.
    > 
    > Azure에 배포된 상태에서 **사용자가 할당한 관리 ID** 를 `DefaultAzureCredential`과 함께 사용하려는 경우 [clientId를 지정](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential)합니다.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>로컬 Git에서 배포

Kudu 빌드 서버를 사용하여 앱에 로컬 Git 배포를 사용하는 가장 쉬운 방법은 [Azure Cloud Shell](https://shell.azure.com)을 사용하는 것입니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Kudu로 로컬 Git을 사용하도록 설정
앱에 대한 로컬 git 리포지토리가 없으면 하나를 초기화해야 합니다. 로컬 git 리포지토리를 초기화하려면 앱의 프로젝트 디렉터리에서 다음 명령을 실행합니다.

```cmd
git init
git add .
git commit -m "Initial version"
```

Kudu 빌드 서버를 사용하여 앱에 로컬 Git 배포를 사용하도록 설정하려면 Cloud Shell에서 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az_webapp_deployment_source_config_local_git)을 실행합니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

해당 명령은 다음과 비슷한 출력을 제공합니다.

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>프로젝트 배포

‘로컬 터미널 창’에서 로컬 Git 리포지토리에 Azure 원격을 추가합니다. _\<url>_ 을 [Kudu로 로컬 Git을 사용](#enable-local-git-with-kudu)에서 가져온 Git 원격의 URL로 바꿉니다.

```bash
git remote add azure <url>
```

다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. 암호를 입력하라는 메시지가 표시되면 [배포 사용자 구성](#configure-a-deployment-user)에서 만든 암호를 입력합니다. Azure Portal에 로그인하는 데 사용하는 암호는 사용하지 마세요.

```bash
git push azure main
```

출력에 ASP.NET용 MSBuild, Node.js용 `npm install` 및 Python용 `pip install`과 같은 런타임 관련 자동화가 표시될 수 있습니다.

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기

브라우저를 통해 웹앱으로 이동하여 콘텐츠가 배포되었는지 확인합니다.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>다른 언어의 관리형 ID 사용

.NET Framework 및 Java Spring 용 App Configuration 공급자에는 관리형 ID에 대한 지원 기능이 내장되어 있습니다. 해당 공급자 중 하나를 구성할 때 전체 연결 문자열 대신 저장소의 URL 엔드포인트를 사용할 수 있습니다.

예를 들어 빠른 시작에서 만든 .NET Framework 콘솔 앱을 업데이트하여 *App.config* 파일에 다음 설정을 지정할 수 있습니다.

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure 관리 ID를 추가하여 App Configuration에 대한 액세스 관리를 간소화하고 앱에 대한 자격 증명 관리를 개선했습니다. App Configuration을 사용하는 방법에 대해 자세히 알아보려면 Azure CLI 샘플로 계속 진행하세요.

> [!div class="nextstepaction"]
> [CLI 샘플](./cli-samples.md)