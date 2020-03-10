---
title: 포함 파일
description: 포함 파일
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202567"
---
| | 표준 Load Balancer | 기본 Load Balancer |
| --- | --- | --- |
| [백 엔드 풀 크기](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | 최대 1000개 인스턴스를 지원합니다. | 최대 300개 인스턴스를 지원합니다. |
| 백 엔드 풀 엔드포인트 | 단일 가상 네트워크에 있는 가상 머신 또는 가상 머신 확장 집합 | 단일 가용성 집합 또는 가상 머신 확장 집합의 가상 머신. |
| [상태 프로브](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [상태 프로브 다운 동작](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP 연결은 인스턴스 프로브 __및__ 모든 프로브가 다운되어 있을 때 그대로 유지됩니다. | TCP 연결은 인스턴스 프로브가 다운되어 있을 때 그대로 유지됩니다. 모든 프로브가 다운되면 모든 TCP 연결이 종료됩니다. |
| 가용성 영역 | 인바운드 및 아웃바운드 트래픽에 대한 영역 중복 및 영역 프런트 엔드가 있습니다. | 사용할 수 없음 |
| 진단 | [Azure Monitor 다차원 메트릭](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Azure Monitor 로그](../articles/load-balancer/load-balancer-monitor-log.md) |
| HA 포트 | [내부 Load Balancer에 사용 가능](../articles/load-balancer/load-balancer-ha-ports-overview.md) | 사용할 수 없음 |
| 기본적으로 보안 적용 | 네트워크 보안 그룹에서 허용하지 않는 한 인바운드 흐름으로 종결됩니다. VNet에서 내부 부하 분산 장치에 대한 내부 트래픽은 허용됩니다. | 기본적으로 엽니다. 네트워크 보안 그룹 옵션입니다. |
| 아웃바운드 규칙 | [선언적 아웃바운드 NAT 구성](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | 사용할 수 없음 |
| 유휴 상태의 TCP 재설정 | [모든 규칙에서 사용 가능](../articles/load-balancer/load-balancer-tcp-reset.md) | 사용할 수 없음 |
| [여러 프론트 엔드](../articles/load-balancer/load-balancer-multivip-overview.md) | 인바운드 및 [아웃바운드](../articles/load-balancer/load-balancer-outbound-connections.md) | 인바운드 전용 |
| 관리 작업 | 대부분 작업을 30초 이내에 수행 | 일반적으로 60-90+초 |
| SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | 사용할 수 없음 | 
