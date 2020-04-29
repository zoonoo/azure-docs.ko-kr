---
title: 지능형 라우팅에 Istio 사용
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service(AKS) 클러스터에서 Istio를 사용하여 인텔리전트 라우팅을 제공하고 카나리아 릴리스를 배포하는 방법 알아보기
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 01a7764eb0a353e6842441093f70ad29c9316bbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668286"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service(AKS)에서 Istio를 사용하여 인텔리전트 라우팅 및 카나리 릴리스 사용

[Istio][istio-github]는 Kubernetes 클러스터에서 마이크로서비스에서 기능 키 세트를 제공하는 오픈 소스 서비스 메시입니다. 이 기능에는 트래픽 관리, 서비스 ID 및 보안, 정책 적용 및 관찰성을 포함됩니다. Istio에 대한 자세한 내용은 [Istio는 무엇인가?][istio-docs-concepts] 공식 설명서를 참조하세요.

이 문서에서는 Istio의 트래픽 관리 기능을 사용하는 방법을 보여줍니다. 샘플 AKS 투표 앱은 인텔리전트 라우팅 및 카나리아 릴리스를 탐색하는 데 사용됩니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 애플리케이션 배포
> * 애플리케이션 업데이트
> * 애플리케이션의 카나리아 릴리스 출시
> * 출시 완료

## <a name="before-you-begin"></a>시작하기 전에

> [!NOTE]
> 이 시나리오는 Istio 버전 `1.3.2`에 대해 테스트 되었습니다.

이 문서에 설명 된 단계에서는 AKS 클러스터 (RBAC를 사용 하 여 `1.13` Kubernetes 이상)를 만들고 클러스터와의 연결을 `kubectl` 설정 했다고 가정 합니다. 또한 Istio가 클러스터에 설치 되어 있어야 합니다.

이러한 항목에 대 한 도움이 필요한 경우 [AKS 빠른][aks-quickstart] 시작을 참조 하 고 [AKS 지침에 Istio를 설치][istio-install] 합니다.

## <a name="about-this-application-scenario"></a>이 애플리케이션 시나리오에 대한 정보

샘플 AKS 투표 앱은 사용자에 게 두 가지 투표 옵션 (**고양이** 또는 **강아지**)을 제공 합니다. 각 옵션에 대한 투표 수를 유지하는 스토리지 구성 요소가 있습니다. 또한 각 옵션의 득표에 대한 세부 정보를 제공하는 분석 구성 요소가 있습니다.

이 응용 프로그램 시나리오에서는 투표 앱 및 버전 `1.0` `1.0` 의 분석 구성 요소 버전을 배포 하 여 시작 합니다. 분석 구성 요소는 투표 수에 대한 간단한 수를 제공합니다. 투표 앱 및 분석 구성 요소는 Redis에 `1.0` 의해 지원 되는 저장소 구성 요소 버전과 상호 작용 합니다.

분석 구성 요소를 버전 `1.1`으로 업그레이드 하 여 개수를 제공 하 고, 이제 합계 및 백분율을 제공 합니다.

카나리아 릴리스를 통해 사용자의 `2.0` 응용 프로그램 테스트 버전의 하위 집합입니다. 이 새 버전에는 MySQL 데이터베이스에서 지원 되는 스토리지 구성 요소를 사용합니다.

버전이 `2.0` 사용자의 하위 집합에서 예상 대로 작동 하는 것을 확신 하는 경우 모든 사용자 `2.0` 에 게 버전을 롤아웃 합니다.

## <a name="deploy-the-application"></a>애플리케이션 배포

Azure Kubernetes Service(AKS) 클러스터에 애플리케이션을 배포합니다. 다음 다이어그램에서는 Istio 수신 게이트웨이를 통해 서비스를 제공 하는 `1.0` 인바운드 요청을 사용 하는 모든 구성 요소의 버전을이 섹션의 끝에서 실행 하는 것을 보여 줍니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/servicemesh/istio/scenario-routing-components-01.png)

[Azure-Samples/aks-voting-app][github-azure-sample] GitHub 리포지토리에서 이 문서와 함께 수행해야 하는 아티팩트를 사용할 수 있습니다. 다음과 같이 아티팩트를 다운로드하거나 리포지토리를 복제할 수 있습니다.

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

다운로드/복제된 리포지토리의 다음 폴더로 변경 하고 이 폴더에서 모든 후속 단계를 실행합니다.

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

먼저 다음과 같이 이라는 `voting` 샘플 AKS 투표 앱에 대 한 AKS 클러스터에 네임 스페이스를 만듭니다.

```console
kubectl create namespace voting
```

`istio-injection=enabled`를 사용하여 네임스페이스를 레이블합니다. 이 레이블은 이 네임스페이스에서 모든 사용자 Pod에 사이드카로 istio-proxies를 자동으로 삽입하도록 Istio에게 지시합니다.

```console
kubectl label namespace voting istio-injection=enabled
```

이제 AKS Voting 앱에 대한 구성 요소를 만들어 보겠습니다. 이전 단계에서 만든 `voting` 네임 스페이스에 이러한 구성 요소를 만듭니다.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

다음 예제 출력에서는 생성 되는 리소스를 보여 줍니다.

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio에는 Pod 및 서비스에 대한 몇 가지 요구 사항이 있습니다. 자세한 내용은 [Pods 및 Services 설명서에 대한 Istio 요구 사항][istio-requirements-pods-and-services]을 참조하세요.

만들었던 Pod를 보려면 다음과 같이 [kubectl get pods][kubectl-get] 명령을 사용합니다.

```console
kubectl get pods -n voting --show-labels
```

다음 예제 출력에서는 `voting-app` pod의 3 개 인스턴스와 `voting-analytics` 및 `voting-storage` pod의 단일 인스턴스를 보여 줍니다. 각 Pod에는 두 개의 컨테이너가 있습니다. 이러한 컨테이너 중 하나는 구성 요소이 고 다른 하나는입니다 `istio-proxy`.

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Pod에 대 한 정보를 보려면 레이블 선택기와 함께 [kubectl 설명 pod][kubectl-describe] 명령을 사용 하 여 `voting-analytics` pod를 선택 합니다. 출력을 필터링 하 여 pod에 있는 두 컨테이너의 세부 정보를 표시 합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Istio [게이트웨이][istio-reference-gateway] 및 [가상 서비스][istio-reference-virtualservice]를 만들 때까지 투표 앱에 연결할 수 없습니다. 이러한 Istio 리소스는 기본 Istio 게이트웨이에서 애플리케이션으로 트래픽을 라우팅합니다.

> [!NOTE]
> **게이트웨이**는 인바운드 또는 아웃 바운드 HTTP 및 TCP 트래픽을 수신하는 서비스 메시의 가장자리에 있는 구성 요소입니다.
> 
> **가상 서비스**는 하나 이상의 대상 서비스에 대 한 라우팅 규칙 세트를 정의합니다.

`kubectl apply` 명령을 사용 하 여 게이트웨이 및 Virtual Service yaml을 배포 합니다. 이러한 리소스가 배포 되는 네임 스페이스를 지정 해야 합니다.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

다음 예제 출력에서는 생성 되는 새 게이트웨이 및 가상 서비스를 보여 줍니다.

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

다음 명령을 사용하여 Istio 수신 게이트웨이의 IP 주소를 가져옵니다.

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

다음 예제 출력에서는 수신 게이트웨이의 IP 주소를 보여줍니다.

```output
20.188.211.19
```

브라우저를 열고 IP 주소에 붙여 넣습니다. 샘플 AKS Voting 앱이 표시됩니다.

![Istio에서 실행 중인 AKS Voting 앱은 AKS 클러스터를 사용할 수 있습니다.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

화면 하단의 정보는 `1.0` 앱이의 `voting-app` `1.0` `voting-storage` 버전 및 버전 (Redis)을 사용 하는 것을 보여 줍니다.

## <a name="update-the-application"></a>애플리케이션 업데이트

새 버전의 분석 구성 요소를 배포 해 보겠습니다. 이 새 버전 `1.1` 은 각 범주에 대 한 개수 외에도 합계 및 백분율을 표시 합니다.

다음 다이어그램은이 섹션의 끝에서 실행 되는 항목을 보여 줍니다 .이 `1.1` `voting-analytics` 구성 요소의 버전에는 `voting-app` 구성 요소에서 라우팅되는 트래픽이 있습니다. 구성 요소의 버전이 `1.0` 계속 실행 되 고 `voting-analytics` 서비스에서 참조 되는 경우에도 istio 프록시는 트래픽을 허용 하지 않습니다. `voting-analytics`

![AKS Voting 앱 구성 요소 및 라우팅](media/servicemesh/istio/scenario-routing-components-02.png)

`voting-analytics` 구성 요소의 버전 `1.1` 을 배포 해 보겠습니다. `voting` 네임 스페이스에서이 구성 요소를 만듭니다.

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

다음 예제 출력에서는 생성 되는 리소스를 보여 줍니다.

```output
deployment.apps/voting-analytics-1-1 created
```

이전 단계에서 가져온 Istio 수신 게이트웨이의 IP주소를 사용하여 다시 브라우저에서 샘플 AKS Voting 앱을 엽니다.

브라우저에서 아래에 표시된 두 가지 보기를 번갈아 보여줍니다. 단일 레이블 선택기(`app: voting-analytics`)만 포함하는 `voting-analytics` 구성 요소에 대해 Kubernetes [서비스][kubernetes-service]를 사용 하 고 있으므로 Kubernetes는 해당 선택기와 일치하는 pod 간에 라운드 로빈의 기본 동작을 사용합니다. 이 경우에는 `1.0` `1.1` `voting-analytics` pod의 버전 및입니다.

![AKS Voting 앱에서 실행 중인 분석 구성 요소의 버전 1.0입니다.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![AKS Voting 앱에서 실행 중인 분석 구성 요소의 버전 1.1입니다.](media/servicemesh/istio/scenario-routing-update-app-01.png)

다음과 같이 두 가지 버전의 `voting-analytics` 구성 요소 간 전환을 시각화할 수 있습니다. 사용자 Istio 수신 게이트웨이의 IP 주소를 사용해야 합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

다음 예제 출력은 버전 간에 사이트 전환으로 반환된 웹 사이트의 관련 부분을 보여 줍니다.

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>애플리케이션의 버전 1.1로의 트래픽 잠그기

이제 `1.1` `voting-analytics` 구성 요소 버전 및 `1.0` `voting-storage` 구성 요소 버전 으로만 트래픽을 잠급니다. 그런 다음, 모든 다른 구성 요소에 대한 라우팅 규칙을 정의합니다.

> * **가상 서비스**는 하나 이상의 대상 서비스에 대 한 라우팅 규칙 세트를 정의합니다.
> * **대상 규칙**은 트래픽 정책 및 특정 정책 버전을 정의합니다.
> * **정책**은 어떤 인증 방법이 워크 로드에 승인될지 있는 지를 정의합니다.

`kubectl apply` 명령을 사용 하 여의 `voting-app` 가상 서비스 정의를 바꾸고 다른 구성 요소에 대 한 [대상 규칙][istio-reference-destinationrule] 및 [가상 서비스][istio-reference-virtualservice] 를 추가 합니다. 서비스 간 통신은 상호 TLS 및 클라이언트 인증서를 사용하여 보호되도록 `voting` 네임스페이스에 [정책][istio-reference-policy]을 추가합니다.

* 정책을로 `peers.mtls.mode` 설정 `STRICT` 하 여 `voting` 네임 스페이스 내에서 서비스 간에 상호 TLS가 적용 되도록 합니다.
* 또한 모든 대상 규칙 `trafficPolicy.tls.mode` 에서 `ISTIO_MUTUAL` 을로 설정 합니다. Istio는 강력한 ID의 서비스를 제공하고 Istio가 투명하게 관리하는 상호 TLS와 클라이언트 인증서를 사용하여 서비스 간 통신을 보호합니다.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

다음 예제 출력에서는 업데이트/생성 되는 새 정책, 대상 규칙 및 가상 서비스를 보여 줍니다.

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

브라우저에서 AKS 투표 앱을 다시 열면 `1.1` `voting-analytics` `voting-app` 구성 요소의 새 버전만 구성 요소에서 사용 됩니다.

![AKS Voting 앱에서 실행 중인 분석 구성 요소의 버전 1.1입니다.](media/servicemesh/istio/scenario-routing-update-app-01.png)

이제 다음과 같이 `1.1` `voting-analytics` 구성 요소 버전에만 라우팅되는 것을 시각화할 수 있습니다. 사용자 Istio 수신 게이트웨이의 IP 주소를 사용해야 합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

다음 예제 출력은 반환된 웹 사이트의 관련 부분을 보여줍니다.

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

이제 Istio가 상호 TLS를 사용 하 여 각 서비스 간의 통신을 보호 하 고 있는지 확인 하겠습니다. 이를 위해 다음 형식을 사용 하는 `istioctl` 클라이언트 이진 파일의 [인증 tls-check][istioctl-authn-tls-check] 명령을 사용 합니다.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

이 명령 집합은 네임 스페이스에 있고 레이블 집합과 일치 하는 모든 pod에서 지정 된 서비스에 대 한 액세스 정보를 제공 합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

다음 예제 출력에서는 위의 각 쿼리에 대해 상호 TLS를 적용 하는 방법을 보여 줍니다. 출력에는 상호 TLS를 적용 하는 정책 및 대상 규칙도 표시 됩니다.

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>애플리케이션의 카나리아 릴리스 출시

이제 `voting-app`, `voting-analytics`및 `voting-storage` 구성 요소의 새 버전 `2.0` 을 배포 해 보겠습니다. 새 `voting-storage` 구성 요소는 Redis 대신 MySQL을 사용 하 고 `voting-app` , `voting-analytics` 및 구성 요소를 업데이트 하 여이 새 `voting-storage` 구성 요소를 사용할 수 있도록 합니다.

이제 `voting-app` 구성 요소가 기능 플래그 기능을 지원 합니다. 이 기능 플래그를 사용 하면 사용자의 하위 집합에 대한 카나리 릴리스 기능을 테스트할 수 있습니다.

다음 다이어그램은이 섹션의 끝에서 실행 되는 작업을 보여 줍니다.

* `voting-analytics` `1.0` 구성 요소 버전의 `voting-storage` 구성 요소 및 구성 요소 버전은 서로 통신할 수 있습니다. `1.0` `voting-app` `1.1`
* `voting-analytics` `2.0` 구성 요소 버전의 `voting-storage` 구성 요소 및 구성 요소 버전은 서로 통신할 수 있습니다. `2.0` `voting-app` `2.0`
* `voting-app` 구성 요소의 버전 `2.0` 은 특정 기능 플래그가 설정 된 사용자만 액세스할 수 있습니다. 이러한 변경은 쿠키를 통해 기능 플래그를 사용 하여 관리 됩니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/servicemesh/istio/scenario-routing-components-03.png)

먼저 이러한 새 구성 요소에 부합하도록 Istio 대상 규칙 및 가상 서비스를 업데이트합니다. 이러한 업데이트는 새 구성 요소로 트래픽을 잘못 라우팅되지는 않는지 사용자가 예기치 않은 액세스를 하지 않는지 확인합니다.

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

다음 예제 출력에서는 업데이트 되는 대상 규칙 및 가상 서비스를 보여 줍니다.

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

다음으로 새 버전 `2.0` 구성 요소에 대 한 Kubernetes 개체를 추가 해 보겠습니다. 또한 MySQL에 대 `voting-storage` 한 `3306` 포트를 포함 하도록 서비스를 업데이트 합니다.

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

다음 예제 출력은 Kubernetes 개체가 성공적으로 업데이트되거나 만들어졌음을 보여줍니다.

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

모든 버전 `2.0` pod 실행 될 때까지 기다립니다. [Kubectl get pod][kubectl-get] 명령을 `-w` 조사식 스위치와 함께 사용 하 여 `voting` 네임 스페이스의 모든 pod에 대 한 변경 내용을 감시 합니다.

```console
kubectl get pods --namespace voting -w
```

이제 투표 응용 프로그램의 버전 `1.0` 및 버전 `2.0` (카나리아) 간을 전환할 수 있습니다. 화면 맨 아래에 있는 기능 플래그 토글에서 쿠키를 설정합니다. 이 쿠키는 `voting-app` 가상 서비스에서 사용자를 새 버전 `2.0`으로 라우팅하는 데 사용 됩니다.

![AKS Voting 앱 버전 1.0 - 기능 플래그가 설정되어 있지 않음](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![AKS Voting 앱 버전 2.0 - 기능 플래그가 설정되어 있음](media/servicemesh/istio/scenario-routing-canary-release-02.png)

투표 수가 앱의 버전 간에 서로 차이가 납니다. 이러한 차이는 두 개의 다른 스토리지 백 엔드를 사용 중임을 강조 표시합니다.

## <a name="finalize-the-rollout"></a>출시 완료

카나리아 릴리스를 성공적으로 테스트 한 후에는 `voting-app` 가상 서비스를 업데이트 하 여 모든 트래픽을 `2.0` `voting-app` 구성 요소 버전으로 라우팅합니다. 그러면 기능 플래그가 설정 되었는지 `2.0` 여부에 관계 없이 모든 사용자가 응용 프로그램의 버전을 볼 수 있습니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/servicemesh/istio/scenario-routing-components-04.png)

대상 규칙을 업데이트하여 더 이상 사용하고 싶지 않은 구성 요소의 버전을 제거합니다. 그런 모든 가상 서비스를 업데이트하여 이 버전 참조를 중지합니다.

구성 요소의 이전 버전에 더 이상 트래픽이 없으므로 이러한 구성 요소에 대한 모든 배포를 안전하게 삭제할 수 있습니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/servicemesh/istio/scenario-routing-components-05.png)

이제 AKS Voting 앱의 새 버전을 성공적으로 출시합니다.

## <a name="clean-up"></a>정리 

다음과 같이 네임 스페이스를 `voting` 삭제 하 여이 시나리오에서 사용한 AKS 투표 앱을 AKS 클러스터에서 제거할 수 있습니다.

```console
kubectl delete namespace voting
```

다음 예제 출력에서는 AKS 투표 앱의 모든 구성 요소가 AKS 클러스터에서 제거 되었음을 보여 줍니다.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>다음 단계

또한 [Istio Bookinfo 애플리케이션 예제][istio-bookinfo-example]를 사용하여 추가 시나리오를 탐색할 수 있습니다.

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
