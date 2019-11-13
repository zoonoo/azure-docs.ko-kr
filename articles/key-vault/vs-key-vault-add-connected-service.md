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
ms.openlocfilehash: 4cbc4044b5d1270cecd1a271d2a1db02801650dd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012764"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio 연결된 서비스를 사용하여 웹 애플리케이션에 Key Vault 추가

이 자습서에서는 ASP.NET Core를 사용하든 아니면 모든 종류의 ASP.NET 프로젝트를 사용하든 Visual Studio에서 웹 프로젝트에 대한 사용자의 비밀을 관리하기 위해 Azure Key Vault를 사용하여 시작해야 하는 모든 항목을 쉽게 추가하는 방법에 대해 알아봅니다. Visual Studio의 연결된 서비스 기능을 사용 하 여 Visual Studio에서 Azure의 Key Vault에 연결 하는 데 필요한 모든 NuGet 패키지 및 구성 설정을 자동으로 추가 하도록 할 수 있습니다.

Key Vault를 사용하도록 설정하기 위해 프로젝트에서 연결된 서비스에서 수행하는 변경 내용에 대한 자세한 내용은 [Key Vault 연결된 서비스 - 내 ASP.NET 4.7.1 프로젝트에서 변경된 내용](#how-your-aspnet-framework-project-is-modified) 또는 [Key Vault 연결된 서비스 - 내 ASP.NET Core 프로젝트에서 변경된 내용](#how-your-aspnet-core-project-is-modified)을 참조하세요.

## <a name="prerequisites"></a>선행 조건

- **Azure 구독**. 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록 합니다.
- **웹 개발** 워크 로드가 설치 된 **visual studio 2019 버전 16.3 Preview 1** 이상 또는 **visual studio 2017 버전 15.7** 입니다. [여기에서 다운로드하세요](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Visual Studio 2017을 사용 하는 ASP.NET (Core 아님)의 경우 기본적으로 설치 되지 않는 .NET Framework 4.7.1 이상 개발 도구가 필요 합니다. 설치하려면 Visual Studio 설치 관리자를 시작하고, **수정**을 선택한 다음, **개별 구성 요소**를 선택한 다음, 오른쪽에서 **ASP.NET 및 웹 개발**을 확장하고 **.NET Framework 4.7.1 개발 도구**를 선택합니다.
- ASP.NET 4.7.1 이상 또는 ASP.NET Core 2.0 웹 프로젝트가 열려 있습니다.

## <a name="add-key-vault-support-to-your-project"></a>프로젝트에 Key Vault 지원 추가

시작 하기 전에 Visual Studio에 로그인 했는지 확인 합니다. Azure 구독에 사용 하는 것과 동일한 계정으로 로그인 합니다. ASP.NET 4.7.1 이상 또는 ASP.NET Core 2.0 웹 프로젝트를 열고 다음 단계를 수행 합니다.

1. **솔루션 탐색기**에서 키 자격 증명 모음 지원을 추가 하려는 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 > **연결 된 서비스** **추가** 를 선택 합니다.
   연결된 서비스 페이지가 프로젝트에 추가할 수 있는 서비스와 함께 표시됩니다.
1. 사용 가능한 서비스 메뉴에서 **Azure Key Vault로 비밀 보호**를 선택합니다.

   ![“Azure Key Vault로 비밀 보호” 선택](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. 사용할 구독을 선택 하 고 새 또는 기존 Key Vault를 선택 합니다. 새 Key Vault을 선택 하면 **편집** 링크가 표시 됩니다. 새 Key Vault를 구성 하려면이를 선택 합니다.

   ![구독 선택](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. **Azure Key Vault 편집**에서 Key Vault에 사용할 이름을 입력 합니다.

1. 기존 리소스 그룹을 선택 하거나 자동으로 생성 된 고유 이름을 사용 하 여 새 **리소스 그룹**을 만들도록 선택 합니다.  다른 이름으로 새 그룹을 만들려면 [Azure Portal](https://portal.azure.com)를 사용 하 고 페이지를 닫은 후 다시 시작 하 여 리소스 그룹 목록을 다시 로드할 수 있습니다.
1. Key Vault를 만들 **위치** 를 선택 합니다. 웹 애플리케이션이 Azure에서 호스트되는 경우 최적의 성능을 위해 웹 애플리케이션을 호스팅하는 지역을 선택합니다.
1. **가격 책정 계층**을 선택 합니다. 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)을 참조하세요.
1. **확인** 을 선택 하 여 구성 선택 항목을 적용 합니다.
1. 기존 Key Vault를 선택 하거나 새 Key Vault를 구성한 후에는 Visual Studio의 **Azure Key Vault** 탭에서 **추가** 를 선택 하 여 연결 된 서비스를 추가 합니다.
1. **이 Key Vault에 저장 된 비밀 관리** 링크를 선택 하 여 Key Vault에 대 한 **암호** 페이지를 엽니다. 페이지 또는 프로젝트를 닫은 경우 **모든 서비스** 를 선택 하 고 **보안**에서 **Key Vault**을 선택한 다음 Key Vault을 선택 하 여 [Azure Portal](https://portal.azure.com) 에서 이동할 수 있습니다.
1. 만든 키 자격 증명 모음의 Key Vault 섹션에서 **비밀**을 선택한 다음, **생성/가져오기**를 선택합니다.

   ![비밀 생성/가져오기](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. *Mysecret* 와 같은 암호를 입력 하 고, 테스트로 임의의 문자열 값을 지정한 다음, **만들기** 단추를 선택 합니다.

   ![비밀 만들기](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (선택 사항) 다른 비밀을 입력합니다. 단, 이번에는 *비밀--MySecret*이라는 이름을 지정하여 범주에 포함시킵니다. 이 구문은 "MySecret" 비밀을 포함 하는 "암호" 범주를 지정 합니다.

이제 코드에서 비밀에 액세스할 수 있습니다. 다음 단계는 ASP.NET 4.7.1 또는 ASP.NET Core를 사용하는지에 따라 달라집니다.

## <a name="access-your-secrets-in-code"></a>코드의 비밀에 액세스

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **NuGet 패키지 관리**를 선택 합니다. **찾아보기** 탭에서 다음과 같은 두 NuGet 패키지를 찾아서 설치 합니다. [microsoft. Azure. Appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 및 [Microsoft. azure. keyvault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

1. `Program.cs` 탭을 선택 하 고 Program 클래스를 다음 코드로 바꿉니다.

   ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
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
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

1. 그런 다음 `About.cshtml.cs` 파일을 열고 다음 코드를 작성 합니다.
   1. 이 using 문을 사용 하 여 `Microsoft.Extensions.Configuration`에 대 한 참조를 포함 합니다.

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. 다음 생성자를 추가 합니다.

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. `OnGet` 메서드를 업데이트 합니다. 위의 명령에서 만든 비밀 이름을 사용 하 여 여기에 표시 된 자리 표시자 값을 업데이트 합니다.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

정보 페이지로 이동 하 여 앱을 로컬로 실행 합니다. 비밀 값이 검색 되는 것을 볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 Key Vault와 관련된 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:** 상자에 리소스 그룹의 이름을 입력 하 고 **삭제**를 선택 합니다.

## <a name="troubleshooting"></a>문제 해결

키 자격 증명 모음이 Visual Studio에 로그인 한 것과 다른 Microsoft 계정에서 실행 중인 경우 (예: 키 자격 증명 모음이 회사 계정에서 실행 중이지만 Visual Studio에서 개인 계정을 사용 하는 경우) Program.cs 파일에 오류가 발생 합니다. 는 Visual Studio에서 키 자격 증명 모음에 액세스할 수 없습니다. 이 문제를 해결하려면

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 Key Vault를 엽니다.

1. **액세스 정책**, **액세스 정책 추가**를 차례로 선택 하 고 보안 주체로 로그인 한 계정을 선택 합니다.

1. Visual Studio에서 **파일** > **계정 설정**을 선택 합니다.
**모든 계정** 섹션에서 **계정 추가** 를 선택 합니다. 액세스 정책의 보안 주체로 선택한 계정으로 로그인 합니다.

1. **도구** > **옵션**을 선택 하 고 **Azure 서비스 인증**을 찾습니다. 그런 다음 Visual Studio에 방금 추가한 계정을 선택 합니다.

이제 응용 프로그램을 디버그할 때 Visual Studio에서 키 자격 증명 모음이 있는 계정에 연결 합니다.

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core 프로젝트를 수정 하는 방법

이 섹션에서는 Visual Studio를 사용 하 여 Key Vault 연결 된 서비스를 추가할 때 ASP.NET 프로젝트에 대 한 정확한 변경 내용을 확인 합니다.

### <a name="added-references-for-aspnet-core"></a>ASP.NET Core에 대 한 참조를 추가 했습니다.

프로젝트 파일 .NET 참조 및 NuGet 패키지 참조에 영향을 줍니다.

| 형식 | 참조 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>ASP.NET Core에 대 한 추가 된 파일

- 추가 `ConnectedService.json` 연결 된 서비스 공급자, 버전 및 설명서 링크에 대 한 일부 정보를 기록 합니다.

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET Core에 대 한 프로젝트 파일 변경

- 연결된 서비스 ItemGroup 및 `ConnectedServices.json` 파일을 추가 했습니다.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings의 ASP.NET Core에 대 한 json 변경 내용

- IIS Express 프로필 및 웹 프로젝트 이름과 일치하는 프로필에 다음 환경 변수 항목이 추가됩니다.

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>ASP.NET Core에 대 한 Azure의 변경 내용

- 리소스 그룹이 생성됩니다. (또는 기존 그룹이 사용됩니다.)
- 지정된 리소스 그룹에 Key Vault가 생성됩니다.

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET Framework 프로젝트가 수정 되는 방법

이 섹션에서는 Visual Studio를 사용 하 여 Key Vault 연결 된 서비스를 추가할 때 ASP.NET 프로젝트에 대 한 정확한 변경 내용을 확인 합니다.

### <a name="added-references-for-aspnet-framework"></a>ASP.NET Framework에 대 한 참조 추가

프로젝트 파일 .NET 참조 및 `packages.config` (NuGet 참조)에 영향을 줍니다.

| 형식 | 참조 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>ASP.NET Framework에 대 한 추가 된 파일

- 추가 `ConnectedService.json` 연결 된 서비스 공급자, 버전 및 설명서에 대 한 링크와 관련 된 일부 정보를 기록 합니다.

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework에 대 한 프로젝트 파일 변경 내용

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

### <a name="changes-on-azure-for-aspnet-framework"></a>ASP.NET Framework에 대 한 Azure의 변경 내용

- 리소스 그룹이 생성됩니다. (또는 기존 그룹이 사용됩니다.)
- 지정된 리소스 그룹에 Key Vault가 생성됩니다.

## <a name="next-steps"></a>다음 단계

[Key Vault 개발자 가이드](key-vault-developers-guide.md)를 읽어 Key Vault 개발에 대해 자세히 알아보세요.
