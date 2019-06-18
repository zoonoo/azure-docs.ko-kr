---
title: Azure Kubernetes Service(AKS)에서 Istio를 사용하여 인텔리전트 라우팅 및 카나리아 릴리스
description: Azure Kubernetes Service(AKS) 클러스터에서 Istio를 사용하여 인텔리전트 라우팅을 제공하고 카나리아 릴리스를 배포하는 방법 알아보기
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702218"
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
> 이 시나리오는 Istio 버전에 대해 테스트 된 `1.1.3`합니다.

이 문서에 설명 된 단계 가정 AKS 클러스터를 만든 (Kubernetes `1.11` 이상에서 RBAC를 사용 하 여 사용 하도록 설정 하 고) 설정 하 고는 `kubectl` 클러스터와 연결 합니다. Istio 클러스터에 설치 해야 합니다.

이러한 항목 중 하나를 사용 하 여 도움이 필요한 경우 다음 참조를 [AKS 빠른 시작] [ aks-quickstart] 하 고 [AKS에서 설치 Istio] [ istio-install] 지침.

## <a name="about-this-application-scenario"></a>이 애플리케이션 시나리오에 대한 정보

두 개의 투표 옵션을 제공 하는 샘플 AKS 투표 앱 (**고양이** 하거나 **Dogs**) 사용자에 게 합니다. 각 옵션에 대한 투표 수를 유지하는 스토리지 구성 요소가 있습니다. 또한 각 옵션의 득표에 대한 세부 정보를 제공하는 분석 구성 요소가 있습니다.

이 응용 프로그램 시나리오에서 버전을 배포 하 여 시작 `1.0` 투표 앱 및 버전 `1.0` 분석 구성 요소입니다. 분석 구성 요소는 투표 수에 대한 간단한 수를 제공합니다. 분석 구성 요소 확인 하 고 투표 앱 버전을 사용 하 여 상호 작용 `1.0` Redis에서 지원 되는 저장소 구성 요소입니다.

분석 구성 요소 버전으로 업그레이드할 `1.1`, 개수를 제공 하며 이제 합계 및 백분율입니다.

사용자 테스트 버전의 하위 집합 `2.0` 카나리아 릴리스를 통해 앱의 합니다. 이 새 버전에는 MySQL 데이터베이스에서 지원 되는 스토리지 구성 요소를 사용합니다.

확신할 되 면 해당 버전 `2.0` 하위 사용자에서 예상 대로 작동 롤아웃하기 버전 `2.0` 모든 사용자에 게 합니다.

## <a name="deploy-the-application"></a>애플리케이션 배포

Azure Kubernetes Service(AKS) 클러스터에 애플리케이션을 배포합니다. 다음 다이어그램에서는이 섹션의 끝에서 실행 되는 것을 보여 줍니다. 버전 `1.0` Istio 수신 게이트웨이 통해 처리 하는 인바운드 요청을 사용 하 여 모든 구성 요소:

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-01.png)

[Azure-Samples/aks-voting-app][github-azure-sample] GitHub 리포지토리에서 이 문서와 함께 수행해야 하는 아티팩트를 사용할 수 있습니다. 다음과 같이 아티팩트를 다운로드하거나 리포지토리를 복제할 수 있습니다.

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

다운로드/복제된 리포지토리의 다음 폴더로 변경 하고 이 폴더에서 모든 후속 단계를 실행합니다.

```console
cd scenarios/intelligent-routing-with-istio
```

먼저, 라는 샘플 AKS 투표 앱에 대 한 AKS 클러스터에는 네임 스페이스를 만들어야 `voting` 다음과 같습니다.

```azurecli
kubectl create namespace voting
```

`istio-injection=enabled`를 사용하여 네임스페이스를 레이블합니다. 이 레이블은 이 네임스페이스에서 모든 사용자 Pod에 사이드카로 istio-proxies를 자동으로 삽입하도록 Istio에게 지시합니다.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

이제 AKS Voting 앱에 대한 구성 요소를 만들어 보겠습니다. 이러한 구성 요소 만들기를 `voting` 이전 단계에서 만든 네임 스페이스입니다.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

다음 예제 출력에는 만들려는 리소스를 보여 줍니다.

```console
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

```azurecli
kubectl get pods -n voting
```

다음 예제 출력에서는 인스턴스의 수가 세 가지는 `voting-app` pod와 둘 다의 단일 인스턴스를 `voting-analytics` 및 `voting-storage` pod입니다. 각 Pod에는 두 개의 컨테이너가 있습니다. 이러한 컨테이너 중 하나는 구성 요소 이며 다른 하나는 `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Pod에 대한 정보를 보려면 [kubectl describe pod][kubectl-describe]을 사용합니다. Pod 이름을 이전 출력에서 사용자 AKS 클러스터의 Pod의 이름으로 바꿉니다.

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

`istio-proxy` 다음 예제 출력 에서처럼 컨테이너에 구성 요소에서 네트워크 트래픽을 관리 하는 Istio에 의해 삽입 되었는지 자동으로:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Istio [게이트웨이][istio-reference-gateway] 및 [가상 서비스][istio-reference-virtualservice]를 만들 때까지 투표 앱에 연결할 수 없습니다. 이러한 Istio 리소스는 기본 Istio 게이트웨이에서 애플리케이션으로 트래픽을 라우팅합니다.

> [!NOTE]
> **게이트웨이**는 인바운드 또는 아웃 바운드 HTTP 및 TCP 트래픽을 수신하는 서비스 메시의 가장자리에 있는 구성 요소입니다.
> 
> **가상 서비스**는 하나 이상의 대상 서비스에 대 한 라우팅 규칙 세트를 정의합니다.

사용 하 여는 `kubectl apply` 게이트웨이 및 가상 서비스 yaml을 배포 하는 명령입니다. 이러한 리소스에 배포 되는 네임 스페이스를 지정 해야 합니다.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

다음 예제 출력에서는 새 게이트웨이 및 생성 되는 가상 서비스를 보여 줍니다.

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

다음 명령을 사용하여 Istio 수신 게이트웨이의 IP 주소를 가져옵니다.

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

다음 예제 출력에서는 수신 게이트웨이의 IP 주소를 보여줍니다.

```
20.188.211.19
```

브라우저를 열고 IP 주소에 붙여 넣습니다. 샘플 AKS Voting 앱이 표시됩니다.

![Istio에서 실행 중인 AKS Voting 앱은 AKS 클러스터를 사용할 수 있습니다.](media/istio/deploy-app-01.png)

앱 버전을 사용 하는 화면 맨 아래에 정보를 표시 `1.0` 의 `voting-app` 버전과 `1.0` 의 `voting-storage` (Redis).

## <a name="update-the-application"></a>애플리케이션 업데이트

분석 구성 요소의 새 버전을 배포합니다. 이 새 버전 `1.1` 합계 및 각 범주에 대 한 개수 외에도 비율을 표시 합니다.

다음 다이어그램에서는 어떤 버전에만이 섹션의 끝에 실행 됩니다 `1.1` 의 우리의 `voting-analytics` 구성 요소에서 라우팅되는 트래픽을는 `voting-app` 구성 요소입니다. 도 버전 `1.0` 의 우리의 `voting-analytics` 구성 요소는 계속 실행 되며에서 참조 되는 `voting-analytics` 서비스 Istio 프록시에서 트래픽을 허용 하지 않습니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-02.png)

버전을 배포 해 보겠습니다 `1.1` 의 `voting-analytics` 구성 요소입니다. 이 구성 요소 만들기를 `voting` 네임 스페이스:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

다음 예제 출력에는 만들려는 리소스를 보여 줍니다.

```console
deployment.apps/voting-analytics-1-1 created
```

이전 단계에서 가져온 Istio 수신 게이트웨이의 IP주소를 사용하여 다시 브라우저에서 샘플 AKS Voting 앱을 엽니다.

브라우저에서 아래에 표시된 두 가지 보기를 번갈아 보여줍니다. Kubernetes를 사용 하므로 [서비스] [ kubernetes-service] 에 대 한 합니다 `voting-analytics` 만 단일 레이블에 선택기를 사용 하 여 구성 요소 (`app: voting-analytics`), 라운드 로빈 간의 기본 동작을 사용 하는 Kubernetes를 해당 선택기와 일치 하는 pod입니다. 이 경우 해당 버전이 모두 `1.0` 하 고 `1.1` 의 `voting-analytics` pod입니다.

![AKS Voting 앱에서 실행 중인 분석 구성 요소의 버전 1.0입니다.](media/istio/deploy-app-01.png)

![AKS Voting 앱에서 실행 중인 분석 구성 요소의 버전 1.1입니다.](media/istio/update-app-01.png)

두 버전 간에 전환 시각화할 수는 `voting-analytics` 다음과 같은 구성 요소입니다. 사용자 Istio 수신 게이트웨이의 IP 주소를 사용해야 합니다.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
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

이제 보겠습니다 버전에만 트래픽을 잠글 `1.1` 의 합니다 `voting-analytics` 구성 요소 및 버전 `1.0` 의 `voting-storage` 구성 요소입니다. 그런 다음, 모든 다른 구성 요소에 대한 라우팅 규칙을 정의합니다.

> * **가상 서비스**는 하나 이상의 대상 서비스에 대 한 라우팅 규칙 세트를 정의합니다.
> * **대상 규칙**은 트래픽 정책 및 특정 정책 버전을 정의합니다.
> * **정책**은 어떤 인증 방법이 워크 로드에 승인될지 있는 지를 정의합니다.

사용 하 여는 `kubectl apply` 명령에는 가상 서비스 정의 바꾸려면 하 `voting-app` 추가한 [대상 규칙] [ istio-reference-destinationrule] 및 [가상 서비스] [ istio-reference-virtualservice] 다른 구성 요소에 대 한 합니다. 추가 [정책] [ istio-reference-policy] 에 `voting` 네임 스페이스는 모든 서비스 간에 통신 하도록 하려면 클라이언트 인증서 및 상호 TLS를 사용 하 여 보호 됩니다.

* 정책에 `peers.mtls.mode` 로 설정 `STRICT` 내의 서비스 간 상호 TLS 적용 되는 `voting` 네임 스페이스입니다.
* 또한 설정 합니다 `trafficPolicy.tls.mode` 에 `ISTIO_MUTUAL` 모든 대상 규칙에 합니다. Istio는 강력한 ID의 서비스를 제공하고 Istio가 투명하게 관리하는 상호 TLS와 클라이언트 인증서를 사용하여 서비스 간 통신을 보호합니다.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

다음 예제 출력에서는 새 정책과 대상 규칙을 업데이트 하 고 생성 되는 가상 서비스를 보여 줍니다.

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

브라우저에서 다시 새 버전만 AKS Voting 앱을 열면 `1.1` 의 합니다 `voting-analytics` 구성 요소를 사용 합니다 `voting-app` 구성 요소입니다.

![AKS Voting 앱에서 실행 중인 분석 구성 요소의 버전 1.1입니다.](media/istio/update-app-01.png)

이제만 라우팅됩니다 버전을 시각화할 수 있습니다 `1.1` 의 프로그램 `voting-analytics` 다음과 같은 구성 요소입니다. 사용자 Istio 수신 게이트웨이의 IP 주소를 사용해야 합니다.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

다음 예제 출력은 반환된 웹 사이트의 관련 부분을 보여줍니다.

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

이제 확인는 Istio 상호 TLS 사용 하 여 각 서비스 간의 통신을 보호 하는 것입니다. 사용이 [authn tls 검사] [ istioctl-authn-tls-check] 명령을 `istioctl` 이진 형식을 사용 하는 클라이언트입니다.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

이 명령 집합을 네임 스페이스에 있으며의 레이블 집합을 일치 하는 모든 pod에서 지정된 된 서비스에 액세스 하는 방법에 대 한 정보를 제공 합니다.

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

PowerShell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

다음 출력 예와 상호 TLS 위의 쿼리는 마다 적용 되도록 보여 줍니다. 또한 출력에는 정책 및 상호 TLS를 적용 하는 대상 규칙을 보여 줍니다.

```console
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

이제 새 버전을 배포 해 보겠습니다 `2.0` 의 합니다 `voting-app`를 `voting-analytics`, 및 `voting-storage` 구성 요소입니다. 새 `voting-storage` Redis 대신 MySQL을 사용 하는 구성 요소 및 `voting-app` 하 고 `voting-analytics` 구성 요소는이 새 사용할 수 있도록 업데이트 됩니다 `voting-storage` 구성 요소입니다.

`voting-app` 구성 요소가 이제 기능 플래그를 지원 합니다. 이 기능 플래그를 사용 하면 사용자의 하위 집합에 대한 카나리 릴리스 기능을 테스트할 수 있습니다.

다음 다이어그램에서는 것은이 섹션의 끝에서 실행 합니다.

* 버전 `1.0` 의 합니다 `voting-app` 구성 요소, 버전 `1.1` 의 `voting-analytics` 구성 요소 및 버전 `1.0` 의 `voting-storage` 구성 요소가 서로 통신할 수 있습니다.
* 버전 `2.0` 의 합니다 `voting-app` 구성 요소, 버전 `2.0` 의 `voting-analytics` 구성 요소 및 버전 `2.0` 의 `voting-storage` 구성 요소가 서로 통신할 수 있습니다.
* 버전 `2.0` 의 `voting-app` 구성 요소는 특정 기능 플래그를 설정 하는 사용자가 액세스할 수만 있습니다. 이러한 변경은 쿠키를 통해 기능 플래그를 사용 하여 관리 됩니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-03.png)

먼저 이러한 새 구성 요소에 부합하도록 Istio 대상 규칙 및 가상 서비스를 업데이트합니다. 이러한 업데이트는 새 구성 요소로 트래픽을 잘못 라우팅되지는 않는지 사용자가 예기치 않은 액세스를 하지 않는지 확인합니다.

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

다음 예제 출력에서는 대상 규칙 및 업데이트 되는 가상 서비스를 보여 줍니다.

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

다음으로 새 버전에 대 한 Kubernetes 개체를 추가 해 보겠습니다 `2.0` 구성 요소입니다. 또한 업데이트 된 `voting-storage` 포함 하도록 서비스를 `3306` MySQL에 대 한 포트:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

다음 예제 출력은 Kubernetes 개체가 성공적으로 업데이트되거나 만들어졌음을 보여줍니다.

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

모든 버전까지 `2.0` pod가 실행 됩니다. 사용 된 [kubectl get pod] [ kubectl-get] 명령에 모든 pod를 볼 수는 `voting` 네임 스페이스:

```azurecli
kubectl get pods --namespace voting
```

이제 버전 간에 전환할 수 있어야 `1.0` 버전과 `2.0` 투표 응용 프로그램의 (카나리아). 화면 맨 아래에 있는 기능 플래그 토글에서 쿠키를 설정합니다. 이 쿠키를 사용 합니다 `voting-app` 새 버전으로 경로 사용자에 게 가상 서비스 `2.0`합니다.

![AKS Voting 앱 버전 1.0 - 기능 플래그가 설정되어 있지 않음](media/istio/canary-release-01.png)

![AKS Voting 앱 버전 2.0 - 기능 플래그가 설정되어 있음](media/istio/canary-release-02.png)

투표 수가 앱의 버전 간에 서로 차이가 납니다. 이러한 차이는 두 개의 다른 스토리지 백 엔드를 사용 중임을 강조 표시합니다.

## <a name="finalize-the-rollout"></a>출시 완료

카나리아 릴리스를 성공적으로 테스트 한 후 업데이트를 `voting-app` 가상 서비스 버전으로 모든 트래픽을 라우팅하도록 `2.0` 의 `voting-app` 구성 요소입니다. 모든 사용자는 다음 버전을 볼 `2.0` 기능 플래그가 설정 되어 있는지 여부에 관계 없이 응용 프로그램:

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-04.png)

대상 규칙을 업데이트하여 더 이상 사용하고 싶지 않은 구성 요소의 버전을 제거합니다. 그런 모든 가상 서비스를 업데이트하여 이 버전 참조를 중지합니다.

구성 요소의 이전 버전에 더 이상 트래픽이 없으므로 이러한 구성 요소에 대한 모든 배포를 안전하게 삭제할 수 있습니다.

![AKS Voting 앱 구성 요소 및 라우팅](media/istio/components-and-routing-05.png)

이제 AKS Voting 앱의 새 버전을 성공적으로 출시합니다.

## <a name="clean-up"></a>정리 

삭제 하 여 AKS 클러스터에서 사용 했습니다 AKS 투표 앱이이 시나리오에서는 제거할 수는 `voting` 같이 네임 스페이스:

```azurecli
kubectl delete namespace voting
```

다음 예제 출력을 보여 줍니다 AKS 투표 앱의 모든 구성 요소는 AKS 클러스터에서 제거 되었습니다.

```console
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
[istio-install]: ./istio-install.md
