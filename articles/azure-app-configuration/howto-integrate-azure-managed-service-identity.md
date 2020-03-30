---
title: Azure 관리 ID를 사용하여 인증
titleSuffix: Azure App Configuration
description: Azure 관리 ID를 사용하여 Azure 앱 구성에 인증
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: bf97a1eae758778efc8d800666af4a5fcb574429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056830"
---
# <a name="integrate-with-azure-managed-identities"></a>Azure Managed Identities와 통합

Azure Active Directory [관리 ID는](../active-directory/managed-identities-azure-resources/overview.md) 클라우드 응용 프로그램에 대한 비밀 관리를 단순화합니다. 관리되는 ID를 사용하여 코드는 실행되는 Azure 서비스에 대해 만든 서비스 주체를 사용할 수 있습니다. Azure Key Vault 또는 로컬 연결 문자열에 저장된 별도의 자격 증명 대신 관리 ID를 사용합니다. 

Azure 앱 구성 및 .NET 코어, .NET 프레임워크 및 Java Spring 클라이언트 라이브러리는 ID 지원을 기본으로 관리했습니다. 이 ID를 사용할 필요는 없지만 관리되는 ID는 기밀이 포함된 액세스 토큰이 필요하지 않습니다. 코드는 서비스 끝점만 사용하여 앱 구성 저장소에 액세스할 수 있습니다. 비밀을 노출하지 않고 코드에 이 URL을 직접 포함할 수 있습니다.

이 문서에서는 관리되는 ID를 활용하여 앱 구성에 액세스하는 방법을 보여 주며 있습니다. 빠른 시작에 소개된 웹앱을 기반으로 합니다. 계속하기 전에 [먼저 앱 구성을 사용하여 ASP.NET 핵심 앱을 만듭니다.](./quickstart-aspnet-core-app.md)

이 문서에서는 앱 구성의 키 볼트 참조와 함께 관리되는 ID를 사용하는 방법도 보여 주며, 이 문서에서는 관리되는 ID를 사용하는 방법을 보여 주기도 합니다. 단일 관리 ID를 사용하면 키 볼트의 비밀과 앱 구성의 구성 값에 원활하게 액세스할 수 있습니다. 이 기능을 탐색하려면 먼저 [ASP.NET 핵심을 사용하여 키 높이 참조 사용을](./use-key-vault-references-dotnet-core.md) 완료합니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * App Configuration에 대한 관리 ID 액세스 권한을 부여합니다.
> * App Configuration에 연결할 때 관리 ID를 사용하도록 앱을 구성합니다.
> * 선택적으로 앱 구성 키 볼트 참조를 통해 키 자격 증명 모음에 연결할 때 관리되는 ID를 사용하도록 앱을 구성합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

* [.NET 코어 SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell 구성](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>관리형 ID 추가

포털에서 관리되는 ID를 설정하려면 먼저 응용 프로그램을 만든 다음 기능을 사용하도록 설정합니다.

1. 평소와 같이 [Azure 포털에서](https://portal.azure.com) 앱 서비스 인스턴스를 만듭니다. 포털에서 이동합니다.

1. 왼쪽 창에서 **설정** 그룹까지 아래로 스크롤하고 **ID**를 선택합니다.

1. **시스템 할당** 탭에서 **상태**를 **켜기**로 전환하고 **저장**을 선택합니다.

1. 시스템 할당된 관리 ID를 사용하도록 설정하라는 메시지가 표시되면 **예라고** 대답합니다.

    ![App Service에서 관리형 ID 설정](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>App Configuration에 대한 액세스 권한 부여

1. Azure [포털에서](https://portal.azure.com) **모든 리소스를** 선택하고 빠른 시작에서 만든 앱 구성 저장소를 선택합니다.

1. **액세스 제어(IAM)를**선택합니다.

1. **액세스 권한 확인** 탭의 **역할 할당 추가** 카드 UI에서 **추가**를 선택합니다.

1. **역할**에서 **앱 구성 데이터 판독기를**선택합니다. **다음에 대한 액세스 할당**의 **시스템이 할당한 관리 ID** 아래에서 **App Service**를 선택합니다.

1. **구독** 아래에서 Azure 구독을 선택합니다. 앱의 App Service 리소스를 선택합니다.

1. **저장**을 선택합니다.

    ![관리형 ID 추가](./media/add-managed-identity.png)

1. 선택 사항: 키 볼트에 대한 액세스 권한도 부여하려는 경우 [관리되는 ID를 사용하여 키 자격 증명 정보 제공의](https://docs.microsoft.com/azure/key-vault/managed-identity)지침을 따릅니다.

## <a name="use-a-managed-identity"></a>관리 ID 사용

1. *Azure.Identity* 패키지에 대한 참조 추가:

    ```cli
    dotnet add package Azure.Identity
    ```

1. 앱 구성 저장소의 끝점을 찾습니다. 이 URL은 Azure 포털의 저장소에 대한 **액세스 키** 탭에 나열됩니다.

1. *appsettings.json*을 열고 다음 스크립트를 추가합니다. 대괄호를 포함한 * \<service_endpoint>* 앱 구성 저장소의 URL로 바꿉습니다. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. *Program.cs*열고 및 `Azure.Identity` `Microsoft.Azure.Services.AppAuthentication` 네임스페이스에 대한 참조를 추가합니다.

    ```csharp-interactive
    using Azure.Identity;
    ```

1. 앱 구성에 직접 저장된 값에만 액세스하려면 `CreateWebHostBuilder` 메서드를 대체하여 메서드를 `config.AddAzureAppConfiguration()` 업데이트합니다.

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
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. 앱 구성 값과 키 볼트 참조를 모두 사용하려면 아래와 같이 *Program.cs* 업데이트합니다. 이 코드는 `KeyVaultClient` a를 `AzureServiceTokenProvider` 사용하여 새 를 만들고 `UseAzureKeyVault` 메서드에 대한 호출에 이 참조를 전달합니다.

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
                    })
                    .UseStartup<Startup>());
    ```
    ---

    이제 다른 앱 구성 키와 마찬가지로 키 볼트 참조에 액세스할 수 있습니다. 구성 공급자는 Key `KeyVaultClient` Vault를 인증하고 값을 검색하도록 구성한 을 사용합니다.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>로컬 Git에서 배포

Kudu 빌드 서버를 사용하여 앱에 로컬 Git 배포를 활성화하는 가장 쉬운 방법은 [Azure Cloud Shell을](https://shell.azure.com)사용하는 것입니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Kudu로 로컬 Git을 사용하도록 설정
앱에 대한 로컬 git 리포지토리가 없는 경우 초기화해야 합니다. 로컬 git 리포지토리를 초기화하려면 앱의 프로젝트 디렉터리에서 다음 명령을 실행합니다.

```cmd
git init
git add .
git commit -m "Initial version"
```

Kudu 빌드 서버를 사용하여 앱에 대한 로컬 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) Git 배포를 사용하려면 클라우드 셸에서 실행합니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

이 명령은 다음과 같은 출력과 유사한 것을 제공합니다.

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>프로젝트 배포

로컬 _터미널 창에서_로컬 Git 리포지토리에 Azure remote를 추가합니다. _ \<url>_ [Kudu를 사용하여 로컬 Git 활성화에서](#enable-local-git-with-kudu)가져온 Git 원격의 URL로 바꿉을 바꿉습니다.

```bash
git remote add azure <url>
```

다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. 암호를 입력하라는 메시지가 표시되면 [배포 사용자 구성](#configure-a-deployment-user)에서 만든 암호를 입력합니다. Azure Portal에 로그인하는 데 사용하는 암호는 사용하지 마세요.

```bash
git push azure master
```

출력에 ASP.NET용 MSBuild, Node.js용 `npm install` 및 Python용 `pip install`과 같은 런타임 관련 자동화가 표시될 수 있습니다.

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기

브라우저를 통해 웹앱으로 이동하여 콘텐츠가 배포되었는지 확인합니다.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>다른 언어의 관리형 ID 사용

.NET Framework 및 Java Spring 용 App Configuration 공급자에는 관리형 ID에 대한 지원 기능이 내장되어 있습니다. 이러한 공급자 중 하나를 구성할 때 전체 연결 문자열 대신 저장소의 URL 끝점을 사용할 수 있습니다. 

예를 들어 빠른 시작에서 만든 .NET Framework 콘솔 앱을 업데이트하여 *App.config* 파일에서 다음 설정을 지정할 수 있습니다.

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
이 자습서에서는 앱 구성에 대한 액세스를 간소화하고 앱에 대한 자격 증명 관리를 개선하기 위해 Azure 관리 ID를 추가했습니다. App Configuration을 사용하는 방법에 대해 자세히 알아보려면 Azure CLI 샘플로 계속 진행하세요.

> [!div class="nextstepaction"]
> [CLI 샘플](./cli-samples.md)
