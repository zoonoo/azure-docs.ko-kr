---
title: Kubernetes 및 헬름과 함께 컴퓨터 비전 컨테이너 사용
titleSuffix: Azure Cognitive Services
description: 컴퓨터 비전 컨테이너를 Azure 컨테이너 인스턴스에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9aac374de5af748eafbe4c22e5fc89f64e483c2a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877979"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Kubernetes 및 헬름과 함께 컴퓨터 비전 컨테이너 사용

온-프레미스에서 컴퓨터 비전 컨테이너를 관리하는 한 가지 옵션은 Kubernetes 및 Helm을 사용하는 것입니다. Kubernetes 및 Helm을 사용하여 컴퓨터 비전 컨테이너 이미지를 정의하고 Kubernetes 패키지를 만듭니다. 이 패키지는 Kubernetes 클러스터 온-프레미스에 배포됩니다. 마지막으로 배포된 서비스를 테스트하는 방법을 살펴보겠습니다. Kubernetes 오케스트레이션 없이 Docker 컨테이너를 실행하는 것에 대한 자세한 내용은 [컴퓨터 비전 컨테이너 설치 및 실행을](computer-vision-how-to-install-containers.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

온-프레미스에서 컴퓨터 비전 컨테이너를 사용하기 전에 다음 필수 구성 조건은 다음과 같은 것입니다.

| 필수 | 목적 |
|----------|---------|
| Azure 계정 | Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][free-azure-account]을 만듭니다. |
| 쿠베르네테스 클리 | [Kubernetes CLI는][kubernetes-cli] 컨테이너 레지스트리에서 공유 자격 증명을 관리하는 데 필요합니다. Kubernetes 패키지 매니저인 헬름 전에도 쿠베네테스가 필요합니다. |
| Helm CLI | 투구 차트(컨테이너 패키지 정의)를 설치하는 데 사용되는 [Helm CLI를][helm-install]설치합니다. |
| 컴퓨터 비전 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>Azure **컴퓨터 비전** 리소스 및 관련 API는 끝점 URI를 키합니다. 두 값 모두 리소스의 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작해야 합니다.<br><br>**{API_KEY}**: **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}**: **개요** 페이지에 제공된 끝점|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>쿠베르네츠 클러스터에 연결

호스트 컴퓨터에 사용 가능한 Kubernetes 클러스터가 있어야 합니다. 호스트 컴퓨터에 [Kubernetes 클러스터를 배포하는](../../aks/tutorial-kubernetes-deploy-cluster.md) 방법에 대한 개념적 이해는 Kubernetes 클러스터 배포에 대한 이 자습서를 참조하십시오.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Kubernetes 클러스터와 Docker 자격 증명 공유

Kubernetes 클러스터를 `docker pull` `containerpreview.azurecr.io` 컨테이너 레지스트리에서 구성된 이미지로 허용하려면 docker 자격 증명을 클러스터로 전송해야 합니다. 컨테이너 [`kubectl create`][kubectl-create] 레지스트리 액세스 필수 구성 사이트에서 제공 된 자격 증명에 따라 *도커 레지스트리 비밀을* 만들려면 아래 명령을 실행 합니다.

선택한 명령줄 인터페이스에서 다음 명령을 실행합니다. `<username>`을 `<password>`및 `<email-address>` 컨테이너 레지스트리 자격 증명으로 바꿔야 합니다.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 이미 컨테이너 레지스트리에 `containerpreview.azurecr.io` 액세스할 수 있는 경우 대신 일반 플래그를 사용하여 Kubernetes 비밀을 만들 수 있습니다. Docker 구성 JSON에 대해 실행되는 다음 명령을 고려하십시오.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

다음 출력은 비밀이 성공적으로 생성되면 콘솔에 인쇄됩니다.

```console
secret "containerpreview" created
```

보안 이 만들어졌는지 확인하려면 [`kubectl get`][kubectl-get] `secrets` 플래그를 사용하여 을 실행합니다.

```console
kubectl get secrets
```

인쇄를 `kubectl get secrets` 실행하는 모든 구성된 비밀.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>배포를 위한 헬름 차트 값 구성

*읽기라는*폴더를 만든 다음 다음 YAML 콘텐츠를 *Chart.yml이라는*새 파일에 붙여넣습니다.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Helm 차트 기본값을 구성하려면 다음 YAML을 복사하여 붙여넣기를 라는 `values.yaml`파일에. 및 `# {API_KEY}` `# {ENDPOINT_URI}` 주석을 사용자 고유의 값으로 바꿉습니다.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> `billing` 및 `apikey` 값이 제공되지 않으면 서비스는 15분 후에 만료됩니다. 마찬가지로 서비스를 사용할 수 없게 되어 확인이 실패합니다.

*읽기* 디렉터리 아래에 *템플릿* 폴더를 만듭니다. 다음 YAML을 복사하여 붙여넣기. `deployment.yaml` 이 `deployment.yaml` 파일은 Helm 템플릿으로 사용될 것입니다.

> 템플릿은 Kubernetes가 이해할 수 있는 YAML 형식의 리소스 설명인 매니페스트 파일을 생성합니다. [- 투구 차트 템플릿 가이드][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

템플릿은 로드 밸런서 서비스와 읽기를 위한 컨테이너/이미지 배포를 지정합니다.

### <a name="the-kubernetes-package-helm-chart"></a>쿠베르네츠 패키지 (투구 차트)

*Helm 차트에는* 컨테이너 레지스트리에서 가져올 docker 이미지의 `containerpreview.azurecr.io` 구성이 포함되어 있습니다.

> [Helm 차트는][helm-charts] Kubernetes 리소스의 관련 집합을 설명하는 파일 모음입니다. 단일 차트는 HTTP 서버, 데이터베이스, 캐시 등이 있는 전체 웹 앱 스택과 같이 간단한 포드 또는 복잡한 것을 배포하는 데 사용될 수 있습니다.

제공된 *Helm 차트는* 컴퓨터 비전 서비스의 도커 이미지와 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 해당 서비스를 가져옵니다.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes 클러스터에 투구 차트 설치

*투구 차트를*설치하려면 [`helm install`][helm-install-cmd] 명령을 실행해야 합니다. `read` 폴더 위의 디렉터리에서 설치 명령을 실행해야 합니다.

```console
helm install read ./read
```

다음은 성공적인 설치 실행에서 볼 수 있는 예제 출력입니다.

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Kubernetes 배포를 완료하는 데 몇 분 이상 걸릴 수 있습니다. 포드와 서비스가 모두 제대로 배포되고 사용 가능한지 확인하려면 다음 명령을 실행합니다.

```console
kubectl get all
```

다음 출력과 비슷한 것을 볼 것으로 예상해야 합니다.

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>다음 단계

Azure Kubernetes 서비스(AKS)에서 Helm을 사용 하 여 응용 프로그램 설치에 대 한 자세한 내용은 [여기를 방문 하십시오.][installing-helm-apps-in-aks]

> [!div class="nextstepaction"]
> [코그너티브 서비스 컨테이너][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
