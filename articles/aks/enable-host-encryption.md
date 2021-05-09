---
title: AKS(Azure Kubernetes Service)에서 호스트 기반 암호화 사용
description: AKS(Azure Kubernetes Service) 클러스터에서 호스트 기반 암호화를 구성하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772922"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>AKS(Azure Kubernetes Service)의 호스트 기반 암호화(미리 보기)

호스트 기반 암호화를 통해, AKS 에이전트 노드 VM의 VM 호스트에 저장된 데이터는 미사용 암호화되고 스토리지 서비스로 암호화되어 흐릅니다. 즉, 임시 디스크는 플랫폼 관리형 키를 사용하여 미사용 암호화됩니다. OS 및 데이터 디스크의 캐시는 해당 디스크에 설정된 암호화 유형에 따라 플랫폼 관리형 키 또는 고객 관리형 키를 사용하여 미사용 암호화됩니다. 기본적으로 AKS를 사용하는 경우 OS 및 데이터 디스크는 플랫폼 관리형 키를 사용하여 미사용 암호화됩니다. 즉, 이러한 디스크에 대한 캐시도 플랫폼 관리형 키를 사용하여 기본적으로 미사용 암호화됩니다.  [Azure Kubernetes Service에서 Azure 디스크를 사용하여 BYOK(Bring your own key)](azure-disk-customer-managed-keys.md)에 따라 고유한 관리형 키를 지정할 수 있습니다. 또한 이 단계에서 지정한 키를 사용하여 이러한 디스크에 대한 캐시도 암호화합니다.


## <a name="before-you-begin"></a>시작하기 전에

해당 기능은 클러스터를 만들거나 노드 풀을 만들 때만 설정할 수 있습니다.

> [!NOTE]
> 호스트 기반 암호화는 [Azure 지역][supported-regions]에서 사용할 수 있으며, 특정 [지원되는 VM 크기][supported-sizes]에 적합해야 하고, Azure 관리 디스크의 서버 쪽 암호화를 지원해야 합니다.

### <a name="prerequisites"></a>사전 요구 사항

- `aks-preview` CLI 확장 버전 0.4.73 이상이 설치되어 있는지 확인합니다.
- `Microsoft.ContainerService` 아래에 `EnableEncryptionAtHostPreview` 기능 플래그가 사용하도록 설정되어 있는지 확인합니다.

Azure Kubernetes Service 클러스터에 대한 EncryptionAtHost 속성을 사용하기 전에 구독에 이 기능을 사용하도록 설정해야 합니다. 다음 단계를 수행하여 구독에 이 기능을 사용하도록 설정하세요.

1. 다음 명령을 실행하여 구독에 대한 기능 등록

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. 기능을 테스트하기 전에 아래 명령을 사용하여 등록 상태가 등록됨으로 나타나는지(몇 분 정도 소요됨) 확인하세요.

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

호스트 기반 암호화를 사용하는 AKS 클러스터를 만들려면 최신 *aks-preview* CLI 확장이 필요합니다. [az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치하거나 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>제한 사항

- 새 노드 풀에서만 사용하도록 설정할 수 있습니다.
- Azure 관리 디스크의 서버 쪽 암호화를 지원하는 [Azure 지역][supported-regions]에서만 사용 설정할 수 있으며, [지원되는 VM 크기][supported-sizes]만 사용할 수 있습니다.
- *VM 집합 유형* 으로 VMSS(Virtual Machine Scale Sets)를 기반으로 하는 AKS 클러스터 및 노드 풀이 필요합니다.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>새 클러스터에서 호스트 기반 암호화 사용(미리 보기)

클러스터를 만들 때 호스트 기반 암호화를 사용하도록 클러스터 에이전트 노드를 구성합니다. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

호스트 기반 암호화를 사용하지 않고 클러스터를 만들려는 경우 매개 변수 `--enable-encryption-at-host`를 생략하여 이 작업을 수행할 수 있습니다.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>기존 클러스터에서 호스트 기반 암호화 사용(미리 보기)

클러스터에 새 노드 풀을 추가하여 기존 클러스터에서 호스트 기반 암호화를 사용하도록 설정할 수 있습니다. `--enable-encryption-at-host` 매개 변수를 사용하여 호스트 기반 암호화를 사용하도록 새 노드 풀을 구성합니다.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

호스트 기반 암호화 기능 없이 새 노드 풀을 만들려면 `--enable-encryption-at-host` 매개 변수를 생략하여 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

[AKS 클러스터 보안에 대한 모범 사례][best-practices-security]를 검토하고 [호스트 기반 암호화](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)에 대해 자세히 알아보세요.


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
