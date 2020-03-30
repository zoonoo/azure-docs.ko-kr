---
title: Azure Kubernetes 서비스(AKS)에 대한 코어DNS 사용자 지정
description: Azure Kubernetes 서비스(AKS)를 사용하여 CoreDNS를 사용자 지정하여 하위 도메인을 추가하거나 사용자 지정 DNS 끝점을 확장하는 방법을 알아봅니다.
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595827"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Azure Kubernetes Service를 사용하여 CoreDNS 사용자 지정

Azure Kubernetes 서비스(AKS)는 *모든 1.12.x* 이상 클러스터에서 클러스터 DNS 관리 및 해결을 위해 [CoreDNS][coredns] 프로젝트를 사용합니다. 이전에는 kube-dns 프로젝트가 사용되었습니다. 이 kube-dns 프로젝트는 이제 더 이상 사용되지 않습니다. CoreDNS 사용자 지정 및 Kubernetes에 대한 자세한 내용은 [공식 업스트림 설명서를][corednsk8s]참조하십시오.

AKS는 관리되는 서비스이기 때문에 CoreDNS(코어파일)의 *CoreFile*기본 구성을 수정할 수 없습니다. 대신 Kubernetes *ConfigMap을* 사용하여 기본 설정을 재정의합니다. 기본 AKS CoreDNS 구성지도를 보려면 `kubectl get configmaps --namespace=kube-system coredns -o yaml` 명령을 사용합니다.

이 문서에서는 AKS에서 CoreDNS의 기본 사용자 지정 옵션에 ConfigMaps를 사용하는 방법을 보여 주며 있습니다. 이 방법은 CoreFile 사용과 같은 다른 컨텍스트에서 CoreDNS를 구성하는 것과 다릅니다. 구성 값이 버전 간에 변경될 수 있으므로 실행 중인 CoreDNS 버전을 확인합니다.

> [!NOTE]
> `kube-dns`Kubernetes 구성 맵을 통해 다양한 [사용자 정의 옵션을][kubednsblog] 제공했습니다. CoreDNS는 kube-dns와 이전 버전과 **호환되지 않습니다.** 이전에 사용한 모든 사용자 지정은 CoreDNS와 함께 사용하도록 업데이트되어야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

## <a name="what-is-supportedunsupported"></a>지원되다/지원되지 않는 것

모든 기본 제공 CoreDNS 플러그인이 지원됩니다. 추가 기능/타사 플러그인이 지원되지 않습니다.

## <a name="rewrite-dns"></a>DNS 다시 쓰기

한 가지 시나리오는 즉석에서 DNS 이름 다시 쓰기를 수행하는 것입니다. 다음 예제에서는 정규화된 고유한 도메인 이름으로 바꿉니다. `<domain to be written>` 다음 예제 `corednsms.yaml` 구성이라는 파일을 만들고 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

[kubectl 적용 configmap][kubectl-apply] 명령을 사용 하 여 ConfigMap을 만들고 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f corednsms.yaml
```

사용자 지정이 적용되었는지 확인하려면 [kubectl get configmaps를][kubectl-get] 사용하고 *coredns-custom* ConfigMap을 지정합니다.

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

이제 CoreDNS가 구성맵을 다시 로드하도록 합니다. [kubectl 삭제 포드][kubectl delete] 명령은 파괴적이지 않으며 가동 중지 시간을 일으키지 않습니다. 포드가 `kube-dns` 삭제되고 Kubernetes 스케줄러가 다시 만듭니다. 이러한 새 포드에는 TTL 값의 변경 내용이 포함되어 있습니다.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> 위의 명령이 정확합니다. 변경하는 `coredns`동안 배포는 **kube-dns** 이름 아래에 있습니다.

## <a name="custom-forward-server"></a>사용자 지정 전달 서버

네트워크 트래픽에 대한 전달 서버를 지정해야 하는 경우 ConfigMap을 만들어 DNS를 사용자 지정할 수 있습니다. 다음 예제에서는 사용자 `forward` 고유의 환경에 대한 값으로 이름과 주소를 업데이트합니다. 다음 예제 `corednsms.yaml` 구성이라는 파일을 만들고 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

이전 예제와 마찬가지로 [kubectl 적용 configmap][kubectl-apply] 명령을 사용 하 여 ConfigMap을 만들고 YAML 매니페스트의 이름을 지정 합니다. 그런 다음 CoreDNS가 Kubernetes 스케줄러에 대한 [kubectl delete 포드를][kubectl delete] 사용하여 ConfigMap을 다시 로드하여 다시 만듭니다.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>사용자 지정 도메인 사용

내부적으로만 해결할 수 있는 사용자 지정 도메인을 구성할 수 있습니다. 예를 들어 유효한 최상위 도메인이 아닌 사용자 지정 도메인 *puglife.local을*해결할 수 있습니다. 사용자 지정 도메인 ConfigMap이 없으면 AKS 클러스터가 주소를 확인할 수 없습니다.

다음 예제에서는 사용자 지정 도메인 및 IP 주소를 업데이트하여 사용자 환경의 값으로 트래픽을 직접 처리합니다. 다음 예제 `corednsms.yaml` 구성이라는 파일을 만들고 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

이전 예제와 마찬가지로 [kubectl 적용 configmap][kubectl-apply] 명령을 사용 하 여 ConfigMap을 만들고 YAML 매니페스트의 이름을 지정 합니다. 그런 다음 CoreDNS가 Kubernetes 스케줄러에 대한 [kubectl delete 포드를][kubectl delete] 사용하여 ConfigMap을 다시 로드하여 다시 만듭니다.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>스텁 도메인

CoreDNS를 사용하여 스텁 도메인을 구성할 수도 있습니다. 다음 예제에서는 사용자 지정 도메인 및 IP 주소를 사용자 고유의 환경에 대한 값으로 업데이트합니다. 다음 예제 `corednsms.yaml` 구성이라는 파일을 만들고 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

이전 예제와 마찬가지로 [kubectl 적용 configmap][kubectl-apply] 명령을 사용 하 여 ConfigMap을 만들고 YAML 매니페스트의 이름을 지정 합니다. 그런 다음 CoreDNS가 Kubernetes 스케줄러에 대한 [kubectl delete 포드를][kubectl delete] 사용하여 ConfigMap을 다시 로드하여 다시 만듭니다.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>호스트 플러그인

모든 기본 제공 플러그인이 지원되므로 CoreDNS [호스트][coredns hosts] 플러그인도 사용자 정의 할 수 있습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>DNS 쿼리 디버깅에 대한 로깅 사용 

DNS 쿼리 로깅을 사용하려면 coredns 사용자 지정 ConfigMap에 다음 구성을 적용합니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 CoreDNS 사용자 지정에 대한 몇 가지 예제 시나리오를 보여 주어 있습니다. CoreDNS 프로젝트에 대한 자세한 내용은 [CoreDNS 업스트림 프로젝트 페이지를][coredns]참조하십시오.

핵심 네트워크 개념에 대한 자세한 내용은 [AKS의 응용 프로그램에 대한 네트워크 개념을][concepts-network]참조하십시오.

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
