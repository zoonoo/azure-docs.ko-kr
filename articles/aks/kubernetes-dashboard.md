---
title: 웹 UI를 사용하여 Azure Kubernetes 클러스터 관리
description: AKS(Azure Kubernetes Service)와 함께 기본 제공 Kubernetes 웹 UI 대시보드를 사용하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: af48af596e86e0eb09fe45deabe13beedef57cd2
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307928"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)를 사용하여 Kubernetes 대시보드에 액세스

Kubernetes에는 기본 관리 작업에 사용할 수 있는 웹 대시보드가 포함됩니다. 이 문서에서는 Azure CLI를 사용하여 Kubernetes 대시보드에 액세스하는 방법을 보여준 후 일부 기본적인 대시보드 작업 과정을 안내합니다. Kubernetes 대시보드에 대한 자세한 내용은 [Kubernetes 웹 UI 대시보드][kubernetes-dashboard]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에 설명된 단계에서는 AKS 클러스터를 만들고 클러스터와 `kubectl` 연결을 설정했다고 가정합니다. AKS 클러스터를 만들어야 하는 경우, [AKS 빠른 시작][aks-quickstart]을 참조하세요.

또한 Azure CLI 버전 2.0.27 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="start-kubernetes-dashboard"></a>Kubernetes 대시보드 시작

Kubernetes 대시보드를 시작하려면 [az aks browse][az-aks-browse] 명령을 사용합니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myAKSCluster*라는 클러스터의 대시보드를 엽니다.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

이 명령은 개발 시스템과 Kubernetes API 사이에 프록시를 만들고 웹 브라우저에서 Kubernetes 대시보드를 엽니다.

### <a name="for-rbac-enabled-clusters"></a>RBAC 지원 클러스터의 경우

AKS 클러스터에서 RBAC를 사용하는 경우, 대시보드에 올바르게 액세스하려면 먼저 *ClusterRoleBinding*을 만들어야 합니다. 기본적으로 Kubernetes 대시보드는 최소한의 읽기 권한을 사용하여 배포되고 RBAC 액세스 오류를 표시합니다. Kubernetes 대시보드는 액세스 수준을 확인하는 사용자 제공 자격 증명을 현재 지원하지 않으며, 대신 서비스 계정에 부여된 역할을 사용합니다. 클러스터 관리자는 *kubernetes 대시보드* 서비스 계정에 대한 추가 액세스 권한을 부여하도록 선택할 수 있지만 이는 권한 상승에 대한 벡터일 수 있습니다. 또한 더 세분화된 수준의 액세스를 제공하려면 Azure Active Directory 인증을 통합할 수 있습니다.

바인딩을 만들려면 다음 예제와 같이 [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] 명령을 사용합니다. 

> [!WARNING]
> 이 샘플 바인딩은 추가 인증 구성 요소를 적용하지 않으므로 사용이 안전하지 않을 수 있습니다. Kubernetes 대시보드는 URL 액세스 권한을 가진 모든 사용자에게 열립니다. Kubernetes 대시보드를 공개적으로 공개하지 마세요.
>
> 다른 인증 방법 사용에 대한 자세한 내용은 [액세스 제어][dashboard-authentication]에 대한 Kubernetes 대시보드 wiki를 참조하세요.

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

이제 RBAC 지원 클러스터의 Kubernetes 대시보드에 액세스할 수 있습니다. Kubernetes 대시보드를 시작하려면 이전 단계에서 설명한 대로 [az aks browse][az-aks-browse] 명령을 사용합니다.

## <a name="run-an-application"></a>응용 프로그램 실행

Kubernetes 대시보드에서 오른쪽 상단 창의 **만들기** 단추를 클릭합니다. 배포 이름을 `nginx`로 지정하고 컨테이너 이미지 이름에 `nginx:latest`를 입력합니다. **서비스**에서 **외부**를 선택하고 포트와 대상 포트 모두에 대해 `80`을 입력합니다.

준비가 되면 **배포**를 클릭하여 배포를 만듭니다.

![Kubernetes 서비스 만들기 대화 상자](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>응용 프로그램 보기

응용 프로그램에 대한 상태는 Kubernetes 대시보드에서 확인할 수 있습니다. 응용 프로그램이 실행되면 각 구성 요소 옆에 녹색 확인란이 표시됩니다.

![Kubernetes 포드](./media/container-service-kubernetes-ui/complete-deployment.png)

응용 프로그램 Pod에 대한 자세한 내용을 보려면 왼쪽 메뉴에서 **Pod**를 클릭하고 **NGINX** Pod를 선택합니다. 여기에서 리소스 사용량과 같은 Pod별 정보를 볼 수 있습니다.

![Kubernetes 리소스](./media/container-service-kubernetes-ui/running-pods.png)

응용 프로그램의 IP 주소를 찾으려면 왼쪽 메뉴에서 **서비스**를 클릭하고 **NGINX** 서비스를 선택합니다.

![nginx 보기](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>응용 프로그램 편집

Kubernetes 대시보드는 응용 프로그램을 만들고 보는 것 외에도 응용 프로그램 배포를 편집하고 업데이트하는 데 사용할 수 있습니다.

배포를 편집하려면 왼쪽 메뉴에서 **배포**를 클릭한 다음 **NGINX** 배포를 선택합니다. 마지막으로, 오른쪽 위 탐색 표시줄에서 **편집**을 선택합니다.

![kubernetes 편집](./media/container-service-kubernetes-ui/view-deployment.png)

1인 `spec.replica` 값을 찾아서 이 값을 3으로 변경합니다. 이렇게 하면 NGINX 배포의 복제본 수가 1에서 3으로 증가합니다.

준비가 되면 **업데이트**를 선택합니다.

![kubernetes 편집](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>다음 단계

Kubernetes 대시보드에 대한 자세한 내용은 Kubernetes 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [Kubernetes 웹 UI 대시보드][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
