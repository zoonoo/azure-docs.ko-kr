---
title: 고객 관리 키를 사용하여 AKS(Azure Kubernetes Service)에서 Azure 디스크 암호화
description: AKS OS 및 데이터 디스크를 암호화하기 위해 BYOK(자체 키)를 가져옵니다.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596507"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>AZURE Kubernetes 서비스(AKS)에서 Azure 디스크를 사용하는 BYOK(사용자 고유키) 가져오기

Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 에서 관리하는 키로 암호화됩니다. 암호화 키를 추가로 제어하려면 고객이 [관리하는 키를][customer-managed-keys] 제공하여 AKS 클러스터의 OS 및 데이터 디스크 모두에 미사용 암호화에 사용할 수 있습니다.

> [!NOTE]
> BYOK Linux 및 Windows 기반 AKS 클러스터는 Azure 관리 디스크의 서버 측 암호화를 지원하는 [Azure 지역에서][supported-regions] 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

* 이 문서에서는 *새 AKS 클러스터를*만드는 것으로 가정합니다.

* 키 볼트를 사용하여 관리되는 디스크를 암호화할 때 *Azure Key Vault에* 대한 소프트 삭제 및 제거 보호를 사용하도록 설정해야 합니다.

* Azure CLI 버전 2.0.79 이상및 aks-preview 0.4.26 확장이 필요합니다.

> [!IMPORTANT]
> AKS 미리보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 대로" 및 "사용 가능한 경우"로 제공되며 서비스 수준 계약 및 제한 보증에서 제외됩니다. AKS 미리 보기는 최선의 노력을 기울여 고객 지원에 의해 부분적으로 적용됩니다. 따라서 이러한 기능은 프로덕션 용으로 사용되지 않습니다. 추가 내용은 다음 지원 문서를 참조하십시오.
>
> * [AKS 지원 정책](support-policies.md)
> * [Azure 지원 FAQ](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>최신 AKS CLI 미리 보기 확장 설치

고객 관리 키를 사용하려면 *aks-preview* CLI 확장 버전 0.4.26 이상이 필요합니다. az 확장 [추가][az-extension-add] 명령을 사용하여 *aks 미리 보기* Azure CLI 확장을 설치한 다음 az [확장 업데이트][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Azure 키 볼트 인스턴스 만들기

Azure 키 볼트 인스턴스를 사용하여 키를 저장합니다.  선택적으로 Azure 포털을 사용하여 [Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 구성할][byok-azure-portal] 수 있습니다.

새 *리소스 그룹을*만든 다음 새 *Key Vault* 인스턴스를 만들고 소프트 삭제 및 제거 보호를 사용하도록 설정합니다.  각 명령에 대해 동일한 지역 및 리소스 그룹 이름을 사용해야 합니다.

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>디스크 암호화 집합의 인스턴스 만들기

*myKeyVaultName을* 키 자격 증명 모음의 이름으로 바꿉니다.  다음 단계를 완료하려면 Azure 키 자격 증명 모음에 저장된 *키도* 필요합니다.  이전 단계에서 만든 키 자격 증명 모음에 기존 키를 저장하거나 [새 키를 생성하고][key-vault-generate] 아래 *myKeyName을* 키 이름으로 바꿉니다.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>키 자격 증명 모음에 대한 디스크 암호화집합 액세스 권한 부여

이전 단계에서 만든 DiskEncryptionSet 및 리소스 그룹을 사용하고 Azure 키 자격 증명 모음에 대한 DiskEncryptionSet 리소스 액세스를 부여합니다.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>새 AKS 클러스터를 만들고 OS 디스크를 암호화합니다.

새 **리소스 그룹** 및 AKS 클러스터를 만든 다음 키를 사용하여 OS 디스크를 암호화합니다. 고객 관리 키는 1.17보다 큰 Kubernetes 버전에서만 지원됩니다. 

> [!IMPORTANT]
> AKS 클러스터에 대한 새 resoruce 그룹을 만들어야 합니다.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

위에 만든 클러스터에 새 노드 풀이 추가되면 생성 중에 제공된 고객 관리 키가 OS 디스크를 암호화하는 데 사용됩니다.

## <a name="encrypt-your-aks-cluster-data-disk"></a>AKS 클러스터 데이터 디스크 암호화

자체 키로 AKS 데이터 디스크를 암호화할 수도 있습니다.

> [!IMPORTANT]
> 적절한 AKS 자격 증명이 있는지 확인합니다. 서비스 주체는 디스크 암호화 집합이 배포된 리소스 그룹에 대한 기여자 액세스 권한이 있어야 합니다. 그렇지 않으면 서비스 주체에 사용 권한이 없다는 오류가 발생합니다.

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

다음 정보가 포함된 **byok-azure-disk.yaml이라는** 파일을 만듭니다.  myAzureSubscriptionId, myResourceGroup 및 myDiskEncrptionName을 값으로 바꾸고 yaml을 적용합니다.  DiskEncryptionSet이 배포된 리소스 그룹을 사용해야 합니다.  Azure Cloud Shell을 사용하는 경우 이 파일은 가상 또는 물리적 시스템에서 작업하는 것처럼 vi 또는 nano를 사용하여 만들 수 있습니다.

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
그런 다음 AKS 클러스터에서 이 배포를 실행합니다.
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>제한 사항

* BYOK는 현재 특정 [Azure 지역에서][supported-regions] GA 및 미리 보기에서만 사용할 수 있습니다.
* Kubernetes 버전 1.17 이상에서 지원되는 OS 디스크 암호화   
* BYOK가 지원되는 지역에서만 사용 가능
* 현재 고객 관리 키로 암호화하는 것은 새 AKS 클러스터에만 사용되며 기존 클러스터를 업그레이드할 수 없습니다.
* 가상 컴퓨터 규모 집합을 사용하는 AKS 클러스터는 필요하며 가상 컴퓨터 가용성 집합은 지원하지 않습니다.


## <a name="next-steps"></a>다음 단계

[AKS 클러스터 보안에 대한 모범 사례][best-practices-security] 검토

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
