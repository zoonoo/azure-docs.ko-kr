---
title: 자습서 - Linux 가상 머신 및 Python 애플리케이션을 사용하여 Azure Key Vault에 비밀 저장 | Microsoft Docs
description: 이 자습서에서는 Azure Key Vault에서 비밀을 읽는 Python 애플리케이션을 구성하는 방법을 알아봅니다.
services: key-vault
author: mbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: a5923c3d5ea7888a3737d7976caded8eef9c1661
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702116"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>자습서: Linux VM 및 Python 앱을 사용하여 Azure Key Vault에 비밀 저장

Azure Key Vault를 통해 애플리케이션, 서비스 및 IT 리소스에 액세스하는 데 필요한 API 키, 데이터베이스 연결 문자열과 같은 비밀을 보호할 수 있습니다.

이 자습서에서는 Azure 리소스의 관리 ID를 사용하여 Azure Key Vault에서 정보를 읽도록 Azure 웹 애플리케이션을 설정합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 키 자격 증명 모음 만들기
> * 키 자격 증명 모음에 비밀 저장
> * Linux 가상 머신 만들기
> * 가상 머신에 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정
> * 콘솔 애플리케이션이 키 자격 증명 모음에서 데이터를 읽는 데 필요한 권한 부여
> * 키 자격 증명 모음에서 비밀 검색

본론으로 들어가기 전에 [Key Vault에 대한 기본 개념](key-vault-whatis.md#basic-concepts)을 이해해야 합니다.

## <a name="prerequisites"></a>필수 조건

* [Git](https://git-scm.com/downloads)
* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Azure CLI 버전 2.0.4 이상](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 Azure Cloud Shell

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>관리 서비스 ID 이해

Azure Key Vault는 자격 증명을 안전하게 저장할 수 있으므로 코드에 없습니다. 이를 검색하려면 Azure Key Vault에 대해 인증을 받아야 합니다. 그러나 Key Vault에 대해 인증을 받으려면 자격 증명이 필요합니다. 이것은 클래식 부트스트랩 문제입니다. MSI(관리 서비스 ID)는 Azure 및 Azure AD(Azure Active Directory)를 통해 작업을 간단하게 만드는 부트스트랩 ID를 제공합니다.

Azure 서비스(예: Virtual Machines, App Service 또는 Functions)에 대해 MSI를 사용하도록 설정하면 Azure는 Azure AD에서 서비스의 인스턴스에 대해 서비스 주체를 만듭니다. 서비스의 인스턴스에 서비스 주체에 대한 자격 증명을 삽입합니다.

![MSI](media/MSI.png)

다음으로, 코드는 액세스 토큰을 가져오기 위해 Azure 리소스에 사용할 수 있는 로컬 메타데이터 서비스를 호출합니다. 코드는 Azure Key Vault 서비스를 인증하기 위해 로컬 MSI 엔드포인트에서 가져오는 액세스 토큰을 사용합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

미국 서부 위치에서 다음 코드로 `az group create` 명령을 사용하여 리소스 그룹을 만듭니다. `YourResourceGroupName`은 원하는 이름으로 바꿉니다.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

이 자습서 전체에서 이 리소스 그룹을 사용합니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

다음으로, 이전 단계에서 만든 리소스 그룹에 키 자격 증명 모음을 만듭니다. 다음 정보를 지정합니다.

* 키 자격 증명 모음 이름: 3-24자의 문자열이어야 하며 0-9, a-z, A-Z 및 하이픈(-)만 포함해야 합니다.
* 리소스 그룹 이름
* 위치: **미국 서부**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

이 시점에서 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 이러한 비밀은 안전하게 유지하면서 애플리케이션에서 사용할 수 있도록 해야 하는 기타 정보 또는 SQL 연결 문자열을 저장할 수 있습니다.

다음 명령을 입력하여 키 자격 증명 모음에 *AppSecret*라고 하는 비밀을 만듭니다. 이 비밀에는 **MySecret** 값이 저장됩니다.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linux 가상 머신 만들기

`az vm create` 명령을 사용하여 VM을 만듭니다.

다음 예제에서는 **myVM**이라는 VM을 만들고 **azureuser**라는 사용자 계정을 추가합니다. `--generate-ssh-keys` 매개 변수는 SSH 키를 자동으로 생성하고 이를 기본 키 위치(**~/.ssh**)에 배치합니다. 특정 키 집합을 대신 만들려면 `--ssh-key-value` 옵션을 사용합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다. 다음 예제 출력은 VM 만들기 작업이 완료되었음을 보여줍니다.

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

VM의 출력에서 사용자 고유의 `publicIpAddress`를 메모해 둡니다. 나중의 단계에서 VM에 액세스하는 데 이 주소를 사용합니다.

## <a name="assign-an-identity-to-the-vm"></a>VM에 ID 할당

다음 명령을 실행하여 가상 머신에 시스템 할당 ID를 만듭니다.

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

명령의 출력은 다음과 같습니다.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

`systemAssignedIdentity`을 기록해 둡니다. 다음 단계에서 사용합니다.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Key Vault에 VM ID 권한 부여

이제 만든 ID에 Key Vault 권한을 부여할 수 있습니다. 다음 명령 실행:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>VM에 로그인

터미널을 사용하여 가상 머신에 로그인합니다.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>VM에 Python 라이브러리 설치

HTTP GET 호출을 위해 [요청](https://pypi.org/project/requests/2.7.0/) Python 라이브러리를 다운로드하여 설치합니다.

## <a name="create-edit-and-run-the-sample-python-app"></a>샘플 Python 앱 만들기, 편집 및 실행

**Sample.py**라고 하는 Python 파일을 만듭니다.

Sample.py를 열어 다음 코드를 포함하도록 편집합니다.

```python
# importing the requests library
  import requests
  
# Step 1: Fetch an access token from an MSI-enabled Azure resource      
  # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
  MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
  r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
  # This request gets an access token from Azure Active Directory by using the local MSI endpoint
  data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
  KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
  kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

위의 코드는 두 단계로 수행됩니다.

   1. VM의 로컬 MSI 엔드포인트에서 토큰을 페치합니다. 그런 다음, 엔드포인트가 Azure Active Directory에서 토큰을 페치합니다.
   1. 토큰을 키 자격 증명 모음에 전달하고 비밀을 페치합니다.

다음 명령을 실행합니다. 비밀 값이 표시됩니다.

```console
python Sample.py
```

이 자습서에서는 Linux 가상 머신에서 실행되는 Python 앱과 함께 Azure Key Vault를 사용하는 방법에 대해 알아보았습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않을 때 리소스 그룹, 가상 머신 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 VM에 대한 리소스 그룹을 선택하고 **삭제**를 선택합니다.

`az keyvault delete` 명령을 사용하여 키 자격 증명 모음을 삭제합니다.

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
