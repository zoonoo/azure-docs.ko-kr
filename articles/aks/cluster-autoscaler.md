---
title: AKS(Azure Kubernetes Service)에서 클러스터 자동 크기 조정기 사용
description: 클러스터 자동 크기 조정기를 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기를 조정하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 58552914f369c49eed33ccefbb7736cf8dbf1fc6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475635"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>미리 보기-Azure Kubernetes Service (AKS)에서 응용 프로그램 요구에 맞게 클러스터를 자동으로 크기 조정

AKS(Azure Kubernetes Service)에서 애플리케이션 수요에 맞추려면 워크로드를 실행하는 노드 수를 조정해야 할 수 있습니다. 클러스터 자동 크기 조정기 구성 요소는 리소스 제약 조건으로 인해 예약할 수 없는 클러스터의 Pod를 확인할 수 있습니다. 문제가 발견되면 애플리케이션 수요에 맞게 노드 수가 증가합니다. 또한 실행 중인 Pod가 부족한지 노드를 주기적으로 확인하고 필요에 따라 노드 수가 감소합니다. AKS 클러스터에서 노드 수를 자동으로 강화하거나 규모 축소하는 이 기능을 통해 효율적이고 비용 효과적인 클러스터를 실행할 수 있습니다.

이 문서에서는 AKS 클러스터에서 클러스터 자동 크기 조정기를 사용하도록 설정하고 관리하는 방법을 보여 줍니다. Autoscaler 클러스터는 단일 노드 풀을 사용 하 여 AKS 클러스터에 미리 보기에만 테스트 해야 합니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스, 옵트인 합니다. 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 미리 보기에서이 기능이 없는 프로덕션 사용 해야 합니다. 공개 미리 보기에서 기능 '최상의' 지원에 속합니다. AKS 기술 지원 팀의 지원 업무 시간은 태평양 표준 시간대 (PST)만 제공 됩니다. 추가 정보는 다음과 같은 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전 2.0.65 실행 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

클러스터 autoscaler를 지 원하는 AKS 클러스터에서 가상 머신 확장 집합을 사용 하 고 Kubernetes 버전을 실행 해야 *1.12.7* 이상. 이 확장 집합 지원은 미리 보기로 제공됩니다. 확장 집합을 사용하는 클러스터를 옵트인하고 만들려면 먼저 다음 예제와 같이 [az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치합니다.

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> 이전에 설치한 경우 합니다 *aks 미리 보기* 사용 하 여 확장을 설치 가능한 업데이트를 `az extension update --name aks-preview` 명령입니다.

### <a name="register-scale-set-feature-provider"></a>확장 집합 기능 공급자 등록

확장 집합을 사용하는 AKS를 만들려면 구독에서 기능 플래그도 사용해야 합니다. *VMSSPreview* 기능 플래그를 등록하려면 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용합니다.

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>제한 사항

만들고 클러스터 autoscaler를 사용 하는 AKS 클러스터를 관리 하는 경우 다음과 같은 제한이 있습니다.

* HTTP 응용 프로그램 라우팅 추가 기능을 사용할 수 없습니다.
* (현재 AKS에서 미리 보기)는에서 여러 노드 풀은 현재 사용할 수 없습니다.

## <a name="about-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 정보

업무 시간에서 야간 사이에 또는 주말에 변화하는 애플리케이션 수요에 맞게 조정하기 위해 일반적으로 클러스터에는 자동으로 크기를 조정하는 방법이 필요합니다. AKS 클러스터는 다음 두 가지 방법 중 하나로 크기를 조정할 수 있습니다.

* **클러스터 자동 크기 조정기**는 리소스 제약 조건으로 인해 노드에서 예약할 수 없는 Pod를 확인합니다. 이에 따라 클러스터가 자동으로 노드 수를 늘립니다.
* **Horizontal Pod Autoscaler**는 Kubernetes 클러스터에서 메트릭 서버를 사용하여 Pod의 리소스 수요를 모니터링합니다. 서비스에 더 많은 리소스가 필요한 경우 수요에 맞게 자동으로 Pod 수가 증가합니다.

![종종 클러스터 자동 크기 조정기 및 Horizontal Pod Autoscaler가 연동되어 필요한 애플리케이션 수요를 지원함](media/autoscaler/cluster-autoscaler.png)

Horizontal Pod Autoscaler 및 클러스터 자동 크기 조정기가 둘 다 필요에 따라 Pod 및 노드 수를 줄일 수도 있습니다. 클러스터 자동 크기 조정기는 일정 기간 사용되지 않은 용량이 있는 경우 노드 수를 줄입니다. 클러스터 자동 크기 조정기가 제거할 노드의 Pod는 클러스터의 다른 위치에서 안전하게 예약됩니다. 다음 상황에서처럼 Pod를 이동할 수 없는 경우 클러스터 자동 크기 조정기가 규모를 축소하지 못할 수 있습니다.

* Pod가 직접 생성되었고 컨트롤러 개체(예: 배포 또는 복제본 세트)에서 지원되지 않습니다.
* PDB(Pod Disruption Budget)가 너무 제한적이고 이를 사용하면 Pod 수가 특정 임계값보다 낮아질 수 없습니다.
* Pod는 다른 노드에서 예약된 경우 적용할 수 없는 노드 선택기 또는 선호도 방지를 사용합니다.

클러스터 자동 크기 조정기가 규모를 축소하지 못할 수 있는 경우에 대한 자세한 내용은 [클러스터 자동 크기 조정기가 노드를 제거할 수 없도록 하는 Pod 유형은 무엇인가요?][autoscaler-scaledown]를 참조하세요.

클러스터 자동 크기 조정기는 크기 조정 이벤트와 리소스 임계값 사이의 시간 간격 같은 항목에 시작 매개 변수를 사용합니다. 이러한 매개 변수는 Azure 플랫폼에 의해 정의되며 현재는 사용자가 조정하도록 표시되지 않습니다. 클러스터 자동 크기 조정기가 사용하는 매개 변수에 대한 자세한 내용은 [클러스터 자동 크기 조정기 매개 변수란?][autoscaler-parameters]을 참조하세요.

두 개의 자동 크기 조정기가 연동되고 종종 둘 다 하나의 클러스터에 배포됩니다. 결합된 경우 Horizontal Pod Autoscaler는 애플리케이션 수요를 충족하는 데 필요한 개수의 Pod를 실행하는 데 중점을 둡니다. 클러스터 자동 크기 조정기는 예약된 Pod를 지원하는 데 필요한 개수의 노드를 실행하는 데 중점을 둡니다.

> [!NOTE]
> 클러스터 자동 크기 조정기를 사용하면 수동 크기 조정이 사용하지 않도록 설정됩니다. 클러스터 자동 크기 조정기가 필요한 노드 수를 결정하도록 해보겠습니다. 클러스터 크기를 수동으로 조정하려면 [클러스터 자동 크기 조정기를 사용하지 않도록 설정](#disable-the-cluster-autoscaler)합니다.

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS 클러스터를 만들고 클러스터 자동 크기 조정기를 사용하도록 설정

AKS 클러스터를 만들어야 하는 경우 [az aks create][az-aks-create] 명령을 사용합니다. 이전 [시작하기 전에](#before-you-begin) 섹션에 간략하게 설명된 대로 필요한 최소 버전 번호를 충족하거나 초과하는 *--kubernetes-version*을 지정합니다. 클러스터 자동 크기 조정기를 사용하도록 설정하고 구성하려면 *--enable-cluster-autoscaler* 매개 변수를 사용하고 *--min-count* 및 *--max-count* 노드를 지정합니다.

> [!IMPORTANT]
> 클러스터 자동 크기 조정기는 Kubernetes 구성 요소입니다. AKS 클러스터는 가상 머신 확장 집합을 노드에 사용하지만, Azure Portal에서 또는 Azure CLI를 사용하여 확장 집합 자동 크기 조정에 대한 설정을 직접 설정하거나 편집하지 마세요. Kubernetes 클러스터 자동 크기 조정기가 필수 크기 조정 설정을 자동으로 관리하게 두세요. 자세한 내용은 [MC_ 리소스 그룹의 AKS 리소스를 수정할 수 있나요?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc_-resource-group)를 참조하세요.

다음 예제에서는 가상 머신 확장 집합 및 클러스터 자동 크기 조정기를 사용하도록 설정하여 AKS 클러스터를 만들고 최소 *1*개 및 최대 *3*개 노드를 사용합니다.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

클러스터를 만들고 클러스터 자동 크기 조정기 설정을 구성하는 데 몇 분 정도 걸립니다.

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>기존 AKS 클러스터에서 클러스터 자동 크기 조정기를 사용하도록 설정

이전 [시작하기 전에](#before-you-begin) 섹션에 간략하게 설명된 대로 요구 사항을 충족하는 기존 AKS 클러스터에서 클러스터 자동 크기 조정기를 사용하도록 설정할 수 있습니다. [az aks update][az-aks-update] 명령을 사용하고, *--enable-cluster-autoscaler*를 선택한 다음, 노드 *--min-count* 및 *--max-count*를 지정합니다. 다음 예제에서는 최소 *1*개 및 최대 *3*개 노드를 사용하는 기존 클러스터에서 클러스터 자동 크기 조정기를 사용하도록 설정합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

최소 노드 수가 클러스터의 기존 노드 수보다 크면 추가 노드를 만드는 데 몇 분이 걸립니다.

## <a name="change-the-cluster-autoscaler-settings"></a>클러스터 자동 크기 조정기 설정 변경

기존 AKS 클러스터를 만들거나 업데이트하는 이전 단계에서 클러스터 자동 크기 조정기의 최소 노드 수는 *1*로 설정되고 최대 노드 수는 *3*으로 설정되었습니다. 애플리케이션 수요가 변경되면 클러스터 자동 크기 조정기의 노드 수를 조정해야 할 수 있습니다.

노드 수를 변경하려면 [az aks update][az-aks-update] 명령을 사용하고 최솟값 및 최댓값을 지정합니다. 다음 예제에서는 *--min-count*를 *1*로 설정하고 *--max-count*를 *5*로 설정합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> 미리 보기 중에는 현재 클러스터에 대해 설정된 것보다 높은 최소 노드 수를 설정할 수 없습니다. 예를 들어 현재 최소 개수가 *1*로 설정되어 있으면 최소 개수를 *3*으로 업데이트할 수 없습니다.

애플리케이션 및 서비스의 성능을 모니터링하고 클러스터 자동 크기 조정기 노드 수를 필요한 성능과 일치하도록 조정합니다.

## <a name="disable-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 사용 안 함

클러스터 자동 크기 조정기를 더 이상 사용하지 않으려면 [az aks update][az-aks-update] 명령을 사용하여 사용하지 않도록 설정하면 됩니다. 클러스터 자동 크기 조정기가 사용하지 않도록 설정되면 노드가 제거되지 않습니다.

클러스터 자동 크기 조정기를 제거하려면 다음 예제와 같이 *--disable-cluster-autoscaler* 매개 변수를 지정합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

[az aks scale][az-aks-scale] 명령을 사용하여 수동으로 클러스터 크기를 조정할 수 있습니다. Horizontal Pod Autoscaler를 사용하는 경우 해당 기능은 클러스터 자동 크기 조정기가 사용되지 않는 상태로 계속 실행되지만, 노드 리소스가 모두 사용 중인 경우 결국 Pod를 예약할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 노드 수를 자동으로 조정하는 방법을 설명했습니다. Horizontal Pod Autoscaler를 사용하여 애플리케이션을 실행하는 Pod 수를 자동으로 조정할 수도 있습니다. Horizontal Pod Autoscaler를 사용하는 단계는 [AKS에서 애플리케이션 크기 조정][aks-scale-apps]을 참조하세요.

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
