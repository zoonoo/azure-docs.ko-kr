---
title: 자습서 - .NET에서 Windows 가상 머신에 Azure Key Vault 사용 | Microsoft Docs
description: 이 자습서에서는 Key Vault에서 비밀을 읽도록 ASP.NET Core 애플리케이션을 구성합니다.
services: key-vault
author: mbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 7fdd2f1832995d279f58af52f96270f7aa3bb5a6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686151"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>자습서: .NET에서 Windows 가상 머신에 Azure Key Vault 사용

Azure Key Vault를 통해 애플리케이션, 서비스 및 IT 리소스에 액세스하는 데 필요한 API 키, 데이터베이스 연결 문자열과 같은 비밀을 보호할 수 있습니다.

이 자습서에서는 Azure Key Vault에서 정보를 읽는 콘솔 애플리케이션을 가져오는 방법을 알아봅니다. 이렇게 하려면 Azure 리소스의 관리 ID를 사용합니다. 

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 리소스 그룹을 만듭니다.
> * 키 자격 증명 모음을 만듭니다.
> * 키 자격 증명 모음에 비밀 추가
> * 키 자격 증명 모음에서 비밀을 검색합니다.
> * Azure 가상 머신을 만듭니다.
> * Virtual Machine에 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정합니다.
> * VM ID에 사용 권한을 할당합니다.

시작하기 전에 [Key Vault 기본 개념](key-vault-whatis.md#basic-concepts)을 읽어보세요. 

Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Windows, Mac 및 Linux:
  * [Git](https://git-scm.com/downloads)
  * 이 자습서에서는 Azure CLI를 로컬로 실행해야 합니다. Azure CLI 버전 2.0.4 이상이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우에는 [Azure CLI 2.0 설치](https://review.docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

## <a name="about-managed-service-identity"></a>관리 서비스 ID 정보

Azure Key Vault는 자격 증명을 안전하게 저장하므로 자격 증명이 코드에 표시되지 않습니다. 그러나 키를 검색하려면 Azure Key Vault에서 인증을 받아야 합니다. Key Vault에서 인증을 받으려면 자격 증명이 필요합니다. 이것이 바로 클래식 부트스트랩의 딜레마입니다. MSI(관리 서비스 ID)는 프로세스를 간소화하는 _부트스트랩 ID_를 제공하여 이 문제를 해결합니다.

Azure 서비스(예: Azure Virtual Machines, Azure App Service 또는 Azure Functions)에 대해 MSI를 사용하도록 설정하면 Azure는 [서비스 주체](key-vault-whatis.md#basic-concepts)를 만듭니다. MSI는 Azure AD(Azure Active Directory)에서 서비스의 인스턴스에 대해 이 작업을 수행하고 서비스 주체 자격 증명을 해당 인스턴스에 삽입합니다. 

![MSI](media/MSI.png)

다음으로, 액세스 토큰을 가져오기 위해 코드는 Azure 리소스에 사용할 수 있는 로컬 메타데이터 서비스를 호출합니다. Azure Key Vault 서비스를 인증하기 위해 코드는 로컬 MSI 엔드포인트에서 가져오는 액세스 토큰을 사용합니다. 

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

이 자습서 전체에서 새로 만든 리소스 그룹을 사용합니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

이전 단계에서 만든 리소스 그룹에 Key Vault를 만들려면 다음 정보를 지정합니다.

* Key Vault 이름: 숫자(0-9), 문자(a-z, A-Z) 및 하이픈(-)만 포함할 수 있는 3~24자 문자열입니다.
* 리소스 그룹 이름
* 위치: **미국 서부**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
이 시점에서 Azure 계정은 이 새 Key Vault에서 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 이러한 비밀은 안전하게 유지하면서 애플리케이션에서 사용할 수 있도록 해야 하는 기타 정보 또는 SQL 연결 문자열일 수 있습니다.

Key Vault에 **AppSecret**라는 비밀을 만들려면 다음 명령을 입력합니다.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

이 비밀에는 값 **MySecret**이 저장됩니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기
다음 방법 중 하나를 사용하여 가상 머신을 만들 수 있습니다.

* [Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Azure 포털](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>VM에 ID 할당
이 단계에서는 Azure CLI에서 다음 명령을 실행하여 가상 머신에 시스템 할당 ID를 만들 것입니다.

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

다음 코드에 표시되는 시스템 할당 ID를 기록해 둡니다. 위 명령의 출력은 다음과 같습니다. 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>VM ID에 사용 권한을 할당합니다.
이제 다음 명령을 실행하여 이전에 만든 ID 사용 권한을 Key Vault에 할당할 수 있습니다.

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>가상 머신에 로그온

가상 머신에 로그온하려면 [Windows를 실행하는 Azure Virtual Machine에 연결 및 로그온](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)의 지침을 따르세요.

## <a name="install-net-core"></a>.NET Core 설치

.NET Core를 설치하려면 [.NET 다운로드](https://www.microsoft.com/net/download) 페이지로 이동합니다.

## <a name="create-and-run-a-sample-net-app"></a>샘플 .NET 앱 만들기 및 실행

명령 프롬프트를 엽니다.

다음 명령을 실행하여 "Hello World"를 콘솔에 출력할 수 있습니다.

```batch
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app"></a>콘솔 앱 편집

*Program.cs* 파일을 열고 다음 패키지를 추가합니다.

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

다음 2단계 프로세스에 따라, 클래스 파일을 편집하여 코드를 포함합니다.

1. VM의 로컬 MSI 엔드포인트에서 토큰을 페치합니다. 이렇게 하면 Azure AD에서 토큰도 페치됩니다.
1. 토큰을 Key Vault에 전달하고 비밀을 페치합니다. 

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }

        static string FetchSecretValueFromKeyVault(string token)
        {
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

위의 코드는 Windows Virtual Machine에서 Azure Key Vault 작업을 수행하는 방법을 보여줍니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 가상 머신 및 Key Vault를 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
