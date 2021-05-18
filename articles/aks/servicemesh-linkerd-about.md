---
title: Linkerd 개요
description: Linkerd 개요 알아보기
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77593770"
---
# <a name="linkerd"></a>linkerd

## <a name="overview"></a>개요

[Linkerd][linkerd]는 사용하기 쉽고 간단한 서비스 메시입니다.

## <a name="architecture"></a>Architecture

Linkerd는 초경량의 [Linkerd][linkerd-proxy] 다루고 proxy 사이드카로 구성된 데이터 평면을 제공합니다. 이러한 지능형 프록시는 메시된 앱 및 워크로드에서 들어오고 나가는 모든 네트워크 트래픽을 제어합니다. 또한 프록시는 [프로메테우스][prometheus] 메트릭 엔드포인트를 통해 메트릭을 노출합니다.

제어 평면은 다음 [구성 요소][linkerd-architecture]를 통해 구성, 정책 및 원격 분석을 관리합니다.

- **컨트롤러** -Linkerd CLI 및 대시보드를 구동하는 api를 제공합니다. 프록시에 대한 구성을 제공합니다.

- **탭** - 요청 및 응답에 대한 실시간 감시를 하여 설정합니다.

- **ID** -서비스 간 mTLS를 허용하는 ID 및 보안 기능을 제공합니다.

- **웹** -Linkerd 대시보드를 제공합니다.


다음 아키텍처 다이어그램에서는 데이터 평면 및 제어 평면 내의 여러 구성 요소가 상호 작용하는 방식을 보여 줍니다.


![Linkerd 구성 요소 및 아키텍처 개요입니다.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>선택 조건

워크로드에 대해 Linkerd를 평가할 때 다음 영역을 이해하고 고려하는 점이 중요합니다.

- [디자인 원칙](#design-principles)
- [Capabilities](#capabilities)
- [시나리오](#scenarios)


### <a name="design-principles"></a>디자인 원칙

다음 설계 원리가 Linkerd 프로젝트를 [안내][design-principles]합니다.

- **간단하게 유지**-쉽게 사용하고 이해해야 합니다.

- **리소스 요구 사항 최소화** -최소한의 성능 및 리소스 비용을 부과합니다.

- **오로지 작업** -기존 애플리케이션을 중단하지 않으며 복잡한 구성을 필요로 하지 않습니다.


### <a name="capabilities"></a>기능

Linkerd는 다음과 같은 기능 집합을 제공합니다.

- **메시** – 기본 제공 디버깅 옵션

- **트래픽 관리** – 분할, 시간 제한, 다시 시도, 수신

- **보안** – 암호화 (mTLS), 24시간 마다 자동순환하는 인증서

- **관찰성** – 골든 메트릭, 탭, 추적, 서비스 프로필과 경로 메트릭 별, 토폴로지 그래프가 포함된 웹 대시보드, 프로메테우스, grafana


### <a name="scenarios"></a>시나리오

Linkerd는 다음과 같은 시나리오에 적합하고 제안됩니다.

- 필수 기능 요구 사항 집합만 사용하는 것이 간단합니다

- 관찰성 및 간단한 트래픽 관리에 집중된 낮은 대기 시간, 낮은 오버헤드


## <a name="next-steps"></a>다음 단계

다음 설명서에서는 Azure Kubernetes Service(AKS)에서 Linkerd를 설치하는 방법을 설명합니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes Service(AKS)에서 Linkerd 설치][linkerd-install]

또한 다음과 같은 Linkerd 기능 및 아키텍처도 추가로 탐색할 수 있습니다.

- [Linkerd 기능][linkerd-features]
- [Linkerd 아키텍처][linkerd-architecture]

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