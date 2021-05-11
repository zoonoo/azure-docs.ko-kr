---
title: Azure 네트워크 아키텍처
description: 이 문서에서는 Microsoft Azure 인프라 네트워크에 대한 일반적인 설명을 제공합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89567873"
---
# <a name="azure-network-architecture"></a>Azure 네트워크 아키텍처
Azure 네트워크 아키텍처는 인터넷에서 Azure 데이터 센터로의 연결을 제공합니다. Azure에 배포된 모든 워크로드(IaaS, PaaS, SaaS)는 Azure 데이터 센터 네트워크를 활용합니다.

## <a name="network-topology"></a>네트워크 토폴로지
Azure 데이터 센터의 네트워크 아키텍처는 다음 구성 요소로 이루어져 있습니다.

- 경계 네트워크
- 광역 네트워크
- 지역 게이트웨이 네트워크
- 데이터 센터 네트워크

![Azure 네트워크의 다이어그램](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>네트워크 구성 요소
네트워크 구성 요소에 대한 간략한 설명

- 경계 네트워크

   - Microsoft 네트워킹과 기타 네트워크(예: 인터넷, 엔터프라이즈 네트워크) 사이의 경계 지점
   - Azure에 대한 인터넷 및 [ExpressRoute](../../expressroute/expressroute-introduction.md) 피어링을 제공합니다.

- 광역 네트워크

   - 전 세계의 Microsoft 지능형 백본 네트워크
   - [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/) 간 연결을 제공합니다.

- 지역 게이트웨이

   - Azure 지역 내 모든 데이터 센터의 집계 지점
   - Azure 지역 내 데이터 센터 간에 대규모 연결을 제공합니다(예: 데이터 센터당 수백 테라바이트).

- 데이터 센터 네트워크

   - 데이터 센터 내 서버 간 연결을 제공합니다(초과 구독된 대역폭 낮음).

위의 네트워크 구성 요소는 항상 사용 가능한 상시 클라우드 비즈니스를 지원하기 위해 최대 가용성을 제공하도록 설계되었습니다. 중복은 물리적 측면에서 제어 프로토콜에 이르기까지 설계되고 네트워크에 기본 제공됩니다.

## <a name="datacenter-network-resiliency"></a>데이터 센터 네트워크 복원력
데이터 센터 네트워크를 사용하여 복원력 설계 원칙을 살펴봅시다.

데이터 센터 네트워크는 [Clos 네트워크](https://en.wikipedia.org/wiki/Clos_network)의 수정된 버전으로, 클라우드 규모 트래픽을 위해 높은 양단면 대역폭을 제공합니다. 네트워크는 개별 하드웨어 오류로 인한 영향을 줄이기 위해 다수의 상용 디바이스를 사용하여 생성됩니다. 디바이스는 환경 이벤트의 영향을 줄이기 위해 개별 전원 및 냉각 도메인을 사용하는 서로 다른 물리적 위치에 전략적으로 배치됩니다.  컨트롤 플레인에서 모든 네트워크 디바이스는 OSI 모델 계층 3 라우팅 모드로 실행되므로 기존의 트래픽 루프 이슈가 제거됩니다. 서로 다른 계층 간의 모든 경로가 활성화되어 ECMP(동일 비용 다중 경로) 라우팅을 사용하여 높은 중복도와 대역폭을 제공합니다.

다음 다이어그램은 데이터 센터 네트워크가 여러 계층의 네트워크 디바이스로 구성되는 것을 보여 줍니다. 다이어그램의 막대는 중복과 높은 대역폭 연결을 제공하는 네트워크 디바이스 그룹을 나타냅니다.

![데이터 센터 네트워크](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](physical-security.md)
- [Azure 인프라 가용성](infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](infrastructure-components.md)
- [Azure 프로덕션 네트워크](production-network.md)
- [Azure SQL Database 보안 기능](infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](infrastructure-operations.md)
- [심층 인프라 모니터링](infrastructure-monitoring.md)
- [Azure 인프라 무결성](infrastructure-integrity.md)
- [Azure 고객 데이터 보호](protection-customer-data.md)
