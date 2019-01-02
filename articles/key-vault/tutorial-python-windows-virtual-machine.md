---
title: 자습서 - Python에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법 | Microsoft Docs
description: 자습서 Key Vault에서 비밀을 읽도록 ASP.NET Core 애플리케이션 구성
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
ms.openlocfilehash: 26b5b16e3eb016edbe53c3526e51c3aa44f307b5
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583587"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-windows-virtual-machine-in-python"></a>자습서: Python에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법

Azure Key Vault를 통해 애플리케이션, 서비스 및 IT 리소스에 액세스하는 데 필요한 API 키, 데이터베이스 연결 문자열과 같은 비밀을 보호할 수 있습니다.

이 자습서에서는 Azure 리소스의 관리 ID를 사용하여 Azure 웹 애플리케이션이 Azure Key Vault에서 정보를 읽는 데 필요한 단계를 수행합니다. 이 자습서는 [Azure Web Apps](../app-service/app-service-web-overview.md)를 기반으로 합니다. 다음에서 사용 방법을 알아봅니다.

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

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 안전하게 유지하면서 애플리케이션에서 사용할 수 있도록 하는 데 필요한 SQL 연결 문자열 또는 기타 정보를 저장할 수 있습니다.

다음 명령을 입력하여 키 자격 증명 모음에 **AppSecret**라고 하는 비밀을 만듭니다. 이 비밀에는 **MySecret** 값이 저장됩니다.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Virtual Machine 만들기
아래 링크를 따라 Windows Virtual Machine 만들기

[Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli) 

[PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)

[포털](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-identity-to-virtual-machine"></a>Virtual Machine에 ID 할당
이 단계에서는 Azure CLI에서 다음 명령을 실행하여 가상 머신에 시스템 할당 ID를 만들 것입니다.

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

## <a name="give-virtual-machine-identity-permission-to-key-vault"></a>Key Vault에 가상 머신 ID 권한 부여
이제 다음 명령을 실행하여 위에서 만든 ID 권한을 Key Vault에 부여할 수 있습니다.

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>Virtual Machine에 로그인

이 [자습서](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)를 수행하면 됩니다.

## <a name="create-and-run-sample-python-app"></a>샘플 Python 앱을 만들고 실행

다음은 이름이 "Sample.py"인 예제 파일에 불과합니다. 이 예제는 [requests](http://docs.python-requests.org/master/) 라이브러리를 사용하여 HTTP GET 호출을 수행합니다.

## <a name="edit-samplepy"></a>Sample.py 편집
Sample.py를 만든 후 파일 열기 및 아래 코드 복사

```
The below is a 2 step process. 
1. Fetch a token from the local MSI endpoint on the VM which in turn fetches a token from Azure Active Directory
2. Pass the token to Key Vault and fetch your secret 
```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets a access_token from Azure Active Directory using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to Key Vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

By running you should see the secret value 
```
python Sample.py
```

The above code shows you how to do operations with Azure Key Vault in an Azure Windows Virtual Machine. 

## Next steps

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
