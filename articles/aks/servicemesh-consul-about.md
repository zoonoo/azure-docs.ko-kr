---
title: 영사의 개요
description: 영사의 개요를 얻기
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594212"
---
# <a name="consul"></a>영사

## <a name="overview"></a>개요

[영사는][consul] 런타임 플랫폼 전반에 걸쳐 서비스를 연결하고 보호하는 멀티 데이터 센터 인식 서비스 네트워킹 솔루션입니다. [연결은][consul-features] 서비스 메시 기능을 제공하는 구성 요소입니다.

## <a name="architecture"></a>Architecture

영사는 기본적으로 [Envoy][envoy-proxy]기반 [사이드카로][consul-sidecar] 구성된 데이터 평면을 제공합니다. 영사는 플러그 가능한 프록시 아키텍처를 가지고 있습니다. 이러한 지능형 프록시는 메시된 앱 및 워크로드의 모든 네트워크 트래픽을 제어합니다.

제어 평면은 다음 [구성 요소를][consul-architecture]통해 구성 및 정책을 관리합니다.

- **서버** - 영사 클러스터 상태를 유지하는 서버 모드에서 실행되는 영사 에이전트입니다.

- **클라이언트** - 경량 클라이언트 모드에서 실행되는 영사 에이전트입니다. 각 계산 노드에는 클라이언트 에이전트가 실행되어야 합니다. 이 클라이언트는 워크로드와 영사 구성 간의 구성 및 정책을 중개합니다. 

다음 아키텍처 다이어그램에서는 데이터 평면 및 제어 평면 내의 다양한 구성 요소가 상호 작용하는 방법을 보여 줍니다.

![영사 구성 요소 및 아키텍처의 개요.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>선택 기준

워크로드에 대한 영사를 평가할 때 다음 영역을 이해하고 고려하는 것이 중요합니다.

- [영사 원칙](#consul-principles)
- [기능](#capabilities)
- [시나리오](#scenarios)


### <a name="consul-principles"></a>영사 원칙

다음 원칙은 영사 프로젝트를 [안내합니다.][consul-principles]

- **API 기반** - 모든 구성 및 정책을 분류합니다.

- **어디서나 실행 및 연결** - 런타임 플랫폼(Kubernetes, VM, 서버리스)에서 워크로드를 연결합니다.

- **확장 및 통합** - 인프라 전반에서 워크로드를 안전하게 연결합니다.


### <a name="capabilities"></a>기능

영사는 다음과 같은 기능 집합을 제공합니다.

- **메시** – 게이트웨이(다중 데이터 센터), 가상 머신(클러스터 노드 제외), 서비스 동기화, 디버깅 옵션 내장

- **프록시** – Envoy, 기본 제공 프록시, 플러그 가능, Windows 워크로드에 사용할 수 있는 l4 프록시

- **트래픽 관리** – 라우팅, 분할, 해결

- **정책** – 의도, ACL

- **보안** – 승인, 인증, 암호화, SPIFFE 기반 ID, 외부 CA(Vault), 인증서 관리 및 회전

- **관찰성** – 메트릭, UI 대시보드, 프로메테우스, 그라파나


### <a name="scenarios"></a>시나리오

영사는 다음과 같은 시나리오에 적합하고 제안 :

- 기존 영사 연결 워크로드 확장

- 인증서 관리와 관련된 규정 준수 요구 사항

- 다중 클러스터 서비스 메시

- 서비스 메시에 포함할 VM 기반 워크로드



## <a name="next-steps"></a>다음 단계

다음 설명서에서는 AKS(Azure Kubernetes Service)에 영사를 설치하는 방법을 설명합니다.

> [!div class="nextstepaction"]
> [Azure Kubernetes 서비스(AKS)에 영사 설치][consul-install]

또한 영사 기능 및 아키텍처를 더 자세히 살펴볼 수 있습니다.

- [영사 특징][consul-features]
- [영사 건축][consul-architecture]
- [영사 - 연결 작동 방식][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
