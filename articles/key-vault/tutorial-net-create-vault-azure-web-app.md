---
title: 자습서 - .NET에서 Azure Web App과 Azure Key Vault를 사용하는 방법 | Microsoft Docs
description: 자습서 Key Vault에서 비밀을 읽도록 ASP.NET Core 응용 프로그램 구성
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 9cc22e158a9473b7b60f7e8bcb57174abc1fb8cc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218555"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>자습서: .NET에서 Azure Web App과 Azure Key Vault를 사용하는 방법

Azure Key Vault를 통해 응용 프로그램, 서비스 및 IT 리소스에 액세스하는 데 필요한 API 키, 데이터베이스 연결 문자열과 같은 비밀을 보호할 수 있습니다.

이 자습서에서는 Azure 리소스의 관리 ID를 사용하여 Azure 웹 응용 프로그램이 Azure Key Vault에서 정보를 읽는 데 필요한 단계를 수행합니다. 이 자습서는 [Azure Web Apps](../app-service/app-service-web-overview.md)를 기반으로 합니다. 다음에서 사용 방법을 알아봅니다.

> [!div class="checklist"]
> * 키 자격 증명 모음을 만듭니다.
> * 키 자격 증명 모음에 비밀을 저장합니다.
> * 키 자격 증명 모음에서 비밀을 검색합니다.
> * Azure 웹 응용 프로그램을 만듭니다.
> * 웹앱의 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정합니다.
> * 웹 응용 프로그램이 키 자격 증명 모음에서 데이터를 읽는 데 필요한 권한을 부여합니다.
> * Azure에서 웹 응용 프로그램 실행

본론으로 들어가기 전에 [기본 개념](key-vault-whatis.md#basic-concepts)을 읽어보세요.

## <a name="prerequisites"></a>필수 조건

* Windows에서:
  * [.NET Core 2.1 SDK 이상](https://www.microsoft.com/net/download/windows)

* Mac에서:
  * [Mac용 Visual Studio의 새로운 기능](https://visualstudio.microsoft.com/vs/mac/)을 참조하세요.

* 모든 플랫폼:
  * Git([다운로드](https://git-scm.com/downloads)).
  * Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 버전 2.0.4 이상. Windows, Mac 및 Linux에서 사용할 수 있습니다.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>관리 서비스 ID란 무엇이고 어떻게 작동하나요?
 계속하기 전에 MSI를 이해하겠습니다. Azure Key Vault가 자격 증명을 안전하게 저장할 수 있도록 해당 코드 이외로 작성되지만 검색하려면 Azure Key Vault에 인증해야 합니다. Key Vault에 인증하려면 자격 증명이 필요합니다. 클래식 부트스트랩 문제가 있습니다. MSI는 Azure 및 Azure AD의 기능을 통해 작업을 간단하게 만드는 "부트스트랩 ID"를 제공합니다.

방법은 다음과 같습니다. Virtual Machines, App Service 또는 Functions와 같은 Azure 서비스에 대해 MSI를 사용하도록 설정하면 Azure에서는 Azure Active Directory의 서비스 인스턴스에 대한 [서비스 주체](key-vault-whatis.md#basic-concepts)를 만들고, 서비스 인스턴스에 서비스 주체의 자격 증명을 삽입합니다. 

![MSI](media/MSI.png)

다음으로, 코드는 액세스 토큰을 가져오기 위해 Azure 리소스에 사용할 수 있는 로컬 메타데이터 서비스를 호출합니다.
코드는 Azure Key Vault 서비스를 인증하기 위해 로컬 MSI_ENDPOINT에서 가져오는 액세스 토큰을 사용합니다. 

이제 자습서를 시작해 보겠습니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

리소스 그룹 이름을 선택하고 자리 표시자를 입력합니다.
다음 예제에서는 미국 서부 위치에 리소스 그룹을 만듭니다.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

방금 만든 리소스 그룹은 이 문서 전체에서 사용됩니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

다음으로, 이전 단계에서 만든 리소스 그룹에 키 자격 증명 모음을 만듭니다. 다음 정보를 지정합니다.

* 키 자격 증명 모음 이름: 3-24자의 문자열이어야 하며 (0-9, a-z, A-Z 및 -)만 포함해야 합니다.
* 리소스 그룹 이름
* 위치: **미국 서부**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

이 시점에서 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 안전하게 유지하면서 응용 프로그램에서 사용할 수 있도록 하는 데 필요한 SQL 연결 문자열 또는 기타 정보를 저장할 수 있습니다.

다음 명령을 입력하여 키 자격 증명 모음에 **AppSecret**라고 하는 비밀을 만듭니다. 이 비밀에는 **MySecret** 값이 저장됩니다.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

비밀에 들어 있는 값을 일반 텍스트로 보려면:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

이 명령은 URI를 포함한 비밀 정보를 표시합니다. 이러한 단계가 완료되면 비밀에 대한 URI가 키 자격 증명 모음에 있어야 합니다. 이 정보를 기록해 둡니다. 이후 단계에서 필요합니다.

## <a name="create-a-net-core-web-app"></a>.NET Core Web App 만들기

이 [자습서](../app-service/app-service-web-get-started-dotnet.md)에 따라 .NET Core Web App을 만들고 Azure에 **게시**합니다. **또는** 아래 비디오를 시청하세요.
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>솔루션 열기 및 편집

1. 페이지 > About.cshtml.cs 파일로 이동합니다.
2. 이러한 2개의 Nuget 패키지를 설치합니다.
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. About.cshtml.cs 파일에서 다음 항목을 가져옵니다.

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. AboutModel 클래스의 코드는 다음과 같이 표시됩니다.
    ```
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
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;             

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }            
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```


## <a name="run-the-app"></a>앱 실행

Visual Studio 2017의 주 메뉴에서 디버깅하거나 디버깅하지 않고 **디버그** > **시작**을 선택합니다. 브라우저가 표시되면 **정보** 페이지로 이동합니다. **AppSecret**에 대한 값이 표시됩니다.

## <a name="enable-a-managed-identity-for-the-web-app"></a>웹앱의 관리 ID를 사용하도록 설정

Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 통해 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

1. Azure CLI로 돌아갑니다.
2. 이 응용 프로그램에 대한 ID를 만들려면 assign-identity 명령을 실행합니다. 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   <YourAppName>을 Azure에 게시된 앱의 이름으로 바꿔야 합니다(예: 게시된 앱 이름이 MyAwesomeapp.azurewebsites.net이면 <YourAppName>을 MyAwesomeapp으로 바꿈).
 
 위 명령의 출력은 다음과 같이 표시됩니다. Azure에 응용 프로그램을 게시할 때 PrincipalId를 적어둡니다. 형식은 다음과 같아야 합니다.
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>이 절차의 명령은 [포털](https://portal.azure.com)로 이동하여 웹 응용 프로그램 속성에서 **ID/시스템 할당됨** 설정을 **켜기**로 전환하는 것과 동일합니다.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>응용 프로그램에 Key Vault에서 비밀을 읽을 수 있는 권한 할당
        
그런 다음, 키 자격 증명 모음의 이름 및 **PrincipalId** 값을 사용하여 다음 명령을 실행합니다.

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>Azure에 웹 응용 프로그램 게시

이 앱을 Azure에 다시 한 번 게시하여 실시간으로 웹앱으로 표시하고 비밀 값을 가져올 수 있는지 확인합니다.

1. Visual Studio에서 **key-vault-dotnet-core-quickstart** 프로젝트를 선택합니다.
2. **게시** > **시작**을 차례로 선택합니다.
3. **만들기**를 선택합니다.

위 명령에서는 Key Vault에서 **가져오기** 및 **나열** 작업을 수행할 App Service 사용 권한의 ID(MSI)를 제공합니다. <br />
이제 응용 프로그램이 실행되면 검색된 비밀 값이 표시됩니다. 

이것으로 끝입니다. 이제 Key Vault에서 비밀을 저장하고 인출하는 Web App을 .NET에서 성공적으로 만들었습니다.
