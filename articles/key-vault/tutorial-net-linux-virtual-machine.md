---
title: 자습서 - .NET에서 Azure Linux Virtual Machine에 Azure Key Vault를 사용하는 방법 | Microsoft Docs
description: 자습서 Key Vault에서 비밀을 읽도록 ASP.NET Core 응용 프로그램 구성
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: afee50db98b361b11371e9ac382060e200a1f62f
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688251"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-net"></a>자습서: .NET에서 Azure Linux Virtual Machine에 Azure Key Vault를 사용하는 방법

Azure Key Vault를 통해 응용 프로그램, 서비스 및 IT 리소스에 액세스하는 데 필요한 API 키, 데이터베이스 연결 문자열과 같은 비밀을 보호할 수 있습니다.

이 자습서에서는 Azure 리소스의 관리 ID를 사용하여 Azure 웹 응용 프로그램이 Azure Key Vault에서 정보를 읽는 데 필요한 단계를 수행합니다. 이 자습서는 [Azure Web Apps](../app-service/app-service-web-overview.md)를 기반으로 합니다. 다음에서 사용 방법을 알아봅니다.

> [!div class="checklist"]
> * 키 자격 증명 모음을 만듭니다.
> * 키 자격 증명 모음에 비밀을 저장합니다.
> * 키 자격 증명 모음에서 비밀을 검색합니다.
> * Azure Virtual Machine을 만듭니다.
> * Virtual Machine에 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정합니다.
> * 콘솔 애플리케이션이 키 자격 증명 모음에서 데이터를 읽는 데 필요한 권한을 부여합니다.
> * Key Vault에서 비밀을 검색합니다.

본론으로 들어가기 전에 [기본 개념](key-vault-whatis.md#basic-concepts)을 읽어보세요.

## <a name="prerequisites"></a>필수 조건
* 모든 플랫폼:
  * Git([다운로드](https://git-scm.com/downloads)).
  * Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 버전 2.0.4 이상. Windows, Mac 및 Linux에서 사용할 수 있습니다.

이 자습서에서는 관리 서비스 ID를 사용합니다.

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>관리 서비스 ID란 무엇이고 어떻게 작동하나요?
계속하기 전에 MSI를 이해하겠습니다. Azure Key Vault가 자격 증명을 안전하게 저장할 수 있도록 해당 코드 이외로 작성되지만 검색하려면 Azure Key Vault에 인증해야 합니다. Key Vault에 인증하려면 자격 증명이 필요합니다. 클래식 부트스트랩 문제가 있습니다. MSI는 Azure 및 Azure AD의 기능을 통해 작업을 간단하게 만드는 "부트스트랩 ID"를 제공합니다.

방법은 다음과 같습니다. Virtual Machines, App Service 또는 Functions와 같은 Azure 서비스에 대해 MSI를 사용하도록 설정하면 Azure에서는 Azure Active Directory의 서비스 인스턴스에 대한 [서비스 주체](key-vault-whatis.md#basic-concepts)를 만들고, 서비스 인스턴스에 서비스 주체의 자격 증명을 삽입합니다. 

![MSI](media/MSI.png)

다음으로, 코드는 액세스 토큰을 가져오기 위해 Azure 리소스에 사용할 수 있는 로컬 메타데이터 서비스를 호출합니다.
코드는 Azure Key Vault 서비스를 인증하기 위해 로컬 MSI_ENDPOINT에서 가져오는 액세스 토큰을 사용합니다. 

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

## <a name="create-a-virtual-machine"></a>Virtual Machine 만들기

[az vm create](/cli/azure/vm#az_vm_create) 명령을 사용하여 VM을 만듭니다.

다음 예제에서는 *myVM*이라는 VM을 만들고 *azureuser*라는 사용자 계정을 추가합니다. `--generate-ssh-keys` 매개 변수는 SSH 키를 자동으로 생성하여 기본 키 위치(*~/.ssh*)에 배치하는 데 사용됩니다. 특정 키 집합을 대신 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다. 다음 예제 출력은 VM 만들기 작업이 완료되었음을 보여줍니다.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

VM의 출력에서 사용자 고유의 `publicIpAddress`를 적어 둡니다. 이 주소는 다음 단계에서 VM에 액세스하는 데 사용됩니다.

## <a name="assign-identity-to-virtual-machine"></a>Virtual Machine에 ID 할당
이 단계에서는 다음 명령을 실행하여 가상 머신에 시스템 할당 ID를 만들 것입니다.

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

아래의 systemAssignedIdentity를 기록해 두세요. 위 명령의 출력은 다음과 같습니다. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-vm-identity-permission-to-key-vault"></a>Key Vault에 VM ID 권한 부여
이제 다음 명령을 실행하여 위에서 만든 ID 권한을 Key Vault에 부여할 수 있습니다.

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>Virtual Machine에 로그인

이제 터미널을 사용하여 Virtual Machine에 로그인합니다.

```
ssh azureuser@<PublicIpAddress>
```

## <a name="install-dot-net-core-on-linux"></a>Linux에 Dot Net Core 설치
### <a name="register-the-microsoft-product-key-as-trusted-run-the-following-two-commands"></a>Microsoft 제품 키를 신뢰할 수 있는 키로 등록합니다. 다음 두 명령을 실행합니다.

```
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
```

### <a name="set-up-desired-version-host-package-feed-based-on-operating-system"></a>운영 체제에 따라 원하는 버전의 호스트 패키지 피드를 설치합니다.
```
# Ubuntu 17.10
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
 
# Ubuntu 17.04
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
 
# Ubuntu 16.04 / Linux Mint 18
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
 
# Ubuntu 14.04 / Linux Mint 17
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
```

### <a name="install-net-core"></a>.NET Core 설치

.NET 버전을 확인합니다.

```
sudo apt-get install dotnet-sdk-2.1.4
dotnet --version
```

## <a name="create-and-run-sample-dot-net-app"></a>샘플 Dot Net 앱을 만들고 실행

아래 명령을 실행하면 콘솔에 "Hello World"가 표시되어야 합니다.

```
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-console-app"></a>콘솔 앱 편집
Program.cs 파일을 열고 다음 패키지를 추가합니다.
```
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```
그런 다음, 아래 코드를 포함하도록 클래스 파일을 변경합니다. 이 작업은 2단계 프로세스입니다. 
1. VM의 로컬 MSI 엔드포인트에서 토큰을 가져옵니다. 그러면 VM이 Azure Active Directory에서 토큰을 가져옵니다.
2. Key Vault에 토큰을 전달하고 비밀 가져오기 

```
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
            var token = GetToken();

            // Step 2: Fetch the secret value from Key Vault
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
            WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
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
                Console.WriteLine(ojObject.Value);                
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

위의 코드는 Azure Linux Virtual Machine에서 Azure Key Vault 작업을 수행하는 방법을 보여줍니다. 


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
