---
title: AKS (Azure Kubernetes Service)에 Hashicorp Consul 설치
description: Consul을 설치 하 고 사용 하 여 AKS (Azure Kubernetes Service) 클러스터에서 서비스 메시를 만드는 방법에 대해 알아봅니다.
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189225"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에서 Consul Connect 설치 및 사용

[Consul][consul-github] 은 Kubernetes 클러스터의 마이크로 서비스에서 주요 기능 집합을 제공 하는 오픈 소스 서비스 메시입니다. 이러한 기능에는 서비스 검색, 상태 검사, 서비스 구분 및 관찰성 포함 됩니다. Consul에 대 한 자세한 내용은 공식 이란 [?][consul-docs-concepts] 설명서를 참조 하세요.

이 문서에서는 Consul을 설치 하는 방법을 보여 줍니다. Consul 구성 요소는 AKS의 Kubernetes 클러스터에 설치 됩니다.

> [!NOTE]
> 이 지침은 Consul 버전 `1.5`을 참조 합니다.
>
> Hashicorp 팀에서 Kubernetes `1.5.x` 버전 `1.12`, `1.14`, `1.14`에 대해 consul 릴리스를 테스트 했습니다. [GitHub][consul-github-releases] 에서 추가 기능을 사용할 수 [있습니다. 릴리스][consul-release-notes]정보에서 각 릴리스에 대 한 정보를 찾을 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * AKS에 Consul 구성 요소 설치
> * Consul 설치 유효성 검사
> * AKS에서 Consul 제거

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에 설명 된 단계에서는 AKS 클러스터 (RBAC를 사용 하 여 Kubernetes `1.11` 이상)를 만들고 클러스터와의 `kubectl` 연결을 설정 했다고 가정 합니다. 이러한 항목에 대 한 도움이 필요한 경우 [AKS 빠른][aks-quickstart]시작을 참조 하세요.

이러한 지침을 따르고 Consul을 설치 하는 [투구][helm] 가 필요 합니다. 클러스터에 버전 `2.12.2` 이상을 올바르게 설치 하 고 구성 하는 것이 좋습니다. 투구 설치와 관련 하 여 도움이 필요한 경우 [AKS 투구 설치 지침][helm-install]을 참조 하세요. 모든 Consul pod는 Linux 노드에서 실행 되도록 예약 되어야 합니다.

이 문서에서는 Consul 설치 지침을 여러 불연속 단계로 구분 합니다. 최종 결과는 공식 Consul 설치 [지침][consul-install-k8]과 동일한 구조에서 동일 합니다.

### <a name="install-the-consul-components-on-aks"></a>AKS에 Consul 구성 요소 설치

먼저 Kubernetes GitHub 리포지토리의 공식 HashiCorp Consul을 복제 합니다.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

그런 다음 Consul 설치를 위한 사용자 지정 투구 값 파일을 만들어야 합니다. Consul을 설치 하기 전에 다음 사용자 지정 값 파일을 만듭니다.

```bash
vim consul-values.yaml
```

그런 다음, 다음 값을 복사 하 여 consul. yaml 파일에 붙여 넣습니다.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

이제 사용자 지정 값 파일이 있으므로 AKS 클러스터에 Consul을 설치할 수 있습니다.

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
투구 `Consul` 차트는 많은 개체를 배포 합니다. 위의 `helm install` 명령 출력에서 목록을 볼 수 있습니다. 클러스터 환경에 따라 Consul 구성 요소의 배포를 완료 하는 데 4 ~ 5 분 정도 걸릴 수 있습니다.

> [!NOTE]
> 모든 Consul pod는 Linux 노드에서 실행 되도록 예약 되어야 합니다. 클러스터에 Linux 노드 풀 외에도 Windows Server 노드 풀이 있으면 모든 Consul pod가 Linux 노드에서 실행 되도록 예약 되었는지 확인 합니다.

이 시점에서 AKS 클러스터에 Consul을 배포 했습니다. Consul이 성공적으로 배포 되었는지 확인 하려면 다음 섹션으로 이동 하 여 Consul 설치의 유효성을 검사 해 보겠습니다.

## <a name="validate-the-consul-installation"></a>Consul 설치 유효성 검사

먼저 예상 서비스가 만들어졌는지 확인합니다. [Kubectl get svc][kubectl-get] 명령을 사용 하 여 실행 중인 서비스를 볼 수 있습니다. 투구 차트에서 consul 구성 요소가 설치 된 네임스페이스를쿼리합니다.`consul` `consul`

```console
kubectl get svc --namespace consul
```

다음 예제 출력에서는 실행되어야 하는 서비스를 보여줍니다.

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

다음으로 필요한 Pod가 만들어졌는지 확인합니다. [Kubectl get pod][kubectl-get] 명령을 사용 하 여 네임 스페이스를 `consul` 다시 쿼리 합니다.

```console
kubectl get pods --namespace consul
```

다음 예제 출력에서는 실행 중인 Pod을 보여줍니다.

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 모든 pod의 상태 `Running`를 표시 해야 합니다. Pod에서 상태가 표시되지 않는 경우 상태가 표시될 때까지 1~2분 정도 걸릴 수 있습니다. Pod에서 문제를 보고 하는 경우 [kubectl 설명 pod][kubectl-describe] 명령을 사용 하 여 출력 및 상태를 검토 합니다.

## <a name="accessing-the-consul-ui"></a>Consul UI 액세스

Consul UI는 Consul에 대 한 UI 기반 구성을 제공 하는 위의 설정에서 설치 되었습니다. Consul의 UI는 외부 ip 주소를 통해 공개적으로 노출 되지 않습니다. 추가 기능 사용자 인터페이스에 액세스 하려면 [kubectl][kubectl-port-forward] 명령을 사용 합니다. 이 명령은 클라이언트 컴퓨터와 AKS 클러스터의 관련 pod 간에 보안 연결을 만듭니다.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
이제 브라우저를 열고를 `http://localhost:8080/ui` 가리켜 consul UI를 열 수 있습니다. UI를 열 때 다음이 표시 됩니다.

![Consul UI](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>AKS에서 Consul 제거

> [!WARNING]
> 실행 중인 시스템에서 Consul을 삭제 하면 서비스 간에 트래픽 관련 문제가 발생할 수 있습니다. 계속 하기 전에 시스템에 대 한 프로 비전을 제대로 작동 하는지 확인 합니다.

### <a name="remove-consul-components-and-namespace"></a>Consul 구성 요소 및 네임 스페이스 제거

AKS 클러스터에서 Consul을 제거 하려면 다음 명령을 사용 합니다. 명령이 차트를 `kubectl delete ns` `consul` 제거 하 고이 명령은 네임 스페이스를 제거 합니다. `consul` `helm delete`

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>다음 단계

Consul에 대 한 추가 설치 및 구성 옵션을 살펴보려면 다음 공식 기술 문서를 참조 하세요.

- [Consul-투구 설치 가이드][consul-install-k8]
- [Consul-투구 설치 옵션][consul-install-helm-options]

[Consul 예제 응용 프로그램][consul-app-example]을 사용 하 여 추가 시나리오를 따를 수도 있습니다.

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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
