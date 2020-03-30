---
title: 미리 보기 - AKS(Azure Kubernetes) 클러스터에서 Windows 서버 컨테이너 만들기
description: Kubernetes 클러스터를 빠르게 만들고 Azure CLI를 사용하여 AKS(Azure Kubernetes Service)의 Windows 서버 컨테이너에 응용 프로그램을 배포하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 259728da5ea7f71110ce183ae25bb47a0f873614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475513"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>미리 보기 - Azure CLI를 사용하여 AKS(Azure Kubernetes) 클러스터에서 Windows 서버 컨테이너 만들기

AKS(Azure Kubernetes Service)는 클러스터를 빠르게 배포하고 관리할 수 있는 관리형 Kubernetes 서비스입니다. 이 문서에서는 Azure CLI를 사용하여 AKS 클러스터를 배포합니다. 또한 Windows Server 컨테이너에 ASP.NET 샘플 응용 프로그램을 클러스터에 배포합니다.

이 기능은 현재 미리 보기로 제공됩니다.

![샘플 응용 프로그램을 ASP.NET 브라우징 이미지](media/windows-container/asp-net-sample-app.png)

이 문서에서는 Kubernetes 개념에 대한 기본적인 이해를 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.0.61 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하십시오.

## <a name="before-you-begin"></a>시작하기 전에

Windows Server 컨테이너를 실행할 수 있는 클러스터를 만든 후 추가 노드 풀을 추가해야 합니다. 추가 노드 풀 추가는 이후 단계에서 다루지만 먼저 몇 가지 미리 보기 기능을 사용하도록 설정해야 합니다.

> [!IMPORTANT]
> AKS 미리보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 대로" 및 "사용 가능한 경우"로 제공되며 서비스 수준 계약 및 제한 보증에서 제외됩니다. AKS 미리 보기는 최선의 노력을 기울여 고객 지원에 의해 부분적으로 적용됩니다. 따라서 이러한 기능은 프로덕션 용으로 사용되지 않습니다. 추가 내용은 다음 지원 문서를 참조하십시오.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

Windows Server 컨테이너를 사용하려면 *aks 미리 보기* CLI 확장 버전 0.4.12 이상이 필요합니다. [az 확장 추가][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치한 다음 [az 확장 업데이트][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Windows 미리 보기 기능 등록

여러 노드 풀을 사용하고 Windows Server 컨테이너를 실행할 수 있는 AKS 클러스터를 만들려면 먼저 구독에서 *WindowsPreview* 기능 플래그를 사용하도록 설정합니다. 또한 *WindowsPreview* 기능은 다중 노드 풀 클러스터 및 가상 시스템 크기 집합을 사용하여 Kubernetes 노드의 배포 및 구성을 관리합니다. 다음 예제와 같이 [az 기능 레지스터][az-feature-register] 명령을 사용하여 *WindowsPreview* 기능 플래그를 등록합니다.

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> *WindowsPreview* 기능 플래그를 성공적으로 등록한 후 만든 모든 AKS 클러스터는 이 미리 보기 클러스터 환경을 사용합니다. 완전히 지원되는 일반 클러스터를 계속 만들려면 프로덕션 구독에서 미리 보기 기능을 사용하도록 설정하지 마십시오. 미리 보기 기능을 테스트하기 위해 별도의 테스트 또는 개발 Azure 구독을 사용합니다.

등록을 완료하는 데 몇 분 정도 걸립니다. [az 피처 목록][az-feature-list] 명령을 사용하여 등록 상태를 확인합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

등록 상태가 되면 `Registered`Ctrl-C를 눌러 상태 모니터링을 중지합니다.  그런 다음 [az 공급자 레지스터][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>제한 사항

여러 노드 풀을 지원하는 AKS 클러스터를 만들고 관리할 때 다음 제한 사항이 적용됩니다.

* 첫 번째 노드 풀은 삭제할 수 없습니다.

이 기능은 미리 보기 중이지만 다음과 같은 추가 제한 사항이 적용됩니다.

* AKS 클러스터에는 최대 8개의 노드 풀이 있을 수 있습니다.
* AKS 클러스터는 8개의 노드 풀에서 최대 400개의 노드를 가질 수 있습니다.
* Windows 서버 풀 이름에는 6자로 제한됩니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. 리소스 그룹을 만들 때 위치를 지정하라는 메시지가 나타납니다. 이 위치는 리소스 그룹 메타데이터가 저장되는 위치이며 리소스를 만드는 동안 다른 지역을 지정하지 않으면 리소스가 Azure에서 실행되는 위치입니다. [az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *동쪽* 위치에 *myResourceGroup이라는* 리소스 그룹을 만듭니다.

> [!NOTE]
> 이 문서에서는 이 자습서의 명령에 대해 Bash 구문을 사용합니다.
> Azure 클라우드 셸을 사용하는 경우 클라우드 셸 창의 왼쪽 상단에 있는 드롭다운이 **Bash로**설정되어 있는지 확인합니다.

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

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

Windows Server 컨테이너에 대한 노드 풀을 지원하는 AKS 클러스터를 실행하려면 클러스터에서 [Azure CNI(고급)][azure-cni-about] 네트워크 플러그인을 사용하는 네트워크 정책을 사용해야 합니다. 필요한 서브넷 범위 및 네트워크 고려 사항을 계획하는 데 도움이 되는 자세한 내용은 [Azure CNI 네트워킹 구성을][use-advanced-networking]참조하십시오. az [aks create][az-aks-create] 명령을 사용하여 *myAKSCluster라는*AKS 클러스터를 만듭니다. 이 명령은 존재하지 않는 경우 필요한 네트워크 리소스를 만듭니다.
  * 클러스터는 두 개의 노드로 구성됩니다.
  * *Windows-관리자 암호* 및 *Windows-관리자-사용자 이름* 매개 변수는 클러스터에서 만든 모든 Windows Server 컨테이너에 대한 관리자 자격 증명을 설정합니다.

> [!NOTE]
> 클러스터가 안정적으로 작동하려면 기본 노드 풀에서 최소 2개(2개) 노드를 실행해야 합니다.

고유한 보안 *PASSWORD_WIN* 제공하십시오(이 문서의 명령이 BASH 셸에 입력됨).

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> 암호 유효성 검사 오류가 발생하면 다른 지역에서 리소스 그룹을 만들어 보십시오.
> 그런 다음 새 리소스 그룹으로 클러스터를 만들어 보십시오.

> [!Note]
> 이 리전에서 버전이 지원되지 않기 때문에 AKS 클러스터를 만들 수 없는 경우 [az aks get-version --location eastus] 명령을 사용하여 이 리전의 지원되는 버전 목록을 찾을 수 있습니다.


몇 분 후 명령이 완료되면 클러스터에 대한 JSON 형식 정보가 반환됩니다. 경우에 따라 클러스터를 프로비전하는 데 몇 분 이상 걸릴 수 있습니다. 이러한 경우 최대 10분 동안 기다립니다. 

## <a name="add-a-windows-server-node-pool"></a>Windows 서버 노드 풀 추가

기본적으로 AKS 클러스터는 Linux 컨테이너를 실행할 수 있는 노드 풀로 만들어집니다. 명령을 `az aks nodepool add` 사용하여 Windows Server 컨테이너를 실행할 수 있는 추가 노드 풀을 추가합니다.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

위의 명령은 *npwin이라는* 새 노드 풀을 만들고 *myAKSCluster에*추가합니다. Windows Server 컨테이너를 실행하는 노드 풀을 만들 때 *노드 vm 크기의* 기본값은 *Standard_D2s_v3.* *노드 vm 크기* 매개 변수를 설정하도록 선택한 경우 [제한된 VM 크기][restricted-vm-sizes]목록을 확인하십시오. 최소 권장 크기는 *Standard_D2s_v3.* 위의 명령은 실행 `az aks create`시 생성된 기본 vnet의 기본 서브넷도 사용합니다.

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

```console
kubectl get nodes
```

다음 예제 출력은 클러스터의 모든 노드를 보여 주며, 출력은 다음과 같은 것입니다. 모든 노드의 상태가 *준비되었는지*확인합니다.

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>애플리케이션 실행

Kubernetes 매니페스트 파일은 어떤 컨테이너 이미지가 실행되는지 등과 같은 클러스터에 대해 원하는 상태를 정의합니다. 이 문서에서는 매니페스트를 사용하여 Windows Server 컨테이너에서 ASP.NET 샘플 응용 프로그램을 실행하는 데 필요한 모든 개체를 만듭니다. 이 매니페스트에는 ASP.NET 샘플 응용 프로그램에 대한 [Kubernetes 배포와][kubernetes-deployment] 인터넷에서 응용 프로그램에 액세스하는 외부 [Kubernetes 서비스가][kubernetes-service] 포함됩니다.

ASP.NET 샘플 응용 프로그램은 [.NET Framework 샘플의][dotnet-samples] 일부로 제공되며 Windows Server 컨테이너에서 실행됩니다. AKS는 Windows Server *2019* 이상의 이미지를 기반으로 Windows 서버 컨테이너를 요구합니다. Kubernetes 매니페스트 파일은 또한 Windows Server 컨테이너를 실행할 수 있는 노드에서 ASP.NET 샘플 응용 프로그램의 포드를 실행하도록 AKS 클러스터에 알리는 노드 [선택기를][node-selector] 정의해야 합니다.

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
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
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

```console
kubectl apply -f sample.yaml
```

다음 예제 출력에서는 성공적으로 만든 배포 및 서비스를 보여 주며 다음과 같은

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 애플리케이션 프런트 엔드를 인터넷에 공개하는 Kubernetes 서비스가 만들어집니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다. 경우에 따라 서비스를 프로비전하는 데 몇 분 이상 걸릴 수 있습니다. 이러한 경우 최대 10분 동안 기다립니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service][kubectl-get] 명령을 사용합니다.

```console
kubectl get service sample --watch
```

처음에는 *샘플* 서비스에 대한 *외부 IP가* *보류 중으로*표시됩니다.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* 주소가 *보류 중*에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

샘플 앱이 작동하는 것을 보려면 웹 브라우저를 서비스의 외부 IP 주소로 엽니다.

![샘플 응용 프로그램을 ASP.NET 브라우징 이미지](media/windows-container/asp-net-sample-app.png)

> [!Note]
> 페이지를 로드하려고 할 때 연결 시간 시간을 받으면 샘플 앱이 다음 명령 [kubectl get pods --watch]로 준비되었는지 확인해야 합니다. 외부 IP 주소를 사용할 수 있을 때까지 창 컨테이너가 시작되지 않는 경우가 있습니다.

## <a name="delete-cluster"></a>클러스터 삭제

클러스터가 더 이상 필요하지 않은 경우 [az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Kubernetes 클러스터를 배포하 고 Windows Server 컨테이너에 ASP.NET 샘플 응용 프로그램을 배포 했습니다. 방금 만든 클러스터에 대한 [Kubernetes 웹 대시보드에 액세스][kubernetes-dashboard]합니다.

AKS에 대해 자세히 알아보고 배포 예제에 대한 전체 코드를 연습해 보려면 Kubernetes 클러스터 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [AKS 튜토리얼][aks-tutorial]

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
