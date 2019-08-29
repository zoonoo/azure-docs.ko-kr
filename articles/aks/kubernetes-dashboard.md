---
title: 웹 대시보드를 사용하여 Azure Kubernetes Service 클러스터를 관리합니다.
description: AKS(Azure Kubernetes Service) 클러스터를 관리하는 데 기본 제공 Kubernetes 웹 UI 대시보드를 사용하는 방법 알아보기
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: 5aa8268fee7d43ad13ea8710760ba493683f502e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126869"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Kubernetes 웹 대시보드에 액세스

Kubernetes에는 기본 관리 작업에 사용할 수 있는 웹 대시보드가 포함됩니다. 이 대시보드를 사용하면 애플리케이션의 기본 상태와 메트릭을 보고 서비스를 작성 및 배포하며 기존 애플리케이션을 편집할 수 있습니다. 이 문서에서는 Azure CLI를 사용하여 Kubernetes 대시보드에 액세스하는 방법을 보여준 후 일부 기본적인 대시보드 작업 과정을 안내합니다.

Kubernetes 대시보드에 대 한 자세한 내용은 [Kubernetes 웹 UI 대시보드][kubernetes-dashboard]를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에 설명된 단계에서는 AKS 클러스터를 만들고 클러스터와 `kubectl` 연결을 설정했다고 가정합니다. AKS 클러스터를 만들어야 하는 경우 [AKS 빠른][aks-quickstart]시작을 참조 하세요.

또한 Azure CLI 버전 2.0.46 이상이 설치되고 구성되어 있어야 합니다.  `az --version` 을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

## <a name="start-the-kubernetes-dashboard"></a>Kubernetes 대시보드 시작

Kubernetes 대시보드를 시작 하려면 [az aks browse][az-aks-browse] 명령을 사용 합니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myAKSCluster*라는 클러스터의 대시보드를 엽니다.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

이 명령은 개발 시스템과 Kubernetes API 사이에 프록시를 만들고 웹 브라우저에서 Kubernetes 대시보드를 엽니다. 웹 브라우저가 Kubernetes 대시보드로 열리지 않는 경우에는 일반적으로 `http://127.0.0.1:8001`Azure CLI에 명시 된 URL 주소를 복사 하 여 붙여 넣습니다.

![Kubernetes 웹 대시보드의 로그인 페이지](./media/kubernetes-dashboard/dashboard-login.png)

클러스터의 대시보드에 로그인 하려면 다음 옵션을 사용할 수 있습니다.

* [Kubeconfig 파일][kubeconfig-file]입니다. [Az aks get 자격 증명][az-aks-get-credentials]을 사용 하 여 kubeconfig 파일을 생성할 수 있습니다.
* [서비스 계정 토큰][aks-service-accounts] 또는 사용자 토큰과 같은 토큰입니다. [Aad 사용 클러스터][aad-cluster]에서이 토큰은 aad 토큰입니다. 를 사용 `kubectl config view` 하 여 kubeconfig 파일의 토큰을 나열할 수 있습니다. AKS 클러스터에 사용할 AAD 토큰을 만드는 방법에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 Azure Kubernetes Service와 Azure Active Directory 통합][aad-cluster]을 참조 하세요.
* 기본 대시보드 서비스 계정- *건너뛰기를*클릭 하면 사용 됩니다.

> [!WARNING]
> 사용 된 인증 방법에 관계 없이 Kubernetes 대시보드를 공개적으로 노출 하지 마십시오.
> 
> Kubernetes 대시보드에 대 한 인증을 설정 하는 경우 기본 대시보드 서비스 계정에서 토큰을 사용 하는 것이 좋습니다. 토큰을 사용 하면 각 사용자가 자신의 권한을 사용할 수 있습니다. 기본 대시보드 서비스 계정을 사용 하면 사용자가 자신의 권한을 무시 하 고 대신 서비스 계정을 사용할 수 있습니다.
> 
> 기본 대시보드 서비스 계정을 사용 하도록 선택 하 고 AKS 클러스터에서 RBAC를 사용 하는 경우, 대시보드에 올바르게 액세스 하려면 먼저 *Clusterrolebinding* 을 만들어야 합니다. 기본적으로 Kubernetes 대시보드는 최소한의 읽기 권한을 사용하여 배포되고 RBAC 액세스 오류를 표시합니다. 클러스터 관리자는 *kubernetes 대시보드* 서비스 계정에 대한 추가 액세스 권한을 부여하도록 선택할 수 있지만 이는 권한 상승에 대한 벡터일 수 있습니다. 또한 더 세분화된 수준의 액세스를 제공하려면 Azure Active Directory 인증을 통합할 수 있습니다.
>
> 바인딩을 만들려면 다음 예제와 같이 [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] 명령을 사용 합니다. **이 샘플 바인딩은 추가 인증 구성 요소를 적용 하지 않으며 안전 하지 않은 사용이 발생할 수 있습니다.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> 이제 RBAC 지원 클러스터의 Kubernetes 대시보드에 액세스할 수 있습니다. Kubernetes 대시보드를 시작 하려면 이전 단계에서 설명한 대로 [az aks browse][az-aks-browse] 명령을 사용 합니다.
>
> 클러스터에서 RBAC를 사용 하지 않는 경우 *Clusterrolebinding*을 만드는 것이 좋습니다.
> 
> 다른 인증 방법을 사용 하는 방법에 대 한 자세한 내용은 [액세스 제어][dashboard-authentication]의 Kubernetes 대시보드 wiki를 참조 하세요.

로그인 할 방법을 선택한 후에는 Kubernetes 대시보드가 표시 됩니다. *토큰* 또는 *건너뛰기를*사용 하도록 선택한 경우 Kubernetes 대시보드는 현재 로그인 한 사용자의 사용 권한을 사용 하 여 클러스터에 액세스 합니다.

![Kubernetes 웹 대시보드의 개요 페이지](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>애플리케이션 만들기

Kubernetes 대시보드가 관리 작업의 복잡도를 줄일 수 있는 방법을 보기 위해 애플리케이션을 작성해 보겠습니다. 텍스트 입력, YAML 파일을 제공하거나 그래픽 마법사를 통해 Kubernetes 대시보드에서 애플리케이션을 작성할 수 있습니다.

애플리케이션을 작성하려면 다음 단계를 완료하세요.

1. 오른쪽 상단 창에서 **만들기** 단추를 선택하세요.
1. 그래픽 마법사를 사용하려면 **앱 만들기**를 선택하세요.
1. 배치 이름(예: *nginx*)을 입력하세요.
1. 사용할 컨테이너 이미지의 이름(예: *1.15.5*)을 입력하세요.
1. 웹 트래픽에 대해 포트 80을 노출하려면 Kubernetes 서비스를 작성하세요. **서비스**에서 **외부**를 선택하고 포트와 대상 포트 모두에 대해 **80**을 입력합니다.
1. 준비가 되면 **배포** 를 선택하여 앱을 만듭니다.

![Kubernetes 웹 대시보드에 앱 배포](./media/kubernetes-dashboard/create-app.png)

공용 외부 IP 주소가 Kubernetes 서비스에 지정되는 데 1분이나 2분이 소요됩니다. 왼쪽에 있는 **감지 및 로드 밸런싱**에서 **서비스**를 선택하세요. 다음 예제에 표시된 대로 ‘외부 엔드포인트’를 포함하여 애플리케이션 서비스가 나열됩니다.

![서비스 및 엔드포인트 목록 보기](./media/kubernetes-dashboard/view-services.png)

웹 브라우저 창을 기본 NGINX 페이지로 열려면 엔드포인트 주소를 선택하세요.

![배포된 애플리케이션의 기본 NGINX 페이지 보기](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Pod 정보 보기

Kubernetes 대시보드는 기본 모니터링 메트릭 및 문제점 해결 정보(예:로그)를 제공할 수 있습니다.

애플리케이션 Pod에 대한 자세한 정보를 보려면 왼쪽 메뉴에서 **Pod**을 선택하세요. 사용 가능한 Pod 목록이 표시됩니다. 리소스 사용량과 같은 정보를 보려면 *nginx* Pod를 선택하세요.

![Pod 정보 보기](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>애플리케이션 편집

Kubernetes 대시보드는 애플리케이션을 만들고 보는 것 외에도 애플리케이션 배포를 편집하고 업데이트하는 데 사용할 수 있습니다. 애플리케이션에 추가 중복을 제공하려면 NGINX 복제본의 수를 늘리세요.

배포를 편집하려면 다음을 수행하세요.

1. 왼쪽 메뉴에서 **배치**를 선택한 다음, *nginx* 배치를 선택하세요.
1. 오른쪽 위 탐색 표시줄에서 **편집**을 선택합니다.
1. 행 20 정도에서 `spec.replica` 값을 찾으세요. 애플리케이션의 복제본 수를 늘리려면 이 값을 *1*에서 *3*으로 변경하세요.
1. 준비가 되면 **업데이트**를 선택합니다.

![배치를 편집하여 복제본 수를 업데이트세요.](./media/kubernetes-dashboard/edit-deployment.png)

새 Pod이 복제본 세트 내에 작성되는 데 몇 분 정도 걸립니다. 왼쪽 메뉴에서 **복제본 세트**를 선택한 다음, *nginx* 복제본 세트를 선택합니다. 이제 Pod 목록은 다음 예제 출력에 표시된 대로 업데이트된 복제본 수를 반영합니다.

![복제본 세트에 대한 정보 보기](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>다음 단계

Kubernetes 대시보드에 대 한 자세한 내용은 [Kubernetes 웹 UI 대시보드][kubernetes-dashboard]를 참조 하세요.

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
