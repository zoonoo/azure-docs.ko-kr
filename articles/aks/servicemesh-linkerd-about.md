---
title: 링커드 개요
description: 링커드 개요 얻기
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593770"
---
# <a name="linkerd"></a>링커드 ()링커드 ()와

## <a name="overview"></a>개요

[링커드는][linkerd] 사용하기 쉽고 가벼운 서비스 메쉬입니다.

## <a name="architecture"></a>Architecture

Linkerd는 초경량 [링커드][linkerd-proxy] 전문 프록시 사이드카로 구성된 데이터 평면을 제공합니다. 이러한 지능형 프록시는 메시된 앱 및 워크로드의 모든 네트워크 트래픽을 제어합니다. 프록시는 [Prometheus][prometheus] 메트릭 끝점을 통해 메트릭을 노출합니다.

제어 평면은 다음 [구성 요소를][linkerd-architecture]통해 구성 및 집계된 원격 분석을 관리합니다.

- **컨트롤러** - Linkerd CLI 및 대시보드를 구동하는 API를 제공합니다. 프록시에 대한 구성을 제공합니다.

- **탭** - 요청 및 응답에 대한 실시간 시계를 설정합니다.

- **ID** - 서비스 간에 mTLS를 허용하는 ID 및 보안 기능을 제공합니다.

- **웹** - 링커드 대시보드를 제공합니다.


다음 아키텍처 다이어그램에서는 데이터 평면 및 제어 평면 내의 다양한 구성 요소가 상호 작용하는 방법을 보여 줍니다.


![Linkerd 구성 요소 및 아키텍처 개요입니다.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>선택 기준

워크로드에 대해 Linkerd를 평가할 때 다음 영역을 이해하고 고려하는 것이 중요합니다.

- [설계 원칙](#design-principles)
- [기능](#capabilities)
- [시나리오](#scenarios)


### <a name="design-principles"></a>디자인 원칙

다음 디자인 원칙은 Linkerd 프로젝트를 [안내합니다.][design-principles]

- **간단하게 유지** - 사용하기 쉽고 이해해야합니다.

- **리소스 요구 사항 최소화** - 최소한의 성능 및 리소스 비용을 부과합니다.

- **Just Work** - 기존 응용 프로그램을 중단하지 말고 복잡한 구성이 필요하지 않습니다.


### <a name="capabilities"></a>기능

Linkerd는 다음과 같은 기능 집합을 제공합니다.

- **메시** – 디버깅 옵션 내장

- **트래픽 관리** – 분할, 시간 시간, 재시도, 정보

- **보안** – 암호화(mTLS), 24시간마다 자동 회전되는 인증서

- **관찰성** – 황금 지표, 탭, 추적, 서비스 프로필 및 경로 메트릭당, 토폴로지 그래프가 있는 웹 대시보드, 프로메테우스, 그라파나


### <a name="scenarios"></a>시나리오

Linkerd는 다음 시나리오에 적합하고 제안됩니다.

- 필수 기능 요구 사항만으로 간단하게 사용

- 관찰 가능성 및 간단한 트래픽 관리에 중점을 둔 낮은 대기 시간, 낮은 오버헤드


## <a name="next-steps"></a>다음 단계

다음 설명서에서는 AKS(Azure Kubernetes Service)에 Linkerd를 설치하는 방법을 설명합니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes 서비스(AKS)에 링커드 설치][linkerd-install]

Linkerd 기능 및 아키텍처를 더 자세히 살펴볼 수도 있습니다.

- [링커드 피쳐][linkerd-features]
- [링커드 아키텍처][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md