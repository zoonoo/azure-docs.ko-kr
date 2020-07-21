---
title: Azure Kubernetes 서비스에서 호스트 기반 암호화 사용 (AKS)
description: AKS (Azure Kubernetes Service) 클러스터에서 호스트 기반 암호화를 구성 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: d2b34d8c3090eb6ae3f1445ff1fc663d90367977
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517725"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>AKS (Azure Kubernetes Service)의 호스트 기반 암호화 (미리 보기)

호스트 기반 암호화를 사용 하 여 AKS agent 노드의 vm 호스트에 저장 된 데이터는 미사용 및 저장소 서비스로 암호화 된 흐름에서 암호화 됩니다. 즉, 임시 디스크는 플랫폼 관리 키를 사용 하 여 미사용에 암호화 됩니다. OS 및 데이터 디스크의 캐시는 해당 디스크에 설정 된 암호화 유형에 따라 플랫폼 관리 키 또는 고객 관리 키를 사용 하 여 미사용에 암호화 됩니다. 기본적으로 AKS를 사용 하는 경우 OS 및 데이터 디스크는 플랫폼 관리 키를 사용 하 여 미사용으로 암호화 됩니다. 즉, 이러한 디스크에 대 한 캐시도 플랫폼 관리 키를 사용 하 여 미사용에서 기본적으로 암호화 됩니다.  [Azure Kubernetes Service에서 azure 디스크를 사용 하 여 사용자 고유의 키 (BYOK)를 사용](azure-disk-customer-managed-keys.md)하 여 고유한 관리 키를 지정할 수 있습니다. 또한이 단계에서 지정한 키를 사용 하 여 이러한 디스크에 대 한 캐시를 암호화 합니다.


## <a name="before-you-begin"></a>시작하기 전에

이 기능은 클러스터를 만들거나 노드 풀을 만들 때만 설정할 수 있습니다.

> [!NOTE]
> 호스트 기반 암호화는 azure [지역][supported-regions] 에서 사용할 수 있으며, azure 관리 디스크의 서버 쪽 암호화와 지원 되는 특정 [VM 크기만][supported-sizes]지원 합니다.

### <a name="prerequisites"></a>필수 구성 요소

- `aks-preview`CLI 확장 v 0.4.55 이상을 설치 했는지 확인 합니다.
- `EncryptionAtHost`사용 아래에 기능 플래그가 있는지 확인 `Microsoft.Compute` 합니다.
- `EnableEncryptionAtHostPreview`사용 아래에 기능 플래그가 있는지 확인 `Microsoft.ContainerService` 합니다.

### <a name="register-encryptionathost--preview-features"></a>`EncryptionAtHost`미리 보기 기능 등록

호스트 기반 암호화를 사용 하는 AKS 클러스터를 만들려면 `EnableEncryptionAtHostPreview` 구독에서 및 기능 플래그를 사용 하도록 설정 해야 합니다 `EncryptionAtHost` .

`EncryptionAtHost`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 하 여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"

az feature register --namespace "Microsoft.ContainerService"  --name "EnableEncryptionAtHostPreview"
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"

az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEncryptionAtHostPreview')].{Name:name,State:properties.state}"
```

준비가 되 면 `Microsoft.ContainerService` `Microsoft.Compute` [az provider register][az-provider-register] 명령을 사용 하 여 및 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.Compute

az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대 한 고객 지원에서 부분적으로 다룹니다. 따라서 이러한 기능은 프로덕션 용도로 사용할 수 없습니다. 자세한 내용은 다음 지원 문서를 참조 하세요.
>
> - [AKS 지원 정책](support-policies.md)
> - [Azure 지원 FAQ](faq.md)

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

기반 암호화를 호스트 하는 AKS 클러스터를 만들려면 최신 *AKS-preview* CLI 확장이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 *aks-preview* Azure CLI 확장을 설치 하거나 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 확인 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>제한 사항

- 새 노드 풀 또는 새 클러스터 에서만 사용 하도록 설정할 수 있습니다.
- Azure 관리 디스크의 서버 쪽 암호화와 지원 되는 특정 [VM 크기][supported-sizes]를 지 원하는 [azure 지역][supported-regions] 에서만 사용 하도록 설정할 수 있습니다.
- *VM 집합 유형*으로 Virtual Machine Scale Sets (vmss)를 기반으로 하는 AKS 클러스터 및 노드 풀이 필요 합니다.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>새 클러스터에서 호스트 기반 암호화 사용 (미리 보기)

클러스터를 만들 때 호스트 기반 암호화를 사용 하도록 클러스터 에이전트 노드를 구성 합니다. 플래그를 사용 `--aks-custom-headers` 하 여 헤더를 설정 합니다 `EnableEncryptionAtHost` .

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

호스트 기반 암호화를 사용 하지 않고 클러스터를 만들려는 경우 사용자 지정 매개 변수를 생략 하 여이 작업을 수행할 수 있습니다 `--aks-custom-headers` .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>기존 클러스터에서 호스트 기반 암호화 사용 (미리 보기)

클러스터에 새 노드 풀을 추가 하 여 기존 클러스터에서 호스트 기반 암호화를 사용 하도록 설정할 수 있습니다. 플래그를 사용 하 여 호스트 기반 암호화를 사용 하도록 새 노드 풀을 구성 `--aks-custom-headers` 합니다.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

호스트 기반 암호화 기능 없이 새 노드 풀을 만들려면 사용자 지정 매개 변수를 생략 하 여 그렇게 할 수 있습니다 `--aks-custom-headers` .

## <a name="next-steps"></a>다음 단계

[AKS 클러스터 보안에 대 한 모범 사례를][best-practices-security] 검토 하 여 [호스트 기반 암호화](../virtual-machines/linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)에 대해 자세히 알아보세요.


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/linux/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/linux/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
