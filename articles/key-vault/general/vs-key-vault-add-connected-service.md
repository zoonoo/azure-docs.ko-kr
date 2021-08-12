---
title: Visual Studio를 사용하여 ASP.NET 프로젝트에 Key Vault 지원 추가 - Azure Key Vault | Microsoft Docs
description: 이 자습서를 통해 ASP.NET 또는 ASP.NET Core 웹 애플리케이션에 Key Vault 지원을 추가하는 방법을 배울 수 있습니다.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure, devx-track-csharp
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 9c62534acdbfbff7fd4e718bad1f07a92c641626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792398"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio 연결된 서비스를 사용하여 웹 애플리케이션에 Key Vault 추가

이 자습서에서는 ASP.NET Core를 사용하든 아니면 모든 종류의 ASP.NET 프로젝트를 사용하든 Visual Studio에서 웹 프로젝트에 대한 사용자의 비밀을 관리하기 위해 Azure Key Vault를 사용하여 시작해야 하는 모든 항목을 쉽게 추가하는 방법에 대해 알아봅니다. Visual Studio에서 연결된 서비스 기능을 사용하면 Azure의 Key Vault에 연결해야 하는 모든 NuGet 패키지 및 구성 설정을 Visual Studio에서 자동으로 추가하도록 할 수 있습니다.

Key Vault를 사용하도록 설정하기 위해 프로젝트에서 연결된 서비스에서 수행하는 변경 내용에 대한 자세한 내용은 [Key Vault 연결된 서비스 - 내 ASP.NET 4.7.1 프로젝트에서 변경된 내용](#how-your-aspnet-framework-project-is-modified) 또는 [Key Vault 연결된 서비스 - 내 ASP.NET Core 프로젝트에서 변경된 내용](#how-your-aspnet-core-project-is-modified)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- **Azure 구독**. 구독이 없는 경우 [무료 계정에 등록](https://azure.microsoft.com/pricing/free-trial/)합니다.
- **Visual Studio 2019, 버전 16.3** 이상 [지금 다운로드](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).


## <a name="add-key-vault-support-to-your-project"></a>프로젝트에 Key Vault 지원 추가

시작하기 전에 Visual Studio에 로그인했는지 확인합니다. Azure 구독을 관리하는 데 사용하는 계정으로 로그인합니다. ASP.NET 4.7.1 이상, 또는 ASP.NET Core 2.0 웹 프로젝트를 열고 다음 단계를 수행합니다.

1. **솔루션 탐색기** 에서 Key Vault 지원을 추가하려는 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **연결된 서비스** > **추가** 를 선택합니다.
   연결된 서비스 페이지가 프로젝트에 추가할 수 있는 서비스와 함께 표시됩니다.
1. 사용 가능한 서비스 메뉴에서 **Azure Key Vault** 를 선택하고 **다음** 을 클릭합니다.

   !["Azure Key Vault"를 선택합니다](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. 사용할 구독을 선택한 다음 기존 Key Vault를 선택하고 **마침** 을 클릭합니다. 

   ![구독 선택](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

이제 Key Vault에 연결이 설정되고 코드에서 암호에 액세스할 수 있습니다. 다음 단계는 ASP.NET 4.7.1 또는 ASP.NET Core를 사용하는지에 따라 달라집니다.

## <a name="access-your-secrets-in-code-aspnet-core"></a>코드에서 암호에 액세스(ASP.NET Core)

1. 페이지 파일 중 하나, 예를 들어 *Index.cshtml.cs* 을 열고 다음 코드를 작성합니다.
   1. 이 지시문을 사용하여 `Microsoft.Extensions.Configuration`에 참조를 포함합니다.

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. 구성 변수를 추가합니다.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. 이 생성자를 추가하거나 기존 생성자를 다음과 같이 바꿉니다.

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. `OnGet` 메서드를 업데이트합니다. 위의 명령에서 만든 암호 이름으로 여기에 표시된 자리 표시자 값을 업데이트합니다.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. 런타임에 값을 확인하려면 `ViewData["Message"]`을 *.cshtml* 파일에 표시해 암호를 메시지에 표시하는 코드를 추가합니다.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

앱을 로컬로 실행하여 Key Vault에서 암호를 성공적으로 가져왔는지 확인할 수 있습니다.

## <a name="access-your-secrets-aspnet"></a>암호에 액세스(ASP.NET)
web.config 파일이 런타임에 true 값으로 대체되는 `appSettings` 요소의 더미 값을 포함하도록 구성을 설정할 수 있습니다. 그런 다음 `ConfigurationManager.AppSettings` 데이터 구조를 통해 이에 액세스할 수 있습니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다. 찾아보기 탭에서 [Microsoft.Configuration.ConfigurationBuilders.Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/)을 찾아 설치합니다
 
1. web.config 파일을 열고 다음 코드를 작성합니다.
    1. `configSections`와 `configBuilders`를 추가합니다.
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
    1. appSettings 태그를 찾아 `configBuilders="AzureKeyVault"` 특성을 추가한 뒤에 다음 줄을 추가합니다.
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. `About` 메서드를 *HomeController.cs* 에서 편집하여 확인 값을 표시합니다.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. 디버거에서 로컬로 앱을 실행하고 **정보** 탭으로 전환한 다음 Key Vault의 값이 표시되는지 확인합니다.

## <a name="troubleshooting"></a>문제 해결

Key Vault가 Visual Studio에 로그인한 계정과 다른 Microsoft 계정에서 실행되는 경우(예: Key Vault는 회사 계정에서 실행되지만 Visual Studio는 개인 계정을 사용하고 있는 경우) Program.cs 파일에서 Visual Studio에서 Key Vault에 액세스할 수 없는 오류가 발생합니다. 이 문제를 해결하려면

1. [Azure Portal](https://portal.azure.com)로 이동하여 Key Vault를 엽니다.

1. **액세스 정책** 을 선택한 뒤 **액세스 정책 추가** 를 선택하고 로그인한 계정을 보안 주체로 선택합니다.

1. Visual Studio **파일** > **계정 설정** 을 선택합니다.
**계정 추가** 를 **모든 계정** 섹션에서 선택합니다. 액세스 정책의 보안 주체로 선택한 계정으로 로그인합니다.

1. **도구** > **옵션** 을 선택하고 **Azure 서비스 인증** 을 찾습니다. 그런 다음, 방금 Visual Studio에 추가한 계정을 선택합니다.

이제 애플리케이션을 디버그할 때 Visual Studio는 Key Vault가 있는 계정에 연결합니다.

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core 프로젝트를 수정하는 방법

이 섹션에서는 Visual Studio를 사용하여 Key Vault에 연결된 서비스를 추가하면 ASP.NET 프로젝트에서 정확히 무엇이 변경되는지 알아봅니다.

### <a name="added-references-for-aspnet-core"></a>ASP.NET Core에 대한 참조 추가

프로젝트 파일 .NET 참조 및 NuGet 패키지 참조에 영향을 줍니다.

| Type | 참조 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>ASP.NET Core에 파일 추가

- 연결된 서비스 공급자, 버전 및 설명서 링크에 대한 일부 정보를 기록하는 `ConnectedService.json`이 추가되었습니다.

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET Core에 프로젝트 파일 변경 내용

- 연결된 서비스 ItemGroup 및 `ConnectedServices.json` 파일이 추가되었습니다.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>ASP.NET Core의 launchsettings.json 변경 내용

- IIS Express 프로필 및 웹 프로젝트 이름과 일치하는 프로필에 다음 환경 변수 항목이 추가됩니다.

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>ASP.NET Core에 대한 Azure의 변경 내용

- 리소스 그룹이 생성됩니다. (또는 기존 그룹이 사용됩니다.)
- 지정된 리소스 그룹에 Key Vault가 생성됩니다.

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET Framework 프로젝트를 수정하는 방법

이 섹션에서는 Visual Studio를 사용하여 Key Vault에 연결된 서비스를 추가하면 ASP.NET 프로젝트에서 정확히 무엇이 변경되는지 알아봅니다.

### <a name="added-references-for-aspnet-framework"></a>ASP.NET Framework에 대한 참조 추가

프로젝트 파일 .NET 참조 및 `packages.config`(NuGet 참조)에 영향을 줍니다.

| Type | 참조 |
| --- | --- |
| .NET; NuGet | Azure.Identity |
| .NET; NuGet | Azure.Security.KeyVault.Keys |
| .NET; NuGet | Azure.Security.KeyVault.Secrets |

> [!IMPORTANT] 
> 기본값으로 Azure.Identity 1.1.1은 Visual Studio 자격 증명을 지원하지 않습니다. Visual Studio 자격 증명을 사용하여 패키지 참조를 1.2+로 수동으로 업데이트할 수 있습니다.

### <a name="added-files-for-aspnet-framework"></a>ASP.NET Framework에 파일 추가

- 연결된 서비스 공급자, 버전 및 설명서 링크에 대한 일부 정보를 기록하는 `ConnectedService.json`이 추가되었습니다.

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework에 대한 프로젝트 파일 변경 내용

- 연결된 서비스 ItemGroup 및 ConnectedServices.json 파일이 추가됩니다.
- [추가된 참조](#added-references-for-aspnet-framework) 섹션에 설명된 .NET 어셈블리에 대한 참조입니다.

## <a name="next-steps"></a>다음 단계

이 자습서를 수행하면 사용자의 Azure 구독을 사용하여 실행하도록 Key Vault 사용 권한이 설정되지만, 프로덕션 시나리오에는 바람직하지 않을 수 있습니다. 관리 ID를 만들어 앱에 대한 Key Vault 액세스를 관리할 수 있습니다. [Key Vault 인증 방법](./authentication.md) 및 [Key Vault 액세스 정책 할당](./assign-access-policy-portal.md)을 참조하세요.

[Key Vault 개발자 가이드](developers-guide.md)를 확인하여 Key Vault 개발에 대해 자세히 알아봅니다.