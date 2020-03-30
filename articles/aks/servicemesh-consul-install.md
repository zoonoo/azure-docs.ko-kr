---
title: Azure Kubernetes 서비스(AKS)에 영사 설치
description: 영사를 설치하고 사용하여 AKS(Azure Kubernetes) 클러스터에서 서비스 메시를 만드는 방법에 대해 알아봅니다.
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273729"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>설치 및 Azure Kubernetes 서비스 (AKS)에서 영사 사용

[영사는][consul-github] Kubernetes 클러스터의 마이크로 서비스 전반에 걸쳐 주요 기능 집합을 제공하는 오픈 소스 서비스 메시입니다. 이러한 기능에는 서비스 검색, 상태 확인, 서비스 세분화 및 관찰 가능성이 포함됩니다. 영사에 대한 자세한 내용은 공식 [영사 무엇입니까?][consul-docs-concepts]

이 문서에서는 영사를 설치하는 방법을 보여줍니다. 영사 구성 요소는 AKS의 Kubernetes 클러스터에 설치됩니다.

> [!NOTE]
> 이 지침은 영사 `1.6.0`버전을 참조하고 적어도 `2.14.2`Helm 버전을 사용합니다.
>
> 영사 `1.6.x` 릴리스는 Kubernetes 버전에 `1.13+`대해 실행할 수 있습니다. 당신은 [GitHub에서][consul-github-releases] 추가 영사 버전을 찾을 수 있습니다 - 영사 릴리스 및 [영사 릴리스 노트에서][consul-release-notes]각 릴리스에 대한 정보 .

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * AKS에 영사 구성 요소 설치
> * 영사 설치 검증
> * AKS에서 영사 제거

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 자세히 설명하는 단계는 AKS 클러스터(RBAC를 `1.13` 사용하도록 설정한 Kubernetes 이상)를 `kubectl` 만들고 클러스터와 연결을 설정했다고 가정합니다. 이 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요. 클러스터에 Linux 노드 풀에 3개 이상의 노드가 있는지 확인합니다.

이 지침을 따르고 영사를 설치하려면 [Helm이][helm] 필요합니다. 클러스터에 최신 안정 버전을 올바르게 설치하고 구성하는 것이 좋습니다. 헬름 설치에 도움이 필요한 경우 [AKS Helm 설치 지침을][helm-install]참조하십시오. 모든 영사 포드는 또한 리눅스 노드에서 실행되도록 예약해야합니다.

이 문서에서는 영사 설치 지침을 몇 가지 개별 단계로 구분합니다. 최종 결과는 공식 영사 설치 [지침과][consul-install-k8]구조가 동일합니다.

### <a name="install-the-consul-components-on-aks"></a>AKS에 영사 구성 요소 설치

먼저 영사 투구 차트 `v0.10.0` 버전을 다운로드합니다. 차트의이 버전은 영사 `1.6.0`버전을 포함한다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Helm 및 다운로드한 `consul-helm` 차트를 사용하여 AKS `consul` 클러스터의 네임스페이스에 영사 구성 요소를 설치합니다. 

> [!NOTE]
> **설치 옵션**
> 
> 설치의 일부로 다음 옵션을 사용하고 있습니다.
> - `connectInject.enabled=true`- 프록시가 포드에 주입 할 수 있도록
> - `client.enabled=true`- 영사 클라이언트가 모든 노드에서 실행할 수 있도록
> - `client.grpc=true`- 연결 주입을위한 gRPC 청취자를 활성화
> - `syncCatalog.enabled=true`- 동기화 Kubernetes 및 영사 서비스
>
> **노드 선택기**
>
> 영사는 현재 리눅스 노드에서 실행되도록 예약해야합니다. 클러스터에 Windows Server 노드가 있는 경우 영사 포드가 Linux 노드에서만 실행되도록 예약되어 있는지 확인해야 합니다. [노드 선택기를][kubernetes-node-selectors] 사용하여 포드가 올바른 노드로 예약되었는지 확인합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

Helm `Consul` 차트는 여러 개체를 배포합니다. 위의 명령 출력에서 목록을 볼 `helm install` 수 있습니다. Consacon 구성 요소의 배포는 클러스터 환경에 따라 완료하는 데 약 3분정도 걸릴 수 있습니다.

이 시점에서 AKS 클러스터에 영사를 배포했습니다. 우리가 영사의 성공적인 배포를 보장하기 위해, 영사 설치의 유효성을 검사하기 위해 다음 섹션으로 이동하자.

## <a name="validate-the-consul-installation"></a>영사 설치 검증

리소스가 성공적으로 만들어졌는지 확인합니다. [kubectl 을][kubectl-get] 사용하여 svc 를 얻고 [kubectl은 명령에서][kubectl-get] 영사 구성 요소가 `consul` 설치된 `helm install` 네임 스페이스를 쿼리하기 위해 포드 명령을 가져옵니다.

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

다음 예제 출력은 현재 실행 중인 서비스 및 포드(Linux 노드에서 예약)를 보여 주며 다음과 같은 을 보여 주며 다음과 같은 을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은

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

모든 포드에는 `Running`의 상태가 표시되어야 합니다. Pod에서 상태가 표시되지 않는 경우 상태가 표시될 때까지 1~2분 정도 걸릴 수 있습니다. Pod에서 문제를 보고하는 경우 [kubectl describe pod][kubectl-describe] 명령을 사용하여 출력과 상태를 검토합니다.

## <a name="accessing-the-consul-ui"></a>영사 UI 액세스

영사 UI는 위의 우리의 설정에 설치되었으며 영사에 대한 UI 기반 구성을 제공합니다. 영사의 UI는 외부 IP 주소를 통해 공개적으로 노출되지 않습니다. 영사 사용자 인터페이스에 액세스하려면 [kubectl 포트 포워드][kubectl-port-forward] 명령을 사용합니다. 이 명령은 클라이언트 컴퓨터와 AKS 클러스터의 관련 포드 간에 보안 연결을 만듭니다.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

이제 브라우저를 열고 영사 `http://localhost:8080/ui` UI를 열도록 가리킬 수 있습니다. UI를 열 때 다음이 표시됩니다.

![영사 UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>AKS에서 영사 제거

> [!WARNING]
> 실행 중인 시스템에서 영사를 삭제하면 서비스 간에 교통 관련 문제가 발생할 수 있습니다. 진행하기 전에 영사관 없이 시스템이 제대로 작동하도록 규정을 마련해야 합니다.

### <a name="remove-consul-components-and-namespace"></a>영사 구성 요소 및 네임스페이스 제거

AKS 클러스터에서 영사를 제거하려면 다음 명령을 사용합니다. `helm delete` 명령은 차트를 `consul` 제거하고 `kubectl delete namespace` 명령은 네임스페이스를 `consul` 제거합니다.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>다음 단계

영사에 대한 더 많은 설치 및 구성 옵션을 탐색하려면 다음 공식 영사 문서를 참조하십시오.

- [영사 - 투구 설치 가이드][consul-install-k8]
- [영사 - 투구 설치 옵션][consul-install-helm-options]

다음을 사용하여 추가 시나리오를 따를 수도 있습니다.

- [영사 예제 응용 프로그램][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
