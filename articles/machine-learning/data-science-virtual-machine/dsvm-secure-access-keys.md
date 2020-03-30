---
title: 액세스 자격 증명을 안전하게 저장
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine에 대한 액세스 자격 증명을 안전하게 저장하는 방법을 알아봅니다. 관리되는 서비스 ID 및 Azure Key Vault를 사용하여 액세스 자격 증명을 저장하는 방법을 알아봅니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 1cb0c5094d49eac5a1c8f63406a28d2927d8fa94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477326"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Azure 데이터 과학 가상 머신에 액세스 자격 증명을 안전하게 저장

클라우드 응용 프로그램의 코드에 클라우드 서비스에 대한 인증을 인증하기 위한 자격 증명이 포함되어 있는 것이 일반적입니다. 이러한 자격 증명을 관리하고 보호하는 방법은 클라우드 응용 프로그램을 빌드할 때 잘 알려진 과제입니다. 이상적으로 자격 증명은 개발자 워크스테이션에 표시되거나 소스 제어에 체크 인해서는 안 됩니다.

Azure 리소스 기능에 [대한 관리ID기능을](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) 사용하면 Azure 서비스에 Azure Active Directory(Azure AD)에서 자동으로 관리되는 ID를 제공하여 이 문제를 더 쉽게 해결할 수 있습니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 어떤 자격 증명도 포함할 필요가 없습니다.

자격 증명을 보호하는 한 가지 방법은 비밀 및 암호화 키를 안전하게 저장하는 관리되는 Azure 서비스인 [Azure Key Vault와](https://docs.microsoft.com/azure/key-vault/)함께 MSI(Windows Installer)를 사용하는 것입니다. 관리되는 ID를 사용하여 키 자격 증명 모음에 액세스한 다음 키 자격 증명 모음에서 승인된 암호 및 암호화 키를 검색할 수 있습니다.

Azure 리소스 및 Key Vault에 대한 관리ID에 대한 설명서는 이러한 서비스에 대한 자세한 정보를 위한 포괄적인 리소스로 구성됩니다. 이 아티클의 나머지 부분은 Azure 리소스에 액세스하기 위해 DSVM(데이터 과학 가상 머신)에서 MSI 및 Key Vault의 기본적인 사용을 설명합니다. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>DSVM에서 관리 ID 만들기

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>VM 보안 원칙 에 키 볼트 액세스 권한 할당

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>DSVM에서 키 자격 증명 모음의 암호에 액세스

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>DSVM에서 스토리지 키에 액세스

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>Python에서 키 자격 증명 모음에 액세스

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Azure CLI에서 키 자격 증명 모음에 액세스

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
