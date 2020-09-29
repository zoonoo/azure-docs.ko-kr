---
title: Azure 전면 도어-라우팅 아키텍처 | Microsoft Docs
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
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449184"
---
# <a name="routing-architecture-overview"></a>라우팅 아키텍처 개요

Azure Front 도어가 클라이언트 요청을 수신 하는 경우 두 가지 중 하나를 수행 합니다. 캐싱을 사용 하도록 설정 하거나 해당 응용 프로그램 백 엔드에 역방향 프록시로 전달 하는 경우 응답 합니다.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>트래픽 라우팅(Anycast)을 위해 Front Door 환경 선택

Azure 전면 도어 환경으로 라우팅되는 트래픽은 DNS (도메인 이름 시스템) 및 HTTP (하이퍼텍스트 전송 프로토콜) 트래픽 모두에 [애니캐스트](https://en.wikipedia.org/wiki/Anycast) 를 사용 하 여 사용자 요청이 가장 적은 네트워크 홉에서 가장 가까운 환경에 도달할 수 있도록 합니다. 이 아키텍처는 분할 TCP의 이점을 극대화 하 여 최종 사용자에 게 더 나은 라운드트립 시간을 제공 합니다. Front Door는 자체 환경을 기본 및 대체 "링(ring)"으로 구성합니다. 외부 링에는 사용자에게 더 가까운 환경이 있어서, 대기 시간을 단축합니다.  내부 링에는 문제가 발생 하는 경우 외부 링 환경의 장애 조치 (failover)를 처리할 수 있는 환경이 있습니다. 외부 링은 모든 트래픽에 대 한 기본 목표 이며 내부 링은 외부 링에서 트래픽 오버플로를 처리 하는 것입니다. 프런트 도어에서 제공 하는 각 프런트 엔드 호스트 또는 도메인에는 기본 VIP (가상 인터넷 프로토콜 주소)가 할당 되며,이는 내부 및 외부 링에서 환경에 의해 알려집니다. 대체 VIP는 내부 링의 환경 에서만 알려집니다. 

이 아키텍처는 최종 사용자의 요청이 항상 가장 가까운 프런트 도어 환경에 도달할 수 있도록 합니다. 기본 설정 된 Front 도어 환경이 비정상 이더라도 모든 트래픽이 자동으로 가장 가까운 다음 환경으로 이동 합니다.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Front Door 환경에 연결(분할 TCP)

[분할 TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy)는 긴 왕복 시간을 초래하는 연결을 작은 조각으로 분리하여 대기 시간과 TCP 문제를 줄이는 기법입니다. 프런트 도어 환경에서 최종 사용자에 게 가까운 경우, TCP 연결은 프런트 도어 환경 내에서 종료 됩니다. 응용 프로그램 백 엔드에 대 한 RTT (왕복 시간)가 많은 TCP 연결은 두 개의 개별 연결로 분할 됩니다. 최종 사용자와 전면 도어 환경 간의 "짧은 연결"은 3 개의 긴 라운드트립 대신 3 개의 짧은 왕복을 통해 연결이 설정 되는 것을 의미 하며,이로 인해 대기 시간이 절약 됩니다. 프런트 도어 환경과 백 엔드 간의 "긴 연결"은 미리 설정 된 후 다른 최종 사용자가 다시 사용 하 여 연결 시간을 절약할 수 있습니다. 연결을 보호 하는 데 더 많은 왕복이 있으므로 SSL/TLS (전송 계층 보안) 연결을 설정할 때 분할 TCP의 효과를 곱합니다.

## <a name="processing-request-to-match-a-routing-rule"></a>라우팅 규칙과 일치하도록 요청 처리
연결을 설정 하 고 TLS 핸드셰이크를 완료 한 후 요청이 프런트 도어 환경에 도착 한 후 첫 번째 단계는 라우팅 규칙과 일치 시킵니다. 일치는 요청과 일치 하는 특정 라우팅 규칙에 대 한 프런트 도어 구성에 의해 결정 됩니다. 자세한 내용은 Front Door에서 [경로 매칭](front-door-route-matching.md)이 수행되는 방법에 대해 읽어보세요.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>라우팅 규칙에 대해 백 엔드 풀에서 사용 가능한 백 엔드 식별
프런트 도어가 들어오는 요청에 대 한 라우팅 규칙과 일치 하는 경우 다음 단계는 캐싱이 없는 경우 라우팅 규칙과 연결 된 백 엔드 풀에 대 한 상태 프로브 상태를 가져오는 것입니다. 자세한 내용은 Front Door에서 [상태 프로브](front-door-health-probes.md)를 사용하여 백엔드 상태를 모니터링하는 방법에 대해 읽어보세요.

## <a name="forwarding-the-request-to-your-application-backend"></a>애플리케이션 백엔드에 요청 전달
마지막으로, 캐싱이 구성 되지 않은 경우 사용자 요청은 [라우팅 메서드](front-door-routing-methods.md) 구성에 따라 "최상" 백 엔드로 전달 됩니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
