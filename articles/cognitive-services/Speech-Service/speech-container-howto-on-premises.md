---
title: Kubernetes 및 헬름과 음성 서비스 컨테이너 사용
titleSuffix: Azure Cognitive Services
description: Kubernetes 및 Helm을 사용하여 음성-텍스트 및 텍스트 음성 변환 컨테이너 이미지를 정의하고 Kubernetes 패키지를 만듭니다. 이 패키지는 Kubernetes 클러스터 온-프레미스에 배포됩니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 6ad5a843c8cc287834305e09b48cd3fafe09ca51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474767"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Kubernetes 및 헬름과 음성 서비스 컨테이너 사용

온-프레미스 에서 음성 컨테이너를 관리하는 한 가지 옵션은 Kubernetes 및 Helm을 사용하는 것입니다. Kubernetes 및 Helm을 사용하여 음성-텍스트 및 텍스트 음성 변환 컨테이너 이미지를 정의하고 Kubernetes 패키지를 만듭니다. 이 패키지는 Kubernetes 클러스터 온-프레미스에 배포됩니다. 마지막으로 배포된 서비스와 다양한 구성 옵션을 테스트하는 방법을 살펴보겠습니다. Kubernetes 오케스트레이션 없이 Docker 컨테이너를 실행하는 것에 대한 자세한 내용은 [음성 서비스 컨테이너 설치 및 실행을](speech-container-howto.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

온-프레미스 에서 음성 컨테이너를 사용하기 전에 다음 필수 구성 조건은 다음과 같은 것입니다.

| 필수 | 목적 |
|----------|---------|
| Azure 계정 | Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][free-azure-account]을 만듭니다. |
| 컨테이너 레지스트리 액세스 | Kubernetes가 도커 이미지를 클러스터로 가져오려면 컨테이너 레지스트리에 액세스해야 합니다. |
| 쿠베르네테스 클리 | [Kubernetes CLI는][kubernetes-cli] 컨테이너 레지스트리에서 공유 자격 증명을 관리하는 데 필요합니다. Kubernetes 패키지 매니저인 헬름 전에도 쿠베네테스가 필요합니다. |
| Helm CLI | 투구 차트(컨테이너 패키지 정의)를 설치하는 데 사용되는 [Helm CLI를][helm-install]설치합니다. |
|음성 리소스 |이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결된 청구 키 및 청구 끝점 URI를 얻기 위한 _Speech_ Azure 리소스입니다. 두 값 모두 Azure 포털의 **음성** 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작 해야 합니다.<br><br>**{API_KEY}**: 리소스 키<br><br>**{ENDPOINT_URI}**: 끝점 URI 예제는 다음과 입니다.`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>권장호스트 컴퓨터 구성

[음성 서비스 컨테이너 호스트 컴퓨터][speech-container-host-computer] 세부 정보를 참조로 참조합니다. 이 *헬름 차트는* 사용자가 지정하는 디코딩(동시 요청) 수를 기준으로 CPU 및 메모리 요구 사항을 자동으로 계산합니다. 또한 오디오/텍스트 입력에 대한 최적화가 로 `enabled`구성되는지 여부에 따라 조정됩니다. 투구 차트는 기본적으로 두 개의 동시 요청 및 최적화를 비활성화합니다.

| 서비스 | CPU / 컨테이너 | 메모리 / 컨테이너 |
|--|--|--|
| **음성 텍스트 변환** | 하나의 디코더에는 최소 1,150밀리코어가 필요합니다. `optimizedForAudioFile` 이 옵션을 사용하면 1,950밀리코어가 필요합니다. (기본값: 두 개의 디코더) | 필수: 2GB<br>한정: 4GB |
| **텍스트 음성 변환** | 하나의 동시 요청에는 최소 500밀리코어가 필요합니다. `optimizeForTurboMode` 이 옵션을 사용하도록 설정하면 1,000밀리코어가 필요합니다. (기본값: 두 개의 동시 요청) | 필수: 1GB<br> 한정: 2GB |

## <a name="connect-to-the-kubernetes-cluster"></a>쿠베르네츠 클러스터에 연결

호스트 컴퓨터에 사용 가능한 Kubernetes 클러스터가 있어야 합니다. 호스트 컴퓨터에 [Kubernetes 클러스터를 배포하는](../../aks/tutorial-kubernetes-deploy-cluster.md) 방법에 대한 개념적 이해는 Kubernetes 클러스터 배포에 대한 이 자습서를 참조하십시오.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Kubernetes 클러스터와 Docker 자격 증명 공유

Kubernetes 클러스터를 `docker pull` `containerpreview.azurecr.io` 컨테이너 레지스트리에서 구성된 이미지로 허용하려면 docker 자격 증명을 클러스터로 전송해야 합니다. 컨테이너 [`kubectl create`][kubectl-create] 레지스트리 액세스 필수 구성 사이트에서 제공 된 자격 증명에 따라 *도커 레지스트리 비밀을* 만들려면 아래 명령을 실행 합니다.

선택한 명령줄 인터페이스에서 다음 명령을 실행합니다. `<username>`을 `<password>`및 `<email-address>` 컨테이너 레지스트리 자격 증명으로 바꿔야 합니다.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 이미 컨테이너 레지스트리에 `containerpreview.azurecr.io` 액세스할 수 있는 경우 대신 일반 플래그를 사용하여 Kubernetes 비밀을 만들 수 있습니다. Docker 구성 JSON에 대해 실행되는 다음 명령을 고려하십시오.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

다음 출력은 비밀이 성공적으로 생성되면 콘솔에 인쇄됩니다.

```console
secret "mcr" created
```

보안 이 만들어졌는지 확인하려면 [`kubectl get`][kubectl-get] `secrets` 플래그를 사용하여 을 실행합니다.

```console
kubectl get secrets
```

인쇄를 `kubectl get secrets` 실행하는 모든 구성된 비밀.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>배포를 위한 헬름 차트 값 구성

Microsoft에서 제공하는 모든 공개적으로 사용 가능한 투구 차트에 대한 Microsoft [헬름 허브를][ms-helm-hub] 방문하십시오. Microsoft 헬름 허브에서 **인지 서비스 음성 온-프레미스 차트를**찾을 수 있습니다. **인지 서비스 음성 온-프레미스는** 설치할 차트이지만 먼저 명시적 구성이 `config-values.yaml` 있는 파일을 만들어야 합니다. 먼저 Helm 인스턴스에 Microsoft 리포지토리를 추가해 보겠습니다.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

다음으로 Helm 차트 값을 구성합니다. 다음 YAML을 복사하여 붙여넣기. `config-values.yaml` **코그너티브 서비스 음성 온-프레미스 헬름 차트**사용자 지정에 대한 자세한 내용은 [헬름 차트 사용자 지정을](#customize-helm-charts)참조하십시오. 및 `# {API_KEY}` `# {ENDPOINT_URI}` 주석을 사용자 고유의 값으로 바꿉습니다.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> `billing` 및 `apikey` 값이 제공되지 않으면 서비스는 15분 후에 만료됩니다. 마찬가지로 서비스를 사용할 수 없게 되어 확인이 실패합니다.

### <a name="the-kubernetes-package-helm-chart"></a>쿠베르네츠 패키지 (투구 차트)

*Helm 차트에는* 컨테이너 레지스트리에서 가져올 docker 이미지의 `containerpreview.azurecr.io` 구성이 포함되어 있습니다.

> [Helm 차트는][helm-charts] Kubernetes 리소스의 관련 집합을 설명하는 파일 모음입니다. 단일 차트는 HTTP 서버, 데이터베이스, 캐시 등이 있는 전체 웹 앱 스택과 같이 간단한 포드 또는 복잡한 것을 배포하는 데 사용될 수 있습니다.

제공된 *Helm 차트는* 음성 변환 서비스의 도커 이미지, 텍스트 음성 변환 및 `containerpreview.azurecr.io` 컨테이너 레지스트리의 음성-텍스트 서비스 모두를 가져옵니다.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes 클러스터에 투구 차트 설치

*투구 차트를* 설치하려면 [`helm install`][helm-install-cmd] 명령을 실행하여 적절한 경로 `<config-values.yaml>` 및 파일 이름 인수로 바꿉니다. 아래 `microsoft/cognitive-services-speech-onpremise` 참조 된 투구 차트는 [Microsoft 헬름 허브에서 사용할][ms-helm-hub-speech-chart]수 있습니다.

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

다음은 성공적인 설치 실행에서 볼 수 있는 예제 출력입니다.

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Kubernetes 배포를 완료하는 데 몇 분 이상 걸릴 수 있습니다. 포드와 서비스가 모두 제대로 배포되고 사용 가능한지 확인하려면 다음 명령을 실행합니다.

```console
kubectl get all
```

다음 출력과 비슷한 것을 볼 것으로 예상해야 합니다.

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>투구 테스트를 통해 투구 전개 확인

설치된 투구 차트는 *Helm 테스트를*정의하며, 이는 검증의 편의를 제공합니다. 이러한 테스트는 서비스 준비 상태를 확인합니다. **음성-텍스트** 및 **텍스트 음성 변환** 서비스를 모두 확인하려면 Helm [테스트][helm-test] 명령을 실행합니다.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> POD 상태가 아니거나 `Running` 배포가 열 아래에 나열되지 않은 `AVAILABLE` 경우 이러한 테스트가 실패합니다. 완료하는 데 10 분 이상 걸릴 수 있기 때문에 인내심을 가져야합니다.

이러한 테스트는 다음과 같은 다양한 상태 결과를 출력합니다.

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

*helm 테스트를*실행하는 대신 명령에서 *외부 IP* 주소와 해당 포트를 수집할 `kubectl get all` 수 있습니다. IP 및 포트를 사용하여 웹 브라우저를 `http://<external-ip>:<port>:/swagger/index.html` 열고 탐색하여 API 스웨거 페이지를 봅니다.

## <a name="customize-helm-charts"></a>헬름 차트 사용자 지정

투구 차트는 계층적입니다. 계층구조는 차트 상속을 허용하므로 보다 구체적인 재정의된 규칙을 재정의하는 구체성 개념도 충족합니다.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
