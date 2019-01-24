---
title: 자습서 - Python에서 Azure Virtual Machine에 Azure Key Vault 사용 | Microsoft Docs
description: 이 자습서에서는 Key Vault에서 비밀을 읽는 Python 애플리케이션을 구성합니다.
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
ms.openlocfilehash: 1e364003093d5e37a75830386cafe855b0bdcad2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467404"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>자습서: Python에서 Azure 가상 머신에 Azure Key Vault 사용

Azure Key Vault를 통해 애플리케이션, 서비스 및 IT 리소스에 액세스하는 데 필요한 API 키, 데이터베이스 연결 문자열과 같은 비밀을 보호할 수 있습니다.

이 자습서에서는 Azure 리소스의 관리형 ID를 사용하여 Azure 웹 애플리케이션이 Azure Key Vault에서 정보를 읽는 데 필요한 단계를 수행합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 키 자격 증명 모음을 만듭니다.
> * 키 자격 증명 모음에 비밀을 저장합니다.
> * Azure 가상 머신을 만듭니다.
> * 가상 머신에 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정합니다.
> * 콘솔 애플리케이션이 키 자격 증명 모음에서 데이터를 읽는 데 필요한 권한을 부여합니다.
> * 키 자격 증명 모음에서 비밀을 검색합니다.

본론으로 들어가기 전에 [Key Vault에 대한 기본 개념](key-vault-whatis.md#basic-concepts)을 읽어보세요.

## <a name="prerequisites"></a>필수 조건
모든 플랫폼의 경우 다음이 필요합니다.

* Git([다운로드](https://git-scm.com/downloads)).
* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 버전 2.0.4 이상. Windows, Mac 및 Linux에 사용 가능합니다.

### <a name="managed-service-identity-and-how-it-works"></a>관리 서비스 ID 및 작동 방식
이 자습서에서는 MSI(관리 서비스 ID)를 사용합니다.

Azure Key Vault는 자격 증명을 안전하게 저장할 수 있으므로 코드에 없습니다. 이를 검색하려면 Key Vault에서 인증을 받아야 합니다. Key Vault에서 인증을 받으려면 자격 증명이 필요합니다. 그것은 클래식 부트스트랩 문제입니다. MSI는 Azure 및 Azure AD(Azure Active Directory)를 통해 작업을 간단하게 만드는 “부트스트랩 ID”를 제공합니다.

Azure 서비스(예: Virtual Machines, App Service 또는 Functions)에 대해 MSI를 사용하도록 설정하면 Azure는 Azure AD에서 서비스의 인스턴스에 대해 [서비스 주체](key-vault-whatis.md#basic-concepts)를 만듭니다. Azure는 서비스의 인스턴스에 서비스 주체에 대한 자격 증명을 삽입합니다. 

![MSI](media/MSI.png)

다음으로, 코드는 액세스 토큰을 가져오기 위해 Azure 리소스에 사용할 수 있는 로컬 메타데이터 서비스를 호출합니다.
코드는 Azure Key Vault 서비스를 인증하기 위해 로컬 MSI 엔드포인트에서 가져오는 액세스 토큰을 사용합니다. 

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

* 키 자격 증명 모음 이름: 3-24자의 문자열이어야 하며 0-9, a-z, A-Z 및 하이픈(-)만 포함해야 합니다.
* 리소스 그룹 이름
* 위치: **미국 서부**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
이 시점에서 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 안전하게 유지하면서 응용 프로그램에서 사용할 수 있도록 하는 데 필요한 SQL 연결 문자열 또는 기타 정보를 저장할 수 있습니다.

다음 명령을 입력하여 키 자격 증명 모음에 *AppSecret*라고 하는 비밀을 만듭니다. 이 비밀에는 **MySecret** 값이 저장됩니다.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm) 명령을 사용하여 VM을 만듭니다.

다음 예제에서는 *myVM*이라는 VM을 만들고 *azureuser*라는 사용자 계정을 추가합니다. `--generate-ssh-keys` 매개 변수는 SSH 키를 자동으로 생성하고 이를 기본 키 위치(*~/.ssh*)에 배치합니다. 특정 키 집합을 대신 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

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

VM의 출력에서 사용자 고유의 `publicIpAddress` 값을 적어 둡니다. 이 주소는 다음 단계에서 VM에 액세스하는 데 사용됩니다.

## <a name="assign-an-identity-to-the-virtual-machine"></a>가상 머신에 ID 할당
이 단계에서는 가상 머신에 대한 시스템 할당 ID를 만듭니다. Azure CLI에서 다음 명령을 실행합니다.

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

명령의 출력은 다음과 같습니다. **systemAssignedIdentity**의 값을 적어 둡니다. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>Key Vault에 가상 머신 ID 권한 부여
이제 Key Vault에 대한 ID 권한을 제공할 수 있습니다. 다음 명령 실행:

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>가상 머신에 로그인합니다.

[이 자습서](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)를 수행하여 가상 머신에 로그인합니다.

## <a name="create-and-run-the-sample-python-app"></a>샘플 Python 앱 만들기 및 실행

다음 예제 파일의 이름은 *Sample.py*입니다. 이 예제는 [requests](https://pypi.org/project/requests/2.7.0/) 라이브러리를 사용하여 HTTP GET 호출을 수행합니다.

## <a name="edit-samplepy"></a>Sample.py 편집
Sample.py를 만든 후 파일을 열고 다음 코드를 복사합니다. 코드는 두 단계로 이루어집니다. 
1. VM의 로컬 MSI 엔드포인트에서 토큰을 페치합니다. 그런 다음, 엔드포인트가 Azure Active Directory에서 토큰을 페치합니다.
2. 토큰을 Key Vault에 전달하고 비밀을 페치합니다. 

```
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

다음 명령을 실행하면 비밀 값이 표시됩니다. 

```
python Sample.py
```

위의 코드는 Windows Virtual Machine에서 Azure Key Vault 작업을 수행하는 방법을 보여줍니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
