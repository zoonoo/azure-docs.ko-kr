---
title: 미리 보기-Azure Kubernetes Service (AKS) 클러스터에서 Windows Server 컨테이너 만들기
description: 신속 하 게 Kubernetes 클러스터를 만드는 Azure Kubernetes Service (AKS)에서 Azure CLI를 사용 하 여 Windows Server 컨테이너에서 응용 프로그램을 배포 하는 방법에 알아봅니다.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: twhitney
ms.openlocfilehash: cdcc1b985c570d1af4bbb33ac29a37e63b1dfa90
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752380"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>미리 보기-Azure CLI를 사용 하 여 Azure Kubernetes Service (AKS) 클러스터에서 Windows Server 컨테이너 만들기

AKS(Azure Kubernetes Service)는 클러스터를 빠르게 배포하고 관리할 수 있는 관리형 Kubernetes 서비스입니다. 이 문서에서는 Azure CLI를 사용 하 여 AKS 클러스터를 배포할 수 있습니다. Windows Server 컨테이너에서 ASP.NET 샘플 응용 프로그램을 클러스터에 배포할 수도 있습니다.

이 기능은 현재 미리 보기로 제공됩니다.

![ASP.NET 샘플 응용 프로그램으로 이동 하는 이미지](media/windows-container/asp-net-sample-app.png)

이 문서에서는 Kubernetes 개념에 대 한 기본적인 이해를 가정 합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 문서에서는 Azure CLI 버전 2.0.61 중인지 필요를 설치 하 고는 CLI를 로컬로 사용 하려는 경우 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

Windows Server 컨테이너를 실행할 수 있는 클러스터를 만든 후 추가 노드 풀을 추가 해야 합니다. 이후 단계에 대해서는 추가 노드 풀을 추가 하지만 먼저 몇 가지 미리 보기 기능을 사용 하도록 설정 해야 합니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스, 옵트인 합니다. 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 미리 보기에서이 기능이 없는 프로덕션 사용 해야 합니다. 공개 미리 보기에서 기능 '최상의' 지원에 속합니다. AKS 기술 지원 팀의 지원 업무 시간은 태평양 표준 시간대 (PST)만 제공 됩니다. 추가 정보는 다음과 같은 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치
    
여러 노드 풀 만들기 및 관리 하기 위해 CLI 명령에서 사용할 수는 *aks 미리 보기* CLI 확장 합니다. 설치를 *aks 미리 보기* 사용 하 여 Azure CLI 확장을 [az 확장 추가] [ az-extension-add] 다음 예와에서 같이 명령:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> 이전에 설치한 경우 합니다 *aks 미리 보기* 사용 하 여 확장을 설치 가능한 업데이트를 `az extension update --name aks-preview` 명령입니다.

### <a name="register-windows-preview-feature"></a>Windows 미리 보기 기능 등록

여러 노드 풀을 사용 하 고 Windows Server 컨테이너를 실행할 수 있는 AKS 클러스터를 만들려면 먼저 사용 하도록 설정 합니다 *WindowsPreview* 기능 플래그를 구독 합니다. 합니다 *WindowsPreview* 기능 풀 다중 노드 클러스터 및 가상 머신 확장 집합 배포 및 Kubernetes 노드는 구성을 관리 하려면 사용 합니다. 등록 합니다 *WindowsPreview* 기능 플래그를 사용 하는 [az 기능 등록] [ az-feature-register] 다음 예와에서 같이 명령:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> 성공적으로 등록 한 후를 만든 AKS 클러스터를 *WindowsPreview* 기능 플래그는이 미리 보기 클러스터 환경을 사용 합니다. 일반, 완벽 하 게 지원 되는 클러스터를 만드는 작업을 계속 하려면 프로덕션 구독에서 미리 보기 기능을 사용 하지 마십시오. 미리 보기 기능을 테스트 하는 것에 대 한 별도 테스트 또는 개발 Azure 구독을 사용 합니다.

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>제한 사항

생성 하 고 여러 노드 풀을 지 원하는 AKS 클러스터를 관리 하는 경우 다음과 같은 제한이 있습니다.

* 여러 노드 풀은 성공적으로 등록 한 후 만든 클러스터에 사용할 수는 *WindowsPreview*합니다. 여러 노드 풀도 등록 하는 경우 사용할 수 있습니다는 *MultiAgentpoolPreview* 하 고 *VMSSPreview* 구독에 대 한 기능입니다. 추가 하거나이 기능이 성공적으로 등록 된 이전에 만든 기존 AKS 클러스터를 사용 하 여 노드 풀을 관리할 수 없습니다.
* 첫 번째 노드 풀을 삭제할 수 없습니다.

이 기능은 미리 보기 상태인 동안에 다음 추가 제한 사항이 적용 됩니다.

* AKS 클러스터에는 최대 8 개의 노드 풀 있을 수 있습니다.
* AKS 클러스터에는 이러한 8 노드 풀에서 노드를 400 개 까지가 있을 수 있습니다.
* Windows Server 노드 풀 이름을 6 자 제한이 있습니다.

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
Windows Server 컨테이너에 대 한 노드 풀을 지 원하는 AKS 클러스터를 실행 하려면 클러스터를 사용 하는 네트워크 정책을 사용 하 여 해야 [Azure CNI] [ azure-cni-about] (고급) 네트워크 플러그 인입니다. 필요한 서브넷 범위 및 네트워크 고려 사항 계획 데 유용한 정보를 자세한 [Azure CNI 네트워킹 구성][use-advanced-networking]합니다. 사용 된 [az aks 만들기] [ az-aks-create] 라는 AKS 클러스터를 만드는 명령 *myAKSCluster*. 이 명령은 없는 경우 필요한 네트워크 리소스를 만듭니다.
  * 하나의 노드가 있는 클러스터를 구성
  * *암호 관리-windows-* 하 고 *windows 관리자-사용자 이름* 매개 변수는 클러스터에서 생성 된 모든 Windows Server 컨테이너에 대 한 관리자 자격 증명을 설정 합니다.

사용자 고유의 보안 제공 *PASSWORD_WIN*합니다.

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

몇 분 후 명령이 완료되면 클러스터에 대한 JSON 형식 정보가 반환됩니다.

## <a name="add-a-windows-server-node-pool"></a>Windows Server 노드 풀을 추가 합니다.

기본적으로 Linux 컨테이너를 실행할 수 있는 노드 풀을 사용 하 여 AKS 클러스터가 만들어집니다. 사용 하 여 `az aks nodepool add` Windows Server 컨테이너를 실행할 수 있는 추가 노드 풀을 추가 하는 명령입니다.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

위의 명령은 이라는 새 노드 풀을 만듭니다 *npwin* 에 추가 합니다 *myAKSCluster*합니다. 기본값에 대 한 Windows Server 컨테이너를 실행 하는 노드 풀을 만들 때 *노드 vm 크기* 됩니다 *Standard_D2s_v3*합니다. 설정 하려는 경우는 *노드 vm 크기* 매개 변수 목록을 확인 하세요 [VM 크기를 제한][restricted-vm-sizes]합니다. 최소 권장 크기는 *Standard_D2s_v3*합니다. 또한 위의 명령을 실행할 때 만든 기본 vnet에서 기본 서브넷을 사용 `az aks create`합니다.

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
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>애플리케이션 실행

Kubernetes 매니페스트 파일은 어떤 컨테이너 이미지가 실행되는지 등과 같은 클러스터에 대해 원하는 상태를 정의합니다. 이 문서에서는 Windows Server 컨테이너에서 ASP.NET 샘플 응용 프로그램을 실행 하는 데 필요한 모든 개체를 만드는 데 매니페스트를 사용 합니다. 이 매니페스트에 포함 된 [Kubernetes 배포] [ kubernetes-deployment] ASP.NET 샘플 응용 프로그램 및 외부 [Kubernetes 서비스] [ kubernetes-service] 를 인터넷에서 응용 프로그램에 액세스 합니다.

ASP.NET 샘플 응용 프로그램의 일부로 제공 되는 [.NET Framework 샘플] [ dotnet-samples] Windows Server 컨테이너에서 실행 됩니다. AKS의 이미지를 기반으로 하는 Windows Server 컨테이너 필요 *Windows Server 2019* 이상. Kubernetes 매니페스트 파일에도 정의 해야 합니다는 [노드 선택기] [ node-selector] AKS 클러스터 노드에서 Windows Server 컨테이너를 실행할 수 있는 ASP.NET 샘플 응용 프로그램 pod를 실행 하 게 합니다.

`sample.yaml`이라는 파일을 만들고 다음 YAML 정의에 복사합니다. Azure Cloud Shell을 사용하는 경우 이 파일은 가상 또는 실제 시스템에서 작업하고 있는 것처럼 `vi` 또는 `nano`를 사용하여 만들 수 있습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

[kubectl apply][kubectl-apply] 명령을 사용하여 애플리케이션을 배포하고 YAML 매니페스트의 이름을 지정합니다.

```azurecli-interactive
kubectl apply -f sample.yaml
```

다음 예제 출력에 배포 및 성공적으로 생성 하는 서비스를 보여 줍니다.

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 애플리케이션 프런트 엔드를 인터넷에 공개하는 Kubernetes 서비스가 만들어집니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

진행 상황을 모니터링하려면 `--watch` 인수와 함께 [kubectl get service][kubectl-get] 명령을 사용합니다.

```azurecli-interactive
kubectl get service sample --watch
```

처음에 *EXTERNAL-IP* 에 대 한 합니다 *샘플* service로 표시 됩니다 *보류 중인*합니다.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* 주소가 *보류 중*에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

작업에 샘플 앱을 보려면 서비스의 외부 IP 주소로 웹 브라우저를 엽니다.

![ASP.NET 샘플 응용 프로그램으로 이동 하는 이미지](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>클러스터 삭제

클러스터가 더 이상 필요하지 않은 경우 [az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Kubernetes 클러스터를 배포 하 고 Windows Server 컨테이너에서 ASP.NET 샘플 응용 프로그램을 배포 합니다. 방금 만든 클러스터에 대한 [Kubernetes 웹 대시보드에 액세스][kubernetes-dashboard]합니다.

AKS에 대해 자세히 알아보고 배포 예제에 대한 전체 코드를 연습해 보려면 Kubernetes 클러스터 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
