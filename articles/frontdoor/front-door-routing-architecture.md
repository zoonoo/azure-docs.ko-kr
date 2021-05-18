---
title: Azure Front Door - 라우팅 아키텍처 | Microsoft Docs
description: 이 문서는 Front Door의 아키텍처에 대한 전체적인 관점을 이해하는 데 도움이 됩니다.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449184"
---
# <a name="routing-architecture-overview"></a>라우팅 아키텍처 개요

Azure Front Door가 클라이언트 요청을 수신하는 경우 두 가지 작업 중 하나를 수행합니다. 캐싱을 사용하도록 설정한 경우 응답하거나 요청을 해당 애플리케이션 백 엔드에 역방향 프록시로 전달합니다.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>트래픽 라우팅(Anycast)을 위해 Front Door 환경 선택

Azure Front Door 환경으로 라우팅되는 트래픽은 DNS(Domain Name System) 및 HTTP(Hypertext Transfer Protocol) 트래픽 둘 다에 [Anycast](https://en.wikipedia.org/wiki/Anycast)를 사용하여 사용자 요청이 가장 적은 네트워크 홉 수로 가장 가까운 환경에 도달할 수 있도록 합니다. 이 아키텍처는 분할 TCP의 혜택을 최대화하여 최종 사용자의 왕복 시간을 향상시킵니다. Front Door는 자체 환경을 기본 및 대체 "링(ring)"으로 구성합니다. 외부 링에는 사용자에게 더 가까운 환경이 있어서, 대기 시간을 단축합니다.  내부 링에는 이슈가 발생할 경우 외부 링 환경의 장애 조치(failover)를 처리할 수 있는 환경이 있습니다. 외부 링은 모든 트래픽의 기본 설정 대상이고 내부 링은 외부 링의 트래픽 오버플로를 처리하기 위한 것입니다. Front Door에서 제공하는 각 프런트 엔드 호스트 또는 도메인에는 기본 VIP(가상 인터넷 프로토콜 주소)가 할당되며 내부 및 외부 링의 환경에서 공지됩니다. 대체 VIP는 내부 링의 환경에서만 공지됩니다. 

이 아키텍처는 최종 사용자의 요청이 항상 가장 가까운 Front Door 환경에 도달하도록 합니다. 기본 설정 Front Door 환경이 비정상인 경우에도 모든 트래픽이 자동으로 다음 가장 가까운 환경으로 이동합니다.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Front Door 환경에 연결(분할 TCP)

[분할 TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy)는 긴 왕복 시간을 초래하는 연결을 작은 조각으로 분리하여 대기 시간과 TCP 문제를 줄이는 기법입니다. 최종 사용자에게 더 가까운 Front Door 환경을 사용하면 TCP 연결이 Front Door 환경 내에서 종료됩니다. 애플리케이션 백 엔드에 대한 RTT(왕복 시간)가 큰 TCP 연결은 두 개의 개별 연결로 분할됩니다. 최종 사용자와 Front Door 환경 간의 “짧은 연결”은 세 번의 긴 왕복 대신 세 번의 짧은 왕복을 통해 연결이 설정됨을 의미하며 대기 시간이 절약됩니다. Front Door 환경과 백 엔드 간의 “긴 연결”을 미리 설정하고 다른 최종 사용자 요청에 재사용하여 연결 시간을 절약할 수 있습니다. 분할 TCP의 효과는 SSL/TLS(전송 계층 보안) 연결을 설정할 때 배가됩니다. 연결을 보호하기 위해 더 많은 왕복이 사용되기 때문입니다.

## <a name="processing-request-to-match-a-routing-rule"></a>라우팅 규칙과 일치하도록 요청 처리
연결을 설정하고 TLS 핸드셰이크를 완료한 후 Front Door 환경에 요청이 도착할 때 수행하는 첫 번째 단계는 회람 규칙에 매칭하는 것입니다. 매칭은 Front Door의 구성에서 요청을 매칭할 특정 회람 규칙으로 결정됩니다. 자세한 내용은 Front Door에서 [경로 매칭](front-door-route-matching.md)이 수행되는 방법에 대해 읽어보세요.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>라우팅 규칙에 대해 백 엔드 풀에서 사용 가능한 백 엔드 식별
Front Door가 들어오는 요청에 대해 회람 규칙을 매칭한 후 다음 단계는 캐싱이 없는 경우 회람 규칙과 연결된 백 엔드 풀의 상태 프로브 상태를 가져오는 것입니다. 자세한 내용은 Front Door에서 [상태 프로브](front-door-health-probes.md)를 사용하여 백엔드 상태를 모니터링하는 방법에 대해 읽어보세요.

## <a name="forwarding-the-request-to-your-application-backend"></a>애플리케이션 백엔드에 요청 전달
마지막으로, 캐싱이 구성되지 않은 경우 사용자 요청은 [라우팅 방법](front-door-routing-methods.md) 구성에 따라 “최적” 백 엔드로 전달됩니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
