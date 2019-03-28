---
title: 자습서 - .NET에서 Azure Web App과 Azure Key Vault 사용 | Microsoft Docs
description: 이 자습서에서는 Key Vault에서 비밀을 읽도록 ASP.NET Core 애플리케이션을 구성합니다.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 2c2bb3e4064294bb8d4a63b009069fd6834ca31e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370893"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>자습서: .NET에서 Azure 웹앱과 함께 Azure Key Vault 사용

Azure Key Vault는 API 키 및 데이터베이스 연결 문자열과 같은 비밀을 보호하는 데 도움이 됩니다. 애플리케이션, 서비스 및 IT 리소스에 대한 액세스도 제공합니다.

이 자습서에서는 Azure Key Vault에서 정보를 읽을 수 있는 Azure 웹 애플리케이션을 만드는 방법을 알아봅니다. 이 프로세스에서는 Azure 리소스에 대해 관리 ID를 사용합니다. Azure 웹 애플리케이션에 대한 자세한 내용은 [Azure App Service](../app-service/overview.md)를 참조하세요.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 키 자격 증명 모음을 만듭니다.
> * 키 자격 증명 모음에 비밀 추가
> * 키 자격 증명 모음에서 비밀을 검색합니다.
> * Azure Web App을 만듭니다.
> * 웹앱의 관리 ID를 사용하도록 설정합니다.
> * 웹앱에 대한 권한을 할당합니다.
> * Azure에서 웹앱을 실행합니다.

시작하기 전에 [Key Vault 기본 개념](key-vault-whatis.md#basic-concepts)을 읽어보세요. 

Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* Windows: [.NET Core 2.1 SDK 이상](https://www.microsoft.com/net/download/windows)
* Mac: [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)
* Windows, Mac 및 Linux:
  * [Git](https://git-scm.com/downloads)
  * 이 자습서에서는 Azure CLI를 로컬로 실행해야 합니다. Azure CLI 버전 2.0.4 이상이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우에는 [Azure CLI 2.0 설치](https://review.docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>관리 서비스 ID 정보

Azure Key Vault는 자격 증명을 안전하게 저장하므로 자격 증명이 코드에 표시되지 않습니다. 그러나 키를 검색하려면 Azure Key Vault에서 인증을 받아야 합니다. Key Vault에서 인증을 받으려면 자격 증명이 필요합니다. 이것이 바로 클래식 부트스트랩의 딜레마입니다. MSI(관리 서비스 ID)는 프로세스를 간소화하는 _부트스트랩 ID_를 제공하여 이 문제를 해결합니다.

Azure 서비스(예: Azure Virtual Machines, Azure App Service 또는 Azure Functions)에 대해 MSI를 사용하도록 설정하면 Azure는 [서비스 주체](key-vault-whatis.md#basic-concepts)를 만듭니다. MSI는 Azure AD(Azure Active Directory)에서 서비스의 인스턴스에 대해 이 작업을 수행하고 서비스 주체 자격 증명을 해당 인스턴스에 삽입합니다.

![MSI 다이어그램](media/MSI.png)

다음으로, 액세스 토큰을 가져오기 위해 코드는 Azure 리소스에 사용할 수 있는 로컬 메타데이터 서비스를 호출합니다. 코드는 Azure Key Vault 서비스를 인증하기 위해 로컬 MSI 엔드포인트에서 가져오는 액세스 토큰을 사용합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다.

그런 후, 리소스 그룹 이름을 선택하고 자리 표시자를 입력합니다. 다음 예제에서는 미국 서부 위치에 리소스 그룹을 만듭니다.

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

이 자습서 전체에서 이 리소스 그룹을 사용합니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

리소스 그룹에 Key Vault를 만들려면 다음 정보를 제공합니다.

* Key Vault 이름: 숫자(0-9), 문자(a-z, A-Z) 및 하이픈(-)만 포함할 수 있는 3~24자 문자열입니다.
* 리소스 그룹 이름
* 위치: **미국 서부**

Azure CLI에서 다음 명령을 입력합니다.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

이 시점에서 Azure 계정은 이 새 자격 증명 모음에서 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

이제 비밀을 추가할 수 있습니다. 이러한 비밀은 안전하게 유지하면서 애플리케이션에서 사용할 수 있도록 해야 하는 기타 정보 또는 SQL 연결 문자열일 수 있습니다.

Key Vault에 **AppSecret**라는 비밀을 만들려면 다음 명령을 입력합니다. 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

이 비밀에는 값 **MySecret**이 저장됩니다.

비밀에 들어 있는 값을 일반 텍스트로 보려면 다음 명령을 입력합니다.

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

이 명령은 URI를 포함한 비밀 정보를 표시합니다. 

이러한 단계가 완료되면 비밀에 대한 URI가 키 자격 증명 모음에 있어야 합니다. 이 자습서에서 나중에 사용하게 되므로 이 정보를 기록해 둡니다. 

## <a name="create-a-net-core-web-app"></a>.NET Core Web App 만들기

.NET Core 웹앱을 만들고 Azure에 게시하려면 [Azure에서 ASP.NET Core 웹앱 만들기](../app-service/app-service-web-get-started-dotnet.md)의 지침을 따릅니다. 

이 비디오를 시청할 수도 있습니다.

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>솔루션 열기 및 편집

1. **Pages** > **About.cshtml.cs** 파일로 이동합니다.

1. 다음 NuGet 패키지를 설치합니다.
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. 다음 코드를 *About.cshtml.cs* 파일로 가져옵니다.

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   AboutModel 클래스의 코드는 다음과 같이 표시됩니다.

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>웹앱 실행

1. Visual Studio 2017의 주 메뉴에서 디버깅하거나 디버깅하지 않고 **디버그** > **시작**을 선택합니다. 
1. 브라우저에서 다음과 같이 **정보** 페이지로 이동합니다.  
    **AppSecret**에 대한 값이 표시됩니다.

## <a name="enable-a-managed-identity"></a>관리 ID 사용

Azure Key Vault를 사용하면 자격 증명과 기타 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 통해 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 표시할 필요가 없습니다.

Azure CLI에서 이 애플리케이션에 대한 ID를 만들려면 assign-identity 명령을 실행합니다.

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

\<YourAppName>을 Azure에 게시된 앱의 이름으로 바꿉니다.  
    예를 들어, 게시된 앱 이름이 **MyAwesomeapp.azurewebsites.net**이면 \<YourAppName>을 **MyAwesomeapp**으로 바꿉니다.

Azure에 애플리케이션을 게시할 때 `PrincipalId`를 기록해 둡니다. 1단계의 명령 출력은 다음과 같은 형식이어야 합니다.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>이 절차의 명령은 [Azure Portal](https://portal.azure.com)로 이동하여 웹 애플리케이션 속성에서 **ID/시스템 할당됨** 설정을 **켜기**로 전환하는 것과 동일합니다.

## <a name="assign-permissions-to-your-app"></a>앱에 사용 권한 할당

다음 명령에서 \<YourKeyVaultName>을 Key Vault의 이름으로, \<PrincipalId>를 **PrincipalId**의 값으로 바꿉니다.

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

이 명령에서는 Key Vault에서 **가져오기** 및 **나열** 작업을 수행할 App Service 사용 권한의 ID(MSI)를 제공합니다.

## <a name="publish-the-web-app-to-azure"></a>Azure에 웹앱 게시

웹앱을 Azure에 한 번 더 게시하여 실시간 웹앱이 비밀 값을 가져올 수 있는지 확인합니다.

1. Visual Studio에서 **key-vault-dotnet-core-quickstart** 프로젝트를 선택합니다.
2. **게시** > **시작**을 차례로 선택합니다.
3. **만들기**를 선택합니다.

애플리케이션을 실행하여 비밀 값을 검색할 수 있는지 확인합니다.

Key Vault에서 비밀을 저장하고 인출하는 웹앱을 .NET에서 성공적으로 만들었습니다.

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않으면 가상 머신 및 Key Vault를 삭제해도 됩니다.

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[Azure Key Vault 개발자 가이드](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
