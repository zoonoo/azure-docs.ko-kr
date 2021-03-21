---
title: Azure Kubernetes 서비스에서 호스트 기반 암호화 사용 (AKS)
description: AKS (Azure Kubernetes Service) 클러스터에서 호스트 기반 암호화를 구성 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: f4e599ae7aa81c15f86d0e8b1c934824010ea45b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102430159"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>AKS (Azure Kubernetes Service)의 호스트 기반 암호화 (미리 보기)

호스트 기반 암호화를 사용 하 여 AKS agent 노드의 vm 호스트에 저장 된 데이터는 미사용 및 저장소 서비스로 암호화 된 흐름에서 암호화 됩니다. 즉, 임시 디스크는 플랫폼 관리 키를 사용 하 여 미사용에 암호화 됩니다. OS 및 데이터 디스크의 캐시는 해당 디스크에 설정 된 암호화 유형에 따라 플랫폼 관리 키 또는 고객 관리 키를 사용 하 여 미사용에 암호화 됩니다. 기본적으로 AKS를 사용 하는 경우 OS 및 데이터 디스크는 플랫폼 관리 키를 사용 하 여 미사용으로 암호화 됩니다. 즉, 이러한 디스크에 대 한 캐시도 플랫폼 관리 키를 사용 하 여 미사용에서 기본적으로 암호화 됩니다.  [Azure Kubernetes Service에서 azure 디스크를 사용 하 여 사용자 고유의 키 (BYOK)를 사용](azure-disk-customer-managed-keys.md)하 여 고유한 관리 키를 지정할 수 있습니다. 또한이 단계에서 지정한 키를 사용 하 여 이러한 디스크에 대 한 캐시를 암호화 합니다.


## <a name="before-you-begin"></a>시작하기 전에

이 기능은 클러스터를 만들거나 노드 풀을 만들 때만 설정할 수 있습니다.

> [!NOTE]
> 호스트 기반 암호화는 azure [지역][supported-regions] 에서 사용할 수 있으며, azure 관리 디스크의 서버 쪽 암호화와 지원 되는 특정 [VM 크기만][supported-sizes]지원 합니다.

### <a name="prerequisites"></a>필수 구성 요소

- `aks-preview`CLI 확장 v 0.4.73 이상 버전이 설치 되어 있는지 확인 합니다.
- `EnableEncryptionAtHostPreview`사용 아래에 기능 플래그가 있는지 확인 `Microsoft.ContainerService` 합니다.

호스트에서 Vm 또는 가상 머신 확장 집합에 대 한 암호화를 사용할 수 있으려면 구독에서 기능을 사용 하도록 설정 해야 합니다. 구독 **encryptionAtHost@microsoft.com** 에 사용할 수 있는 기능을 얻으려면 구독 id로 전자 메일을 보내세요. 

> [!IMPORTANT]
> **encryptionAtHost@microsoft.com** 계산 리소스에 대해 사용 하도록 설정 된 기능을 얻으려면 구독 id로 전자 메일을 보내야 합니다. 계산 리소스에 대해 스스로를 사용 하도록 설정할 수 없습니다.


### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

기반 암호화를 호스트 하는 AKS 클러스터를 만들려면 최신 *AKS-preview* CLI 확장이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 *aks-preview* Azure CLI 확장을 설치 하거나 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 확인 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>제한 사항

- 새 노드 풀 에서만 사용할 수 있습니다.
- Azure 관리 디스크의 서버 쪽 암호화와 지원 되는 특정 [VM 크기][supported-sizes]를 지 원하는 [azure 지역][supported-regions] 에서만 사용 하도록 설정할 수 있습니다.
- *VM 집합 유형* 으로 Virtual Machine Scale Sets (vmss)를 기반으로 하는 AKS 클러스터 및 노드 풀이 필요 합니다.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>새 클러스터에서 호스트 기반 암호화 사용 (미리 보기)

클러스터를 만들 때 호스트 기반 암호화를 사용 하도록 클러스터 에이전트 노드를 구성 합니다. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

호스트 기반 암호화를 사용 하지 않고 클러스터를 만들려는 경우 매개 변수를 생략 하 여이 작업을 수행할 수 있습니다 `--enable-encryption-at-host` .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>기존 클러스터에서 호스트 기반 암호화 사용 (미리 보기)

클러스터에 새 노드 풀을 추가 하 여 기존 클러스터에서 호스트 기반 암호화를 사용 하도록 설정할 수 있습니다. 매개 변수를 사용 하 여 호스트 기반 암호화를 사용 하도록 새 노드 풀을 구성 `--enable-encryption-at-host` 합니다.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

호스트 기반 암호화 기능 없이 새 노드 풀을 만들려면 매개 변수를 생략 하 여 그렇게 할 수 있습니다 `--enable-encryption-at-host` .

## <a name="next-steps"></a>다음 단계

[AKS 클러스터 보안에 대 한 모범 사례를][best-practices-security] 검토 하 여 [호스트 기반 암호화](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)에 대해 자세히 알아보세요.


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
