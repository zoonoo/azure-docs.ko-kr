---
title: Azure Event Grid에서 지역 재해 복구 | Microsoft Docs
description: Azure Event Grid에서 자동으로 지역 재해 복구 (GeoDR)를 지 원하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ccb16971020a65932daa8f9adf4b7cd9008a9253
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105849"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure Event Grid의 서버 쪽 지역 재해 복구
이제 Event Grid에는 새로운 기능에 대 한 메타 데이터의 자동 지역 재해 복구 (GeoDR) 뿐만 아니라 기존의 모든 도메인, 토픽 및 이벤트 구독이 있습니다. 전체 Azure 지역이 중단 되는 경우 Event Grid는 이미 쌍을 이루는 지역에 동기화 된 모든 이벤트 관련 인프라 메타 데이터를 포함 합니다. 새 이벤트는 사용자의 개입 없이 다시 전달 되기 시작 합니다. 

재해 복구는 다음과 같은 두 가지 메트릭을 사용 하 여 측정 됩니다.

- [RPO (복구 지점 목표](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)): 손실 될 수 있는 데이터의 시간 (분)입니다.
- [RTO (복구 시간 목표)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): 서비스가 종료 될 수 있는 시간 (분)입니다.

Event Grid의 자동 장애 조치 (failover)에는 메타 데이터 (이벤트 구독 등) 및 데이터 (이벤트)에 대 한 Rpo 및 RTOs가 다릅니다. 다음 사양과 다른 사양이 필요한 경우 [상태 api 항목을 사용 하 여 클라이언트 쪽 장애 조치 (failover)](custom-disaster-recovery.md)를 계속 구현할 수 있습니다.

## <a name="recovery-point-objective-rpo"></a>복구 지점 목표(RPO)
- **메타 데이터 RPO**: 0 분. Event Grid에서 리소스가 생성 될 때마다 지역 간에 즉시 복제 됩니다. 장애 조치 (failover)가 발생 하면 메타 데이터가 손실 되지 않습니다.
- **데이터 RPO**: 시스템이 정상이 고 지역 장애 조치 (failover) 시 기존 트래픽에 대해 발생 하는 경우 이벤트에 대 한 RPO는 약 5 분입니다.

## <a name="recovery-time-objective-rto"></a>복구 시간 목표(RTO)
- **메타 데이터 RTO**: 일반적으로 60 분 내에 훨씬 더 신속 하 게 발생 하지만 토픽 및 구독에 대 한 만들기/업데이트/삭제 호출을 허용 하기 시작 Event Grid 합니다.
- **데이터 RTO**: 메타 데이터와 마찬가지로 일반적으로 훨씬 더 빠르게 발생 하지만, 60 분 이내에 Event Grid는 지역 장애 조치 (failover) 후 새 트래픽을 허용 하기 시작 합니다.

> [!NOTE]
> Event Grid의 메타 데이터 GeoDR에 대 한 비용은 $0입니다.


## <a name="next-steps"></a>다음 단계
클라이언트 쪽 장애 조치 (failover) 논리를 구현 하려는 경우 [의 사용자 지정 항목에 대 한 고유한 재해 복구 빌드](custom-disaster-recovery.md) 를 참조 하세요 Event Grid
