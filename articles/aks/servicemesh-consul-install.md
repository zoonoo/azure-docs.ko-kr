---
title: AKS(Azure Kubernetes Service)에서 Consul 설치
description: Consul을 설치 및 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 서비스 메시를 만드는 방법을 알아봅니다
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 7c5ad53c0040009e9ed1f28072540b46ce7b0b9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683922"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Consul 설치 및 사용

[Consul][consul-github]은 Kubernetes 클러스터의 마이크로 서비스에서 기능 키 세트를 제공하는 오픈 소스 서비스 메시입니다. 이러한 기능에는 서비스 검색, 상태 검사, 서비스 구분 및 가시성이 포함됩니다. Consul에 대한 자세한 내용은 [Consul은 무엇인가?][consul-docs-concepts] 공식 설명서를 참조하세요.

이 문서에서는 Consul을 설치하는 방법을 보여줍니다. Consul 구성 요소는 AKS의 Kubernetes 클러스터에 설치됩니다.

> [!NOTE]
> 이 지침은 Consul 버전 `1.6.0`을 참조하고 최소한 Helm 버전 `2.14.2`을 사용합니다.
>
> Consul `1.6.x` 릴리스는 Kubernetes 버전 `1.13+`에 대해 실행 가능합니다. [GitHub - Consul 릴리스][consul-github-releases]에서 추가적인 Consul 버전을 찾을 수 있으며 각 릴리스에 대한 정보는[Consul- 릴리스 정보][consul-release-notes]에서 찾을 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * AKS에 Consul 구성 요소 설치
> * Consul 설치 유효성 검사
> * AKS에서 Consul 제거

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 자세히 설명된 단계에서는 AKS 클러스터(Kubernetes `1.13` 이상, Kubernetes RBAC 사용 설정됨)를 만들고 클러스터와 `kubectl`을 연결했다고 가정합니다. 이 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요. 클러스터의 Linux 노드 풀에 노드가 3개 이상 있는지 확인합니다.

이러한 지침을 따라서 Consul을 설치하려면 [Helm][helm]이 필요합니다. 클러스터에 제대로 설치되고 구성된 안정적인 최신 버전을 설치하는 것이 좋습니다. Helm 설치와 관련하여 도움이 필요한 경우 [AKS Helm 설치 참고 자료][helm-install]를 참조하세요. 모든 Consul Pod는 Linux 노드에서 실행되도록 예약되어야 합니다.

이 문서에서는 몇 단계로 Consul 설치 참고 자료를 구분합니다. 최종 결과는 공식 Consul 설치 [참고 자료][consul-install-k8]과 동일한 구조입니다.

### <a name="install-the-consul-components-on-aks"></a>AKS에 Consul 구성 요소 설치

먼저 Consul Helm 차트의 `v0.10.0` 버전을 다운로드합니다. 이 버전의 차트에는 Consul 버전 `1.6.0`이 포함됩니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Helm과 다운로드한 `consul-helm` 차트를 사용하여 AKS 클러스터의 `consul` 네임스페이스에 Consul 구성 요소를 설치합니다. 

> [!NOTE]
> **설치 옵션**
> 
> 설치의 일부로 다음 옵션을 사용하고 있습니다.
> - `connectInject.enabled=true` -프록시를 Pod에 삽입할 수 있습니다
> - `client.enabled=true` - Consul 클라이언트를 모든 노드에서 실행할 수 있습니다
> - `client.grpc=true` - connectInject에 대해 gRPC 수신기를 사용하도록 설정
> - `syncCatalog.enabled=true` - Kubernetes와 Consul 서비스를 동기화
>
> **노드 선택기**
>
> Consul은 현재 Linux 노드에서 실행하도록 예약되어야 합니다. 클러스터에 Windows Server 노드가 있는 경우 Consul Pod가 Linux 노드에서만 실행되도록 예약해야 합니다. [노드 선택기][kubernetes-node-selectors]를 사용하여 Pod가 올바른 노드로 예약되었는지 확인합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul` Helm 차트는 여러 개체를 배포합니다. 위의 `helm install` 명령 출력에서 목록을 볼 수 있습니다. Consul 구성 요소의 배포는 클러스터 환경에 따라 완료하는 데 약 3분이 걸릴 수 있습니다.

이 시점에서 AKS 클러스터에 Consul을 배포했습니다. Consul을 성공적으로 배포하기 위해 다음 섹션으로 이동하여 Consul 설치의 유효성을 검사해 보겠습니다.

## <a name="validate-the-consul-installation"></a>Consul 설치 유효성 검사

리소스가 성공적으로 만들어졌는지 확인합니다. [kubectl get svc][kubectl-get] 및 [kubectl get pod][kubectl-get] 명령을 사용하여 `consul` 네임스페이스, `helm install` 명령으로 Consul 구성 요소가 설치된 위치를 쿼리합니다.

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

다음 예제 출력에서는 현재 실행되고 있는 서비스 및 Pod(Linux 노드에 예약됨)를 보여줍니다.

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

모든 Pod는 `Running` 상태를 표시해야 합니다. Pod에서 상태가 표시되지 않는 경우 상태가 표시될 때까지 1~2분 정도 걸릴 수 있습니다. Pod에서 문제를 보고하는 경우 [kubectl describe pod][kubectl-describe] 명령을 사용하여 출력과 상태를 검토합니다.

## <a name="accessing-the-consul-ui"></a>Consul UI 액세스

Consul UI는 위의 설정에 설치되었으며 Consul에 UI 기반 구성을 제공합니다. Consul UI는 외부 IP 주소를 통해 공개적으로 노출하지 않습니다. Consul 사용자 인터페이스에 액세스하려면 [kubectl port-forward][kubectl-port-forward] 명령을 사용합니다. 이 명령은 클라이언트 컴퓨터와 AKS 클러스터의 관련 포드 간에 보안 연결을 만듭니다.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

이제 브라우저를 열고 `http://localhost:8080/ui`로 가리키면 Consul UI를 열 수 있습니다. UI를 열면 다음이 표시됩니다.

![Consul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>AKS에서 Consul 제거

> [!WARNING]
> 실행 중인 시스템에서 Consul을 삭제하면 서비스 간에 트래픽 관련 문제가 발생할 수 있습니다. 계속 진행하기 전에 Consul 없이 제대로 작동하도록 시스템에 프로비전을 수행했는지 확인합니다.

### <a name="remove-consul-components-and-namespace"></a>Consul 구성 요소 및 네임스페이스 제거

AKS 클러스터에서 Consul을 제거하려면 다음 명령을 사용합니다. `helm delete` 명령이 `consul` 차트를 제거하고, `kubectl delete namespace` 명령은 `consul` 네임스페이스를 제거합니다.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>다음 단계

Consul에 대한 자세한 설치 및 구성 옵션을 탐색하려면 다음 공식 Consul 문서를 참조하세요.

- [Consul - Helm 설치 가이드][consul-install-k8]
- [Consul - Helm 설치 옵션][consul-install-helm-options]

다음을 사용하여 추가 시나리오를 따를 수도 있습니다.

- [Consul 예제 애플리케이션][consul-app-example]
- [Consul Kubernetes 참조 아키텍처][consul-reference]
- [Consul 메시 게이트웨이][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
