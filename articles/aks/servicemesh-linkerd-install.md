---
title: Azure Kubernetes 서비스(AKS)에 링커드 설치
description: Linkerd를 설치하고 사용하여 AKS(Azure Kubernetes) 클러스터에서 서비스 메시를 만드는 방법에 대해 알아봅니다.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593735"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)에 링커드 설치

[링커드는][linkerd-github] 오픈 소스 서비스 메쉬 및 [CNCF 인큐베이팅 프로젝트입니다.][linkerd-cncf] Linkerd는 트래픽 관리, 서비스 ID 및 보안, 안정성 및 관찰 가능성을 포함하는 기능을 제공하는 초경량 서비스 메시입니다. 링커드에 대한 자세한 내용은 [링커드][linkerd-faq] 공식 FAQ 및 [링커드 아키텍처][linkerd-architecture] 문서를 참조하십시오.

이 문서에서는 Linkerd를 설치하는 방법을 보여 주며 이 문서에서는 이를 확인할 수 있습니다. Linkerd `linkerd` 클라이언트 바이너리는 클라이언트 컴퓨터에 설치되고 Linkerd 구성 요소는 AKS의 Kubernetes 클러스터에 설치됩니다.

> [!NOTE]
> 이 지침은 Linkerd `stable-2.6.0`버전을 참조합니다.
>
> 링커드는 `stable-2.6.x` Kubernetes 버전에 대해 `1.13+`실행할 수 있습니다. [GitHub - 링커드 릴리스에서][linkerd-github-releases]추가 안정 및 에지 링커드 버전을 찾을 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 링커드 링커드 클라이언트 바이너리 다운로드 및 설치
> * AKS에 링커드 설치
> * 링커드 설치 유효성 검사
> * 대시보드에 액세스
> * AKS에서 링커드 제거

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 자세히 설명하는 단계는 AKS 클러스터(RBAC를 `1.13` 사용하도록 설정한 Kubernetes 이상)를 `kubectl` 만들고 클러스터와 연결을 설정했다고 가정합니다. 이 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

모든 Linkerd 포드는 Linux 노드에서 실행되도록 예약되어야 합니다 .

이 문서에서는 Linkerd 설치 지침을 몇 가지 개별 단계로 구분합니다. 결과는 공식 Linkerd가 [지침을][linkerd-getting-started]시작하는 것과 동일한 구조입니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>AKS에 링커드 설치

Linkerd를 설치하기 전에 설치 전 검사를 실행하여 AKS 클러스터에 제어 평면을 설치할 수 있는지 확인합니다.

```console
linkerd check --pre
```

AKS 클러스터가 Linkerd의 유효한 설치 대상임을 나타내기 위해 다음과 같은 것이 표시됩니다.

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

이제 Linkerd 구성 요소를 설치할 차례입니다. `linkerd` 및 `kubectl` 바이너리를 사용하여 Linkerd 구성 요소를 AKS 클러스터에 설치합니다. 네임스페이스가 `linkerd` 자동으로 만들어지고 구성 요소가 이 네임스페이스에 설치됩니다.

```console
linkerd install | kubectl apply -f -
```

Linkerd는 여러 개체를 배포합니다. 위의 `linkerd install` 명령 출력에서 목록이 표시됩니다. Linkerd 구성 요소를 배포하는 데 클러스터 환경에 따라 완료하는 데 약 1분이 소요됩니다.

이 시점에서 AKS 클러스터에 Linkerd를 배포했습니다. Linkerd를 성공적으로 배포할 수 있도록 다음 섹션으로 이동하여 [Linkerd 설치 유효성 검사를](#validate-the-linkerd-installation)살펴보겠습니다.

## <a name="validate-the-linkerd-installation"></a>링커드 설치 유효성 검사

리소스가 성공적으로 만들어졌는지 확인합니다. [kubectl get svc를][kubectl-get] 사용하고 [kubectl 은 명령에서][kubectl-get] Linkerd 구성 요소가 설치된 `linkerd` `linkerd install` 네임스페이스를 쿼리하기 위해 포드 명령을 가져옵니다.

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

다음 예제 출력은 현재 실행 중인 서비스 및 포드(Linux 노드에서 예약)를 보여 주며 다음과 같은 을 보여 주며 다음과 같은 을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은

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

Linkerd는 `linkerd` 클라이언트 바이너리를 통해 Linkerd 제어 평면이 성공적으로 설치되고 구성됐는지 확인하는 명령을 제공합니다.

```console
linkerd check
```

설치가 성공했음을 나타내기 위해 다음과 같은 것이 표시됩니다.

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

Linkerd에는 서비스 메시 및 워크로드에 대한 통찰력을 제공하는 대시보드가 함께 제공됩니다. 대시보드에 액세스하려면 `linkerd dashboard` 명령을 사용합니다. 이 명령은 [kubectl 포트포워드를][kubectl-port-forward] 활용하여 클라이언트 컴퓨터와 AKS 클러스터의 관련 포드 간에 보안 연결을 만듭니다. 그러면 기본 브라우저에서 대시보드가 자동으로 열립니다.

```console
linkerd dashboard
```

또한 명령은 포트 포워드를 만들고 Grafana 대시보드에 대한 링크를 반환합니다.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>AKS에서 링커드 제거

> [!WARNING]
> 실행 중인 시스템에서 Linkerd를 삭제하면 서비스 간에 트래픽 관련 문제가 발생할 수 있습니다. 계속하기 전에 Linkerd 없이 시스템이 올바르게 작동하도록 규정을 만들어야 합니다.

먼저 데이터 평면 프록시를 제거해야 합니다. 워크로드 네임스페이스에서 자동 프록시 삽입 [주석을][linkerd-automatic-proxy-injection] 제거하고 워크로드 배포를 롤아웃합니다. 워크로드에 더 이상 연결된 데이터 평면 구성 요소가 없어야 합니다.

마지막으로 다음과 같이 제어 평면을 제거합니다.

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>다음 단계

Linkerd에 대한 더 많은 설치 및 구성 옵션을 살펴보려면 다음 공식 Linkerd 지침을 참조하십시오.

- [링커드 - 투구 설치][linkerd-install-with-helm]
- [링커드 - 역할 권한을 충족시키는 다단계 설치][linkerd-multi-stage-installation]

다음을 사용하여 추가 시나리오를 따를 수도 있습니다.

- [링커드 이모티콘 데모][linkerd-demo-emojivoto]
- [링커드 북 데모][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
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
