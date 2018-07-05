---
title: AKS(Azure Kubernetes Service) 클러스터에서 Virtual Kubelet 실행
description: Virtual Kubelet을 사용하여 Azure Container Instances에서 Kubernetes 컨테이너를 실행합니다.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: nepeters
ms.openlocfilehash: 0d046970b40f5253b07005ab578035c7c179e7df
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304014"
---
# <a name="virtual-kubelet-with-aks"></a>AKS를 사용한 Virtual Kubelet

ACI(Azure Container Instances)는 Azure에서 실행 중인 컨테이너에 호스팅된 환경을 제공합니다. ACI를 사용할 때는 기본 계산 인프라를 관리할 필요가 없습니다. Azure에서 사용자 대신 관리합니다. ACI에서 컨테이너를 실행할 때는 실행 중인 각 컨테이너에 초당 요금이 청구됩니다.

Azure Container Instances에 Virtual Kubelet 공급자를 사용할 때 표준 Kubernetes 노드의 경우처럼 Linux 및 Windows 컨테이너를 컨테이너 인스턴스에서 예약할 수 있습니다. 이 구성을 사용하면 Kubernetes의 기능 및 컨테이너 인스턴스의 관리 및 비용 혜택을 모두 활용할 수 있습니다.

> [!NOTE]
> Virtual Kubelet은 실험적 오픈 소스 프로젝트이며 그렇게 사용해야 합니다. Virtual Kubelet에 참여하고, 문제를 파일링하고, 자세히 알아보려면 [Virtual Kubelet GitHub 프로젝트][vk-github]를 참조하세요.

이 문서에서는 AKS의 컨테이너 인스턴스에 대해 Virtual Kubelet을 구성하는 방법을 자세히 설명합니다.

## <a name="prerequisite"></a>필수 요소

이 문서에서는 AKS 클러스터가 설치되어 있다고 가정합니다. AKS 클러스터가 필요한 경우 [AKS(Azure Kubernetes Service) 빠른 시작][aks-quick-start]을 참조하세요.

또한 Azure CLI 버전 **2.0.33** 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

[Helm](https://docs.helm.sh/using_helm/#installing-helm)은 Virtual Kubelet을 설치하기 위해 필요합니다.

## <a name="installation"></a>설치

[az aks install-connector][aks-install-connector] 명령을 사용하여 Virtual Kubelet을 설치합니다. 다음 예제에서는 Linux 및 Windows 커넥터를 배포합니다.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

이 인수는 `aks install-connector` 명령에 사용할 수 있습니다.

| 인수: | 설명 | 필수 |
|---|---|:---:|
| `--connector-name` | ACI 커넥터의 이름입니다.| 예 |
| `--name` `-n` | 관리되는 클러스터의 이름입니다. | 예 |
| `--resource-group` `-g` | 리소스 그룹의 이름입니다. | 예 |
| `--os-type` | 컨테이너 인스턴스 운영 체제 형식입니다. 허용되는 값: Linux 및 Windows 모두입니다. 기본값: Linux입니다. | 아니오 |
| `--aci-resource-group` | ACI 컨테이너 그룹을 만들 리소스 그룹입니다. | 아니오 |
| `--location` `-l` | ACI 컨테이너 그룹을 만들 위치입니다. | 아니오 |
| `--service-principal` | Azure API에 대한 인증에 사용되는 서비스 사용자입니다. | 아니오 |
| `--client-secret` | 서비스 사용자와 연결된 암호입니다. | 아니오 |
| `--chart-url` | ACI 커넥터를 설치하는 Helm 차트의 URL입니다. | 아니오 |
| `--image-tag` | Virtual Kubelet 컨테이너 이미지의 이미지 태그입니다. | 아니오 |

## <a name="validate-virtual-kubelet"></a>Virtual Kubelet 유효성 검사

Virtual Kubelet이 설치되었는지 확인하려면 [kubectl get nodes][kubectl-get] 명령을 사용하여 Kubernetes 노드 목록을 반환합니다.

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Linux 컨테이너 실행

파일 `virtual-kubelet-linux.yaml`을 만들고 다음 YAML에 복사합니다. `kubernetes.io/hostname` 값을 Linux Virtual Kubelet 노드의 이름으로 바꿉니다. [nodeSelector][node-selector] 및 [toleration][toleration]이 노드에서 컨테이너를 예약하는 데 사용됩니다.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

[kubectl create][kubectl-create] 명령을 사용하여 응용 프로그램을 실행합니다.

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

`-o wide` 인수와 [kubectl get pods][kubectl-get] 명령을 사용하여 예약된 노드를 포함한 Pod 목록을 출력합니다. `aci-helloworld` Pod는 `virtual-kubelet-virtual-kubelet-linux` 노드에서 예약되었습니다.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Windows 컨테이너 실행

파일 `virtual-kubelet-windows.yaml`을 만들고 다음 YAML에 복사합니다. `kubernetes.io/hostname` 값을 Windows Virtual Kubelet 노드의 이름으로 바꿉니다. [nodeSelector][node-selector] 및 [toleration][toleration]이 노드에서 컨테이너를 예약하는 데 사용됩니다.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

[kubectl create][kubectl-create] 명령을 사용하여 응용 프로그램을 실행합니다.

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

`-o wide` 인수와 [kubectl get pods][kubectl-get] 명령을 사용하여 예약된 노드를 포함한 Pod 목록을 출력합니다. `nanoserver-iis` Pod는 `virtual-kubelet-virtual-kubelet-win` 노드에서 예약되었습니다.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Virtual Kubelet 제거

[az aks remove-connector][aks-remove-connector] 명령을 사용하여 Virtual Kubelet을 제거합니다. 커넥터, AKS 클러스터 및 AKS 클러스터 리소스 그룹의 이름으로 인수 값을 바꿉니다.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>다음 단계

[Virtual Kubelet Github 프로젝트][vk-github]에서 Virtual Kubelet에 대해 자세히 읽어보세요.

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
