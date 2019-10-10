---
title: AKS (Azure Kubernetes Service)에서 표준 SKU 부하 분산 장치 사용
description: 표준 SKU에서 부하 분산 장치를 사용 하 여 Azure Kubernetes 서비스 (AKS)를 통해 서비스를 노출 하는 방법을 알아봅니다.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 55ded9a733baaac7fbc78621bd625d57d1d37ad1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255483"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)에서 표준 SKU 부하 분산 장치 사용

AKS (Azure Kubernetes Service)에서 응용 프로그램에 대 한 액세스를 제공 하기 위해 Azure Load Balancer를 만들고 사용할 수 있습니다. AKS에서 실행 중인 부하 분산 장치는 내부 또는 외부 부하 분산 장치로 사용할 수 있습니다. 내부 부하 분산 장치는 AKS 클러스터와 동일한 가상 네트워크에서 실행 되는 응용 프로그램에만 Kubernetes 서비스에 액세스할 수 있도록 합니다. 외부 부하 분산 장치는 수신에 대 한 공용 Ip를 하나 이상 수신 하 고 공용 Ip를 사용 하 여 외부에서 Kubernetes 서비스에 액세스할 수 있도록 합니다.

Azure Load Balancer는 ‘기본’ 및 ‘표준’이라는 두 SKU에서 사용할 수 있습니다. 기본적으로 *기본* SKU는 서비스 매니페스트가 AKS에서 부하 분산 장치를 만드는 데 사용 되는 경우에 사용 됩니다. *표준* SKU 부하 분산 장치를 사용 하면 더 큰 백 엔드 풀 크기 및 가용성 영역 같은 추가 기능 및 기능이 제공 됩니다. 사용할 *것을 선택* 하기 전에 *표준* 부하 분산 장치 간의 차이점을 이해 하는 것이 중요 합니다. AKS 클러스터를 만든 후에는 해당 클러스터에 대 한 부하 분산 장치 SKU를 변경할 수 없습니다. *기본* 및 *표준* sku에 대 한 자세한 내용은 [Azure 부하 분산 장치 SKU 비교][azure-lb-comparison]를 참조 하세요.

이 문서에서는 AKS (Azure Kubernetes Service)를 사용 하 여 *표준* SKU와 함께 Azure Load Balancer를 만들고 사용 하는 방법을 보여 줍니다.

이 문서에서는 Kubernetes 및 Azure Load Balancer 개념을 기본적으로 이해 하 고 있다고 가정 합니다. 자세한 내용은 [Kubernetes core 개념에 대 한 AKS (Azure Kubernetes Service)][kubernetes-concepts] 및 [Azure Load Balancer 정의][azure-lb]를 참조 하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에서는 Azure CLI 버전 2.0.74 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

기존 서브넷 또는 리소스 그룹을 사용 하는 경우 AKS 클러스터 서비스 주체에 네트워크 리소스를 관리할 수 있는 권한이 있어야 합니다. 일반적으로 위임 된 리소스의 서비스 사용자에 게 *네트워크 참가자* 역할을 할당 합니다. 사용 권한에 대 한 자세한 내용은 [DELEGATE AKS access to Other Azure resources를][aks-sp]참조 하세요.

기본 *기본*이 아닌 *표준* 으로 부하 분산 장치의 SKU를 설정 하는 AKS 클러스터를 만들어야 합니다.

### <a name="limitations"></a>제한 사항

*표준* SKU를 사용 하 여 부하 분산 장치를 지 원하는 AKS 클러스터를 만들고 관리 하는 경우 다음과 같은 제한 사항이 적용 됩니다.

* AKS 클러스터에서 송신 트래픽을 허용 하려면 하나 이상의 공용 IP 또는 IP 접두사가 필요 합니다. 공용 IP 또는 IP 접두사는 AKS의 이전 버전과의 호환성을 유지 하기 위해 제어 평면과 에이전트 노드 간의 연결을 유지 하는 데에도 필요 합니다. *표준* SKU 부하 분산 장치를 사용 하 여 공용 IP 또는 IP 접두사를 지정 하는 다음과 같은 옵션이 있습니다.
    * 사용자 고유의 공용 Ip를 제공 합니다.
    * 사용자 고유의 공용 IP 접두사를 제공 합니다.
    * AKS 클러스터가 AKS 클러스터로 만든 것과 동일한 리소스 그룹에 많은 *표준* SKU 공용 ip를 만들 수 있도록 허용 하는 100 숫자 (일반적으로 *MC_* 로 이름이 지정 됨)를 지정 합니다. AKS는 *표준* SKU 부하 분산 장치에 공용 IP를 할당 합니다. 공용 ip, 공용 IP 접두사 또는 Ip 수가 지정 되지 않은 경우 기본적으로 하나의 공용 IP가 AKS 클러스터와 동일한 리소스 그룹에 자동으로 만들어집니다. 또한 공용 주소를 허용 하 고 IP 생성을 차단을 하는 Azure Policy를 만들지 않도록 해야 합니다.
* 부하 분산 장치에 대 한 *표준* SKU를 사용 하는 경우 Kubernetes 버전 1.13 이상을 사용 해야 합니다.
* 부하 분산 장치 SKU를 정의 하는 것은 AKS 클러스터를 만들 때만 수행할 수 있습니다. AKS 클러스터를 만든 후에는 부하 분산 장치 SKU를 변경할 수 없습니다.
* 단일 클러스터에서 하나의 부하 분산 장치 SKU만 사용할 수 있습니다.

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
*표준* SKU를 사용 하 여 부하 분산 장치를 지 원하는 AKS 클러스터를 실행 하기 위해 클러스터에서 *부하 분산 장치-SKU* 매개 변수를 *표준*으로 설정 해야 합니다. 이 매개 변수는 클러스터를 만들 때 *표준* SKU를 사용 하 여 부하 분산 장치를 만듭니다. 클러스터에서 *LoadBalancer* 서비스를 실행 하는 경우 *표준* SKU 부하 분산 장치의 구성이 서비스의 구성으로 업데이트 됩니다. [Az aks create][az-aks-create] 명령을 사용 하 여 *myAKSCluster*라는 aks 클러스터를 만듭니다.

> [!NOTE]
> *부하 분산 장치-sku* 속성은 클러스터를 만들 때만 사용할 수 있습니다. AKS 클러스터를 만든 후에는 부하 분산 장치 SKU를 변경할 수 없습니다. 또한 단일 클러스터에서 한 가지 유형의 부하 분산 장치 SKU만 사용할 수 있습니다.
> 
> 사용자 고유의 공용 Ip를 사용 하려면 *부하 분산 장치-아웃 바운드* *ip 또는 부하 분산 장치-접두사* 매개 변수를 사용 합니다. 이러한 매개 변수는 모두 [클러스터를 업데이트할](#optional---provide-your-own-public-ips-or-prefixes-for-egress)때 사용할 수도 있습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

몇 분 후 명령이 완료되면 클러스터에 대한 JSON 형식 정보가 반환됩니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리하려면 [kubectl][kubectl] Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 [az aks install-cli][az-aks-install-cli] 명령을 사용합니다.

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
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>클러스터가 *표준* SKU를 사용 하는지 확인

[Az aks show][az-aks-show] 를 사용 하 여 클러스터의 구성을 표시 합니다.

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

*LoadBalancerSku* 속성이 *표준*으로 표시 되는지 확인 합니다.

## <a name="use-the-load-balancer"></a>부하 분산 장치 사용

클러스터에서 부하 분산 장치를 사용 하려면 서비스 유형이 *LoadBalancer*인 서비스 매니페스트를 만듭니다. 부하 분산 장치가 작동 하는 것을 표시 하려면 클러스터에서 실행할 샘플 응용 프로그램을 사용 하 여 다른 매니페스트를 만듭니다. 이 샘플 응용 프로그램은 부하 분산 장치를 통해 노출 되며 브라우저를 통해 볼 수 있습니다.

다음 예제와 같이 `sample.yaml` 이라는 매니페스트를 만듭니다.

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

위의 매니페스트는 두 개의 배포 인 *azure-투표* 와 *azure-투표*를 구성 합니다. 부하 분산 장치를 사용 하 여 노출 되도록 *azure-투표-프런트* 배포를 구성 하려면 다음 예제와 같이 `standard-lb.yaml` 이라는 매니페스트를 만듭니다.

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

*Azure-투표 프런트* 은 *LoadBalancer* 유형을 사용 하 여 AKS 클러스터에 대 한 부하 분산 장치를 구성 하 고 *azure-투표* 배포에 연결 합니다.

[Kubectl apply][kubectl-apply] 를 사용 하 여 샘플 응용 프로그램 및 부하 분산 장치를 배포 하 고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

이제 *표준* SKU 부하 분산 장치는 샘플 응용 프로그램을 노출 하도록 구성 됩니다. [Kubectl get][kubectl-get] 을 사용 하 여 *azure-투표* 의 서비스 세부 정보를 확인 하 여 부하 분산 장치의 공용 IP를 확인 합니다. 부하 분산 장치의 공용 IP 주소는 *외부 ip* 열에 표시 됩니다. 다음 예와 같이 IP 주소를 *\< pending @ no__t-2* 에서 실제 외부 IP 주소로 변경 하는 데 1 ~ 2 시간이 걸릴 수 있습니다.

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

브라우저에서 공용 IP로 이동 하 여 샘플 응용 프로그램이 표시 되는지 확인 합니다. 위의 예제에서 공용 IP는 `52.179.23.131`입니다.

![Azure Vote로 이동하는 이미지](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

> [!NOTE]
> 부하 분산 장치를 내부로 구성 하 고 공용 IP를 노출 하지 않을 수도 있습니다. 부하 분산 장치를 내부로 구성 하려면 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"`을 *LoadBalancer* 서비스에 대 한 주석으로 추가 합니다. [여기][internal-lb-yaml]에서 내부 부하 분산 장치에 대 한 자세한 정보 뿐만 아니라 yaml 매니페스트 예제를 볼 수 있습니다.

## <a name="optional---scale-the-number-of-managed-public-ips"></a>선택 사항-관리 되는 공용 Ip 수를 조정 합니다.

기본적으로 생성 되는 관리 되는 아웃 바운드 공용 Ip를 사용 하 여 *표준* SKU 부하 분산 장치를 사용 하는 경우, *부하 분산 장치 관리-ip 카운트* 매개 변수를 사용 하 여 관리 되는 아웃 바운드 공용 ip 수를 조정할 수 있습니다.

기존 클러스터를 업데이트 하려면 다음 명령을 실행 합니다. 이 매개 변수는 클러스터 만들기 시간에 여러 관리 되는 아웃 바운드 공용 Ip를 갖도록 설정할 수도 있습니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

위의 예에서는 *Myresourcegroup*의 *myAKSCluster* 클러스터에 대 한 관리 되는 아웃 바운드 공용 ip 수를 *2* 로 설정 합니다. 

또한 `--load-balancer-managed-outbound-ip-count` 매개 변수를 추가 하 고 원하는 값으로 설정 하 여 클러스터를 만들 때 *부하 분산 장치-관리-ip 카운트* 매개 변수를 사용 하 여 초기에 관리 되는 아웃 바운드 공용 ip 수를 설정할 수 있습니다. 관리 되는 아웃 바운드 공용 Ip의 기본 수는 1입니다.

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>선택 사항-사용자 고유의 공용 Ip 또는 송신에 대 한 접두사를 제공 합니다.

*표준* sku 부하 분산 장치를 사용 하는 경우 AKS 클러스터는 AKS 클러스터에 대해 생성 된 동일한 리소스 그룹에서 공용 ip를 자동으로 만들고 *표준* SKU 부하 분산 장치에 공용 ip를 할당 합니다. 또는 클러스터를 만들 때 사용자 고유의 공용 IP를 할당 하거나 기존 클러스터의 부하 분산 장치 속성을 업데이트할 수 있습니다.

여러 IP 주소 또는 접두사를 가져오면 단일 부하 분산 장치 개체 뒤에 IP 주소를 정의할 때 여러 지원 서비스를 정의할 수 있습니다. 특정 노드의 송신 끝점은 연결 된 서비스에 따라 달라 집니다.

> [!IMPORTANT]
> 부하 분산 장치를 *표준* sku와 함께 수신 하려면 *표준* sku 공용 ip를 사용 해야 합니다. [Az network public ip show][az-network-public-ip-show] 명령을 사용 하 여 공용 IP의 SKU를 확인할 수 있습니다.
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

[Az network 공용-ip show][az-network-public-ip-show] 명령을 사용 하 여 공용 Ip의 id를 나열 합니다.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

위의 명령은 *Myresourcegroup* 리소스 그룹의 *myPublicIP* 공용 IP에 대 한 ID를 표시 합니다.

Aks *매개 변수와* 함께 *az update* 명령을 사용 하 여 공용 ip로 클러스터를 업데이트 합니다.

다음 예제에서는 이전 명령의 Id를 사용 하 여 *부하 분산 장치-아웃 바운드 ip* 매개 변수를 사용 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*표준* SKU 부하 분산 장치에 대 한 공용 IP 접두사를 사용할 수도 있습니다. 다음 예제에서는 [az network 공용 ip prefix show][az-network-public-ip-prefix-show] 명령을 사용 하 여 공용 ip 접두사의 id를 나열 합니다.

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

위의 명령은 *Myresourcegroup* 리소스 그룹의 *myPublicIPPrefix* 공용 IP 접두사에 대 한 ID를 표시 합니다.

다음 예제에서는 이전 명령의 Id를 사용 하 여 *부하 분산 장치-아웃 바운드 ip 접두사* 매개 변수를 사용 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> 공용 Ip 및 IP 접두사는 AKS 클러스터와 동일한 구독의 일부 및 동일한 지역에 있어야 합니다. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>클러스터를 만들 때 사용자 고유의 공용 IP 또는 접두사를 정의 합니다.

허용 목록 송신 끝점과 같은 시나리오를 지원 하기 위해 클러스터를 만들 때 송신 하기 위해 고유한 IP 주소 또는 IP 접두사를 가져올 수 있습니다. 클러스터 생성 단계에 위에 표시 된 것과 동일한 매개 변수를 추가 하 여 클러스터의 수명 주기 시작 부분에 고유한 공용 ip 및 IP 접두사를 정의 합니다.

Aks *매개 변수와* 함께 *az create* 명령을 사용 하 여 시작 시 공용 ip를 사용 하 여 새 클러스터를 만듭니다.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*로드 균형 조정기-* aks 매개 변수와 함께 *az create* 명령을 사용 하 여 시작 시 공용 ip 접두사를 사용 하 여 새 클러스터를 만듭니다.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>표준 SKU 부하 분산 장치 구성 정리

예제 응용 프로그램 및 부하 분산 장치 구성을 제거 하려면 [kubectl delete][kubectl-delete]를 사용 합니다.

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>다음 단계

Kubernetes services에 대 한 자세한 내용은 [Kubernetes services 설명서][kubernetes-services]를 참조 하세요.

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
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

