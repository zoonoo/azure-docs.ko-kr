---
title: 자체 호스팅 Azure API 관리 게이트웨이 개요 | 마이크로 소프트 문서
description: 자체 호스팅 Azure API Management 게이트웨이가 조직이 하이브리드 및 멀티클라우드 환경에서 API를 관리하는 데 어떻게 도움이 되는지 알아봅니다.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513719"
---
# <a name="self-hosted-api-management-gateway-overview"></a>자체 호스팅 API 관리 게이트웨이 개요

이 문서에서는 자체 호스팅 게이트웨이 기능을 통해 하이브리드 및 다중 클라우드 API 관리를 가능하게 하고, 고급 아키텍처를 제공하며, 기본 기능을 강조합니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 기능이 미리 보기 상태입니다. 미리 보기 중에 자체 호스팅 게이트웨이는 추가 비용 없이 개발자 및 프리미엄 계층에서만 사용할 수 있습니다. 개발자 계층은 단일 자체 호스팅 게이트웨이 배포로 제한됩니다.

## <a name="hybrid-and-multi-cloud-api-management"></a>하이브리드 및 멀티 클라우드 API 관리

자체 호스팅 게이트웨이 기능은 하이브리드 및 다중 클라우드 환경에 대한 API 관리 지원을 확장하고 조직이 Azure의 단일 API 관리 서비스에서 온-프레미스 및 클라우드 전체에서 호스팅되는 API를 효율적이고 안전하게 관리할 수 있도록 합니다.

자체 호스팅 게이트웨이를 통해 고객은 API를 호스팅하는 동일한 환경에 컨테이너화된 버전의 API Management 게이트웨이 구성 요소를 유연하게 배포할 수 있습니다. 모든 자체 호스팅 게이트웨이는 페더레이션된 API 관리 서비스에서 관리되므로 고객에게 모든 내부 및 외부 API에서 가시성과 통합된 관리 환경을 제공합니다. 게이트웨이를 API 가까이에 배치하면 고객이 API 트래픽 흐름을 최적화하고 보안 및 규정 준수 요구 사항을 해결할 수 있습니다.

각 API 관리 서비스는 다음과 같은 주요 구성 요소로 구성됩니다.

-   Azure 포털, PowerShell 및 기타 지원되는 메커니즘을 통해 서비스를 구성하는 데 사용되는 API로 노출된 관리 평면입니다.
-   게이트웨이(또는 데이터 평면)는 API 요청을 프록시하고, 정책을 적용하고, 원격 분석을 수집합니다.
-   개발자가 API를 검색, 학습 및 온보온하는 데 사용하는 개발자 포털

기본적으로 이러한 모든 구성 요소는 Azure에 배포되어 API를 구현하는 백엔드가 호스팅되는 위치에 관계없이 모든 API 트래픽(아래 그림에 검은색 단색 화살표로 표시됨)이 Azure를 통해 흐르게 됩니다. 이 모델의 운영 단순성은 지연 시간 증가, 규정 준수 문제 및 경우에 따라 추가 데이터 전송 비용을 발생시게 됩니다.

![자체 호스팅 게이트웨이가 없는 API 트래픽 흐름](media/self-hosted-gateway-overview/without-gateways.png)

자체 호스팅 게이트웨이를 백 엔드 API 구현과 동일한 환경에 배포하고 API 관리 서비스에 추가하면 API 트래픽이 백 엔드 API로 직접 흐르도록 하여 대기 시간을 개선하고 데이터 전송 비용을 최적화하며 구현이 호스팅되는 위치에 관계없이 조직 내의 모든 API를 관리하고 검색할 수 있는 이점을 유지하면서 규정 준수를 준수합니다.

![자체 호스팅 게이트웨이를 통한 API 트래픽 흐름](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>포장 및 기능

자체 호스팅 게이트웨이는 모든 API 관리 서비스의 일부로 Azure에 배포된 관리 게이트웨이의 컨테이너화되고 기능적으로 동등한 버전입니다. 자체 호스팅 게이트웨이는 Microsoft 컨테이너 레지스트리에서 Linux 기반 Docker 컨테이너로 사용할 수 있습니다. Docker, Kubernetes 또는 데스크톱, 서버 클러스터 또는 클라우드 인프라에서 실행되는 다른 컨테이너 오케스트레이션 솔루션에 배포할 수 있습니다.

> [!IMPORTANT]
> 관리되는 게이트웨이에서 사용할 수 있는 일부 기능은 아직 미리 보기에서 사용할 수 없습니다. 가장 주목할 만한: 이벤트 허브 정책, 서비스 패브릭 통합, 다운스트림 HTTP/2로 로그. 자체 호스팅 게이트웨이에서 기본 제공 캐시를 사용할 수 있도록 할 계획은 없습니다.

## <a name="connectivity-to-azure"></a>Azure에 대한 연결

자체 호스팅 게이트웨이에는 포트 443에서 Azure에 대한 아웃바운드 TCP/IP 연결이 필요합니다. 각 자체 호스팅 게이트웨이는 단일 API 관리 서비스와 연결되어야 하며 관리 평면을 통해 구성됩니다. 자체 호스팅 게이트웨이는 다음을 위해 Azure에 대한 연결을 사용합니다.

-   1분마다 하트비트 메시지를 전송하여 상태 보고
-   정기적으로 (10 초마다) 확인하고 사용할 수 있습니다 때마다 구성 업데이트를 적용
-   요청 로그 및 메트릭을 Azure 모니터로 보내기(이렇게 하도록 구성된 경우)
-   이벤트 전송(이렇게 설정된 경우)으로 이벤트 보내기

Azure에 대한 연결이 끊어지면 자체 호스팅 게이트웨이는 구성 업데이트를 수신하거나 상태를 보고하거나 원격 분석을 업로드할 수 없습니다.

자체 호스팅 게이트웨이는 "장애 정적"으로 설계되었으며 Azure에 대한 일시적인 연결 손실에서 살아남을 수 있습니다. 로컬 구성 백업이 켜져 있거나 켜지 않고 배포할 수 있습니다. 이전의 경우 자체 호스팅 게이트웨이는 컨테이너 또는 포드에 연결된 영구 볼륨에 구성의 백업 복사본을 정기적으로 저장합니다.

구성 백업이 꺼져 있고 Azure에 대한 연결이 중단되는 경우:

-   실행 중인 자체 호스팅 게이트웨이는 구성의 메모리 내 복사본을 사용하여 계속 작동합니다.
-   중지된 자체 호스팅 게이트웨이를 시작할 수 없습니다.

구성 백업이 켜져 있고 Azure에 대한 연결이 중단되는 경우:

-   실행 중인 자체 호스팅 게이트웨이는 구성의 메모리 내 복사본을 사용하여 계속 작동합니다.
-   중지된 자체 호스팅 게이트웨이는 구성의 백업 복사본을 사용하기 시작합니다.

연결이 복원되면 중단의 영향을 받는 각 자체 호스팅 게이트웨이는 연결된 API 관리 서비스와 자동으로 다시 연결되고 게이트웨이가 "오프라인"인 동안 발생한 모든 구성 업데이트를 다운로드합니다.

## <a name="next-steps"></a>다음 단계

-   [이 주제에 대한 추가 배경에 대한 백서 읽기](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Docker에 자체 호스팅 게이트웨이 배포](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Kubernetes에 자체 호스팅 게이트웨이 배포](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
