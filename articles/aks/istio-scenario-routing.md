---
title: Azure Kubernetes Service(AKS)에서 Istio를 사용하여 인텔리전트 라우팅 및 카나리아 릴리스
description: Azure Kubernetes Service(AKS) 클러스터에서 Istio를 사용하여 인텔리전트 라우팅을 제공하고 카나리아 릴리스를 배포하는 방법 알아보기
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465890"
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

이 문서에서 자세히 설명된 단계에서는 AKS 클러스터(Kubernetes 1.10 이상, RBAC 사용 설정됨)를 만들고 클러스터와 `kubectl`을 연결했다고 가정합니다. 또한 클러스터에 Istio를 설치해야 합니다.

이 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작][aks-quickstart] 및 [AKS에서 lstio 설치][istio-install]를 참조하세요.

## <a name="about-this-application-scenario"></a>이 애플리케이션 시나리오에 대한 정보

샘플 AKS 투표 앱은 사용자에게 두 가지 투표 옵션(고양이 또는 개)을 제공합니다. 각 옵션에 대한 투표 수를 유지하는 스토리지 구성 요소가 있습니다. 또한 각 옵션의 득표에 대한 세부 정보를 제공하는 분석 구성 요소가 있습니다.

이 문서에서는 투표 앱 *1.0* 버전 및 분석 구성 요소의 *1.0* 버전을 먼저 배포합니다. 분석 구성 요소는 투표 수에 대한 간단한 수를 제공합니다. 투표 앱과 분석 구성 요소는 Redis에서 지원되는 스토리지 구성 요소의 *1.0* 버전과 상호작용합니다.

수 및 전체 백분율을 제공하는 *1.1* 버전으로 분석 구성 요소를 업그레이드합니다.

카나리 릴리스를 통한 앱 사용자 테스트 버전 *2.0*의 하위 집합입니다. 이 새 버전에는 MySQL 데이터베이스에서 지원 되는 스토리지 구성 요소를 사용합니다.

버전 *2.0*이 사용자의 하위 집합에서 예상대로 작동하면 모든 사용자에게 *2.0* 버전을 출시합니다.

## <a name="deploy-the-application"></a>애플리케이션 배포

Azure Kubernetes Service(AKS) 클러스터에 애플리케이션을 배포합니다. 다음 다이어그램에서는 이 섹션의 끝부분에서 Istio 수신 게이트웨이를 통해 처리되는 인바운드 요청을 사용하여 모든 구성 요소의 버전 *1.0*을 어떻게 실행하는지를 보여줍니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-01.png)

[Azure-Samples/aks-voting-app][github-azure-sample] GitHub 리포지토리에서 이 문서와 함께 수행해야 하는 아티팩트를 사용할 수 있습니다. 다음과 같이 아티팩트를 다운로드하거나 리포지토리를 복제할 수 있습니다.

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

다운로드/복제된 리포지토리의 다음 폴더로 변경 하고 이 폴더에서 모든 후속 단계를 실행합니다.

```console
cd scenarios/intelligent-routing-with-istio
```

먼저 다음과 같이 *투표*라고 명명된 샘플 AKS Voting 앱에 대한 AKS 클러스터에 네임스페이스를 만듭니다.

```console
kubectl create namespace voting
```

`istio-injection=enabled`를 사용하여 네임스페이스를 레이블합니다. 이 레이블은 이 네임스페이스에서 모든 사용자 Pod에 사이드카로 istio-proxies를 자동으로 삽입하도록 Istio에게 지시합니다.

```console
kubectl label namespace voting istio-injection=enabled
```

이제 AKS Voting 앱에 대한 구성 요소를 만들어 보겠습니다. 이전 단계에서 만든 *투표* 네임스페이스에 이 구성 요소를 만듭니다.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

다음 예제 출력에서는 성공적으로 만들어진 리소스를 보여줍니다.

```
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
kubectl get pods -n voting
```

다음 예제 출력에서는 *voting-app* Pod의 세 가지 인스턴스 및 *voting-analytics*와 *voting-storage* Pod의 단일 인스턴스가 있음을 보여 줍니다. 각 Pod에는 두 개의 컨테이너가 있습니다. 이 컨테이너 중 하나는 구성 요소이며 다른 하나는 *istio-proxy*입니다.

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

Pod에 대한 정보를 보려면 [kubectl describe pod][kubectl-describe]을 사용합니다. Pod 이름을 이전 출력에서 사용자 AKS 클러스터의 Pod의 이름으로 바꿉니다.

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

*istio-proxy* 컨테이너는 다음 예제 출력과 같이 구성 요소에서 또는 구성 요소로 네트워크 트래픽을 관리하는 Istio에서 자동으로 삽입됩니다.

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.0.4
[...]
```

Istio [게이트웨이][istio-reference-gateway] 및 [가상 서비스][istio-reference-virtualservice]를 만들 때까지 투표 앱에 연결할 수 없습니다. 이러한 Istio 리소스는 기본 Istio 게이트웨이에서 애플리케이션으로 트래픽을 라우팅합니다.

> [!NOTE]
> *게이트웨이*는 인바운드 또는 아웃 바운드 HTTP 및 TCP 트래픽을 수신하는 서비스 메시의 가장자리에 있는 구성 요소입니다.
>
> *가상 서비스*는 하나 이상의 대상 서비스에 대 한 라우팅 규칙 세트를 정의합니다.

`istioctl` 클라이언트 이진 파일을 사용하여 게이트웨이 및 가상 서비스 yaml을 배포합니다. `kubectl apply` 명령과 같이 이러한 리소스에 배포되는 네임스페이스를 지정해야 합니다.

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

다음 명령을 사용하여 Istio 수신 게이트웨이의 IP 주소를 가져옵니다.

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

다음 예제 출력에서는 수신 게이트웨이의 IP 주소를 보여줍니다.

```
52.187.250.239
```

브라우저를 열고 IP 주소에 붙여 넣습니다. 샘플 AKS Voting 앱이 표시됩니다.

![Istio에서 실행 중인 AKS Voting 앱은 AKS 클러스터를 사용할 수 있습니다.](media/istio/deploy-app-01.png)

화면 맨 아래에 있는 정보는 앱에서 *Voting-app*의 버전 *1.0* 및 스토리지 옵션으로 버전 *1.0*(Redis)을 사용하고 있음을 보여줍니다.

## <a name="update-the-application"></a>애플리케이션 업데이트

분석 구성 요소의 새 버전을 배포합니다. 이 새 버전 *1.1*은 각 카테고리에 대한 수 외에도 합계 및 백분율을 표시합니다.

다음 다이어그램은 이 섹션의 끝부분에서 *voting-analytics* 구성 요소의 버전 *1.1*만이 *voting-app* 구성 요소에서 트래픽이 어떻게 라우팅되는 지를 보여줍니다. *voting-analytics* 구성 요소의 버전 *1.0*이 계속 실행되고 *voting-app* 서비스에서 참조하는 데도 불구하고 Istio 프록시는 트래픽을 허용하지 않습니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-02.png)

*voting-analytics* 구성 요소의 버전 *1.1*을 배포합니다.  *투표* 네임스페이스에서 이 구성 요소를 만듭니다.

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

이전 단계에서 가져온 Istio 수신 게이트웨이의 IP주소를 사용하여 다시 브라우저에서 샘플 AKS Voting 앱을 엽니다.

브라우저에서 아래에 표시된 두 가지 보기를 번갈아 보여줍니다. 단일 레이블 선택기만(`app: voting-analytics`)을 사용하는 *voting-analytics* 구성 요소에 대한 Kubernetes [서비스][kubernetes-service]를 사용하고 있으므로 Kubernetes는 해당 선택기와 일치하는 Pod 간에 라운드 로빈의 기본 동작을 사용합니다. 이 경우 *voting-analytics* Pod의 두 버전 *1.0* 및 *1.1*이 있습니다.

![AKS Voting 앱에서 실행 중인 분석 구성 요소의 버전 1.0입니다.](media/istio/deploy-app-01.png)

![AKS Voting 앱에서 실행 중인 분석 구성 요소의 버전 1.1입니다.](media/istio/update-app-01.png)

다음과 같이 *voting-analytics* 구성 요소의 두 버전 간에 전환을 시각화할 수 있습니다. 사용자 Istio 수신 게이트웨이의 IP 주소를 사용해야 합니다.

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

다음 예제 출력은 버전 간에 사이트 전환으로 반환된 웹 사이트의 관련 부분을 보여 줍니다.

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>애플리케이션의 버전 1.1로의 트래픽 잠그기

이제 *voting-analytics* 구성 요소의 버전 *1.1*만으로의 트래픽과 *voting-storage* 구성 요소의 버전 *1.0*로의 트래픽을 잠가 보겠습니다. 그런 다음, 모든 다른 구성 요소에 대한 라우팅 규칙을 정의합니다.

> * *가상 서비스*는 하나 이상의 대상 서비스에 대 한 라우팅 규칙 세트를 정의합니다.
> * *대상 규칙*은 트래픽 정책 및 특정 정책 버전을 정의합니다.
> * *정책*은 어떤 인증 방법이 워크 로드에 승인될지 있는 지를 정의합니다.

`istioctl` 이진 바이너리를 사용하여 *voting-app*에서 가상 서비스 정의를 바꾸고 다른 구성 요소에 대한 [대상 규칙][istio-reference-destinationrule] 및 [가상 서비스][istio-reference-virtualservice]를 추가합니다.

또한 [정책][istio-reference-policy]을 *투표* 네임스페이스에 추가하여 서비스 간에 모든 통신이 상호 TLS 및 클라이언트 인증서를 사용하여 보호되는지 확인합니다.

사용자가 바꿀 *voting-app*에 대한 기존 가상 서비스 정의가 있으므로 다음과 같이 `istioctl replace` 명령을 사용합니다.

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

다음 예제 출력은 Istio 가상 서비스가 성공적으로 업데이트되었음을 보여줍니다.

```
Updated config virtual-service/voting/voting-app to revision 141902
```

다음으로 `istioctl create` 명령을 사용하여 다른 모든 구성 요소에 대한 새 정책, 새 대상 규칙 및 가상 서비스를 추가합니다.

* 정책은 상호 TLS가 *투표* 네임스페이스 내 서비스 간에 수행되는지 확인하도록 `peers.mtls.mode`를 `STRICT`으로 설정합니다.
* 또한 모든 대상 규칙에서 `trafficPolicy.tls.mode`를 `ISTIO_MUTUAL`로 설정합니다. Istio는 강력한 ID의 서비스를 제공하고 Istio가 투명하게 관리하는 상호 TLS와 클라이언트 인증서를 사용하여 서비스 간 통신을 보호합니다.

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

다음 예제 출력에서는 새 정책, 대상 규칙 및 가상 서비스가 성공적으로 만들어졌음을 보여줍니다.

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

다시 브라우저에서 AKS Voting 앱을 여는 경우 *Voting-analytics*의 버전 *1.1*은 *Voting-app* 구성 요소에서 사용됩니다.

![AKS Voting 앱에서 실행 중인 분석 구성 요소의 버전 1.1입니다.](media/istio/update-app-01.png)

다음과 같이 *voting-analytics* 구성 요소의 버전 *1.1*로만 라우팅되는 것을 보다 쉽게 시각화할 수 있습니다. 사용자 Istio 수신 게이트웨이의 IP 주소를 사용해야 합니다.

다음과 같이 *voting-analytics* 구성 요소의 버전 *1.1*로만 라우팅되는 것을 보다 쉽게 시각화할 수 있습니다. 사용자 Istio 수신 게이트웨이의 IP 주소를 사용해야 합니다.

```azurecli-interactive
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

다음 예제 출력은 반환된 웹 사이트의 관련 부분을 보여줍니다.

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Istio는 상호 TLS를 사용하여 각 서비스 간에 통신통신을 보호하는지 확인 합니다. 다음 명령을 사용하여 각 *voting-app* 서비스에 대한 TLS 설정을 확인합니다.

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

다음 예제 출력에서는 정책 및 대상 규칙을 통해 서비스에 대해 상호 TLS가 적용되는 것을 보여줍니다.

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>애플리케이션의 카나리아 릴리스 출시

이제 *Voting-app*, *Voting-analytics* 및 *Voting-storage* 구성 요소의 버전 *2.0*을 배포합니다. 새 *voting-storage* 구성 요소는 Redis 대신 MySQL을 사용하며 *voting-app* 및 *voting-analytics* 구성 요소는 새 *voting-storage* 구성 요소를 사용하도록 업데이트됩니다.

*voting-app* 구성 요소는 플래그 기능을 지원합니다. 이 기능 플래그를 사용 하면 사용자의 하위 집합에 대한 카나리 릴리스 기능을 테스트할 수 있습니다.

다음 다이어그램은 이 섹션의 끝에서 실행되는 것을 보여줍니다.

* *voting-app* 구성 요소의 버전 *1.0*, *voting-analytics* 구성 요소의 버전 *1.1* 및 *voting-storage* 구성 요소의 버전 *1.0*은 서로 통신할 수 있습니다.
* *voting-app* 구성 요소의 버전 *2.0*, *voting-analytics* 구성 요소의 버전 *2.0* 및 *voting-storage* 구성 요소의 버전 *2.0*은 서로 통신할 수 있습니다.
* *voting-app* 구성 요소의 버전 *2.0*은 특정 기능 플래그 집합을 사용하는 사용자에게 액세스만 할 수 있습니다. 이러한 변경은 쿠키를 통해 기능 플래그를 사용 하여 관리 됩니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-03.png)

먼저 이러한 새 구성 요소에 부합하도록 Istio 대상 규칙 및 가상 서비스를 업데이트합니다. 이러한 업데이트는 새 구성 요소로 트래픽을 잘못 라우팅되지는 않는지 사용자가 예기치 않은 액세스를 하지 않는지 확인합니다.

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

다음 예제 출력에서는 대상 규칙 및 가상 서비스가 성공적으로 업데이트됨을 보여줍니다.

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

다음으로 새 버전 *2.0* 구성 요소에 대한 Kubernetes 개체를 추가합니다. 또한 MySQL에 대한 *3306* 포트를 포함하여  *Voting-storage* 서비스를 업데이트합니다.

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

다음 예제 출력은 Kubernetes 개체가 성공적으로 업데이트되거나 만들어졌음을 보여줍니다.

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

모든 버전 *2.0* Pod가 실행될 때까지 대기합니다. [kubectl get pods][kubectl-get] 명령을 사용하여 *투표* 네임스페이스에서 모든 Pod를 봅니다.

```azurecli-interactive
kubectl get pods --namespace voting
```

이제 투표 애플리케이션의 *1.0* 버전 및 *2.0* 간에 전환할 수 있습니다. 화면 맨 아래에 있는 기능 플래그 토글에서 쿠키를 설정합니다. 이 쿠키는 사용자를 새 버전 *2.0*으로 라우팅하도록 *voting-app* 가상 서비스에서 사용됩니다.

![AKS Voting 앱 버전 1.0 - 기능 플래그가 설정되어 있지 않음](media/istio/canary-release-01.png)

![AKS Voting 앱 버전 2.0 - 기능 플래그가 설정되어 있음](media/istio/canary-release-02.png)

투표 수가 앱의 버전 간에 서로 차이가 납니다. 이러한 차이는 두 개의 다른 스토리지 백 엔드를 사용 중임을 강조 표시합니다.

## <a name="finalize-the-rollout"></a>출시 완료

카나리 릴리스를 성공적으로 테스트한 경우 모든 트래픽을 *voting-app* 구성 요소의 버전 *2.0*으로 라우팅하도록 *voting-app* Virtual Service를 업데이트합니다. 기능 플래그가 설정되어 있는지 여부에 관계없이 모든 사용자는 애플리케이션의 버전 *2.0*을 볼 수 있습니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-04.png)

대상 규칙을 업데이트하여 더 이상 사용하고 싶지 않은 구성 요소의 버전을 제거합니다. 그런 모든 가상 서비스를 업데이트하여 이 버전 참조를 중지합니다.

구성 요소의 이전 버전에 더 이상 트래픽이 없으므로 이러한 구성 요소에 대한 모든 배포를 안전하게 삭제할 수 있습니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-05.png)

이제 AKS Voting 앱의 새 버전을 성공적으로 출시합니다.

## <a name="next-steps"></a>다음 단계

또한 [Istio Bookinfo 애플리케이션 예제][istio-bookinfo-example]를 사용하여 추가 시나리오를 탐색할 수 있습니다.

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
