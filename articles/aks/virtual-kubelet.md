---
title: AKS(Azure Kubernetes Service) 클러스터에서 Virtual Kubelet 실행
description: AKS(Azure Kubernetes Service)에서 Virtual Kubelet을 사용하여 Azure Container Instances에서 Linux 및 Windows 컨테이너를 실행하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: cc0c3becf21cb54b97a88e9ba35b38308af81a85
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475416"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Virtual Kubelet 사용

ACI(Azure Container Instances)는 Azure에서 실행 중인 컨테이너에 호스팅된 환경을 제공합니다. ACI를 사용할 때는 기본 계산 인프라를 관리할 필요가 없습니다. Azure에서 사용자 대신 관리합니다. ACI에서 컨테이너를 실행할 때는 실행 중인 각 컨테이너에 초당 요금이 청구됩니다.

Azure Container Instances에 Virtual Kubelet 공급자를 사용할 때 표준 Kubernetes 노드의 경우처럼 Linux 및 Windows 컨테이너를 컨테이너 인스턴스에서 예약할 수 있습니다. 이 구성을 사용하면 Kubernetes의 기능 및 컨테이너 인스턴스의 관리 및 비용 혜택을 모두 활용할 수 있습니다.

> [!NOTE]
> AKS에는 ACI에서 컨테이너를 예약하기 위해 *가상 노드*라고 하는 기본 제공 기능이 있습니다. 이러한 가상 노드는 현재 Linux 컨테이너 인스턴스를 지원합니다. Windows 컨테이너 인스턴스를 예약해야 하는 경우 Virtual Kubelet을 사용하여 계속할 수 있습니다. 그렇지 않으면 이 문서에서 언급된 수동 Virtual Kubelet 지침 대신 가상 노드를 사용해야 합니다. [Azure CLI][virtual-nodes-cli] 또는 [Azure Portal][virtual-nodes-portal]을 사용하여 가상 노드를 시작할 수 있습니다.
>
> Virtual Kubelet은 실험적 오픈 소스 프로젝트이며 그렇게 사용해야 합니다. Virtual Kubelet에 참여하고, 문제를 파일링하고, 자세히 알아보려면 [Virtual Kubelet GitHub 프로젝트][vk-github]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 AKS 클러스터가 설치되어 있다고 가정합니다. AKS 클러스터가 필요한 경우 [AKS(Azure Kubernetes Service) 빠른 시작][aks-quick-start]을 참조하세요.

Azure CLI 버전 해야 **2.0.65** 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

Virtual Kubelet을 설치 하려면 설치 및 구성 [Helm] [ aks-helm] AKS 클러스터에 있습니다. 반드시에 Tiller [Kubernetes RBAC를 사용 하 여 사용 하도록 구성 된](#for-rbac-enabled-clusters), 필요한 경우.

### <a name="register-container-instances-feature-provider"></a>Container Instances 기능 공급자 등록

컨테이너 인스턴스 ACI (Azure) 서비스에서 이전에 사용 하지 않은 경우 구독을 사용 하 여 서비스 공급자를 등록 합니다. 다음 예제와 같이 [az provider list][az-provider-list] 명령을 사용하여 ACI 공급자 등록의 상태를 확인할 수 있습니다.

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* 공급자는 다음 예제 출력에 나온 대로 *Registered*로 보고됩니다.

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

공급자가 *NotRegistered*로 표시되는 경우 다음 예제에 나온 대로 [az provider register][az-provider-register]를 사용하여 공급자를 등록합니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>RBAC 지원 클러스터의 경우

AKS 클러스터에서 RBAC가 지원될 경우 Tiller에서 사용할 수 있게 서비스 계정 및 역할 바인딩을 만들어야 합니다. 자세한 내용은 [Helm 역할 기반 액세스 제어][helm-rbac].를 참조하세요. 서비스 계정과 역할 바인딩을 만들려면 *rbac-virtual-kubelet.yaml* 파일을 만들고 다음 정의를 붙여넣습니다.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

[kubectl apply][kubectl-apply]를 사용하여 서비스 계정과 바인딩을 적용하고 다음 예제와 같이 *rbac-virtual-kubelet.yaml* 파일을 지정합니다.

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Tiller 서비스 계정을 사용하도록 Helm 구성:

```console
helm init --service-account tiller
```

이제 Virtual Kubelet을 AKS 클러스터에 계속 설치할 수 있습니다.

## <a name="installation"></a>설치

[az aks install-connector][aks-install-connector] 명령을 사용하여 Virtual Kubelet을 설치합니다. 다음 예제에서는 Linux 및 Windows 커넥터를 배포합니다.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

사용할 수 있는 이러한 인수는 [az aks 설치-커넥터] [ aks-install-connector] 명령입니다.

| 인수: | 설명 | 필수 |
|---|---|:---:|
| `--connector-name` | ACI 커넥터의 이름입니다.| 예 |
| `--name` `-n` | 관리되는 클러스터의 이름입니다. | 예 |
| `--resource-group` `-g` | 리소스 그룹의 이름입니다. | 예 |
| `--os-type` | 컨테이너 인스턴스 운영 체제 형식입니다. 허용되는 값은 다음과 같습니다. 둘 다, Linux, Windows. Default: Linux. | 아닙니다. |
| `--aci-resource-group` | ACI 컨테이너 그룹을 만들 리소스 그룹입니다. | 아닙니다. |
| `--location` `-l` | ACI 컨테이너 그룹을 만들 위치입니다. | 아닙니다. |
| `--service-principal` | Azure API에 대한 인증에 사용되는 서비스 사용자입니다. | 아닙니다. |
| `--client-secret` | 서비스 사용자와 연결된 암호입니다. | 아닙니다. |
| `--chart-url` | ACI 커넥터를 설치하는 Helm 차트의 URL입니다. | 아닙니다. |
| `--image-tag` | Virtual Kubelet 컨테이너 이미지의 이미지 태그입니다. | 아닙니다. |

## <a name="validate-virtual-kubelet"></a>Virtual Kubelet 유효성 검사

Virtual Kubelet 설치 되어 있는지 확인을 위해 사용 하 여 Kubernetes 노드 목록을 반환 합니다 [kubectl get 노드] [ kubectl-get] 명령:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Linux 컨테이너 실행

파일 `virtual-kubelet-linux.yaml`을 만들고 다음 YAML에 복사합니다. [nodeSelector][node-selector] 및 [toleration][toleration]이 노드에서 컨테이너를 예약하는 데 사용됩니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
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
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

[kubectl create][kubectl-create] 명령을 사용하여 애플리케이션을 실행합니다.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

`-o wide` 인수와 [kubectl get pods][kubectl-get] 명령을 사용하여 예약된 노드를 포함한 Pod 목록을 출력합니다. `aci-helloworld` Pod는 `virtual-kubelet-virtual-kubelet-linux` 노드에서 예약되었습니다.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Windows 컨테이너 실행

파일 `virtual-kubelet-windows.yaml`을 만들고 다음 YAML에 복사합니다. [nodeSelector][node-selector] 및 [toleration][toleration]이 노드에서 컨테이너를 예약하는 데 사용됩니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

[kubectl create][kubectl-create] 명령을 사용하여 애플리케이션을 실행합니다.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

`-o wide` 인수와 [kubectl get pods][kubectl-get] 명령을 사용하여 예약된 노드를 포함한 Pod 목록을 출력합니다. `nanoserver-iis` Pod는 `virtual-kubelet-virtual-kubelet-windows` 노드에서 예약되었습니다.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Virtual Kubelet 제거

[az aks remove-connector][aks-remove-connector] 명령을 사용하여 Virtual Kubelet을 제거합니다. 커넥터, AKS 클러스터 및 AKS 클러스터 리소스 그룹의 이름으로 인수 값을 바꿉니다.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> 두 OS 커넥터를 제거하는 오류가 발생하거나 Windows 또는 Linux OS 커넥터를 제거하려는 경우 OS 유형을 수동으로 지정할 수 있습니다. `--os-type` 매개 변수를 이전 `az aks remove-connector` 명령에 추가하고, `Windows` 또는 `Linux`를 지정합니다.

## <a name="next-steps"></a>다음 단계

Virtual Kubelet에서 발생할 수 있는 문제는 [알려진 단점 및 해결 방법][vk-troubleshooting]을 참조하세요. Virtual Kubelet의 문제를 보고하려면 [GitHub 문제를 개설][vk-issues]하세요.

Virtual Kubelet에 대한 자세한 내용은 [Virtual Kubelet GitHub 프로젝트][vk-github]를 참조하세요.

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
