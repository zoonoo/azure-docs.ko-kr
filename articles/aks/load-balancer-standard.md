---
title: 미리 보기-Azure Kubernetes Service (AKS)에서 표준 SKU 부하 분산 장치를 사용 합니다.
description: 표준 SKU가 있는 부하 분산 장치를 사용 하 여 Azure Kubernetes Service (AKS)를 사용 하 여 서비스를 노출 하는 방법에 알아봅니다.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476801"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>미리 보기-Azure Kubernetes Service (AKS)에서 표준 SKU 부하 분산 장치를 사용 합니다.

Azure Kubernetes Service (AKS)에서 응용 프로그램에 대 한 액세스를 제공 하려면 만들고 하는 Azure Load Balancer를 사용 합니다. AKS에서 실행 중인 부하 분산 장치는 내부 또는 외부 부하 분산 장치를 사용할 수 있습니다. 내부 부하 분산은 Kubernetes 서비스가 AKS 클러스터와 동일한 가상 네트워크에서 실행 되는 응용 프로그램에만 액세스할 수 있습니다. 외부 부하 분산 장치를 수신에 대 한 하나 이상의 공용 Ip를 받아 Kubernetes 서비스 외부에서 공용 Ip를 사용 하 여 액세스할 수 있습니다.

Azure Load Balancer는 ‘기본’ 및 ‘표준’이라는 두 SKU에서 사용할 수 있습니다.   기본적으로 *기본* SKU는 AKS에서 부하 분산 장치를 만들려면 서비스 매니페스트를 사용 하는 경우에 사용 됩니다. 사용 하는 *표준* SKU 부하 분산 장치 추가 기능 및 백 엔드 풀 크기 및 가용성 영역 등의 기능을 제공 합니다. 간의 차이점을 이해 하는 것 *표준* 하 고 *기본* 부하 분산 장치를 사용 하는 선택 하기 전에 합니다. AKS 클러스터를 만든 후 해당 클러스터에 대 한 부하 분산 장치의 SKU를 변경할 수 없습니다. 에 대 한 자세한 합니다 *기본* 하 고 *표준* Sku를 참조 하세요 [Azure load balancer SKU 비교][azure-lb-comparison]합니다.

이 문서를 만들어 사용 하 여 Azure Load Balancer를 사용 하는 방법을 보여 줍니다.는 *표준* SKU Azure Kubernetes Service (AKS)를 사용 하 여 합니다.

이 문서에서는 Kubernetes 및 Azure Load Balancer 개념의 기본 지식이 있다고 가정 합니다. 자세한 내용은 [Kubernetes 개념에 대 한 Azure Kubernetes Service (AKS)를 핵심][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb]합니다.

이 기능은 현재 미리 보기로 제공됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 문서에서는 Azure CLI 버전 2.0.59 중인지 필요를 설치 하 고는 CLI를 로컬로 사용 하려는 경우 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

AKS 클러스터 서비스 주체에 기존 서브넷 또는 리소스 그룹을 사용 하는 경우 네트워크 리소스를 관리할 수 있는 권한이 필요 합니다. 일반적으로 할당 합니다 *네트워크 참가자* 위임 된 리소스에 서비스 주체에 역할입니다. 사용 권한에 대 한 자세한 내용은 참조 하세요. [다른 Azure 리소스에 대 한 액세스를 대리자 AKS][aks-sp]합니다.

부하 분산 장치의 SKU를 설정 하는 AKS 클러스터를 만들어야 *표준* 기본값 대신 *기본*입니다. AKS 클러스터 만들기는 이후 단계에서 설명 하지만 먼저 몇 가지 미리 보기 기능을 사용 하도록 설정 해야 합니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스, 옵트인 합니다. 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 미리 보기에서이 기능이 없는 프로덕션 사용 해야 합니다. 공개 미리 보기에서 기능 '최상의' 지원에 속합니다. AKS 기술 지원 팀의 지원 업무 시간은 태평양 표준 시간대 (PST)만 제공 됩니다. 추가 정보는 다음과 같은 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

Azure load balancer를 사용 하도록 해야 표준 SKU는 *aks 미리 보기* CLI 확장 버전 0.4.1 이상. 설치를 *aks 미리 보기* 사용 하 여 Azure CLI 확장을 [az 확장 추가][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] 명령:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>AKSAzureStandardLoadBalancer 미리 보기 기능 등록

Load balancer를 사용할 수 있는 AKS 클러스터를 만드는 *표준* SKU를 사용 하도록 설정 해야 합니다 *AKSAzureStandardLoadBalancer* 기능 플래그가 구독 합니다. 합니다 *AKSAzureStandardLoadBalancer* 사용 하 여 기능 *VMSSPreview* 가상 머신 확장 집합을 사용 하 여 클러스터를 만들 때. 이 기능은 클러스터를 구성 하는 경우 최신 서비스 향상 된 기능 집합을 제공 합니다. 필요 하지 않는 것이 좋습니다 설정 하는 *VMSSPreview* 기능 플래그도 있습니다.

> [!CAUTION]
> 구독에서 기능을 등록 하면 수 없는 현재 등록을 취소 기능. 일부 미리 보기 기능을 사용 하도록 설정 하면 구독에서 만든 모든 AKS 클러스터에 대 한 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하지 마십시오. 별도 구독을 사용 하 여 미리 보기 기능을 테스트 및 피드백을 수집 합니다.

등록 합니다 *VMSSPreview* 하 고 *AKSAzureStandardLoadBalancer* 기능 플래그를 사용 하 여는 [az 기능 등록][az-feature-register] 다음 예와에서 같이 명령:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> 성공적으로 등록 한 후를 만든 AKS 클러스터를 *VMSSPreview* 하거나 *AKSAzureStandardLoadBalancer* 기능 플래그는이 미리 보기 클러스터 환경을 사용 합니다. 일반, 완벽 하 게 지원 되는 클러스터를 만드는 작업을 계속 하려면 프로덕션 구독에서 미리 보기 기능을 사용 하지 마십시오. 미리 보기 기능을 테스트 하는 것에 대 한 별도 테스트 또는 개발 Azure 구독을 사용 합니다.

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. 사용 하 여 등록 상태를 확인할 수 있습니다 합니다 [az 기능 목록][az-feature-list] 명령:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

준비가 되 면 새로 고침의 등록 합니다 *Microsoft.ContainerService* 사용 하 여 리소스 공급자를 [az provider register][az-provider-register] 명령:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>제한 사항

만들고 사용 하 여 부하 분산 장치를 지 원하는 AKS 클러스터를 관리 하는 경우 다음과 같은 제한 사항이 적용 된 *표준* SKU:

* 사용 하는 경우는 *표준* SKU 부하 분산 장치에 대 한 공용 주소를 허용 하며 IP 만들기를 금지 하는 모든 Azure Policy를 만들지 마세요. AKS 클러스터를 자동으로 만듭니다는 *표준* 일반적으로 사용 하 여 이름이 지정 된 AKS 클러스터에 대해 동일한 리소스 그룹에 SKU 공용 IP 생성 *MC_* 부분입니다. AKS에 공용 IP를 할당 합니다 *표준* SKU 부하 분산 장치. 공용 IP AKS 클러스터에서 송신 트래픽을 허용 하는 데 필요 합니다. 또한이 공용 IP는 제어 평면 및 에이전트 노드 간에 AKS의 이전 버전과 호환성을 유지 하는 것에 대 한 연결을 유지 해야 합니다.
* 사용 하는 경우는 *표준* SKU 부하 분산 장치의 경우 Kubernetes 버전 1.13.5 사용 해야 이상.

이 기능은 미리 보기 상태인 동안에 다음 추가 제한 사항이 적용 됩니다.

* 사용 하는 경우는 *표준* AKS에서 부하 분산 장치의 SKU 부하 분산 장치에 대 한 송신에 대 한 사용자 고유의 공용 IP 주소를 설정할 수 없습니다. AKS 부하 분산 장치에 할당 된 IP 주소를 사용 해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. 리소스 그룹을 만들 때 위치를 지정하라는 메시지가 나타납니다. 이 위치는 리소스 그룹 메타데이터가 저장되는 위치이며 리소스를 만드는 동안 다른 지역을 지정하지 않으면 리소스가 Azure에서 실행되는 위치입니다. [az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

다음 예제 출력에서는 성공적으로 만들어진 리소스 그룹이 나와 있습니다.

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>AKS 클러스터 만들기
Load balancer를 지 원하는 AKS 클러스터를 실행 하기 위해 합니다 *표준* SKU 클러스터 설정 해야 합니다 *부하-분산-sku* 매개 변수를 *표준*. 이 매개 변수는 load balancer를 만듭니다는 *표준* 클러스터를 만들 때 SKU입니다. 실행 하는 경우는 *LoadBalancer* 클러스터의 구성에서 서비스를 *표준* SK 부하 분산 장치는 서비스의 구성으로 업데이트 됩니다. 사용 된 [az aks 만들기][az-aks-create] 라는 AKS 클러스터를 만드는 명령 *myAKSCluster*.

> [!NOTE]
> 합니다 *부하-분산-sku* 속성 클러스터를 만들 때에 사용 수 있습니다. AKS 클러스터를 만든 후에 부하 분산 장치의 SKU를 변경할 수 없습니다. 또한 한 가지 유형의 단일 클러스터의 load balancer SKU만 사용할 수 있습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

몇 분 후 명령이 완료되면 클러스터에 대한 JSON 형식 정보가 반환됩니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리 하려면 사용 [kubectl][kubectl], Kubernetes 명령줄 클라이언트입니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. 설치 하려면 `kubectl` 로컬로 사용 하 여 합니다 [az aks 설치 cli][az-aks-install-cli] 명령:

```azurecli
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력은 이전 단계에서 만든 단일 노드를 보여줍니다. 노드 상태가 *준비*인지 확인합니다.

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>클러스터에서 사용 하는지 확인 합니다 *표준* SKU

사용 된 [az aks 표시][az-aks-show] 클러스터의 구성을 표시 하려면.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

확인 합니다 *loadBalancerSku* 속성으로 표시 *표준*합니다.

## <a name="use-the-load-balancer"></a>부하 분산 장치 사용

클러스터에서 부하 분산 장치를 사용 하려면 서비스 형식을 사용 하 여 한 서비스 매니페스트를 만듭니다 *LoadBalancer*합니다. 작업 부하 분산 장치를 표시 하려면 클러스터에서 실행할 샘플 응용 프로그램을 사용 하 여 다른 매니페스트를 만듭니다. 이 샘플 응용 프로그램 부하 분산 장치를 통해 노출 되 고 브라우저를 통해 볼 수 있습니다.

명명 된 매니페스트 만들기 `sample.yaml` 다음 예와에서 같이:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

위의 매니페스트 두 배포 구성: *azure vote 프런트* 하 고 *azure 투표 백*합니다. 구성 하려면 *azure vote 프런트* 부하 분산 장치를 사용 하 여 노출 해야 하는 배포 라는 매니페스트 만들기 `standard-lb.yaml` 다음 예와에서 같이:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

서비스 *azure vote 프런트* 사용 하 여는 *LoadBalancer* 형식에 연결 하 여 AKS 클러스터에 부하 분산 장치를 구성 하는 *azure vote 프런트* 배포 합니다.

샘플 응용 프로그램을 배포 하 고 부하 분산 장치를 사용 하 여는 [kubectl 적용][kubectl-apply] 프로그램 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

합니다 *표준* SKU 부하 분산 장치는 이제 샘플 응용 프로그램을 노출 하도록 구성 됩니다. 서비스 세부 정보를 보려면 *azure vote 프런트* 사용 하 여 [kubectl get][kubectl-get] 부하 분산 장치의 공용 IP를 확인 합니다. 부하 분산 장치의 공용 IP 주소에 표시 됩니다는 *EXTERNAL-IP* 열입니다. 변경 하려면 IP 주소에 대 일 분 정도 걸릴 수 있습니다 *\<보류 중\>* 는 실제 외부 IP 주소에 다음 예와에서 같이:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

브라우저에서 공용 IP로 이동한 후 샘플 응용 프로그램을 확인 합니다. 위의 예제에서 공용 IP는 `52.179.23.131`합니다.

![Azure Vote로 이동하는 이미지](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> 또한 내부적으로 부하 분산 장치를 구성 하 고 공용 IP를 노출 하지 않습니다 수 있습니다. 내부로 부하 분산 장치를 구성 하려면 추가 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` 를 주석으로는 *LoadBalancer* 서비스입니다. 매니페스트도에 대 한 자세한 내부 부하 분산 하는 예제에서는 yaml을 볼 수 있습니다 [여기][internal-lb-yaml]합니다.

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>표준 SKU 부하 분산 장치 구성 정리

샘플 응용 프로그램 및 부하 분산 장치 구성 제거를 사용 하 여 [kubectl 삭제][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>다음 단계

Kubernetes 서비스에 자세히 알아보려면 합니다 [Kubernetes 서비스 설명서][kubernetes-services]합니다.

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
