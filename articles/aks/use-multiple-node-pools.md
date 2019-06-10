---
title: Azure Kubernetes Service (AKS)에서 여러 노드 풀을 사용 하 여
description: Azure Kubernetes Service (AKS)에서 클러스터에 대 한 여러 노드 풀 만들기 및 관리 하는 방법을 알아봅니다
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: a295dfa1f7f2c58b3e45036212434837ac4bfb4d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475461"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>미리 보기-만들기 및 Azure Kubernetes Service (AKS)에서 클러스터에 대 한 여러 노드 풀을 관리

Azure Kubernetes Service (AKS)를 동일한 구성의 노드는으로 그룹화 *노드 풀*합니다. 이러한 노드 풀 응용 프로그램을 실행 하는 기본 Vm을 포함 합니다. 노드 및 해당 크기 (SKU)의 초기 수는 만드는 AKS 클러스터를 만들 때 정의 된 한 *기본 노드 풀*합니다. 다른 계산 또는 저장소 수요가 있는 응용 프로그램을 지원 하려면 추가 노드 풀을 만들 수 있습니다. 예를 들어, Gpu 계산 집약적인 응용 프로그램을 제공 하거나 고성능 SSD 저장소에 대 한 액세스를 이러한 추가 노드 풀을 사용 합니다.

이 문서에서는 AKS 클러스터에서 여러 노드 풀 만들기 및 관리 하는 방법을 보여 줍니다. 이 기능은 현재 미리 보기로 제공됩니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스, 옵트인 합니다. 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 미리 보기에서이 기능이 없는 프로덕션 사용 해야 합니다. 공개 미리 보기에서 기능 '최상의' 지원에 속합니다. AKS 기술 지원 팀의 지원 업무 시간은 태평양 표준 시간대 (PST)만 제공 됩니다. 추가 정보는 다음과 같은 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전에

이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.61 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

여러 노드 풀 만들기 및 관리 하기 위해 CLI 명령에서 사용할 수는 *aks 미리 보기* CLI 확장 합니다. 설치를 *aks 미리 보기* 사용 하 여 Azure CLI 확장을 [az 확장 추가] [ az-extension-add] 다음 예와에서 같이 명령:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> 이전에 설치한 경우 합니다 *aks 미리 보기* 사용 하 여 확장을 설치 가능한 업데이트를 `az extension update --name aks-preview` 명령입니다.

### <a name="register-multiple-node-pool-feature-provider"></a>여러 노드 풀 기능 공급자 등록

여러 노드 풀을 사용할 수 있는 AKS 클러스터를 만들려면 먼저 구독에 두 기능 플래그를 설정 합니다. 다중 노드 풀 클러스터 배포 및 Kubernetes 노드 구성을 관리 하는 가상 머신 확장 집합 (VMSS)를 사용 합니다. 등록 된 *MultiAgentpoolPreview* 및 *VMSSPreview* 기능 플래그를 사용 하 여를 [az 기능 등록] [ az-feature-register] 명령에 표시 된 대로 다음 예제:

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> 성공적으로 등록 한 후를 만든 AKS 클러스터를 *MultiAgentpoolPreview* 이 미리 보기 클러스터 환경을 사용 합니다. 일반, 완벽 하 게 지원 되는 클러스터를 만드는 작업을 계속 하려면 프로덕션 구독에서 미리 보기 기능을 사용 하지 마십시오. 미리 보기 기능을 테스트 하는 것에 대 한 별도 테스트 또는 개발 Azure 구독을 사용 합니다.

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>제한 사항

생성 하 고 여러 노드 풀을 지 원하는 AKS 클러스터를 관리 하는 경우 다음과 같은 제한이 있습니다.

* 여러 노드 풀 성공적으로 등록 한 후 만든 클러스터에만 사용할 수는 *MultiAgentpoolPreview* 하 고 *VMSSPreview* 구독에 대 한 기능입니다. 추가 하거나이 기능이 성공적으로 등록 된 이전에 만든 기존 AKS 클러스터를 사용 하 여 노드 풀을 관리할 수 없습니다.
* 첫 번째 노드 풀을 삭제할 수 없습니다.
* HTTP 응용 프로그램 라우팅 추가 기능을 사용할 수 없습니다.
* 대부분의 작업에서와 마찬가지로 기존 Resource Manager 템플릿을 사용 하 여 추가/업데이트/삭제 노드 풀 수 없습니다. 대신 [별도 Resource Manager 템플릿을 사용 하 여](#manage-node-pools-using-a-resource-manager-template) AKS 클러스터에서 노드 풀을 변경 해야 합니다.
* (현재 AKS에서 미리 보기)는에서 클러스터 autoscaler는 사용할 수 없습니다.

이 기능은 미리 보기 상태인 동안에 다음 추가 제한 사항이 적용 됩니다.

* AKS 클러스터에는 최대 8 개의 노드 풀 있을 수 있습니다.
* AKS 클러스터에는 이러한 8 노드 풀에서 노드를 400 개 까지가 있을 수 있습니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

시작 하려면 단일 노드 풀을 사용 하 여 AKS 클러스터를 만듭니다. 다음 예제에서는 합니다 [az 그룹 만들기] [ az-group-create] 이라는 리소스 그룹을 만들려면 명령 *myResourceGroup* 에 *eastus* 영역입니다. 명명 된 AKS 클러스터 *myAKSCluster* 가 사용 하 여 생성 됩니다 합니다 [az aks 만들기] [ az-aks-create] 명령입니다. A *-kubernetes 버전* 의 *1.12.6* 다음 단계에서는 노드 풀을 업데이트 하는 방법을 보여주는 데 사용 됩니다. 지정할 수 있습니다 [지원 되는 Kubernetes 버전][supported-versions]합니다.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.12.6
```

클러스터를 만드는 데 몇 분이 걸립니다.

클러스터 준비 되 면 사용 된 [az aks 자격 증명 가져오기] [ az-aks-get-credentials] 사용에 대 한 클러스터 자격 증명을 가져오려면 명령 `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>노드 풀을 추가 합니다.

이전 단계에서 만든 클러스터에는 단일 노드 풀이 있습니다. 사용 하 여 두 번째 노드 풀을 추가 해 보겠습니다 합니다 [az aks 노드 풀 추가] [ az-aks-nodepool-add] 명령입니다. 다음 예에서는 이라는 노드 풀을 만듭니다 *mynodepool* 실행 하는 *3* 노드:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

노드 풀의 상태를 보려면 사용 합니다 [az aks 노드 풀 목록] [ az-aks-nodepool-list] 명령 및 리소스 그룹과 클러스터 이름을 지정 합니다.

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

다음 예제 출력을 보여 줍니다 *mynodepool* 노드 풀에서 노드가 3 개를 사용 하 여 성공적으로 만들었습니다. 이전 단계의 기본값 AKS 클러스터를 만든 경우 *nodepool1* 의 노드 수를 사용 하 여 만든 *1*합니다.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> 없으면 *OrchestratorVersion* 하거나 *VmSize* 노드 풀을 추가 하는 경우 지정 된 노드는 AKS 클러스터에 대 한 기본값에 따라 생성 됩니다. 이 예제에서는 Kubernetes 버전 있던 *1.12.6* 및 노드 크기를 *Standard_DS2_v2*합니다.

## <a name="upgrade-a-node-pool"></a>노드 풀을 업그레이드 합니다.

첫 번째 단계에서는 AKS 클러스터를 만든 경우는 `--kubernetes-version` 의 *1.12.6* 지정 되었습니다. 업그레이드 된 *mynodepool* Kubernetes로 *1.12.7*합니다. 사용 된 [az aks 노드 풀 업그레이드가] [ az-aks-nodepool-upgrade] 다음 예와에서 같이 노드 풀을 업그레이드 하는 명령:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

다시 사용 하 여 노드 풀의 상태를 나열 합니다 [az aks 노드 풀 목록] [ az-aks-nodepool-list] 명령입니다. 다음 예에서는 *mynodepool* 에 *업그레이드* 상태 *1.12.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

지정된 된 버전에 노드를 업그레이드 하려면 몇 분 정도 걸립니다.

모범 사례로, 동일한 Kubernetes 버전을 AKS 클러스터의 모든 노드 풀을 업그레이드 해야 합니다. 개별 노드 풀을 업그레이드 하는 기능을 사용 하 여 롤링 업그레이드를 수행 하 고 응용 프로그램 가동 시간을 유지 하기 위해 노드 풀 간에 pod를 예약할 수 있습니다.

## <a name="scale-a-node-pool"></a>노드 풀의 크기 조정

응용 프로그램 워크 로드 요구 사항이 변경 될, 노드 풀의 노드 수를 조정 해야 할 수 있습니다. 노드 수를 확장 또는 축소할 수 있습니다.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

노드 풀의 노드 수를 조정 하려면 사용 합니다 [az aks 노드 풀 규모] [ az-aks-nodepool-scale] 명령입니다. 다음 예제에서는 확장의 노드 수가 *mynodepool* 하 *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

다시 사용 하 여 노드 풀의 상태를 나열 합니다 [az aks 노드 풀 목록] [ az-aks-nodepool-list] 명령입니다. 다음 예에서는 *mynodepool* 에 *크기 조정* 상태에 대해 새 횟수 이며 *5* 노드:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

크기 조정 작업을 완료 하려면 몇 분 정도 걸립니다.

## <a name="delete-a-node-pool"></a>노드 풀을 삭제 합니다.

풀에 더 이상 필요한 경우에 삭제할 수 있으며 기본 VM 노드는 제거할 수 없습니다. 노드 풀을 삭제 하려면 사용 합니다 [az aks 노드 풀 삭제] [ az-aks-nodepool-delete] 명령 및 노드 풀 이름을 지정 합니다. 다음 예에서는 삭제 합니다 *mynoodepool* 이전 단계에서 만든:

> [!CAUTION]
> 노드 풀을 삭제 하는 경우 발생할 수 있는 데이터 손실에 대 한 복구 옵션 없이 있습니다. 다른 노드 풀에서 pod를 예약할 수 없습니다, 경우에 해당 응용 프로그램 제공 되지 않습니다. 사용 중인 응용 프로그램 데이터 백업 또는 클러스터의 다른 노드 풀에서 실행할 수 없을 때 노드 풀을 삭제 하지 않으면 있는지 확인 합니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

다음 예제 출력에서는 [az aks 노드 풀 목록] [ az-aks-nodepool-list] 명령에 따르면 *mynodepool* 에 *삭제* 상태:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

노드 및 노드 풀을 삭제 하려면 몇 분 정도 걸립니다.

## <a name="specify-a-vm-size-for-a-node-pool"></a>노드가 풀에 대 한 VM 크기를 지정 합니다.

노드 풀을 만들려면 이전 예제에서 만들어지는 클러스터의 노드에 기본 VM 크기를 사용 했습니다. 보다 일반적인 시나리오는 다양 한 VM 크기 및 기능을 사용 하 여 노드 풀을 만들 수입니다. 예를 들어, 많은 양의 CPU 또는 메모리를 사용 하 여 노드를 포함 하는 노드 풀 또는 GPU 지원을 제공 하는 노드 풀을 만들 수 있습니다. 다음 단계에 있습니다 [taints 및 tolerations 사용](#schedule-pods-using-taints-and-tolerations) Kubernetes 스케줄러 이러한 노드에서 실행할 수 있는 pod에 대 한 액세스를 제한 하는 방법을 알려야 합니다.

다음 예제에서 사용 하는 GPU 기반 노드 풀을 만들 합니다 *Standard_NC6* VM 크기입니다. 이러한 Vm은 NVIDIA Tesla K80 카드에서 전원이 공급 됩니다. 사용 가능한 VM 크기에 대 한 내용은 참조 하세요 [Azure에서 Linux 가상 머신 크기][vm-sizes]합니다.

사용 하 여 노드 풀을 만들 합니다 [az aks 노드 풀 추가] [ az-aks-nodepool-add] 명령을 다시 합니다. 이 이번에는 이름을 지정 *gpunodepool*를 사용 하 여 합니다 `--node-vm-size` 매개 변수를 지정 합니다 *Standard_NC6* 크기:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

다음 예제 출력에서는 [az aks 노드 풀 목록] [ az-aks-nodepool-list] 명령에 따르면 *gpunodepool* 는 *만들기* 사용 하 여 노드를 지정 된 *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

몇 분 정도 걸리는 합니다 *gpunodepool* 만들 수 있습니다.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Taints 및 tolerations를 사용 하 여 pod를 예약 합니다.

이제 두 개의 노드 풀 클러스터에서 처음으로 생성 하는 기본 노드 풀 및 GPU 기반 노드 풀 합니다. 사용 하 여는 [kubectl get 노드] [ kubectl-get] 명령을 클러스터의 노드를 확인 합니다. 다음 예제 출력 각 노드 풀에서 노드 하나를 보여 줍니다.

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

Kubernetes 스케줄러는 taint 및 toleration을 사용하여 노드에서 실행할 수 있는 워크로드를 제한할 수 있습니다.

* **Taint**는 노드에서 특정 Pod만 예약할 수 있음을 나타내는 노드에 적용됩니다.
* **Toleration**은 노드의 오류를 ‘허용’할 수 있도록 하는 Pod에 적용됩니다. 

사용 하는 방법에 대 한 자세한 내용은 고급 Kubernetes 예약 기능을 참조 [AKS에서 고급 스케줄러 기능에 대 한 유용한 정보][taints-tolerations]

이 예에서 사용 하 여 GPU 기반 노드를 검은 적용 합니다 [kubectl 검은 노드] [ kubectl-taint] 명령입니다. 이전 출력에서 GPU 기반 노드의 이름을 지정 `kubectl get nodes` 명령입니다. 검은으로 적용 되는 *키: 값* 및 다음 예약 옵션입니다. 다음 예제에서는 *sku gpu =* 쌍 및 pod를 정의 해야 합니다 *NoSchedule* 기능:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

다음 기본 예제에서는 YAML 매니페스트는 Kubernetes 스케줄러 GPU 기반 노드에서 NGINX pod를 실행 하도록 허용 하는 허용을 사용 합니다. MNIST 데이터 집합에 대 한 Tensorflow 작업 실행에 더 적합 하지만 시간이 많이 예제를 보려면 [AKS에서 계산 집약적 워크 로드에 사용 하 여 Gpu][gpu-cluster]합니다.

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

일정을 사용 하 여 pod를 `kubectl apply -f gpu-toleration.yaml` 명령:

```console
kubectl apply -f gpu-toleration.yaml
```

Pod를 예약 하 고 NGINX 이미지 풀을 몇 초 정도가 걸립니다. 사용 하 여는 [kubectl 설명 pod] [ kubectl-describe] pod 상태를 보려면 명령입니다. 압축 된 다음 예제 출력 표시는 *sku gpu:NoSchedule =* 허용 적용 됩니다. 이벤트 섹션에서 스케줄러를 pod에 할당 합니다 *aks gpunodepool-28993262 vmss000000* GPU 기반 노드:

```console
$ kubectl describe pod mypod

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

노드에 적용이 검은 있는 pod만 예약할 수 있습니다 *gpunodepool*합니다. 다른 pod를 예약할 수는 합니다 *nodepool1* 노드 풀 합니다. 추가 노드 풀을 만드는 경우 추가 taints 사용할 수 있으며 해당 노드 리소스에 대해 어떤 pod를 제한 하려면 tolerations를 예약할 수 있습니다.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용 하 여 노드 풀 관리

Azure Resource Manager 템플릿 만들기를 사용 하는 경우 관리 되는 리소스 및 리소스를 업데이트 하는 서식 파일 및 재배포 설정은 일반적으로 업데이트할 수 있습니다. AKS에서 노드 풀을 사용 하 여 AKS 클러스터를 만든 후 초기 노드 풀 프로필을 업데이트할 수 없습니다. 이 동작은 없습니다 기존 Resource Manager 템플릿을 업데이트, 노드 풀을 변경 하는 재배포를 의미 합니다. 대신, 기존 AKS 클러스터에 대 한 에이전트 풀만 업데이트 하는 별도 Resource Manager 템플릿을 만들어야 합니다.

같은 템플릿 만들기 `aks-agentpools.json` 다음 예제에서는 매니페스트를 붙여넣습니다. 이 예제에서는 서식 파일에 다음 설정을 구성합니다.

* 업데이트를 *Linux* 이라는 에이전트 풀 *myagentpool* 노드가 3 개를 실행 합니다.
* Kubernetes 버전을 실행 하는 노드 풀에서 노드를 설정 *1.12.8*합니다.
* 노드 크기를 정의 *Standard_DS2_v2*합니다.

업데이트를 추가 하거나 필요에 따라 노드 풀을 삭제 하는 대로이 값을 편집 합니다.

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
      "aks": "2019-04-01"
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
      "apiVersion": "2019-04-01",
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
            "orchestratorVersion": "1.12.8"
      }
    }
  ]
}
```

사용 하 여이 템플릿을 배포 합니다 [az 그룹 배포 만들기] [ az-group-deployment-create] 명령을 다음 예와에서 같이 합니다. 기존 AKS 클러스터 이름 및 위치를 묻는 메시지가 나타납니다.

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

노드 풀 설정 및 Resource Manager 템플릿에서 정의 하는 작업에 따라 AKS 클러스터를 업데이트 하려면 몇 분 정도 걸릴 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 GPU 기반 노드를 포함 하는 AKS 클러스터를 만들었습니다. 불필요 한 비용을 줄이기 위해 삭제 하려는 합니다 *gpunodepool*, 또는 전체 AKS 클러스터.

GPU 기반 노드 풀을 삭제 하려면 사용 합니다 [az aks nodepool 삭제할] [ az-aks-nodepool-delete] 다음 예와에서 같이 명령:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

클러스터 자체를 삭제 하려면 사용 합니다 [az 그룹 삭제] [ az-group-delete] AKS 리소스 그룹을 삭제 하는 명령:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에서 여러 노드 풀 만들기 및 관리 하는 방법을 알아보았습니다. 노드 풀에서 pod를 제어 하는 방법에 대 한 자세한 내용은 참조 하세요. [AKS에 고급 스케줄러 기능에 대 한 유용한][operator-best-practices-advanced-scheduler]합니다.

참조를 만들고 Windows Server 컨테이너 노드 풀을 사용 하려면 [AKS에 Windows Server 컨테이너를 만들고][aks-windows]합니다.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
