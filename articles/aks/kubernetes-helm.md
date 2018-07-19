---
title: Azure Kubernetes에서 Helm을 사용하여 컨테이너 배포
description: Helm 패키징 도구를 사용하여 AKS Kubernetes 클러스터에 컨테이너 배포
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101840"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Helm 사용

[Helm][helm]은 Kubernetes 응용 프로그램을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. *APT*, *Yum* 등의 Linux 패키지 관리자와 마찬가지로 Helm은 사전 구성된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리하는 데 사용합니다.

이 문서에서는 AKS Kubernetes 클러스터에서 Helm을 구성하고 사용하는 과정을 단계별로 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 AKS 클러스터를 만들고 클러스터와 kubectl 연결을 설정했다고 가정합니다. 이러한 항목이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

## <a name="install-helm-cli"></a>Helm CLI 설치

Helm CLI는 개발 시스템에서 실행되는 클라이언트로, Helm이 있는 응용 프로그램을 시작, 중지 및 관리할 수 있습니다.

Azure Cloud Shell을 사용하는 경우 Helm CLI가 이미 설치되어 있습니다. Mac에서 Helm CLI를 설치하려면 `brew`를 사용합니다. 추가 설치 옵션은 [Helm 설치][helm-install-options]를 참조하세요.

```console
brew install kubernetes-helm
```

출력

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="create-service-account"></a>서비스 계정 만들기

RBAC 지원 클러스터에서 Helm을 구성하려면 먼저 서비스 계정과 Tiller 서비스에 대한 역할 바인딩이 필요합니다. RBAC 지원 클러스터에서 Helm/Tiller를 보호하는 방법에 대한 자세한 내용은 [Tiller, 네임스페이스 및 RBAC][tiller-rbac]를 참조하세요. 클러스터가 RBAC를 사용할 수 없는 경우 이 단계를 건너뜁니다.

파일 `helm-rbac.yaml`을 만들고 다음 YAML에 복사합니다.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
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

`kubectl create` 명령을 사용하여 서비스 계정 및 역할 바인딩을 만듭니다.

```
kubectl create -f helm-rbac.yaml
```

RBAC 지원 클러스터를 사용하는 경우 Tiller가 클러스터에 대해 가져야 하는 액세스 수준에 대한 옵션이 제공됩니다. 구성 옵션에 대한 자세한 내용은 [Helm: 역할 기반 액세스 제어][helm-rbac]를 참조하세요.

## <a name="configure-helm"></a>Helm 구성

이제 [helm init][helm-init] 명령을 사용하여 tiller를 설치합니다. 클러스터가 RBAC를 사용할 수 없는 경우 `--service-account` 인수 및 값을 제거합니다.

```
helm init --service-account tiller
```

## <a name="find-helm-charts"></a>Helm 차트 찾기

Helm 차트는 Kubernetes 클러스터에 응용 프로그램을 배포하는 데 사용됩니다. 미리 만들어진 Helm 차트를 검색하려면 [helm search][helm-search] 명령을 사용합니다.

```azurecli-interactive
helm search
```

다음과 유사한 출력이 표시되지만, 차트가 훨씬 더 많습니다.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

차트 목록을 업데이트하려면 [helm repo update][helm-repo-update] 명령을 사용합니다.

```azurecli-interactive
helm repo update
```

출력

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Helm 차트 실행

Helm 차트를 사용하여 Wordpress를 배포하려면 [helm install][helm-install] 명령을 사용합니다.

```azurecli-interactive
helm install stable/wordpress
```

다음과 유사한 출력이 표시되지만, Kubernetes 배포 사용 방법에 대한 지침 등의 추가 정보가 포함되어 있습니다.

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>Helm 릴리스 나열

클러스터에 설치된 릴리스 목록을 보려면 [helm list][helm-list] 명령을 사용합니다.

```azurecli-interactive
helm list
```

출력

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>다음 단계

Kubernetes 차트 관리 방법에 대한 자세한 내용은 Helm 설명서를 참조하세요.

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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
