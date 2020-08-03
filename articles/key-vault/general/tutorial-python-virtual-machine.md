---
title: 자습서 - Python에서 가상 머신이 있는 Azure Key Vault 사용 | Microsoft Docs
description: 이 자습서에서는 Key Vault에서 비밀을 읽도록 ASP.NET Core 애플리케이션을 구성합니다.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093936"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>자습서: Python에서 가상 머신이 있는 Azure Key Vault 사용

Azure Key Vault를 통해 애플리케이션, 서비스 및 IT 리소스에 액세스하는 데 필요한 API 키, 데이터베이스 연결 문자열과 같은 비밀을 보호할 수 있습니다.

이 자습서에서는 Azure Key Vault에서 정보를 읽는 콘솔 애플리케이션을 가져오는 방법을 알아봅니다. 이렇게 하려면 Azure 리소스의 관리 ID를 사용합니다. 

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 키 자격 증명 모음을 만듭니다.
> * 키 자격 증명 모음에 비밀 추가
> * 키 자격 증명 모음에서 비밀을 검색합니다.
> * Azure 가상 머신을 만듭니다.
> * 관리 ID를 사용하도록 설정합니다.
> * VM ID에 사용 권한을 할당합니다.

시작하기 전에 [Key Vault 기본 개념](basic-concepts.md)을 읽어보세요. 

Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Windows, Mac 및 Linux:
  * [Git](https://git-scm.com/downloads)
  * 이 자습서에서는 Azure CLI를 로컬로 실행해야 합니다. Azure CLI 버전 2.0.4 이상이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우에는 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>리소스 그룹 및 키 자격 증명 모음 만들기

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 이러한 비밀은 안전하게 유지하면서 애플리케이션에서 사용할 수 있도록 해야 하는 기타 정보 또는 SQL 연결 문자열일 수 있습니다.

Key Vault에 **AppSecret**라는 비밀을 만들려면 다음 명령을 입력합니다.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

이 비밀에는 값 **MySecret**이 저장됩니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기
다음 방법 중 하나를 사용하여 가상 머신을 만들 수 있습니다.

* [Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure 포털](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>VM에 ID 할당
이 단계에서는 Azure CLI에서 다음 명령을 실행하여 가상 머신에 시스템 할당 ID를 만들 것입니다.

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

다음 코드에 표시되는 시스템 할당 ID를 기록해 둡니다. 위 명령의 출력은 다음과 같습니다. 

```output
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

가상 머신에 로그온하려면 [Windows를 실행하는 Azure Virtual Machine에 연결 및 로그온](../../virtual-machines/windows/connect-logon.md)의 지침을 따르세요.

## <a name="create-and-run-a-sample-python-app"></a>샘플 Python 앱을 만들고 실행

다음 섹션은 예제 파일 *Sample.py*입니다. 이 예제는 [requests](https://2.python-requests.org/en/master/) 라이브러리를 사용하여 HTTP GET 호출을 수행합니다.

## <a name="edit-samplepy"></a>Sample.py 편집

*Sample.py*를 만든 후 파일을 열고 이 섹션의 코드를 복사합니다. 

코드는 다음 두 단계 프로세스를 제공합니다.
1. VM의 로컬 MSI 엔드포인트에서 토큰을 페치합니다.  
  이렇게 하면 Azure AD에서 토큰도 페치됩니다.
1. 토큰을 Key Vault에 전달하고 비밀을 페치합니다. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

다음 명령을 실행하여 비밀 값을 표시할 수 있습니다. 

```console
python Sample.py
```

위의 코드는 Windows Virtual Machine에서 Azure Key Vault 작업을 수행하는 방법을 보여줍니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 가상 머신 및 Key Vault를 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
