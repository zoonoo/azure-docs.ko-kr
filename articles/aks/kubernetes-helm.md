---
title: AKS에서 Helm을 사용 하 여 기존 응용 프로그램 설치
description: Helm 패키징 도구를 사용 하 여 AKS (Azure Kubernetes Service) 클러스터에 컨테이너를 배포 하는 방법을 알아봅니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96779170"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)에서 Helm을 사용 하 여 기존 응용 프로그램 설치

[Helm][helm]은 Kubernetes 애플리케이션을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. *APT*, *Yum* 등의 Linux 패키지 관리자와 마찬가지로 Helm은 사전 구성된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리하는 데 사용합니다.

이 아티클에서는 AKS의 Kubernetes 클러스터에서 Helm을 구성하고 사용하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 개발 시스템에서 실행 되는 클라이언트 인 Helm CLI가 설치 되어 있어야 합니다. 이를 통해 Helm으로 응용 프로그램을 시작, 중지 및 관리할 수 있습니다. Azure Cloud Shell을 사용하는 경우 Helm CLI가 이미 설치되어 있습니다. 로컬 플랫폼에 대 한 설치 지침은 [Helm 설치][helm-install]를 참조 하세요.

> [!IMPORTANT]
> Helm은 Linux 노드에서 실행하기 위해 사용됩니다. 클러스터에 Windows Server 노드가 있는 경우 Helm Pod 실행이 Linux 노드에서만 이루어지도록 예약되었는지 확인해야 합니다. 또한 설치하는 Helm 차트 실행이 올바른 노드에서 이루어지도록 예약되었는지 확인해야 합니다. 이 문서의 명령은 [노트 선택기][k8s-node-selector]를 사용하여 Pod가 올바른 노드에 예약되었는지 확인하지만 일부 Helm 차트는 노드 선택기를 표시하지 않을 수 있습니다. 클러스터에서 [Taints][taints]와 같은 다른 옵션의 사용을 고려할 수 있습니다.

## <a name="verify-your-version-of-helm"></a>Helm 버전 확인

명령을 사용 하 여 `helm version` Helm 3이 설치 되어 있는지 확인 합니다.

```console
helm version
```

다음 예제에서는 3.0.0 설치 된 Helm 버전을 보여 줍니다.

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Helm v3을 사용 하 여 응용 프로그램 설치

### <a name="add-helm-repositories"></a>Helm 리포지토리 추가

[Helm][helm-repo-add] 리포지토리 명령을 사용 하 여 *nginx* 리포지토리를 추가 합니다.

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Helm 차트 찾기

Helm 차트는 Kubernetes 클러스터에 애플리케이션을 배포하는 데 사용됩니다. 미리 만든 Helm 차트를 검색 하려면 [Helm search][helm-search] 명령을 사용 합니다.

```console
helm search repo ingress-nginx
```

다음 압축된 예제 출력에서는 사용할 Helm 차트의 일부를 보여줍니다.

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

차트 목록을 업데이트하려면 [helm repo update][helm-repo-update] 명령을 사용합니다.

```console
helm repo update
```

다음 예제에서는 성공적인 리포지토리 업데이트를 보여줍니다.

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Helm 차트 실행

Helm을 사용 하 여 차트를 설치 하려면 [Helm install][helm-install-command] 명령을 사용 하 고 릴리스 이름 및 설치할 차트 이름을 지정 합니다. 동작에서 Helm 차트 설치를 보려면 Helm 차트를 사용 하 여 기본 nginx 배포를 설치 해 보겠습니다.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

다음 압축된 예제 출력에서는 Helm 차트에서 만든 Kubernetes 리소스의 배포 상태를 보여줍니다.

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

`kubectl get services` 명령을 사용하여 서비스의 *외부-IP* 를 가져옵니다.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

예를 들어 아래 명령은 *my-nginx-ingress-ingress-nginx-controller* 서비스의 *외부-IP* 를 표시합니다.

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>릴리스 목록

클러스터에 설치된 릴리스 목록을 보려면 `helm list` 명령을 사용하세요.

```console
helm list
```

다음 예에서는 이전 단계에서 배포된 *my-nginx-ingress* 릴리스를 보여 줍니다.

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>리소스 정리

Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이러한 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 이러한 리소스를 정리 하려면 [Helm uninstall][helm-cleanup] 명령을 사용 하 여 이전 명령에 나와 있는 대로 릴리스 이름을 지정 `helm list` 합니다.

```console
helm uninstall my-nginx-ingress
```

다음 예에서는 *my-nginx-ingress* 릴리스가 제거되었음을 보여 줍니다.

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>다음 단계

Helm을 사용하여 Kubernetes 애플리케이션 배포를 관리하는 방법에 대한 자세한 내용은 Helm 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [Helm 설명서][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
