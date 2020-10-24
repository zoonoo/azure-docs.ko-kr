---
title: 전용 이벤트 허브 개요 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Event Hubs의 단일 테 넌 트 배포를 제공 하는 전용 Azure Event Hubs에 대 한 개요를 제공 합니다.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 20b153c9093c96e7357a8e439b6655f1db80bd46
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517001"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated의 개요

*Event Hubs 클러스터* 는 요구 사항이 가장 까다로운 고객을 위해 단일 테 넌 트 배포를 제공 합니다. 이 단일 테 넌 트 제품은 99.99%의 SLA를 보장 하며 전용 가격 책정 계층 에서만 사용할 수 있습니다. 보장된 용량을 제공하고 대기 시간이 1초 미만에 불과한 Event Hubs 클러스터는 초당 수백만 개의 이벤트를 수신할 수 있습니다. 전용 클러스터 내에서 생성 되는 네임 스페이스 및 event hubs에는 표준 제공 및 기타 모든 기능 (수신 제한 없음)이 포함 됩니다. 또한 자주 사용 하는 [Event Hubs 캡처](event-hubs-capture-overview.md) 기능을 추가 비용 없이 포함 하 여 자동으로 Azure Storage 또는 Azure Data Lake 데이터 스트림을 일괄 처리 하 고 기록할 수 있습니다. 

클러스터는 사전 할당 된 CPU 및 메모리 리소스 용량의 **CUs (용량 단위)** 로 프로 비전 되 고 청구 됩니다. 각 클러스터에 1, 2, 4, 8, 12, 16 또는 20개 CU를 구매할 수 있습니다. CU 당 수집 하 고 스트리밍할 수 있는 양은 생산자와 소비자의 수, 페이로드 모양, 송신 요금 등 다양 한 요인에 따라 달라 집니다. 자세한 내용은 아래의 벤치 마크 결과를 참조 하세요. 

> [!NOTE]
> 모든 Event Hubs 클러스터는 기본적으로 사용 하도록 설정 되며, 기존 Kafka 기반 응용 프로그램에서 사용할 수 있는 Kafka 끝점을 지원 합니다. 클러스터에서 Kafka을 사용 하도록 설정 하면 Kafka 사용 사례에 영향을 주지 않습니다. 클러스터에서 Kafka을 사용 하지 않도록 설정 하거나 옵션을 사용 하지 않도록 설정 해야 합니다.

## <a name="why-dedicated"></a>전용 이유

전용 Event Hubs는 엔터프라이즈급 용량을 필요로 하는 고객을 위한 세 가지 강력한 혜택을 제공 합니다.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>성능 향상을 위해 단일 테 넌 트의 용량 보장

전용 클러스터는 전체 규모에서 용량을 보장 하 고 모든 트래픽 버스트를 수용 하기 위해 완전 내구성이 뛰어난 저장소 및 초이 대기 시간을 사용 하 여 최대 기가바이트의 스트리밍 데이터를 수신 합니다. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>기능에 대 한 포함 및 배타적 액세스 
전용 제품에는 추가 비용 없이 캡처와 같은 기능을 포함 하 고, BYOK (Bring Your Own Key)와 같은 예정 된 기능에 대 한 배타적 액세스를 포함 합니다. 또한 서비스는 인프라 유지 관리에 대 한 시간을 줄이고 클라이언트 쪽 기능을 빌드하는 데 더 많은 시간을 소비할 수 있도록 고객에 대 한 부하 분산, OS 업데이트, 보안 패치 및 분할을 관리 합니다.  

#### <a name="cost-savings"></a>비용 절감
대량 수신 볼륨 (>100 처리량 단위)에서는 표준 제품에서 비슷한 처리량 단위를 구입 하는 것 보다 클러스터 비용이 시간당 훨씬 더 적습니다.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>할당량 및 한도 Event Hubs Dedicated

Event Hubs Dedicated 제품은 고정된 월별 가격으로 청구되며, 최소 사용량은 4시간입니다. Dedicated 계층은 표준 요금제의 모든 기능을 제공합니다. 하지만 워크로드가 까다로운 고객을 위해 엔터프라이즈급 용량 및 제한을 제공합니다. 

| 기능 | Standard | 전용 |
| --- |:---:|:---:|
| 대역폭 | 20 Tu (최대 40 Tu) | 20개 CU |
| 네임스페이스 |  1 | CU당 50개 |
| Event Hubs |  네임 스페이스 당 10 | 네임스페이스당 1,000개 |
| 수신 이벤트 | 100만 이벤트당 요금 부과 | 포함 |
| 메시지 크기 | 100만 바이트 | 100만 바이트 |
| 파티션 | 32 이벤트 허브 당 | 1024 이벤트 허브 당 |
| 소비자 그룹 | 이벤트 허브 당 20 | CU당 제한 없음, 이벤트 허브당 1,000개 |
| 조정된 연결 | 1000 포함, 최대 5000 | 100 K 포함 및 최대 |
| 메시지 보존 | TU 당 7 일, 84 GB 포함 | 90일, CU당 10TB 포함 |
| 캡처 | 시간당 요금 부과 | 포함 |

## <a name="how-to-onboard"></a>등록 방법

[Azure Portal](https://aka.ms/eventhubsclusterquickstart) 를 통해 [Event Hubs 클러스터를 만드는](event-hubs-dedicated-cluster-create-portal.md) 셀프 서비스 환경은 이제 미리 보기로 제공 됩니다. Event Hubs Dedicated에 대 한 온 보 딩에 대 한 질문이 있거나 도움이 필요한 경우 [Event Hubs 팀](mailto:askeventhubs@microsoft.com)에 문의 하세요.

## <a name="faqs"></a>FAQ

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>다음 단계

Event Hubs Dedicated에 대 한 자세한 내용을 보려면 Microsoft 영업 담당자 또는 Microsoft 지원에 문의 하세요. 다음 링크를 방문 하 여 클러스터를 만들거나 Event Hubs 가격 책정 계층에 대 한 자세한 정보를 확인할 수도 있습니다.

- [Azure Portal를 통해 Event Hubs 클러스터 만들기](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Dedicated 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/) Microsoft 영업 담당자 또는 Microsoft 지원 서비스에서 Event Hubs Dedicated 용량에 대한 추가 정보를 얻을 수도 있습니다.
- [Event Hubs FAQ](event-hubs-faq.md)에서는 가격 책정 정보를 설명하고 이벤트 허브에 대해 일부 자주 묻는 질문에 대한 답변을 제공합니다.
