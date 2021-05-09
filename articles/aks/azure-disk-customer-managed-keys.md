---
title: AKS(Azure Kubernetes Service)에서 고객 관리형 키를 사용하여 Azure 디스크 암호화
description: BYOK(Bring Your Own Key)를 통해 AKS OS 및 데이터 디스크를 암호화합니다.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: c5c555d7eb5142f5f41f65b24f754c65450a2713
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776194"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Azure 디스크에 BYOK(Bring Your Own Key) 사용

Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키를 추가로 제어하기 위해 AKS 클러스터의 OS 및 데이터 디스크 모두에 미사용 암호화에 사용할 고객 관리형 키를 제공할 수 있습니다. [Linux][customer-managed-keys-linux] 및 [Windows][customer-managed-keys-windows]용 고객 관리형 키에 대해 자세히 알아보세요.

## <a name="limitations"></a>제한 사항
* 데이터 디스크 암호화 지원은 Kubernetes 버전 1.17 이상을 실행하는 AKS 클러스터로 제한됩니다.
* AKS 클러스터를 만들 때만 고객 관리형 키를 사용하여 OS 및 데이터 디스크를 암호화할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
* Key Vault를 사용하여 관리 디스크를 암호화할 때 *Azure Key Vault* 에 일시 삭제 및 제거 방지를 사용하도록 설정해야 합니다.
* Azure CLI 버전 2.11.1 이상이 필요합니다.

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault 인스턴스 만들기

Azure Key Vault 인스턴스를 사용하여 키를 저장합니다.  필요에 따라 Azure Portal을 사용하여 [Azure Key Vault로 고객 관리형 키를 구성][byok-azure-portal]할 수 있습니다.

새 ‘리소스 그룹’을 만든 다음 새 *Key Vault* 인스턴스를 만들고 일시 삭제 및 제거 방지를 사용하도록 설정합니다.  각 명령에 동일한 지역 및 리소스 그룹 이름을 사용해야 합니다.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>DiskEncryptionSet의 인스턴스 만들기

*myKeyVaultName* 을 키 자격 증명 모음의 이름으로 바꿉니다.  또한 다음 단계를 완료하기 위해서는 Azure Key Vault에 저장된 ‘키’가 필요합니다.  이전 단계에서 만든 키 자격 증명 모음에 기존 키를 저장하거나 [새 키를 생성][key-vault-generate]하고 아래의 *myKeyName* 을 키 이름으로 바꿉니다.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>키 자격 증명 모음에 DiskEncryptionSet 액세스 권한 부여

이전 단계에서 만든 DiskEncryptionSet 및 리소스 그룹을 사용하여 Azure Key Vault에 대한 DiskEncryptionSet 리소스 액세스 권한을 부여합니다.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>새 AKS 클러스터 만들기 및 OS 디스크를 암호화

**새 리소스 그룹** 및 AKS 클러스터를 만든 다음, 키를 사용하여 OS 디스크를 암호화합니다. 고객 관리형 키는 1.17보다 높은 Kubernetes 버전에서만 지원됩니다. 

> [!IMPORTANT]
> AKS 클러스터에 새 리소스 그룹 만들기

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

위에서 만든 클러스터에 새 노드 풀을 추가하면 만들 때 제공된 고객 관리형 키를 사용해 OS 디스크를 암호화할 수 있습니다.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>AKS 클러스터 데이터 디스크 암호화(선택 사항)
v1.17.2에서 데이터 디스크용 키가 제공되지 않은 경우 OS 디스크 암호화 키를 사용하여 데이터 디스크를 암호화할 수 있으며, 다른 키로 AKS 데이터 디스크를 암호화할 수도 있습니다.

> [!IMPORTANT]
> 적절한 AKS 자격 증명을 보유해야 합니다. 관리 ID에는 diskencryptionset가 배포되는 리소스 그룹에 대한 참가자 액세스 권한이 있어야 합니다. 그렇지 않으면 관리 ID에 권한이 없다는 것을 알리는 오류가 표시됩니다.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

다음 정보를 포함하는 **byok-azure-disk.yaml** 이라는 파일을 만듭니다.  myAzureSubscriptionId, myResourceGroup, myDiskEncrptionSetName을 해당하는 값으로 바꾸고 yaml을 적용합니다.  DiskEncryptionSet가 배포되는 리소스 그룹을 사용해야 합니다.  Azure Cloud Shell을 사용하는 경우 이 파일은 가상 또는 실제 시스템에서 작업하고 있는 것처럼 vi 또는 nano를 사용하여 만들 수 있습니다.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
다음으로 AKS 클러스터에서 이 배포를 실행합니다.
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>다음 단계

[AKS 클러스터 보안 모범 사례][best-practices-security] 검토

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions