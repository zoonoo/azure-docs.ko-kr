---
title: 포함 파일
description: 포함 파일
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 8/8/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 6514bcdbe79db4a22b2a4bf13e5808bbb9abbb06
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40027073"
---
| | 표준 SKU | 기본 SKU |
| --- | --- | --- |
| 백 엔드 풀 크기 | 최대 1000개 인스턴스 지원 | 최대 100개 인스턴스 지원 |
| 백 엔드 풀 끝점 | 단일 가상 네트워크의 가상 머신(가상 머신, 가용성 집합, 가상 머신 확장 집합 혼합 포함). | 단일 가용성 집합 또는 가상 머신 확장 집합의 가상 머신. |
| [상태 프로브](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [상태 프로브 다운 동작](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP 연결은 인스턴스 프로브 __및__ 모든 프로브가 다운되어 있을 때 그대로 유지됩니다. | TCP 연결은 인스턴스 프로브가 다운되어 있을 때 그대로 유지됩니다. 모든 프로브가 다운되면 모든 TCP 연결이 종료됩니다. |
| 가용성 영역 | 기본 SKU에서 인바운드 및 아웃바운드, 아웃바운드 흐름 매핑 생존 영역 장애, 영역 간 부하 분산을 위한 영역 중복 및 영역 프런트 엔드. | 해당 없음|
| 진단 | Azure Monitor, 바이트 및 패킷 카운터, 상태 프로브 상태, 연결 시도(TCP SYN), 아웃바운드 연결 상태(SNAT 성공 및 실패 흐름), 활성 데이터 평면 측정을 포함하는 다차원 메트릭 | 공용 Load Balancer 전용, SNAT 소모 경고, 백 엔드 풀 상태 수에 대한 Azure Log Analytics. |
| HA 포트 | 내부 부하 분산 장치 | 해당 없음 |
| 기본적으로 보안 적용 | 공용 IP 및 Load Balancer 끝점에 대해 기본적으로 닫혀 있으며, 네트워크 보안 그룹을 사용하여 트래픽이 흐르도록 명시적으로 허용해야 합니다. | 기본적으로 열려 있는 선택적 네트워크 보안 그룹입니다. |
| [아웃바운드 연결](../articles/load-balancer/load-balancer-outbound-connections.md) | 부하 분산 규칙별 옵트아웃이 적용되는 여러 프런트 엔드. 가상 머신이 아웃바운드 연결을 사용할 수 있도록 하려면 _반드시_ 아웃바운드 시나리오를 만들어야 합니다.  가상 네트워크 서비스 엔드포인트에 아웃바운드 연결 없이 연결할 수 있으며,처리된 데이터 수는 고려되지 않습니다.  VNet 서비스 끝점으로 사용할 수 없는 Azure PaaS 서비스를 포함하는 모든 공용 IP 주소는 아웃바운드 연결을 통해 연결해야 하며, 처리된 데이터 수도 고려되어야 합니다. 내부 Load Balancer만 가상 머신에 작동할 경우 기본 SNAT를 통한 아웃바운드 연결은 사용할 수 없습니다. 아웃바운드 SNAT 프로그래밍은 인바운드 부하 분산 규칙의 프로토콜을 기준으로 하는 전송 프로토콜 기준 방식입니다. | 여러 프런트 엔드가 있을 때 임의로 선택되는 단일 프런트 엔드입니다.  내부 Load Balancer만 가상 머신에 작동할 경우 기본 SNAT가 사용됩니다. |
| [여러 프런트 엔드](../articles/load-balancer/load-balancer-multivip-overview.md) | 인바운드 및 [아웃바운드](../articles/load-balancer/load-balancer-outbound-connections.md) | 인바운드 전용 |
| 관리 작업 | 대부분 작업을 30초 이내에 수행 | 일반적으로 60-90+초. |
| SLA | 2개의 정상 가상 머신이 있는 데이터 경로에 대해 99.99%. | VM SLA에서 암시적. | 
| 가격 | 규칙의 수, 리소스와 연결해서 인바운드 또는 아웃바운드로 처리된 데이터에 따라 요금이 부과됩니다.  | 무료 |
|  |  |  |
