---
title: Azure Event Grid의 지리적 재해 복구 | Microsoft Docs
description: Azure Event Grid에서 자동 GeoDR(지리적 재해 복구)을 지원하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94980854"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure Event Grid의 서버 측 지리적 재해 복구
이제 Event Grid에는 새로운 항목뿐만 아니라 기존의 모든 도메인, 항목 및 이벤트 구독에 대한 메타데이터의 자동 GeoDR(지리적 재해 복구)이 있습니다. 전체 Azure 지역이 중단되는 경우 Event Grid에는 모든 이벤트 관련 인프라 메타데이터가 쌍을 이룬 지역에 이미 동기화됩니다. 새 이벤트는 사용자의 개입 없이 다시 전달되기 시작합니다. 

재해 복구는 다음과 같은 두 가지 메트릭을 사용하여 측정됩니다.

- RPO(복구 지점 목표): 손실될 수 있는 데이터의 시간(분 또는 시)입니다.
- RTO(복구 시간 목표): 서비스가 다운될 수 있는 시간(분)입니다.

Event Grid의 자동 장애 조치(failover)에서는 메타데이터(이벤트 구독 등) 및 데이터(이벤트)에 대한 RPO 및 RTO가 다릅니다. 다음과는 다른 사양이 필요한 경우 자체적으로 [토픽 상태 API를 사용하여 클라이언트 쪽 장애 조치](custom-disaster-recovery.md)를 계속 구현할 수 있습니다.

## <a name="recovery-point-objective-rpo"></a>복구 지점 목표(RPO)
- **메타데이터 RPO**: 0분. Event Grid에서 리소스가 생성될 때마다 지역 간에 즉시 복제됩니다. 장애 조치가 발생하면 메타데이터가 손실되지 않습니다.
- **데이터 RPO**: 시스템이 정상이고 지역 장애 조치 시 기존 트래픽을 처리하는 경우 이벤트에 대한 RPO는 약 5분입니다.

## <a name="recovery-time-objective-rto"></a>복구 시간 목표(RTO)
- **메타데이터 RTO**: Event Grid는 일반적으로 60분 내에 훨씬 더 신속하게 발생하지만 토픽 및 구독에 대한 만들기/업데이트/삭제 호출을 허용하기 시작합니다.
- **데이터 RTO**: Event Grid는 메타데이터와 마찬가지로 일반적으로 훨씬 더 빠르게 발생하지만 60분 이내에 지역 장애 조치 후 새 트래픽을 허용하기 시작합니다.

> [!NOTE]
> Event Grid의 메타데이터 GeoDR 비용은 $0입니다.


## <a name="next-steps"></a>다음 단계
클라이언트 쪽 장애 조치 논리를 구현하려는 경우 [# Event Grid에서 사용자 지정 토픽용 자체 재해 복구 빌드](custom-disaster-recovery.md)를 참조하세요.
