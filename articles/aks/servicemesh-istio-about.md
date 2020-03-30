---
title: 이시오 개요
description: 이시오 개요 보기
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593903"
---
# <a name="istio"></a>이시오 섬

## <a name="overview"></a>개요

[Istio는][istio] 완전한 기능을 갖춘 사용자 정의 및 확장 가능한 서비스 메시입니다.

## <a name="architecture"></a>Architecture

Istio는 [Envoy][envoy-proxy]기반 사이드카로 구성된 데이터 평면을 제공합니다. 이러한 지능형 프록시는 메시된 앱 및 워크로드의 모든 네트워크 트래픽을 제어합니다.

제어 평면은 다음 [구성 요소를][what-is-istio]통해 구성, 정책 및 원격 분석을 관리합니다.

- **믹서** - 액세스 제어 및 사용 정책을 적용합니다. [Prometheus로][prometheus]푸시되는 프록시에서 원격 분석을 수집합니다.

- **파일럿** - 프록시에 대한 서비스 검색 및 트래픽 관리 정책/구성을 제공합니다.

- **요새** - 서비스 간에 mTLS를 허용하는 ID 및 보안 기능을 제공합니다.

- **갤리** - 구성 요소에 대한 구성을 요약하고 제공합니다.

다음 아키텍처 다이어그램에서는 데이터 평면 및 제어 평면 내의 다양한 구성 요소가 상호 작용하는 방법을 보여 줍니다.


![Istio 구성 요소 및 아키텍처 개요.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>선택 기준

워크로드에 대해 Istio를 평가할 때 다음 영역을 이해하고 고려하는 것이 중요합니다.

- [설계 목표](#design-goals)
- [기능](#capabilities)
- [시나리오](#scenarios)


### <a name="design-goals"></a>설계 목표

다음은 Istio 프로젝트를 [안내합니다.][design-goals]

- **투명성 극대화** - 최소한의 작업으로 채택을 허용하여 시스템에서 실제 가치를 얻을 수 있습니다.

- **확장성** - 변화하는 요구에 맞게 성장하고 적응할 수 있어야 합니다.

- **이식성** - 클라우드, 온-프레미스 등 다양한 환경에서 쉽게 실행할 수 있습니다.

- **정책 균일성** - 다양한 리소스에 대한 정책 정의의 일관성입니다.


### <a name="capabilities"></a>기능

Istio는 다음과 같은 기능 집합을 제공합니다.

- **메시** – 게이트웨이(다중 클러스터), 가상 머신(메시 확장)

- **트래픽 관리** – 라우팅, 분할, 시간 시간, 회로 차단기, 재시도, 송신, 송신

- **정책** – 액세스 제어, 속도 제한, 할당량, 사용자 지정 정책 어댑터

- **보안** – 인증(jwt), 승인, 암호화(mTLS), 외부 CA(HashiCorp Vault)

- **관찰성** – 황금 색 지표, 거울, 추적, 사용자 정의 어댑터, 프로메테우스, 그라파나

### <a name="scenarios"></a>시나리오

Istio는 다음 시나리오에 적합하고 제안됩니다.

- 확장성 및 풍부한 기능 요구

- VM 기반 워크로드를 포함하는 메시 확장

- 다중 클러스터 서비스 메시

## <a name="next-steps"></a>다음 단계

다음 설명서에서는 AKS(Azure Kubernetes Service)에 Istio를 설치하는 방법을 설명합니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes Service(AKS)에서 Istio 설치][istio-install]

Istio 개념 및 추가 배포 모델을 자세히 살펴볼 수도 있습니다.

- [이시오 개념][what-is-istio]
- [이시티오 배포 모델][deployment-models]

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
