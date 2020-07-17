---
title: Consul 개요
description: Consul 개요 알아보기
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773998"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>개요

[Consul][consul]은 런타임 플랫폼 간에 서비스를 연결하고 보호하는 다중 데이터 센터 인식 서비스 네트워킹 솔루션입니다. [Connect][consul-features]는 서비스 메시 기능을 제공하는 구성 요소입니다.

## <a name="architecture"></a>Architecture

Consul은 기본적으로 [Envoy][envoy-proxy] 기반 [사이드카][consul-sidecar]로 구성된 데이터 평면을 제공합니다. Consul에는 플러그형 프록시 아키텍처가 있습니다. 이러한 지능형 프록시는 메시된 앱 및 워크로드에서 들어오고 나가는 모든 네트워크 트래픽을 제어합니다.

제어 평면은 다음 [구성 요소][consul-architecture]를 통해 구성 및 정책을 관리합니다.

- **서버** - 서버 모드에서 실행되는 Consul 에이전트로 Consul 클러스터 상태를 유지 관리합니다.

- **클라이언트** - 경량 클라이언트 모드로 실행되는 Consul 에이전트입니다. 각 컴퓨팅 노드에는 클라이언트 에이전트가 실행 중이어야 합니다. 이 클라이언트는 워크로드와 Consul 구성 간의 정책 및 구성을 조정합니다. 

다음 아키텍처 다이어그램에서는 데이터 평면 및 제어 평면 내의 여러 구성 요소가 상호 작용하는 방식을 보여 줍니다.

![Consul 구성 요소 및 아키텍처에 대한 개요입니다.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>선택 조건

워크로드에 대해 Consul을 평가할 때 다음 영역을 이해하고 고려해야 합니다.

- [Consul 원칙](#consul-principles)
- [Capabilities](#capabilities)
- [시나리오](#scenarios)


### <a name="consul-principles"></a>Consul 원칙

Consul 프로젝트를 [규정][consul-principles]하는 원칙은 다음과 같습니다.

- **API 기반** - 모든 구성 및 정책을 코드화합니다.

- **어디서나 실행 및 연결** - 런타임 플랫폼(Kubernetes, VM, 서버리스) 간에 워크로드를 연결합니다.

- **확장 및 통합** - 인프라에서 안전하게 워크로드를 연결합니다.


### <a name="capabilities"></a>기능

Consul은 다음과 같은 기능 집합을 제공합니다.

- **메시** – 게이트웨이(다중 데이터 센터), 가상 머신(클러스터 노드 외부), 서비스 동기화, 기본 제공 디버깅 옵션

- **프록시** – Envoy, 기본 제공 프록시, 플러그형, Windows 워크로드에 사용할 수 있는 l4 프록시

- **트래픽 관리** - 라우팅, 분할, 해결

- **정책** – 의도, ACL

- **보안** – 권한 부여, 인증, 암호화, SPIFFE 기반 ID, 외부 CA(자격 증명 모음), 인증서 관리 및 회전

- **가시성** – 메트릭, UI 대시보드, Prometheus, Grafana


### <a name="scenarios"></a>시나리오

Consul은 다음과 같은 시나리오에 적합합니다.

- 기존에 연결된 Consul 워크로드 확장

- 인증서 관리와 관련된 규정 준수 요구 사항

- 다중 클러스터 서비스 메시

- 서비스 메시에 포함될 VM 기반 워크로드



## <a name="next-steps"></a>다음 단계

다음 설명서에서는 AKS(Azure Kubernetes Service)에서 Consul을 설치하는 방법을 설명합니다.

> [!div class="nextstepaction"]
> [AKS(Azure Kubernetes Service)에서 Consul 설치][consul-install]

또한 다음과 같은 Consul 기능 및 아키텍처도 추가로 탐색할 수 있습니다.

- [Consul 시작 자습서][consul-getting-started]
- [Consul 기능][consul-features]
- [Consul 아키텍처][consul-architecture]
- [Consul - Connect 작동 방법][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
