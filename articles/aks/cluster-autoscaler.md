---
title: AKS(Azure Kubernetes Service)에서 클러스터 자동 크기 조정기 사용
description: 클러스터 자동 크기 조정기를 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기를 조정하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 0b94865d81afc56c24d470012c668662f003a1b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596252"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기 조정

AKS(Azure Kubernetes Service)에서 애플리케이션 수요에 맞추려면 워크로드를 실행하는 노드 수를 조정해야 할 수 있습니다. 클러스터 자동 크기 조정기 구성 요소는 리소스 제약 조건으로 인해 예약할 수 없는 클러스터의 Pod를 확인할 수 있습니다. 문제가 감지되면 응용 프로그램 요구를 충족하기 위해 노드 풀의 노드 수가 증가합니다. 또한 실행 중인 Pod가 부족한지 노드를 주기적으로 확인하고 필요에 따라 노드 수가 감소합니다. AKS 클러스터에서 노드 수를 자동으로 강화하거나 규모 축소하는 이 기능을 통해 효율적이고 비용 효과적인 클러스터를 실행할 수 있습니다.

이 문서에서는 AKS 클러스터에서 클러스터 자동 크기 조정기를 사용하도록 설정하고 관리하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전 2.0.76 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하십시오.

## <a name="limitations"></a>제한 사항

클러스터 자동 크기 조정기를 사용하는 AKS 클러스터를 만들고 관리할 때 다음 제한 사항이 적용됩니다.

* HTTP 응용 프로그램 라우팅 추가 기능을 사용할 수 없습니다.

## <a name="about-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 정보

업무 시간에서 야간 사이에 또는 주말에 변화하는 애플리케이션 수요에 맞게 조정하기 위해 일반적으로 클러스터에는 자동으로 크기를 조정하는 방법이 필요합니다. AKS 클러스터는 다음 두 가지 방법 중 하나로 크기를 조정할 수 있습니다.

* **클러스터 자동 크기 조정기**는 리소스 제약 조건으로 인해 노드에서 예약할 수 없는 Pod를 확인합니다. 그런 다음 클러스터는 노드 수를 자동으로 늘입니다.
* **Horizontal Pod Autoscaler**는 Kubernetes 클러스터에서 메트릭 서버를 사용하여 Pod의 리소스 수요를 모니터링합니다. 응용 프로그램에 더 많은 리소스가 필요한 경우 요구 사항을 충족하기 위해 포드 수가 자동으로 증가합니다.

![종종 클러스터 자동 크기 조정기 및 Horizontal Pod Autoscaler가 연동되어 필요한 애플리케이션 수요를 지원함](media/autoscaler/cluster-autoscaler.png)

수평 포드 자동 크기 조정기와 클러스터 자동 크기 조정기 모두 필요에 따라 포드 및 노드 수를 줄일 수 있습니다. 클러스터 자동 크기 조정기는 일정 기간 사용되지 않은 용량이 있는 경우 노드 수를 줄입니다. 클러스터 자동 크기 조정기가 제거할 노드의 Pod는 클러스터의 다른 위치에서 안전하게 예약됩니다. 다음 상황에서처럼 Pod를 이동할 수 없는 경우 클러스터 자동 크기 조정기가 규모를 축소하지 못할 수 있습니다.

* 포드는 직접 만들어지며 배포 또는 복제본 집합과 같은 컨트롤러 개체에 의해 백업되지 않습니다.
* PDB(Pod Disruption Budget)가 너무 제한적이고 이를 사용하면 Pod 수가 특정 임계값보다 낮아질 수 없습니다.
* Pod는 다른 노드에서 예약된 경우 적용할 수 없는 노드 선택기 또는 선호도 방지를 사용합니다.

클러스터 자동 크기 조정기가 규모를 축소하지 못할 수 있는 경우에 대한 자세한 내용은 [클러스터 자동 크기 조정기가 노드를 제거할 수 없도록 하는 Pod 유형은 무엇인가요?][autoscaler-scaledown]를 참조하세요.

클러스터 자동 크기 조정기는 크기 조정 이벤트와 리소스 임계값 사이의 시간 간격 같은 항목에 시작 매개 변수를 사용합니다. 이러한 매개 변수는 Azure 플랫폼에 의해 정의되며 현재는 사용자가 조정하도록 표시되지 않습니다. 클러스터 자동 크기 조정기가 사용하는 매개 변수에 대한 자세한 내용은 [클러스터 자동 크기 조정기 매개 변수란?][autoscaler-parameters]을 참조하세요.

클러스터와 수평 포드 자동 크기 조정기는 함께 작동할 수 있으며 클러스터에 배포되는 경우가 많습니다. 결합된 경우 Horizontal Pod Autoscaler는 애플리케이션 수요를 충족하는 데 필요한 개수의 Pod를 실행하는 데 중점을 둡니다. 클러스터 자동 크기 조정기는 예약된 Pod를 지원하는 데 필요한 개수의 노드를 실행하는 데 중점을 둡니다.

> [!NOTE]
> 클러스터 자동 크기 조정기를 사용하면 수동 크기 조정이 사용하지 않도록 설정됩니다. 클러스터 자동 크기 조정기가 필요한 노드 수를 결정하도록 해보겠습니다. 클러스터 크기를 수동으로 조정하려면 [클러스터 자동 크기 조정기를 사용하지 않도록 설정](#disable-the-cluster-autoscaler)합니다.

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS 클러스터를 만들고 클러스터 자동 크기 조정기를 사용하도록 설정

AKS 클러스터를 만들어야 하는 경우 [az aks create][az-aks-create] 명령을 사용합니다. 클러스터풀에서 클러스터 자동 스케일러를 활성화하고 구성하려면 *--enable-cluster-자동 크기 조정기* 매개 변수를 사용하고 *노드--최소 수* 및 *--max-count를*지정합니다.

> [!IMPORTANT]
> 클러스터 자동 크기 조정기는 Kubernetes 구성 요소입니다. AKS 클러스터는 가상 머신 확장 집합을 노드에 사용하지만, Azure Portal에서 또는 Azure CLI를 사용하여 확장 집합 자동 크기 조정에 대한 설정을 직접 설정하거나 편집하지 마세요. Kubernetes 클러스터 자동 크기 조정기가 필수 크기 조정 설정을 자동으로 관리하게 두세요. 자세한 내용은 [노드 리소스 그룹의 AKS 리소스를 수정할 수 있습니까?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

다음 예제는 가상 시스템 규모 집합에 의해 백업되는 단일 노드 풀이 있는 AKS 클러스터를 만듭니다. 또한 클러스터의 노드 풀에서 클러스터 자동 크기 조정기와 최소 *1개* 및 최대 *3개의* 노드를 설정합니다.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

클러스터를 만들고 클러스터 자동 크기 조정기 설정을 구성하는 데 몇 분 정도 걸립니다.

## <a name="change-the-cluster-autoscaler-settings"></a>클러스터 자동 크기 조정기 설정 변경

> [!IMPORTANT]
> AKS 클러스터에 여러 개의 노드 풀이 있는 경우 [여러 에이전트 풀 섹션이 있는 자동 크기 조정으로](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)건너뜁니다. 에이전트 풀이 여러 개인 클러스터는 `az aks nodepool` `az aks`명령 집합을 사용하여 .

이전 단계에서 AKS 클러스터를 만들거나 기존 노드 풀을 업데이트하는 경우 클러스터 자동 크기 조정기 최소 노드 수가 *1로*설정되고 최대 노드 수가 *3으로*설정되었습니다. 애플리케이션 수요가 변경되면 클러스터 자동 크기 조정기의 노드 수를 조정해야 할 수 있습니다.

노드 수를 변경하려면 [az aks 업데이트][az-aks-update] 명령을 사용합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

위의 예제에서는 *myAKSCluster의* 단일 노드 풀에서 클러스터 자동 크기 조정기를 최소 *1개* 및 최대 *5개의* 노드로 업데이트합니다.

> [!NOTE]
> 현재 노드 풀에 대해 설정된 것보다 더 높은 최소 노드 수를 설정할 수 없습니다. 예를 들어 현재 최소 개수가 *1*로 설정되어 있으면 최소 개수를 *3*으로 업데이트할 수 없습니다.

애플리케이션 및 서비스의 성능을 모니터링하고 클러스터 자동 크기 조정기 노드 수를 필요한 성능과 일치하도록 조정합니다.

## <a name="using-the-autoscaler-profile"></a>자동 크기 조정기 프로파일 사용

클러스터 전체 자동 크기 조정기 프로필의 기본값을 변경하여 클러스터 자동 크기 조정기의 세부 정보를 구성할 수도 있습니다. 예를 들어, 노드가 10분 후에 사용률이 낮은 후에 축소 이벤트가 발생합니다. 15분마다 실행되는 워크로드가 있는 경우 15분 또는 20분 후에 사용 중인 노드에서 축소되도록 자동 크기 조정기 프로필을 변경할 수 있습니다. 클러스터 자동 크기 조정기활성화를 사용하면 다른 설정을 지정하지 않는 한 기본 프로필이 사용됩니다. 클러스터 자동 크기 조정기 프로필에는 업데이트할 수 있는 다음 설정이 있습니다.

| 설정                          | 설명                                                                              | 기본값 |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| 스캔 간격                    | 확장 또는 축소를 위해 클러스터를 재평가하는 빈도                                    | 10초    |
| 축소 지연 후 추가       | 확장 후 확장 평가 재개 시간                               | 10분    |
| 축소 지연 후 삭제    | 평가를 축소하는 노드 삭제 후 의 기간                          | 스캔 간격 |
| 축소 지연 후 실패   | 평가 를 축소하는 확장 실패 후 시간                     | 3분     |
| 축소-축소-불필요한 시간         | 노드를 축소할 수 있는 기간 전에 노드가 필요하지 않아야 하는 기간                  | 10분    |
| 확장 축소 준비 시간          | 확장할 수 있는 노드를 사용할 수 있는 기간 전에 준비되지 않은 노드가 필요하지 않아야 하는 기간         | 20분    |
| 축소-사용률 임계값 | 요청된 리소스의 합계를 용량으로 나눈 노드 사용률 수준, 아래 노드를 축소할 때 고려할 수 있는 노드 | 0.5 |
| 최대 우아하게 종료-초     | 클러스터 자동 크기 조정기는 노드를 축소하려고 할 때 포드 종료를 기다립니다. | 600초   |

> [!IMPORTANT]
> 클러스터 자동 크기 조정기 프로필은 클러스터 자동 크기 조정기를 사용하는 모든 노드 풀에 영향을 줍니다. 노드 풀당 자동 스케일러 프로파일을 설정할 수 없습니다.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

클러스터 자동 크기 조정기 설정 프로필을 설정하려면 *aks 미리 보기* CLI 확장 버전 0.4.30 이상이 필요합니다. az 확장 [추가][az-extension-add] 명령을 사용하여 *aks 미리 보기* Azure CLI 확장을 설치한 다음 az [확장 업데이트][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>기존 AKS 클러스터에서 클러스터 자동 크기 조정기 프로파일 설정

*클러스터 자동 크기 조정기 프로필* 매개 변수를 사용하여 az [aks 업데이트][az-aks-update] 명령을 사용하여 클러스터의 클러스터 자동 크기 조정기 프로필을 설정합니다. 다음 예제에서는 검색 간격 설정을 프로파일에서 30s로 구성합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

클러스터의 노드 풀에서 클러스터 자동 크기 조정기기능을 활성화하면 해당 클러스터도 클러스터 자동 크기 조정기 프로파일을 사용합니다. 예를 들어:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> 클러스터 자동 크기 조정기 프로파일을 설정하면 클러스터 자동 크기 조정기가 활성화된 기존 노드 풀이 프로파일을 즉시 사용하기 시작합니다.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>AKS 클러스터를 만들 때 클러스터 자동 크기 조정기 프로파일 설정

클러스터를 만들 때 *클러스터 자동 크기 조정기 프로필* 매개 변수를 사용할 수도 있습니다. 예를 들어:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

위의 명령은 AKS 클러스터를 만들고 클러스터 전체 자동 크기 조정기 프로파일에 대해 검색 간격을 30초로 정의합니다. 또한 이 명령은 초기 노드 풀에서 클러스터 자동 크기 조정기를 활성화하고 최소 노드 수를 1로 설정하고 최대 노드 수를 3으로 설정합니다.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>클러스터 자동 크기 조정기 프로파일을 기본값으로 재설정

az [aks 업데이트][az-aks-update] 명령을 사용하여 클러스터의 클러스터 자동 크기 조정기 프로필을 재설정합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 사용 안 함

클러스터 자동 크기 조정기를 더 이상 사용하지 않으려면 [az aks 업데이트][az-aks-update] 명령을 사용하여 *--disable-cluster-자동 크기 조정기* 매개 변수를 지정하여 비활성화할 수 있습니다. 클러스터 자동 크기 조정기가 사용하지 않도록 설정되면 노드가 제거되지 않습니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

[az aks 배율][az-aks-scale] 명령을 사용하여 클러스터 자동 크기 조정기 사용을 비활성화한 후 클러스터를 수동으로 확장할 수 있습니다. 가로 포드 자동 크기 조정기를 사용하는 경우 해당 기능은 클러스터 자동 크기 조정기를 사용하지 않도록 설정한 채 계속 실행되지만 모든 노드 리소스가 사용 중인 경우 포드를 예약할 수 없게 될 수 있습니다.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>비활성화된 클러스터 자동 크기 조정기 다시 활성화

기존 클러스터에서 클러스터 자동 크기 조정기를 다시 활성화하려면 [az aks 업데이트][az-aks-update] 명령을 사용하여 *--enable-cluster-자동 크기 조정기*, *--min-count*및 *--max-count* 매개 변수를 지정하여 클러스터 자동 크기 조정기를 다시 활성화할 수 있습니다.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>클러스터 자동 크기 조정기 로그 및 상태 검색

자동 크기 조정기 이벤트를 진단하고 디버깅하기 위해 자동 크기 조정기 추가 기능에서 로그 및 상태를 검색할 수 있습니다.

AKS는 사용자 대신 클러스터 자동 크기 조정기를 관리하고 관리되는 제어 평면에서 실행합니다. 마스터 노드 로그를 구성하여 결과적으로 볼 수 있도록 구성해야 합니다.

클러스터 자동 크기 조정기에서 로그 분석으로 푸시할 로그를 구성하려면 다음 단계를 따르십시오.

1. 진단 로그에 대한 규칙을 설정하여 클러스터 자동 크기 조정기 로그를 Log Analytics로 푸시합니다. [지침은 여기에 자세히 설명되어 있으므로](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs) `cluster-autoscaler` "로그"에 대한 옵션을 선택할 때 확인란을 선택하십시오.
1. Azure 포털을 통해 클러스터의 "로그" 섹션을 클릭합니다.
1. 다음 예제 쿼리를 로그 분석에 입력합니다.

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

검색할 로그가 있는 한 다음 예제와 유사한 로그가 표시됩니다.

![로그 분석 로그](media/autoscaler/autoscaler-logs.png)

또한 클러스터 자동 크기 조정기는 .라는 `cluster-autoscaler-status`구성 맵에 상태 상태를 기록합니다. 이러한 로그를 검색하려면 다음 `kubectl` 명령을 실행합니다. 클러스터 자동 크기 조정기로 구성된 각 노드 풀에 대해 상태 상태가 보고됩니다.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

자동 크기 조정기에서 기록된 내용에 대해 자세히 알아보려면 [Kubernetes/자동 크기 조정기 GitHub 프로젝트의](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why)FAQ를 참조하십시오.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>여러 노드 풀을 사용하도록 설정한 클러스터 자동 크기 조정기 사용

클러스터 자동 크기 조정기는 [여러 노드 풀을](use-multiple-node-pools.md) 사용하도록 설정된 상태로 함께 사용할 수 있습니다. 이 문서를 따라 여러 노드 풀을 사용하도록 설정하고 기존 클러스터에 추가 노드 풀을 추가하는 방법을 알아봅니다. 두 기능을 함께 사용하는 경우 클러스터의 각 개별 노드 풀에서 클러스터 자동 크기 조정기를 사용하도록 설정하고 고유한 자동 크기 조정 규칙을 각각 전달할 수 있습니다.

아래 명령은 이 문서의 초기 [지침을](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) 따랐으며 기존 노드 풀의 최대 수를 *3에서* *5로*업데이트하려고 한다고 가정합니다. az [aks 노드풀 업데이트][az-aks-nodepool-update] 명령을 사용하여 기존 노드 풀의 설정을 업데이트합니다.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

[az aks 노드풀 업데이트][az-aks-nodepool-update] 및 매개 변수 전달을 `--disable-cluster-autoscaler` 통해 클러스터 자동 크기 조정기는 비활성화할 수 있습니다.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

기존 클러스터에서 클러스터 자동 크기 조정기를 다시 활성화하려면 [az aks nodepool 업데이트][az-aks-nodepool-update] 명령을 사용하여 *--enable-cluster-autoscaler*, *--min-count*및 *--max-count* 매개 변수를 지정하여 클러스터 자동 크기 조정기를 다시 활성화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 노드 수를 자동으로 조정하는 방법을 설명했습니다. Horizontal Pod Autoscaler를 사용하여 애플리케이션을 실행하는 Pod 수를 자동으로 조정할 수도 있습니다. Horizontal Pod Autoscaler를 사용하는 단계는 [AKS에서 애플리케이션 크기 조정][aks-scale-apps]을 참조하세요.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
