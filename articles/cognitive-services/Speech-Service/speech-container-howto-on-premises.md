---
title: Kubernetes 온-프레미스를 사용 합니다.
titleSuffix: Azure Cognitive Services
description: Kubernetes 및 Helm을 사용 하 여 음성-텍스트 및 텍스트 음성 변환 컨테이너 이미지를 정의 Kubernetes 패키지를 만들어 보겠습니다. 이 패키지를 배포할 수는 kubernetes 클러스터 온-프레미스입니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/10/2019
ms.author: dapine
ms.openlocfilehash: 33d9de956a6d43145fc68f4ec46b09b8e8bf0188
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786249"
---
# <a name="use-kubernetes-on-premises"></a>Kubernetes 온-프레미스를 사용 합니다.

Kubernetes 및 Helm을 사용 하 여 음성-텍스트 및 텍스트 음성 변환 컨테이너 이미지를 정의 Kubernetes 패키지를 만들어 보겠습니다. 이 패키지를 배포할 수는 kubernetes 클러스터 온-프레미스입니다. 마지막으로 배포 된 서비스 및 다양 한 구성 옵션을 테스트 하는 방법을 살펴봅니다.

## <a name="prerequisites"></a>전제 조건

음성 컨테이너 온-프레미스를 사용 하기 전에 다음 필수 조건을 충족 해야 합니다.

|필수|용도|
|--|--|
| Azure 계정 | Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][free-azure-account]을 만듭니다. |
| 컨테이너 레지스트리 액세스 | Kubernetes 클러스터에 docker 이미지를 끌어오기를 순서 대로 컨테이너 레지스트리에 액세스를 해야 합니다. 해야 [container registry에 대 한 액세스 요청][speech-preview-access] 첫 번째입니다. |
| Kubernetes CLI | 합니다 [Kubernetes CLI][kubernetes-cli] container registry에서 공유 자격 증명을 관리 하기 위한 필요 합니다. Kubernetes는 Helm은 Kubernetes 패키지 관리자는 하기 전에 필요 합니다. |
| Helm CLI | 일부로 합니다 [Helm CLI][helm-install] install, you'll also need to initialize Helm which will install [Tiller][tiller-install]합니다. |
|음성 리소스 |이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결된 청구 키 및 청구 끝점 URI를 가져올 수 있는 _음성_ Azure 리소스. 두 값은 Azure portal의 **음성** 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작하기 위해 필요합니다.<br><br>**{API_KEY}** : 리소스 키<br><br>**{ENDPOINT_URI}** : 끝점 URI 예제: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>권장 되는 호스트 컴퓨터 구성

참조를 [Speech Service 컨테이너 호스트 컴퓨터로][speech-container-host-computer] 세부 정보를 참조 합니다. 이 *helm 차트* 자동으로 계산 하는 CPU 및 메모리 요구 사항이 얼마나 많은 디코딩합니다 (동시 요청 수)에 따라 사용자 지정 하는 합니다. 또한 오디오/텍스트 입력에 대 한 최적화로 구성 된 여부에 따라 조정 됩니다 `enabled`합니다. 두 개의 동시 요청 수 및 최적화를 해제 하면 helm 차트 기본값은입니다.

| 서비스 | CPU / 컨테이너 | 메모리 / 컨테이너 |
|--|--|--|
| **음성 텍스트 변환** | 하나의 디코더 1, 150 (밀리 코어)의 최소가 필요합니다. 경우는 `optimizedForAudioFile` 1,950 (밀리 코어)는 필요한 가능 합니다. (기본값: 두 디코더) | 필수: 2GB<br>제한:  4GB |
| **텍스트 음성 변환** | 하나의 동시 요청에는 500 (밀리 코어)의 최소가 필요합니다. 경우는 `optimizeForTurboMode` 1,000 (밀리 코어)는 필요한 가능 합니다. (기본값: 두 개의 동시 요청) | 필수: 1GB<br> 제한: 2GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes 클러스터에 연결

호스트 컴퓨터는 사용할 수 있는 Kubernetes 클러스터가 해야 합니다. 이 자습서를 참조 하세요 [Kubernetes 클러스터 배포](../../aks/tutorial-kubernetes-deploy-cluster.md) 호스트 컴퓨터에 Kubernetes 클러스터를 배포 하는 방법에 대 한 개념적 이해에 대 한 합니다.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Kubernetes 클러스터를 사용 하 여 Docker 자격 증명 공유

Kubernetes 클러스터를 허용 하도록 `docker pull` 에서 구성 된 이미지는 `containerpreview.azurecr.io` 클러스터에 docker 자격 증명을 전송 해야 하는 컨테이너 레지스트리. 실행 된 [ `kubectl create` ][kubectl-create] 을 만들려면 아래 명령을 *docker 레지스트리 암호* 컨테이너 레지스트리 액세스 필수 구성 요소에서 제공 된 자격 증명에 따라 합니다.

선택한 명령줄 인터페이스에서 다음 명령을 실행 합니다. 로 대체 해야 합니다 `<username>`, `<password>`, 및 `<email-address>` 컨테이너 레지스트리 자격 증명을 사용 하 여 합니다.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> 에 대 한 액세스를 이미 있는 경우는 `containerpreview.azurecr.io` container registry 대신 일반 플래그를 사용 하 여 Kubernetes 비밀을 만들 수 있습니다. 명령을 Docker 구성 JSON에 대해 실행 하는 것이 좋습니다.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

비밀이 성공적으로 생성 된 경우 다음 출력이 콘솔에 출력 됩니다.

```console
secret "containerpreview" created
```

비밀 생성 되어 있는지 확인 하려면 실행 합니다 [ `kubectl get` ][kubectl-get] 사용 하 여는 `secrets` 플래그입니다.

```console
kuberctl get secrets
```

실행 된 `kubectl get secrets` 모든 구성 된 암호를 인쇄 합니다.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>배포에 대 한 Helm 차트 값 구성

방문 합니다 [Microsoft Helm Hub][ms-helm-hub] Microsoft에서 제공 하는 모든 공개 helm 차트에 대 한 합니다. Microsoft Helm 허브에서 찾을 수 있습니다 합니다 **Cognitive Services 음성 온-프레미스 차트**합니다. 합니다 **Cognitive Services 음성 온-프레미스** 차트를 설치 하지만 먼저 만들어야 합니다를 `config-values.yaml` 명시적 구성 파일입니다. Microsoft 리포지토리 Helm 인스턴스를 추가 하 여 시작 해 보겠습니다.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

다음으로 Helm 차트 값이 구성 됩니다. 다음 YAML 라는 파일에 복사한 `config-values.yaml`합니다. 사용자 지정 하는 방법은 합니다 **Cognitive Services 음성 온-프레미스 Helm 차트**를 참조 하세요 [helm 차트를 사용자 지정](#customize-helm-charts)합니다. 대체는 `billing` 및 `apikey` 고유한 값입니다.

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> 경우는 `billing` 고 `apikey` 값을 제공 하지, 서비스는 15 분이 지나면 만료 됩니다. 마찬가지로, 서비스를 사용할 수 없습니다 확인 실패 합니다.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 패키지 (Helm 차트)

*Helm 차트* 는 docker 이미지를 끌어오는 구성이 포함 된 `containerpreview.azurecr.io` 컨테이너 레지스트리.

> A [Helm 차트][helm-charts] 는 Kubernetes 리소스의 관련된 집합을 설명 하는 파일의 컬렉션입니다. Memcached pod, 또는 복잡 하 고 HTTP 서버, 데이터베이스, 캐시 및 등을 사용 하 여 전체 웹 응용 프로그램 스택을 같은 간단한를 배포 하려면 단일 차트를 사용할 수 있습니다.

제공 된 *Helm 차트* Speech Service를 둘 다 텍스트 음성 변환, 음성-텍스트 서비스에서의 docker 이미지를 끌어오기는 `containerpreview.azurecr.io` 컨테이너 레지스트리.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes 클러스터에 Helm 차트 설치

설치 하는 *helm 차트* 실행 해야 합니다 [ `helm install` ][helm-install-cmd] 명령을 대체를 `<config-values.yaml>` 적절 한 경로 및 파일 이름 인수를 사용 하 여 합니다. 합니다 `microsoft/cognitive-services-speech-onpremise` 아래에 참조 된 Helm 차트에서 사용할 수는 [Microsoft Helm Hub 여기][ms-helm-hub-speech-chart]합니다.

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

성공적인 설치 실행에서 보려는 예상 출력 예는 다음과 같습니다.

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

Kubernetes 배포를 완료 하려면 몇 분 정도 대신할 수 있습니다. Pod와 서비스는 제대로 배포 되어 있고 사용할 수를 확인 하려면 다음 명령을 실행 합니다.

```console
kubectl get all
```

다음 출력과 유사한 출력이 표시 되어야 합니다.

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Helm 테스트를 사용 하 여 Helm 배포를 확인 합니다.

설치 된 Helm 차트 정의 *테스트 Helm*, 확인을 위한 편의 기능으로 제공 하는 합니다. 이러한 테스트는 서비스 준비 상태를 확인합니다. 모두 확인할 **음성-텍스트** 및 **text to speech** services, 여기에서는 실행 합니다 [Helm 테스트][helm-test] 명령입니다.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> POD 상태가 없는 경우 이러한 테스트가 실패 `Running` 배포 목록에 없는 경우 또는 `AVAILABLE` 열입니다. 환자 될 완료 하려면 10 분 이상 걸릴 수 있습니다.

이러한 테스트는 다양 한 상태 결과 출력 됩니다.

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

실행 하는 대신 합니다 *테스트 helm*를 수집할 수 있는 *외부 IP* 주소 및 해당 포트에서를 `kubectl get all` 명령. 웹 브라우저를 열고 이동할 IP 및 포트를 사용 하 여, `http://<external-ip>:<port>:/swagger/index.html` API swagger 페이지 보기로 합니다.

## <a name="customize-helm-charts"></a>Helm 차트를 사용자 지정

Helm 차트는 계층적입니다. 차트 상속에 대 한 허용 되 고 계층적 것도 사항에 맞는 개념의 보다 구체적인 설정을 상속 된 규칙을 재정의 하는 위치.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>다음 단계

Azure Kubernetes Service (AKS)에서 Helm을 사용 하 여 응용 프로그램 설치에 대 한 자세한 내용은 [보려면 이곳을 방문][installing-helm-apps-in-aks]합니다.

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
