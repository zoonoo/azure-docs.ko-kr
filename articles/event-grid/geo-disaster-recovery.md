---
title: Azure 이벤트 그리드의 지오 재해 복구 | 마이크로 소프트 문서
description: Azure Event Grid가 GeoDR(지역 재해 복구)을 자동으로 지원하는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307319"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure 이벤트 그리드의 서버 측 지역 재해 복구
이벤트 그리드에는 이제 새 도메인, 토픽 및 이벤트 구독뿐만 아니라 모든 기존 도메인, 주제 및 이벤트 구독에 대한 메타 데이터의 자동 지오 재해 복구(GeoDR)가 있습니다. 전체 Azure 지역이 다운되면 Event Grid는 이미 모든 이벤트 관련 인프라 메타데이터를 페어링된 리전에 동기화합니다. 새로운 이벤트는 사용자의 개입없이 다시 흐르기 시작합니다. 

재해 복구는 다음 두 가지 메트릭으로 측정됩니다.

- [RPO(복구 지점 목표)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): 손실될 수 있는 데이터의 분 또는 시간입니다.
- [복구 시간 목표(RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): 서비스가 다운될 수 있는 시간(분)입니다.

Event Grid의 자동 장애 조치(이벤트 구독 등)와 데이터(이벤트)에 대해 서로 다른 RPO 및 RPO가 있습니다. 다음 사양과 다른 사양이 필요한 경우에도 항목 [상태 apis를 사용하여](custom-disaster-recovery.md)사용자 고유의 클라이언트 측 장애 검사를 구현할 수 있습니다.

## <a name="recovery-point-objective-rpo"></a>복구 지점 목표(RPO)
- **메타데이터 RPO**: 0분. 이벤트 그리드에서 리소스가 생성될 때마다 리전 간에 즉시 복제됩니다. 장애 조치(failover)가 발생하면 메타데이터가 손실되지 않습니다.
- **데이터 RPO:** 시스템이 정상이고 지역 장애 조치 발생 시 기존 트래픽을 따라잡은 경우 이벤트에 대한 RPO는 약 5분입니다.

## <a name="recovery-time-objective-rto"></a>복구 시간 목표(RTO)
- **메타데이터 RTO**: 일반적으로 60분 이내에 훨씬 더 빠르게 발생하지만 이벤트 그리드는 토픽 및 구독에 대한 만들기/업데이트/삭제 호출을 수락하기 시작합니다.
- **데이터 RTO**: 메타데이터와 마찬가지로 일반적으로 훨씬 더 빠르게 발생하지만 60분 이내에 Event Grid는 지역 장애 조치 후 새 트래픽을 수락하기 시작합니다.

> [!NOTE]
> 이벤트 그리드의 메타데이터 GeoDR 비용은 $0입니다.


## <a name="next-steps"></a>다음 단계
클라이언트 쪽 장애 조치 논리를 직접 구현하려면 [Event Grid에서 사용자 지정 항목에 대한 자체 재해 복구 빌드를](custom-disaster-recovery.md) 참조하십시오.
