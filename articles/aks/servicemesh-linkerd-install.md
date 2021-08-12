---
title: Azure Kubernetes Service(AKS)에서 Linkerd 설치
description: Linkerd를 설치 및 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 서비스 메시를 만드는 방법을 알아봅니다.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: b2b859515481281b571c9803329c6e41cd365ef5
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108070050"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service(AKS)에서 Linkerd 설치

[Linkerd][linkerd-github]는 오픈 소스 서비스 메시이며 [CNCF 인큐베이팅 프로젝트][linkerd-cncf]입니다. Linkerd는 트래픽 관리, 서비스 ID 및 보안, 안정성 및 가시성을 포함한 기능을 제공하는 초경량 서비스 메시입니다. Linkerd에 대한 자세한 내용은 공식 [Linkerd FAQ][linkerd-faq] 및 [Linkerd 아키텍처][linkerd-architecture] 문서를 참조하세요.

이 문서에서는 Linkerd를 설치하는 방법을 보여줍니다. Linkerd `linkerd` 클라이언트 이진은 클라이언트 컴퓨터에 설치되며 Linkerd 구성 요소는 AKS의 Kubernetes 클러스터에 설치됩니다.

> [!NOTE]
> 이 지침은 Linkerd 버전 `stable-2.6.0`을 참조합니다.
>
> Linkerd `stable-2.6.x`는 Kubernetes 버전 `1.13+`에 대해 실행할 수 있습니다. [GitHub - Linkerd 릴리스][linkerd-github-releases]에서 안정적인 추가 Linkerd 버전과 Edge Linkerd 버전을 찾을 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Linkerd linkerd 클라이언트 이진 파일 다운로드 및 설치
> * AKS에 Linkerd 설치
> * Linkerd 설치 확인
> * 대시보드에 액세스
> * AKS에서 Linkerd 제거

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 자세히 설명된 단계에서는 AKS 클러스터(Kubernetes `1.13` 이상, Kubernetes RBAC 사용 설정됨)를 만들고 클러스터와 `kubectl`을 연결했다고 가정합니다. 이 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

모든 Linkerd pod는 Linux 노드에서 실행되도록 예약되어야 합니다. 이 설정은 아래에 자세히 설명된 설치 방법의 기본값이며 추가 구성은 필요하지 않습니다.

이 문서에서는 몇 단계로 Linkerd 설치 지침을 구분합니다. 결과는 공식 Linkerd 시작 [지침][linkerd-getting-started]의 구조와 동일합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>AKS에 Linkerd 설치

Linkerd를 설치하기 전에 사전 설치 검사를 실행하여 AKS 클러스터에 컨트롤 플레인을 설치할 수 있는지 여부를 확인합니다.

```console
linkerd check --pre
```

AKS 클러스터가 Linkerd에 대한 올바른 설치 대상임을 나타내려면 다음과 유사한 내용이 표시되어야 합니다.

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

이제 Linkerd 구성 요소를 설치합니다. `linkerd` 및 `kubectl` 이진을 사용하여 Linkerd 구성 요소를 AKS 클러스터에 설치합니다. `linkerd` 네임스페이스가 자동으로 생성되고 구성 요소가 이 네임스페이스에 설치됩니다.

```console
linkerd install | kubectl apply -f -
```

Linkerd는 많은 개체를 배포합니다. 위의 `linkerd install` 명령 출력에서 목록을 확인할 수 있습니다. Linkerd 구성 요소의 배포는 클러스터 환경에 따라 완료하는 데 약 1분이 소요됩니다.

이제 Linkerd를 AKS 클러스터에 배포했습니다. Linkerd를 성공적으로 배포할 수 있도록 다음 섹션인 [Linkerd 설치 확인](#validate-the-linkerd-installation)으로 넘어가겠습니다.

## <a name="validate-the-linkerd-installation"></a>Linkerd 설치 확인

리소스가 성공적으로 만들어졌는지 확인합니다. [kubectl get svc][kubectl-get] 및 [kubectl get pod][kubectl-get] 명령을 사용하여 `linkerd install` 명령으로 Linkerd 구성 요소가 설치되어 있는 `linkerd` 네임스페이스를 쿼리합니다.

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

다음 예제 출력에서는 현재 실행되고 있는 서비스 및 Pod(Linux 노드에 예약됨)를 보여 줍니다.

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd는 `linkerd` 클라이언트 이진 파일을 통해 명령을 제공하여 Linkerd 컨트롤 플레인이 성공적으로 설치 및 구성되었는지 확인합니다.

```console
linkerd check
```

설치가 완료되었음을 나타내기 위해 다음과 같은 내용이 표시됩니다.

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>대시보드에 액세스

Linkerd는 서비스 메시 및 워크로드에 대한 통찰력을 제공하는 대시보드와 함께 제공됩니다. 대시보드에 액세스하려면 `linkerd dashboard` 명령을 사용합니다. 이 명령은 [kubectl port-forward][kubectl-port-forward]를 활용하여 클라이언트 시스템과 AKS 클러스터의 관련 Pod 간에 안전한 연결을 만듭니다. 그런 다음 자동으로 기본 브라우저에서 대시보드를 엽니다.

```console
linkerd dashboard
```

또한 이 명령은 Grafana 대시보드에 대한 포트 전달을 만들고 링크를 반환합니다.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>AKS에서 Linkerd 제거

> [!WARNING]
> 실행 중인 시스템에서 Linkerd를 삭제하면 서비스 간에 트래픽 관련 문제가 발생할 수 있습니다. 계속하기 전에 Linkerd 없이도 시스템이 올바르게 작동하도록 프로비저닝했는지 확인합니다.

먼저 데이터 평면 프록시를 제거해야 합니다. 워크로드 네임스페이스에서 자동 프록시 삽입 [주석][linkerd-automatic-proxy-injection]을 제거하고 워크로드 배포를 롤아웃합니다. 워크로드에 연결된 데이터 평면 구성 요소가 더 이상 없어야 합니다.

마지막으로 다음과 같이 컨트롤 플레인을 제거합니다.

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>다음 단계

Linkerd에 대한 추가 설치 및 구성 옵션을 탐색하려면 다음 공식 Linkerd 지침을 참조하세요.

- [Linkerd - Helm 설치][linkerd-install-with-helm]
- [Linkerd - 역할 권한을 위한 다단계 설치][linkerd-multi-stage-installation]

다음을 사용하여 추가 시나리오를 따를 수도 있습니다.

- [Linkerd emojivoto 데모][linkerd-demo-emojivoto]
- [Linkerd books 데모][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/?selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
