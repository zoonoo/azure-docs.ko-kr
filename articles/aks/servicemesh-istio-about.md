---
title: Istio 개요
description: Istio 개요 알아보기
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77593903"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>개요

[Istio][istio]는 전체 기능을 갖춘 사용자 지정 및 확장 가능한 서비스 메시입니다.

## <a name="architecture"></a>Architecture

Istio는 [Envoy][envoy-proxy] 기반 사이드카로 구성된 데이터 평면을 제공합니다. 이러한 지능형 프록시는 메시된 앱 및 워크로드에서 들어오고 나가는 모든 네트워크 트래픽을 제어합니다.

제어 평면은 다음 [구성 요소][what-is-istio]를 통해 구성, 정책 및 원격 분석을 관리합니다.

- **Mixer** - 액세스 제어 및 사용량 정책을 적용합니다. [Prometheus][prometheus]로 푸시되는 프록시에서 원격 분석을 수집합니다.

- **Pilot** -프록시에 대한 서비스 검색 및 트래픽 관리 정책/구성을 제공합니다.

- **Citadel** -서비스 간 mTLS를 허용하는 ID 및 보안 기능을 제공합니다.

- **Galley** -구성 요소에 대한 구성을 추상화하여 제공합니다.

다음 아키텍처 다이어그램에서는 데이터 평면 및 제어 평면 내의 여러 구성 요소가 상호 작용하는 방식을 보여 줍니다.


![Istio 구성 요소 및 아키텍처 개요입니다.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>선택 조건

워크로드에 대해 Istio를 평가할 때 다음 영역을 이해하고 고려해야 합니다.

- [설계 목표](#design-goals)
- [Capabilities](#capabilities)
- [시나리오](#scenarios)


### <a name="design-goals"></a>설계 목표

다음 설계 목표는 Istio 프로젝트를 [안내][design-goals]합니다.

- **투명도 최대화** - 최소 작업량을 채택하도록 허용하여 시스템에서 실제 값을 얻을 수 있습니다.

- **확장성** - 변경되는 필요에 따라 확장 및 조정할 수 있어야 합니다.

- **이식성** -클라우드, 온-프레미스와 같이 다양한 종류의 환경에서 쉽게 실행됩니다.

- **정책 균일성** - 다양한 리소스에 걸쳐 정책 정의에 일관성이 있습니다.


### <a name="capabilities"></a>기능

Istio는 다음과 같은 기능 집합을 제공합니다.

- **메시** – 게이트웨이(다중 클러스터), 가상 머신(메시 확장)

- **트래픽 관리** – 라우팅, 분할, 시간 제한, 회로 차단기, 다시 시도, 수신, 송신

- **정책** - 액세스 제어, 요금 제한, 할당량, 사용자 지정 정책 어댑터

- **보안** – 인증(jwt), 권한 부여, 암호화(mTLS), 외부 CA(HashiCorp 자격 증명 모음)

- **관찰성** – 골든 메트릭, 미러, 추적, 사용자 지정 어댑터, 프로메테우스, grafana

### <a name="scenarios"></a>시나리오

Istio는 다음과 같은 시나리오에 적합하고 제안됩니다.

- 확장성 및 풍부한 기능 집합 필요

- VM 기반 워크로드를 포함하도록 메시 확장

- 다중 클러스터 서비스 메시

## <a name="next-steps"></a>다음 단계

다음 설명서에서는 AKS(Azure Kubernetes Service)에서 Istio를 설치하는 방법을 설명합니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes Service(AKS)에서 Istio 설치][istio-install]

또한 Istio 개념 및 추가 배포 모델을 자세하게 탐색할 수 있습니다.

- [Istio 개념][what-is-istio]
- [Istio 배포 모델][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
