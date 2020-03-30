---
title: Visual Studio를 사용하여 ASP.NET 프로젝트에 Key Vault 지원 추가 - Azure Key Vault | Microsoft Docs
description: 이 자습서를 통해 ASP.NET 또는 ASP.NET Core 웹 애플리케이션에 Key Vault 지원을 추가하는 방법을 배울 수 있습니다.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 1c12cf4a7bd097c6d33d032065734b477920644b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457005"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio 연결된 서비스를 사용하여 웹 애플리케이션에 Key Vault 추가

이 자습서에서는 ASP.NET Core를 사용하든 아니면 모든 종류의 ASP.NET 프로젝트를 사용하든 Visual Studio에서 웹 프로젝트에 대한 사용자의 비밀을 관리하기 위해 Azure Key Vault를 사용하여 시작해야 하는 모든 항목을 쉽게 추가하는 방법에 대해 알아봅니다. Visual Studio에서 연결된 서비스 기능을 사용하면 Visual Studio에서 Azure의 키 볼트에 연결하는 데 필요한 모든 NuGet 패키지 및 구성 설정을 자동으로 추가할 수 있습니다.

Key Vault를 사용하도록 설정하기 위해 프로젝트에서 연결된 서비스에서 수행하는 변경 내용에 대한 자세한 내용은 [Key Vault 연결된 서비스 - 내 ASP.NET 4.7.1 프로젝트에서 변경된 내용](#how-your-aspnet-framework-project-is-modified) 또는 [Key Vault 연결된 서비스 - 내 ASP.NET Core 프로젝트에서 변경된 내용](#how-your-aspnet-core-project-is-modified)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- **Azure 구독**. 구독이 없는 경우 [무료 계정에](https://azure.microsoft.com/pricing/free-trial/)가입하세요.
- **Visual Studio 2019 버전 16.3** 이상 또는 **웹 개발** 워크로드가 설치된 Visual **Studio 2017 버전 15.7입니다.** [여기에서 다운로드하세요](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Visual Studio 2017의 ASP.NET(코어아님)의 경우 기본적으로 설치되지 않은 .NET Framework 4.7.1 이상 개발 도구가 필요합니다. 설치하려면 Visual Studio 설치 관리자를 시작하고, **수정**을 선택한 다음, **개별 구성 요소**를 선택한 다음, 오른쪽에서 **ASP.NET 및 웹 개발**을 확장하고 **.NET Framework 4.7.1 개발 도구**를 선택합니다.
- ASP.NET 4.7.1 이상이거나 코어 2.0 이상 웹 프로젝트를 ASP.NET.

## <a name="add-key-vault-support-to-your-project"></a>프로젝트에 Key Vault 지원 추가

시작하기 전에 Visual Studio에 로그인했는지 확인합니다. Azure 구독에 사용하는 것과 동일한 계정으로 로그인합니다. 그런 다음 ASP.NET 4.7.1 이상 또는 코어 2.0 웹 프로젝트를 ASP.NET 다음 단계를 수행합니다.

1. **솔루션 탐색기에서**키 볼트 지원을 추가할 프로젝트를 마우스 오른쪽 단추로 클릭하고**연결된 서비스** **추가를** > 선택합니다.
   연결된 서비스 페이지가 프로젝트에 추가할 수 있는 서비스와 함께 표시됩니다.
1. 사용 가능한 서비스 메뉴에서 **Azure Key Vault로 비밀 보호**를 선택합니다.

   ![“Azure Key Vault로 비밀 보호” 선택](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. 사용할 구독을 선택한 다음 새 또는 기존 키 자격 증명 모음을 선택합니다. 새 키 볼트를 선택하면 **편집** 링크가 나타납니다. 새 키 자격 증명 모음을 구성하려면 선택합니다.

   ![구독 선택](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. **Azure 키 자격 증명 모음 편집에서**키 자격 증명 모음에 사용할 이름을 입력합니다.

1. 기존 리소스 그룹을 선택하거나 자동으로 생성된 고유 이름으로 새 **리소스 그룹을**만들도록 선택합니다.  다른 이름으로 새 그룹을 만들려면 [Azure portal을](https://portal.azure.com)사용한 다음 페이지를 닫고 다시 시작하여 리소스 그룹 목록을 다시 로드할 수 있습니다.
1. 키 자격 증명 모음을 작성할 **위치를** 선택합니다. 웹 애플리케이션이 Azure에서 호스트되는 경우 최적의 성능을 위해 웹 애플리케이션을 호스팅하는 지역을 선택합니다.
1. 가격 **책정 계층을**선택합니다. 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)을 참조하세요.
1. 구성 선택 사항을 수락하려면 **확인을** 선택합니다.
1. 기존 키 볼트를 선택하거나 Visual Studio의 Azure 키 자격 증명 모음 탭에서 새 키 **자격 증명 모음을** 구성한 후 연결된 서비스 **추가를** 선택합니다.
1. 이 **키 볼트 링크에 저장된 비밀 관리를** 선택하여 키 볼트의 **비밀** 페이지를 엽니다. 페이지 또는 프로젝트를 닫은 경우 **모든 서비스를** 선택하여 [Azure 포털로](https://portal.azure.com) 이동할 수 있으며 **보안에서** **키 자격 증명 모음을**선택한 다음 키 자격 증명 모음을 선택할 수 있습니다.
1. 만든 키 볼트의 키 높이 섹션에서 **비밀을**선택한 다음 **생성/가져오기**를 선택합니다.

   ![비밀 생성/가져오기](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. *MySecret와* 같은 비밀을 입력하고 문자열 값을 테스트로 지정한 다음 **만들기** 단추를 선택합니다.

   ![비밀 만들기](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (선택 사항) 다른 비밀을 입력합니다. 단, 이번에는 *비밀-MySecret*이라는 이름을 지정하여 범주에 포함시킵니다. 이 구문은 비밀 "MySecret"를 포함하는 범주 "비밀"을 지정합니다.

이제 코드에서 비밀에 액세스할 수 있습니다. 다음 단계는 ASP.NET 4.7.1 또는 ASP.NET Core를 사용하는지에 따라 달라집니다.

## <a name="access-your-secrets-in-code-aspnet-core"></a>코드의 비밀에 액세스 (ASP.NET 코어)

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리를 선택합니다.** **찾아보기** 탭에서 다음 두 NuGet 패키지를 찾아 설치합니다: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 및 .NET 코어 2의 경우 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) 또는 .NET Core 3에 대해[Microsoft.Azure.KeyVault.Core를](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)추가합니다.

1. .NET Core 2의 `Program.cs` 경우 탭을 `BuildWebHost` 선택하고 프로그램 클래스의 정의를 다음으로 변경합니다.

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   .NET Core 3의 경우 다음 코드를 사용합니다.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. 다음으로 *Index.cshtml.cs* 같은 페이지 파일 중 하나를 열고 다음 코드를 작성합니다.
   1. 지시문을 사용하여 `Microsoft.Extensions.Configuration` 이에 대한 참조를 포함하십시오.

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. 구성 변수를 추가합니다.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. 이 생성자 추가 하거나 기존 생성자 대체 이:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. `OnGet` 메서드를 업데이트합니다. 위의 명령에서 만든 비밀 이름으로 여기에 표시된 자리 표시자 값을 업데이트합니다.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. 런타임 시 값을 확인하려면 `ViewData["Message"]` *.cshtml* 파일에 표시할 코드를 추가하여 메시지에 암호를 표시합니다.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

로컬에서 앱을 실행하여 키 볼트에서 비밀이 성공적으로 획득되었는지 확인할 수 있습니다.

## <a name="access-your-secrets-aspnet"></a>당신의 비밀에 액세스 (ASP.NET)

web.config 파일이 런타임시 참 값으로 대체되는 `appSettings` 요소에 더미 값을 가지게 되도록 구성을 설정할 수 있습니다. 그런 다음 `ConfigurationManager.AppSettings` 데이터 구조를 통해 이 정보에 액세스할 수 있습니다.

1. web.config 파일을 편집합니다.  appSettings 태그를 찾고, 특성을 `configBuilders="AzureKeyVault"`추가하고, 줄을 추가합니다.

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. `About` *HomeController.cs*메서드를 편집하여 확인값을 표시합니다.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. 디버거 에서 로컬로 앱을 **실행하고, About** 탭으로 전환하고, 키 볼트의 값이 표시되는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 Key Vault와 관련된 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. 리소스 **그룹 이름 유형:** 상자에서 리소스 그룹의 이름을 입력하고 **삭제를**선택합니다.

## <a name="troubleshooting"></a>문제 해결

키 볼트가 Visual Studio에 로그인한 계정과 다른 Microsoft 계정에서 실행 중인 경우(예: 키 볼트가 직장 계정에서 실행중이지만 Visual Studio에서 개인 계정을 사용하고 있음) Program.cs 파일에 오류가 발생합니다. 을 사용하여 Visual Studio에서 키 볼트에 액세스할 수 없습니다. 이 문제를 해결하려면

1. [Azure 포털로](https://portal.azure.com) 이동하여 키 자격 증명 모음을 엽니다.

1. **액세스 정책을**선택한 다음 액세스 **정책 추가를**선택하고 로그온한 계정을 보안 주체로 선택합니다.

1. 비주얼 스튜디오에서 **파일** > **계정 설정을**선택합니다.
**모든 계정** 섹션에서 **계정 추가를** 선택합니다. 액세스 정책의 보안 주체로 선택한 계정으로 로그인합니다.

1. **도구** > **옵션을**선택하고 Azure **서비스 인증을**찾습니다. 그런 다음 Visual Studio에 방금 추가한 계정을 선택합니다.

이제 응용 프로그램을 디버깅하면 Visual Studio가 키 볼트가 있는 계정에 연결합니다.

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET 핵심 프로젝트 수정 방법

이 섹션에서는 Visual Studio를 사용하여 Key Vault 연결 서비스를 추가할 때 ASP.NET 프로젝트에 대한 정확한 변경 사항을 식별합니다.

### <a name="added-references-for-aspnet-core"></a>ASP.NET 코어에 대한 참조추가

프로젝트 파일 .NET 참조 및 NuGet 패키지 참조에 영향을 줍니다.

| Type | 참고 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>코어에 ASP.NET 파일 추가

- `ConnectedService.json`추가되어 설명서의 링크에 대한 일부 정보를 기록합니다.

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET 코어에 대한 프로젝트 파일 변경

- 연결된 서비스 항목 그룹 `ConnectedServices.json` 및 파일을 추가했습니다.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>ASP.NET 코어에 대한 launchsettings.json 변경

- IIS Express 프로필 및 웹 프로젝트 이름과 일치하는 프로필에 다음 환경 변수 항목이 추가됩니다.

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>ASP.NET 코어에 대 한 Azure에 변경 사항

- 리소스 그룹이 생성됩니다. (또는 기존 그룹이 사용됩니다.)
- 지정된 리소스 그룹에 Key Vault가 생성됩니다.

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET 프레임워크 프로젝트를 수정하는 방법

이 섹션에서는 Visual Studio를 사용하여 Key Vault 연결 서비스를 추가할 때 ASP.NET 프로젝트에 대한 정확한 변경 사항을 식별합니다.

### <a name="added-references-for-aspnet-framework"></a>ASP.NET 프레임워크에 대한 참조추가

프로젝트 파일 .NET 참조 `packages.config` 및 (NuGet 참조)에 영향을 줍니다.

| Type | 참고 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>ASP.NET 프레임워크용 파일 추가

- `ConnectedService.json`추가되어 연결된 서비스 공급자, 버전 및 설명서에 대한 링크에 대한 일부 정보를 기록합니다.

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET 프레임워크에 대한 프로젝트 파일 변경

- 연결된 서비스 ItemGroup 및 ConnectedServices.json 파일이 추가됩니다.
- [추가된 참조](#added-references-for-aspnet-framework) 섹션에 설명된 .NET 어셈블리에 대한 참조입니다.

### <a name="webconfig-or-appconfig-changes"></a>web.config 또는 app.config 변경 내용

- 다음 구성 항목을 추가했습니다.

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>ASP.NET 프레임워크용 Azure의 변경 사항

- 리소스 그룹이 생성됩니다. (또는 기존 그룹이 사용됩니다.)
- 지정된 리소스 그룹에 Key Vault가 생성됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서를 따르는 경우 Key Vault 권한은 자체 Azure 구독으로 실행되도록 설정되어 있지만 프로덕션 시나리오에서는 바람직하지 않을 수 있습니다. 관리되는 ID를 만들어 앱에 대한 Key Vault 액세스를 관리할 수 있습니다. [관리되는 ID로 키 볼트 인증 제공](/azure/key-vault/managed-identity)을 참조하십시오.

키 볼트 [개발자 가이드를](key-vault-developers-guide.md)읽고 키 볼트 개발에 대해 자세히 알아보십시오.
