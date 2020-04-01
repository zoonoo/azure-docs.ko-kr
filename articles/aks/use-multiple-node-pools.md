---
title: Azure Kubernetes 서비스(AKS)에서 여러 노드 풀 사용
description: AKS(Azure Kubernetes Service)에서 클러스터에 대한 여러 노드 풀을 만들고 관리하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 607419787bc0bab243d6cc2b8cbaa0ec22921e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422324"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)에서 클러스터에 대한 여러 노드 풀 생성 및 관리

AZURE Kubernetes 서비스(AKS)에서 동일한 구성의 노드가 *노드 풀로 그룹화됩니다.* 이러한 노드 풀에는 응용 프로그램을 실행하는 기본 VM이 포함됩니다. *기본 노드 풀을*만드는 AKS 클러스터를 만들 때 초기 노드 수와 해당 크기(SKU)가 정의됩니다. 계산 또는 저장소 요구가 다른 응용 프로그램을 지원하려면 추가 노드 풀을 만들 수 있습니다. 예를 들어 이러한 추가 노드 풀을 사용하여 계산 집약적인 응용 프로그램에 GPU를 제공하거나 고성능 SSD 저장소에 액세스할 수 있습니다.

> [!NOTE]
> 이 기능을 사용하면 여러 노드 풀을 만들고 관리하는 방법을 보다 세한 제어할 수 있습니다. 따라서 만들기/업데이트/삭제를 위해 별도의 명령이 필요합니다. 이전에는 클러스터 `az aks create` `az aks update` 작업을 통해 관리클러스터 API를 사용했으며 제어 평면과 단일 노드 풀을 변경할 수 있는 유일한 옵션이었습니다. 이 기능은 에이전트Pool API를 통해 에이전트 풀에 대한 별도의 작업 `az aks nodepool` 집합을 노출하고 개별 노드 풀에서 작업을 실행하기 위해 명령 집합을 사용해야 합니다.

이 문서에서는 AKS 클러스터에서 여러 노드 풀을 만들고 관리하는 방법을 보여 주며, 이를 보여 주시고 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.2.0 이상 설치 및 구성이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하십시오.

## <a name="limitations"></a>제한 사항

여러 노드 풀을 지원하는 AKS 클러스터를 만들고 관리할 때 다음 제한 사항이 적용됩니다.

* [Azure Kubernetes 서비스(AKS)에서 할당량, 가상 시스템 크기 제한 및 지역 가용성을][quotas-skus-regions]참조하십시오.
* 기본적으로 첫 번째 노드 풀은 시스템 노드 풀을 삭제할 수 없습니다.
* AKS 클러스터는 표준 SKU 로드 밸런서를 사용하여 여러 노드 풀을 사용해야 하며 기본 SKU 로드 밸런서에서는 이 기능이 지원되지 않습니다.
* AKS 클러스터는 노드에 가상 시스템 배율 집합을 사용해야 합니다.
* 노드 풀의 이름은 소문자 숫자 문자만 포함할 수 있으며 소문자로 시작해야 합니다. Linux 노드 풀의 경우 길이는 1에서 12자 사이여야 하며 Windows 노드 풀의 경우 길이는 1에서 6자 사이여야 합니다.
* 모든 노드 풀은 동일한 가상 네트워크에 있어야 합니다.
* 클러스터 생성 시간에 여러 노드 풀을 만들 때 노드 풀에서 사용되는 모든 Kubernetes 버전은 제어 평면에 대한 버전 집합과 일치해야 합니다. 노드 풀 당 작업을 사용하여 클러스터를 프로비전한 후 업데이트할 수 있습니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

시작하려면 단일 노드 풀을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 [az 그룹 만들기][az-group-create] 명령을 사용 하 여 *eastus* 지역에서 *myResourceGroup* 라는 리소스 그룹을 만듭니다. 그런 다음 [az aks create][az-aks-create] 명령을 사용하여 *myAKSCluster라는* AKS 클러스터가 만들어집니다. *1.15.7의* *--kubernetes 버전에서는* 다음 단계에서 노드 풀을 업데이트하는 방법을 보여 줄 수 있습니다. [지원되는 Kubernetes 버전을][supported-versions]지정할 수 있습니다.

> [!NOTE]
> 여러 노드 풀을 사용할 때 *기본* 로드 밸로이터 SKU가 **지원되지 않습니다.** 기본적으로 AKS 클러스터는 Azure CLI 및 Azure 포털의 *표준* 로드 밸런서 SKU를 사용 하 여 만들어집니다.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

클러스터를 만드는 데 몇 분이 걸립니다.

> [!NOTE]
> 클러스터가 안정적으로 작동하도록 하려면 필수 시스템 서비스가 이 노드 풀에서 실행되고 있으므로 기본 노드 풀에서 최소 2개(2개) 노드를 실행해야 합니다.

클러스터가 준비되면 az [aks get-credentials][az-aks-get-credentials] 명령을 사용하여 다음과 함께 `kubectl`사용할 클러스터 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>노드 풀 추가

이전 단계에서 만든 클러스터에는 단일 노드 풀이 있습니다. [az aks 노드풀 추가][az-aks-nodepool-add] 명령을 사용하여 두 번째 노드 풀을 추가해 보겠습니다. 다음 예제는 *3개의* 노드를 실행하는 *mynodepool이라는* 노드 풀을 만듭니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> 노드 풀의 이름은 소문자로 시작해야 하며 숫자 문자만 포함할 수 있습니다. Linux 노드 풀의 경우 길이는 1에서 12자 사이여야 하며 Windows 노드 풀의 경우 길이는 1에서 6자 사이여야 합니다.

노드 풀의 상태를 보려면 [az aks 풀 풀 목록][az-aks-nodepool-list] 명령을 사용하고 리소스 그룹 및 클러스터 이름을 지정합니다.

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

다음 예제 출력은 *마이노드풀이* 노드 풀에 있는 세 개의 노드로 성공적으로 생성되었음을 보여 주며, 이전 단계에서 AKS 클러스터가 만들어졌을 때 기본 *nodepool1은* 노드 수가 *2인*으로 만들어졌습니다.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> 노드 풀을 추가할 때 *VmSize가* 지정되지 않은 경우 기본 크기는 Windows 노드 풀의 *Standard_DS2_v3* Linux 노드 풀의 *Standard_DS2_v2.* *OrchestratorVersion를* 지정하지 않으면 컨트롤 평면과 동일한 버전으로 기본설정됩니다.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>고유한 서브넷이 있는 노드 풀 추가(미리 보기)

워크로드를 사용하려면 논리적 격리를 위해 클러스터의 노드를 별도의 풀로 분할해야 할 수 있습니다. 이 격리는 클러스터의 각 노드 풀전용 별도의 서브넷으로 지원될 수 있습니다. 이렇게 하면 노드 풀간에 분할할 비연속 가상 네트워크 주소 공간이 있는 등의 요구 사항을 해결할 수 있습니다.

#### <a name="limitations"></a>제한 사항

* 노드풀에 할당된 모든 서브넷은 동일한 가상 네트워크에 속해야 합니다.
* 시스템 포드는 coreDNS를 통해 DNS 해상도와 같은 중요한 기능을 제공하기 위해 클러스터의 모든 노드에 액세스할 수 있어야 합니다.
* 노드 풀당 고유한 서브넷 할당은 미리 보기 중에 Azure CNI로 제한됩니다.
* 미리 보기 중에 노드 풀당 고유한 서브넷이 있는 네트워크 정책을 사용하는 것은 지원되지 않습니다.

전용 서브넷이 있는 노드 풀을 만들려면 노드 풀을 만들 때 서브넷 리소스 ID를 추가 매개 변수로 전달합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>노드 풀 업그레이드

> [!NOTE]
> 오류가 반환되면 클러스터 또는 노드 풀에서 업그레이드 및 확장 작업이 동시에 발생할 수 없습니다. 대신 각 작업 유형은 동일한 리소스에 대한 다음 요청 전에 대상 리소스에서 완료되어야 합니다. 이에 대한 자세한 내용은 [문제 해결 가이드에서](https://aka.ms/aks-pending-upgrade)확인할 수 있습니다.

AKS 클러스터가 처음 첫 번째 단계에서 `--kubernetes-version` 만들어졌을 때 *1.15.7이* 지정되었습니다. 이렇게 하면 제어 평면과 기본 노드 풀 모두에 대해 Kubernetes 버전이 설정됩니다. 이 섹션의 명령은 단일 특정 노드 풀을 업그레이드하는 방법을 설명합니다.

제어 평면의 Kubernetes 버전을 업그레이드하는 것과 노드 풀의 업그레이드 간의 관계는 [아래 섹션에](#upgrade-a-cluster-control-plane-with-multiple-node-pools)설명되어 있습니다.

> [!NOTE]
> 노드 풀 OS 이미지 버전은 클러스터의 Kubernetes 버전에 연결됩니다. 클러스터 업그레이드 후 OS 이미지 업그레이드만 받게 됩니다.

이 예제에는 두 개의 노드 풀이 있으므로 [az aks 노드풀 업그레이드를][az-aks-nodepool-upgrade] 사용하여 노드 풀을 업그레이드해야 합니다. *mynodepool을* Kubernetes *1.15.7로*업그레이드해 보겠습니다. az [aks 노드풀 업그레이드][az-aks-nodepool-upgrade] 명령을 사용하여 다음 예제와 같이 노드 풀을 업그레이드합니다.

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

[az aks 노드][az-aks-nodepool-list] 풀 목록 명령을 사용하여 노드 풀의 상태를 다시 나열합니다. 다음 예제에서는 *mynodepool이* *업그레이드* 상태에 있음을 *1.15.7로*보여 주며 다음과 같은 것을 보여 주며 다음과 같은

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

노드를 지정된 버전으로 업그레이드하는 데 몇 분 정도 걸립니다.

가장 좋은 방법은 AKS 클러스터의 모든 노드 풀을 동일한 Kubernetes 버전으로 업그레이드해야 합니다. 기본 `az aks upgrade` 동작은 이 정렬을 달성하기 위해 제어 평면과 함께 모든 노드 풀을 업그레이드하는 것입니다. 개별 노드 풀을 업그레이드하는 기능을 사용하면 롤롤링 업그레이드를 수행하고 노드 풀 간에 포드를 예약하여 위의 제약 조건 내에서 응용 프로그램 가동 시간을 유지할 수 있습니다.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>여러 노드 풀로 클러스터 제어 평면 업그레이드

> [!NOTE]
> Kubernetes는 표준 [시맨틱 버전 버전 전환](https://semver.org/) 체계를 사용합니다. 버전 번호는 *x.y.z로*표현되며 여기서 *x는* 주 버전이고 *y는* 마이너 버전이고 *z는* 패치 버전입니다. 예를 들어 버전 *1.12.6에서*1은 주 버전이고 12는 마이너 버전이고 6은 패치 버전입니다. 제어 평면의 Kubernetes 버전과 초기 노드 풀은 클러스터 를 만드는 동안 설정됩니다. 모든 추가 노드 풀에는 클러스터에 추가될 때 Kubernetes 버전이 설정됩니다. Kubernetes 버전은 노드 풀과 노드 풀 과 제어 평면 간에 다를 수 있습니다.

AKS 클러스터에는 Kubernetes 버전이 연결된 두 개의 클러스터 리소스 개체가 있습니다.

1. 클러스터 제어평면 Kubernetes 버전입니다.
2. Kubernetes 버전이 있는 노드 풀입니다.

컨트롤 평면은 하나 또는 여러 개의 노드 풀에 매핑됩니다. 업그레이드 작업의 동작은 사용되는 Azure CLI 명령에 따라 다릅니다.

AKS 컨트롤 플레인을 업그레이드하려면 을 사용해야합니다. `az aks upgrade` 이 명령은 제어 평면 버전과 클러스터의 모든 노드 풀을 업그레이드합니다.

`--control-plane-only` 플래그로 `az aks upgrade` 명령을 실행하여 클러스터 제어 평면만 업그레이드합니다. 클러스터의 연결된 노드 풀은 변경되지 않습니다.

개별 노드 풀을 업그레이드하려면 `az aks nodepool upgrade`을 사용해야합니다. 이 명령은 지정된 Kubernetes 버전으로 대상 노드 풀만 업그레이드합니다.

### <a name="validation-rules-for-upgrades"></a>업그레이드에 대한 유효성 검사 규칙

클러스터의 제어 평면 및 노드 풀에 대한 유효한 Kubernetes 업그레이드는 다음 규칙 집합에 의해 유효성을 검사합니다.

* 노드 풀을 업그레이드하는 유효한 버전에 대한 규칙:
   * 노드 풀 버전은 제어 평면과 동일한 *주* 버전을 가져야 합니다.
   * 노드 풀 *부* 버전은 제어 평면 버전의 두 *개의 마이너* 버전 내에 있어야 합니다.
   * 노드 풀 버전은 컨트롤 `major.minor.patch` 버전보다 클 수 없습니다.

* 업그레이드 작업 제출 규칙:
   * 제어 평면 또는 노드 풀 Kubernetes 버전을 다운그레이드할 수 없습니다.
   * 노드 풀 Kubernetes 버전을 지정하지 않은 경우 동작은 사용 중인 클라이언트에 따라 다릅니다. Resource Manager 템플릿의 선언은 노드 풀에 대해 정의된 기존 버전으로 되돌아갑니다.
   * 지정된 시간에 제어 평면 또는 노드 풀을 업그레이드하거나 확장할 수 있으며 단일 제어 평면 또는 노드 풀 리소스에 대해 여러 작업을 동시에 제출할 수 없습니다.

## <a name="scale-a-node-pool-manually"></a>노드 풀을 수동으로 확장

응용 프로그램 워크로드 요구가 변경되면 노드 풀의 노드 수를 확장해야 할 수 있습니다. 노드 수를 확장하거나 축소할 수 있습니다.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

노드 풀의 노드 수를 조정하려면 az [aks 노드 풀 스케일][az-aks-nodepool-scale] 명령을 사용합니다. 다음 예제에서는 *마이노드풀의* 노드 수를 *5로*확장합니다.

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

[az aks 노드][az-aks-nodepool-list] 풀 목록 명령을 사용하여 노드 풀의 상태를 다시 나열합니다. 다음 예제에서는 *mynodepool이* *5개의* 노드수를 새로 *가진 배율 조정* 상태에 있음을 보여 주며,

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

스케일 작업이 완료되는 데 몇 분 정도 걸립니다.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 사용으로 특정 노드 풀을 자동으로 확장

AKS는 [클러스터 자동 크기 조정기라는](cluster-autoscaler.md)기능을 사용하여 노드 풀을 자동으로 확장하는 별도의 기능을 제공합니다. 이 기능은 고유한 최소 및 노드 풀당 최대 축척 수를 가진 노드 풀당 활성화할 수 있습니다. [노드 풀당 클러스터 자동 크기 조정기를 사용하는](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)방법에 대해 알아봅니다.

## <a name="delete-a-node-pool"></a>노드 풀 삭제

더 이상 풀이 필요하지 않은 경우 풀을 삭제하고 기본 VM 노드를 제거할 수 있습니다. 노드 풀을 삭제하려면 [az aks 노드 풀 삭제][az-aks-nodepool-delete] 명령을 사용하고 노드 풀 이름을 지정합니다. 다음 예제에서는 이전 단계에서 만든 *mynoodepool을* 삭제합니다.

> [!CAUTION]
> 노드 풀을 삭제할 때 발생할 수 있는 데이터 손실에 대한 복구 옵션은 없습니다. 다른 노드 풀에서 포드를 예약할 수 없는 경우 해당 응용 프로그램을 사용할 수 없습니다. 사용 중 응용 프로그램에 데이터 백업이 없거나 클러스터의 다른 노드 풀에서 실행할 수 있는 기능이 없는 경우 노드 풀을 삭제하지 않도록 합니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

[az aks 노드 풀 목록 명령의][az-aks-nodepool-list] 다음 예제 출력은 *mynodepool이* *삭제* 상태에 있음을 보여 주며 있습니다.

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

노드 및 노드 풀을 삭제하는 데 몇 분 정도 걸립니다.

## <a name="specify-a-vm-size-for-a-node-pool"></a>노드 풀에 대한 VM 크기 지정

노드 풀을 만드는 이전 예제에서는 클러스터에서 만든 노드에 대해 기본 VM 크기가 사용되었습니다. 보다 일반적인 시나리오는 VM 크기와 기능이 다른 노드 풀을 만드는 것입니다. 예를 들어 CPU 또는 메모리가 많은 노드가 포함된 노드 풀또는 GPU 지원을 제공하는 노드 풀을 만들 수 있습니다. 다음 단계에서는 [오염 및 내포를 사용하여](#schedule-pods-using-taints-and-tolerations) Kubernetes 스케줄러에 이러한 노드에서 실행할 수 있는 포드에 대한 액세스를 제한하는 방법을 알려줍니다.

다음 예제에서는 *Standard_NC6* VM 크기를 사용하는 GPU 기반 노드 풀을 만듭니다. 이 VM은 엔비디아 테슬라 K80 카드에 의해 구동된다. 사용 가능한 VM 크기에 대한 자세한 내용은 [Azure의 Linux 가상 컴퓨터용 크기를][vm-sizes]참조하십시오.

[az aks 노드][az-aks-nodepool-add] 풀을 사용하여 노드 풀을 다시 추가합니다. 이번에는 *gpunodepool*이라는 이름을 지정하고 `--node-vm-size` 매개 변수를 사용하여 *Standard_NC6* 크기를 지정합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

[az aks 노드 풀 목록 명령의][az-aks-nodepool-list] 다음 예제 출력은 *gpunodepool이* 지정된 *VmSize를*가진 노드를 *만드는* 것을 보여줍니다.

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

*gpunodepool을* 성공적으로 만들려면 몇 분 정도 걸립니다.

## <a name="schedule-pods-using-taints-and-tolerations"></a>오염 및 관용을 사용하여 포드 일정

이제 클러스터에 처음 생성된 기본 노드 풀과 GPU 기반 노드 풀인 두 개의 노드 풀이 있습니다. [kubectl get 노드][kubectl-get] 명령을 사용하여 클러스터의 노드를 봅니다. 다음 예제 출력은 노드를 보여 주며, 다음 을 보여 주며,

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Kubernetes 스케줄러는 taint 및 toleration을 사용하여 노드에서 실행할 수 있는 워크로드를 제한할 수 있습니다.

* **Taint**는 노드에서 특정 Pod만 예약할 수 있음을 나타내는 노드에 적용됩니다.
* **Toleration**은 노드의 오류를 ‘허용’할 수 있도록 하는 Pod에 적용됩니다.**

고급 Kubernetes 예약 기능을 사용하는 방법에 대한 자세한 내용은 [AKS의 고급 스케줄러 기능에 대한 모범 사례를][taints-tolerations] 참조하십시오.

이 예제에서는 --node-taints 명령을 사용하여 GPU 기반 노드에 오염을 적용합니다. 이전 `kubectl get nodes` 명령의 출력에서 GPU 기반 노드의 이름을 지정합니다. taint은 *키:값으로* 적용된 다음 일정 옵션으로 적용됩니다. 다음 예제에서는 *sku=gpu* 쌍을 사용 하 고 그렇지 않으면 *NoSchedule* 기능을 가지고 있는 포드를 정의 합니다.

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

다음 기본 예제 YAML 매니페스트는 Kubernetes 스케줄러가 GPU 기반 노드에서 NGINX 포드를 실행할 수 있도록 허용하기 위해 허용 을 사용합니다. MNIST 데이터 집합에 대해 Tensorflow 작업을 실행하는 데 시간이 많이 많은 예제를 보려면 [AKS에서 계산 집약적인 워크로드에 GPU 사용을][gpu-cluster]참조하십시오.

`gpu-toleration.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

다음 명령을 사용하여 `kubectl apply -f gpu-toleration.yaml` 포드를 예약합니다.

```console
kubectl apply -f gpu-toleration.yaml
```

포드를 예약하고 NGINX 이미지를 가져오는 데 몇 초 정도 걸립니다. [kubectl 설명 포드][kubectl-describe] 명령을 사용하여 포드 상태를 봅니다. 다음 응축 된 예제 출력 *sku=gpu:NoSchedule* 내포적용 을 보여 주어 있습니다. 이벤트 섹션에서 스케줄러는 *aks-gpunodepool-28993262-vmss0000000* GPU 기반 노드에 포드를 할당했습니다.

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

이 오염이 적용된 포드만 *gpunodepool의*노드에서 예약할 수 있습니다. 다른 모든 포드는 *nodepool1* 노드 풀에서 예약됩니다. 추가 노드 풀을 만드는 경우 추가 오염 및 내포를 사용하여 해당 노드 리소스에서 예약할 수 있는 포드를 제한할 수 있습니다.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>노드 풀에 대한 오염, 레이블 또는 태그 지정

노드 풀을 만들 때 해당 노드 풀에 오염, 레이블 또는 태그를 추가할 수 있습니다. 오염, 레이블 또는 태그를 추가하면 해당 노드 풀 내의 모든 노드도 해당 오염, 레이블 또는 태그를 얻습니다.

오염된 노드 풀을 만들려면 [az aks 노드풀 추가를][az-aks-nodepool-add]사용합니다. taintnp *taintnp* 이름을 지정하고 `--node-taints` 매개 변수를 사용하여 *sku=gpu:NoSchedule을* 오염에 지정합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

[az aks 노드풀 목록][az-aks-nodepool-list] 명령의 다음 예제 출력은 *taintnp가* 지정된 *노드Taints를*가진 노드를 *만드는* 것을 보여줍니다.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

노드에 대한 일정 규칙을 처리하기 위해 Kubernetes에서 오염 정보를 볼 수 있습니다.

노드 풀을 만드는 동안 노드 풀에 레이블을 추가할 수도 있습니다. 노드 풀에 설정된 레이블은 노드 풀의 각 노드에 추가됩니다. 이러한 레이블은 노드에 대한 일정 규칙을 처리하기 위해 [Kubernetes에 표시됩니다.][kubernetes-labels]

레이블이 있는 노드 풀을 만들려면 [az aks 노드풀 추가를][az-aks-nodepool-add]사용합니다. 이름 *labelnp를* 지정하고 `--labels` 매개 변수를 사용하여 *레이블에 대해 dept=IT* 및 *costcenter=9999를* 지정합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> 레이블은 노드 풀을 만드는 동안에만 노드 풀에 대해 설정할 수 있습니다. 레이블은 키/값 쌍이어야 하며 [유효한 구문이][kubernetes-label-syntax]있어야 합니다.

[az aks 노드풀 목록][az-aks-nodepool-list] 명령의 다음 예제 출력은 *labelnp가* 지정된 *노드를*가진 *노드를 만드는* 것을 보여줍니다.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

AKS 클러스터의 노드 풀에 Azure 태그를 적용할 수 있습니다. 노드 풀에 적용된 태그는 노드 풀 내의 각 노드에 적용되며 업그레이드를 통해 유지됩니다. 태그는 확장 작업 중에 노드 풀에 추가된 새 노드에도 적용됩니다. 태그를 추가하면 정책 추적 또는 비용 추정과 같은 작업에 도움이 될 수 있습니다.

[az aks 노드풀 추가를][az-aks-nodepool-add]사용하여 노드 풀을 만듭니다. *태그노드풀* 이라는 이름을 지정하고 매개 `--tag` 변수를 사용하여 *태그에 대해 dept=IT* 및 *costcenter=9999를* 지정합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> [az aks 노드풀 업데이트][az-aks-nodepool-update] 명령을 사용하는 경우뿐만 아니라 클러스터 를 만드는 동안 `--tags` 매개 변수를 사용할 수도 있습니다. 클러스터를 만드는 `--tags` 동안 매개 변수는 클러스터로 만든 초기 노드 풀에 태그를 적용합니다. 모든 태그 이름은 [Azure 리소스를 구성하기 위해 사용 태그의][tag-limitation]제한 사항을 준수해야 합니다. 매개 변수를 사용 `--tags` 하 고 노드 풀을 업데이트 하는 모든 기존 태그 값을 업데이트 하 고 새 태그를 추가 합니다. 예를 들어 노드 풀에 태그에 대한 *dept=IT* 및 *costcenter=9999가* 있고 태그에 대해 *team=dev* 및 *costcenter=111로* 업데이트한 경우 노드풀에는 *dept=IT,* *costcenter=111,* 태그에 대한 *팀=dev가* 있습니다.

[az aks 노드풀 목록][az-aks-nodepool-list] 명령의 다음 예제 출력은 *tagnodepool이* 지정된 *태그가*있는 *노드를 만드는* 것을 보여 주며 다음과 같은 것을 보여 주십니까?

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 노드 풀 관리

Azure Resource Manager 템플릿을 사용하여 리소스를 만들고 관리하는 경우 일반적으로 템플릿의 설정을 업데이트하고 리소스를 업데이트하기 위해 다시 배포할 수 있습니다. AKS의 노드 풀을 사용하면 AKS 클러스터가 만들어지면 초기 노드 풀 프로필을 업데이트할 수 없습니다. 이 동작은 기존 Resource Manager 템플릿을 업데이트하고, 노드 풀을 변경하고, 다시 배포할 수 없다는 것을 의미합니다. 대신 기존 AKS 클러스터의 노드 풀만 업데이트하는 별도의 리소스 관리자 템플릿을 만들어야 합니다.

다음 예제 매니페스트와 같은 `aks-agentpools.json` 템플릿을 만들고 붙여넣습니다. 이 예제 템플릿은 다음 설정을 구성합니다.

* *myagentpool이라는* *Linux* 노드 풀을 업데이트하여 세 개의 노드를 실행합니다.
* Kubernetes 버전 *1.15.7을*실행하도록 노드 풀의 노드를 설정합니다.
* 노드 크기를 *Standard_DS2_v2*로 정의합니다.

필요에 따라 노드 풀을 업데이트, 추가 또는 삭제하는 데 필요한 대로 이러한 값을 편집합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

다음 예제와 같이 [az 그룹 배포 create][az-group-deployment-create] 명령을 사용하여 이 템플릿을 배포합니다. 기존 AKS 클러스터 이름 및 위치에 대한 메시지가 표시됩니다.

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> 다음 예제와 같이 템플릿에 태그 속성을 추가하여 노드 풀에 *태그를* 추가할 수 있습니다.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

리소스 관리자 템플릿에서 정의한 노드 풀 설정 및 작업에 따라 AKS 클러스터를 업데이트하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>노드 풀에 대해 노드당 공용 IP 할당(미리 보기)

> [!WARNING]
> 노드당 공용 IP를 할당하는 미리 보기 중에 VM 프로비저닝과 충돌할 수 있는 로드 밸런서 규칙으로 인해 *AKS의 표준 로드 밸런서 SKU와* 함께 사용할 수 없습니다. 이러한 제한으로 인해 Windows 에이전트 풀은 이 미리 보기 기능에서 지원되지 않습니다. 미리 보기에서 노드당 공용 IP를 할당해야 하는 경우 *기본 로드 밸런서 SKU를* 사용해야 합니다.

AKS 노드는 통신을 위해 자체 공용 IP 주소를 요구하지 않습니다. 그러나 시나리오에서는 자체 전용 공용 IP 주소를 수신하기 위해 노드 풀의 노드가 필요할 수 있습니다. 일반적인 시나리오는 콘솔이 홉을 최소화하기 위해 클라우드 가상 머신에 직접 연결해야 하는 게임 워크로드입니다. 이 시나리오는 미리 보기 기능인 노드 공용 IP(미리 보기)에 등록하여 AKS에서 수행할 수 있습니다.

다음 Azure CLI 명령을 실행하여 노드 공용 IP 기능에 등록합니다.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

등록에 성공한 후 [위와](#manage-node-pools-using-a-resource-manager-template) 동일한 지침에 따라 Azure 리소스 관리자 `enableNodePublicIP` 템플릿을 배포하고 에이전트PoolProfiles에 부울 속성을 추가합니다. 값을 `true` 지정하지 않은 것처럼 `false` 기본적으로 설정합니다. 

이 속성은 만들기 시간 전용 속성이며 2019-06-01의 최소 API 버전이 필요합니다. 이는 Linux 및 Windows 노드 풀모두에 적용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 GPU 기반 노드를 포함하는 AKS 클러스터를 만들었습니다. 불필요한 비용을 줄이려면 *gpunodepool*또는 전체 AKS 클러스터를 삭제할 수 있습니다.

GPU 기반 노드 풀을 삭제하려면 다음 예제와 같이 [az aks nodedelete][az-aks-nodepool-delete] 명령을 사용합니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

클러스터 자체를 삭제하려면 [az 그룹 삭제][az-group-delete] 명령을 사용하여 AKS 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에서 여러 노드 풀을 만들고 관리하는 방법을 배웠습니다. 노드 풀에서 포드를 제어하는 방법에 대한 자세한 내용은 [AKS의 고급 스케줄러 기능에 대한 모범 사례를][operator-best-practices-advanced-scheduler]참조하십시오.

Windows Server 컨테이너 풀을 만들고 사용하려면 [AKS의 Windows 서버 컨테이너 만들기를][aks-windows]참조하십시오.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md