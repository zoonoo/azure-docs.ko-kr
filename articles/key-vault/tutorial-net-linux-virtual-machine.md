---
title: 자습서 - Linux 가상 머신 및 ASP.NET 콘솔 애플리케이션을 사용하여 Azure Key Vault에 비밀 저장 | Microsoft Docs
description: 이 자습서에서는 Azure Key Vault에서 비밀을 읽는 ASP.NET Core 애플리케이션을 구성하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 576ce0abc15a646738fea57dfabf43c889635a4b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416942"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>자습서: Linux VM 및 .NET 앱을 사용하여 Azure Key Vault에 비밀 저장

Azure Key Vault를 통해 애플리케이션, 서비스 및 IT 리소스에 액세스하는 데 필요한 API 키, 데이터베이스 연결 문자열과 같은 비밀을 보호할 수 있습니다.

이 자습서에서는 Azure 리소스의 관리 ID를 사용하여 Azure Key Vault에서 정보를 읽도록 .NET 콘솔 애플리케이션을 설정합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 키 자격 증명 모음 만들기
> * Key Vault에 비밀 저장
> * Azure Linux 가상 머신 만들기
> * 가상 머신에 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정
> * 콘솔 애플리케이션이 Key Vault에서 데이터를 읽는 데 필요한 권한 부여
> * Key Vault에서 비밀 검색

본론으로 들어가기 전에 [키 자격 증명 모음 기본 개념](key-vault-whatis.md#basic-concepts)을 읽어보세요.

## <a name="prerequisites"></a>필수 조건

* [Git](https://git-scm.com/downloads)
* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Azure CLI 2.0 이상](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 Azure Cloud Shell

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>관리 서비스 ID 이해

Azure Key Vault가 자격 증명을 안전하게 저장할 수 있도록 해당 코드 이외로 작성되지만 검색하려면 Azure Key Vault에 인증해야 합니다. 그러나 Key Vault에 대해 인증을 받으려면 자격 증명이 필요합니다. 이것은 클래식 부트스트랩 문제입니다. MSI(관리 서비스 ID)는 Azure 및 Azure AD(Azure Active Directory)를 사용하여 작업 시작을 훨씬 간단하게 만드는 부트스트랩 ID를 제공할 수 있습니다.

Azure 서비스(예: Virtual Machines, App Service 또는 Functions)에 대해 MSI를 사용하도록 설정하면 Azure는 Azure Active Directory에서 서비스의 인스턴스에 대해 서비스 주체를 만듭니다. 서비스의 인스턴스에 서비스 주체에 대한 자격 증명을 삽입합니다.

![MSI](media/MSI.png)

다음으로, 코드는 액세스 토큰을 가져오기 위해 Azure 리소스에 사용할 수 있는 로컬 메타데이터 서비스를 호출합니다.
코드는 Azure Key Vault 서비스를 인증하기 위해 로컬 MSI_ENDPOINT에서 가져오는 액세스 토큰을 사용합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

`az group create` 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

리소스 그룹을 미국 서부 위치에 만듭니다. 다음 예제에서 리소스 그룹의 이름을 선택하고 `YourResourceGroupName`을 바꿉니다.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

이 자습서 전체에서 이 리소스 그룹을 사용합니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

그런 다음, 리소스 그룹에 키 자격 증명 모음을 만듭니다. 다음 정보를 지정합니다.

* 키 자격 증명 모음 이름: 숫자, 문자 및 하이픈(0-9, a-z, A-Z 및 \-)만 포함할 수 있는 3~24자 문자열입니다.
* 리소스 그룹 이름
* 위치: **미국 서부**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

이때 사용자의 Azure 계정만이 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있습니다.

## <a name="add-a-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

이제 비밀을 추가합니다. 실제 시나리오에서는 보안이 필요하지만 애플리케이션에서 사용할 수 있는 SQL 연결 문자열 또는 기타 정보를 저장할 수 있습니다.

이 자습서에서는 다음 명령을 입력하여 키 자격 증명 모음에 비밀을 만듭니다. 비밀은 **AppSecret**이라 하며 해당 값은 **MySecret**입니다.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linux 가상 머신 만들기

`az vm create` 명령을 사용하여 VM을 만듭니다.

다음 예제에서는 **myVM**이라는 VM을 만들고 **azureuser**라는 사용자 계정을 추가합니다. `--generate-ssh-keys` 매개 변수는 SSH 키를 자동으로 생성하여 기본 키 위치( **~/.ssh**)에 배치하는 데 사용됩니다. 특정 키 집합을 대신 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다. 다음 예제 출력은 VM 만들기 작업이 성공했음을 보여줍니다.

```azurecli
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

VM의 출력에서 사용자의 `publicIpAddress`를 메모해 둡니다. 나중의 단계에서 VM에 액세스하는 데 이 주소를 사용합니다.

## <a name="assign-an-identity-to-the-vm"></a>VM에 ID 할당

다음 명령을 실행하여 가상 머신에 시스템 할당 ID를 만듭니다.

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

명령의 출력은 다음과 같아야 합니다.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

`systemAssignedIdentity`을 기록해 둡니다. 이 ID를 다음 단계에서 사용합니다.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Key Vault에 VM ID 권한 부여

이제 만든 ID에 Key Vault 권한을 부여할 수 있습니다. 다음 명령 실행:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>VM에 로그인

터미널을 사용하여 가상 머신에 로그인합니다.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Linux에 .NET Core 설치

Linux VM에서 다음을 수행합니다.

다음 명령을 실행하여 Microsoft 제품 키를 신뢰할 수 있는 것으로 등록합니다.

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

운영 체제에 따라 원하는 버전의 호스트 패키지 피드를 설치합니다.

```console
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

.NET을 설치하고 버전을 확인합니다.

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>샘플 .NET 앱 만들기 및 실행

다음 명령을 실행합니다. 콘솔에 "Hello World"가 표시되어야 합니다.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>비밀을 가져오도록 콘솔 앱 편집

Program.cs 파일을 열고 다음 패키지를 추가합니다.

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

이는 앱이 키 자격 증명 모음의 비밀에 액세스할 수 있도록 클래스 파일을 변경하는 2단계 프로세스입니다.

1. VM의 로컬 MSI 엔드포인트에서 토큰을 가져오면 VM이 Azure Active Directory에서 토큰을 가져옵니다.
1. 키 자격 증명 모음에 토큰을 전달하고 비밀을 가져옵니다.

   클래스 파일을 편집하여 다음 코드를 포함합니다.

   ```csharp
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
                   token = ojObject.Value.ToString();
               }
               return token;
           }
       }
   ```

지금까지 Azure Linux 가상 머신에서 실행하는 .NET 애플리케이션의 Azure Key Vault를 사용하여 작업을 수행하는 방법을 알아보았습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않을 때 리소스 그룹, 가상 머신 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 VM에 대한 리소스 그룹을 선택하고 **삭제**를 선택합니다.

`az keyvault delete` 명령을 사용하여 키 자격 증명 모음을 삭제합니다.

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
