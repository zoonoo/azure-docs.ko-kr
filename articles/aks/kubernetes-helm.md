---
title: Azure Kubernetes에서 Helm을 사용하여 컨테이너 배포
description: Helm 패키징 도구를 사용하여 AKS(Azure Kubernetes Service) 클러스터에 컨테이너 배포
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/01/2018
ms.author: iainfou
ms.openlocfilehash: 2c74e3ffaa5ced0925b5ad0edfc357afb375803e
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363966"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Helm을 사용하여 응용 프로그램 설치

[Helm][helm]은 Kubernetes 응용 프로그램을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. *APT*, *Yum* 등의 Linux 패키지 관리자와 마찬가지로 Helm은 사전 구성된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리하는 데 사용합니다.

이 아티클에서는 AKS의 Kubernetes 클러스터에서 Helm을 구성하고 사용하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에 설명된 단계에서는 AKS 클러스터를 만들고 클러스터와 `kubectl` 연결을 설정했다고 가정합니다. 이러한 항목이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

Helm CLI도 설치되어 있어야 합니다. 이것은 개발 시스템에서 실행되는 클라이언트로, Helm으로 응용 프로그램을 시작, 중지 및 관리할 수 있습니다. Azure Cloud Shell을 사용하는 경우 Helm CLI가 이미 설치되어 있습니다. 로컬 플랫폼에 대한 설치 지침은 [Helm 설치][helm-install]를 참조하세요.

## <a name="create-a-service-account"></a>서비스 계정 만들기

RBAC 지원 AKS 클러스터에서 Helm을 배포하려면 먼저 서비스 계정과 Tiller 서비스에 대한 역할 바인딩이 필요합니다. RBAC 지원 클러스터에서 Helm/Tiller를 보호하는 방법에 대한 자세한 내용은 [Tiller, 네임스페이스 및 RBAC][tiller-rbac]를 참조하세요. AKS 클러스터가 RBAC를 사용할 수 없는 경우 이 단계를 건너뜁니다.

`helm-rbac.yaml`이라는 파일을 만들고 다음 YAML에 복사합니다.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

`kubectl apply` 명령을 사용하여 서비스 계정 및 역할 바인딩을 만듭니다.

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Tiller 및 Helm 보호

Helm 클라이언트 및 Tiller 서비스는 TLS/SSL을 사용하여 인증하고 서로 통신합니다. 이 인증 방법을 통해 Kubernetes 클러스터 및 배포할 수 있는 서비스를 보호할 수 있습니다. 보안을 향상시키 위해 자체 서명된 인증서를 생성할 수 있습니다. 각 Helm 사용자는 고유한 클라이언트 인증서를 수신하고, Tiller는 인증서를 적용하여 Kubernetes 클러스터에서 초기화될 수 있습니다. 자세한 내용은 [Helm과 Tiller 간에 TLS/SSL 사용][helm-ssl]을 참조하세요.

RBAC 지원 Kubernetes 클러스터를 사용하여 Tiller가 클러스터에 대해 가진 액세스 수준을 제어할 수 있습니다. Tiller를 배포한 Kubernetes 네임스페이스를 정의한 다음, 리소스를 배포할 수 있는 Tiller 네임스페이스를 제한할 수 있습니다. 이 방법을 통해 다른 네임스페이스에 Tiller 인스턴스를 만들고, 배포 경계를 제한하고, Helm 클라이언트의 사용자 범위를 특정 네임스페이스로 지정할 수 있습니다. 자세한 내용은 [Helm 역할 기반 액세스 제어][helm-rbac]를 참조하세요.

## <a name="configure-helm"></a>Helm 구성

기본 Tiller를 AKS 클러스터에 배포하려면 [helm init][helm-init] 명령을 사용합니다. 클러스터가 RBAC를 사용할 수 없는 경우 `--service-account` 인수 및 값을 제거합니다. Tiller 및 Helm에 대한 TLS/SSL를 구성한 경우 이 기본 초기화 단계를 건너뛰고 대신 다음 예제와 같이 필수 `--tiller-tls-`를 제공합니다.

```console
helm init --service-account tiller
```

Helm과 Tiller 간에 TLS/SSL을 구성한 경우 다음 예제와 같이 `--tiller-tls-*` 매개 변수 및 고유한 인증서 이름을 제공합니다.

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller
```

## <a name="find-helm-charts"></a>Helm 차트 찾기

Helm 차트는 Kubernetes 클러스터에 응용 프로그램을 배포하는 데 사용됩니다. 미리 만들어진 Helm 차트를 검색하려면 [helm search][helm-search] 명령을 사용합니다.

```console
helm search
```

다음 압축된 예제 출력에서는 사용할 Helm 차트의 일부를 보여줍니다.

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/acs-engine-autoscaler   2.2.0            2.1.1        Scales worker nodes within agent pools
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

차트 목록을 업데이트하려면 [helm repo update][helm-repo-update] 명령을 사용합니다. 다음 예제에서는 성공적인 리포지토리 업데이트를 보여줍니다.

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Helm 차트 실행

Helm을 사용하여 차트를 설치하려면 [helm install][helm-install] 명령을 사용하고 설치할 차트의 이름을 지정합니다. 실행 중인지 확인하려면 Helm 차트를 사용하여 기본 Wordpress 배포를 설치하겠습니다. TLS/SSL을 구성한 경우 `--tls` 매개 변수를 추가하여 Helm 클라이언트 인증서를 사용합니다.

```console
helm install stable/wordpress
```

다음 압축된 예제 출력에서는 Helm 차트에서 만든 Kubernetes 리소스의 배포 상태를 보여줍니다.

```
$ helm install stable/wordpress

NAME:   wishful-mastiff
LAST DEPLOYED: Thu Jul 12 15:53:56 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                       DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
wishful-mastiff-wordpress  1        1        1           0          1s

==> v1beta1/StatefulSet
NAME                     DESIRED  CURRENT  AGE
wishful-mastiff-mariadb  1        1        1s

==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
wishful-mastiff-wordpress-6f96f8fdf9-q84sz  0/1    Pending  0         1s
wishful-mastiff-mariadb-0                   0/1    Pending  0         1s

==> v1/Secret
NAME                       TYPE    DATA  AGE
wishful-mastiff-mariadb    Opaque  2     2s
wishful-mastiff-wordpress  Opaque  2     2s

==> v1/ConfigMap
NAME                           DATA  AGE
wishful-mastiff-mariadb        1     2s
wishful-mastiff-mariadb-tests  1     2s

==> v1/PersistentVolumeClaim
NAME                       STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
wishful-mastiff-wordpress  Pending  default  2s

==> v1/Service
NAME                       TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
wishful-mastiff-mariadb    ClusterIP     10.1.116.54  <none>       3306/TCP                    2s
wishful-mastiff-wordpress  LoadBalancer  10.1.217.64  <pending>    80:31751/TCP,443:31264/TCP  2s
...
```

Wordpress 서비스의 *EXTERNAL-IP* 주소를 채우고 웹 브라우저를 사용하여 액세스할 수 있도록 하는 데 1~2분이 걸립니다.

## <a name="list-helm-releases"></a>Helm 릴리스 나열

클러스터에 설치된 릴리스 목록을 보려면 [helm list][helm-list] 명령을 사용합니다. 다음 예제에서는 이전 단계에서 배포된 Wordpress 릴리스를 보여줍니다. TLS/SSL을 구성한 경우 `--tls` 매개 변수를 추가하여 Helm 클라이언트 인증서를 사용합니다.

```console
$ helm list

NAME             REVISION    UPDATED                     STATUS      CHART              NAMESPACE
wishful-mastiff  1           Thu Jul 12 15:53:56 2018    DEPLOYED    wordpress-2.1.3  default
```

## <a name="clean-up-resources"></a>리소스 정리

Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 이러한 리소스를 정리하려면 `helm delete` 명령을 사용하고 이전 `helm list` 명령에서 찾은 릴리스 이름을 지정합니다. 다음 예제는 *wishful mastiff*라는 이름의 릴리스를 삭제합니다.

```console
$ helm delete wishful-mastiff

release "wishful-mastiff" deleted
```

## <a name="next-steps"></a>다음 단계

Helm을 사용하여 Kubernetes 응용 프로그램 배포를 관리하는 방법에 대한 자세한 내용은 Helm 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [Helm 설명서][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
