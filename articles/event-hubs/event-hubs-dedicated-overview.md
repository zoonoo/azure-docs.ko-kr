---
title: 전용 이벤트 허브 개요 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 event hubs의 단일 테 넌 트 배포를 제공 하는 전용된 Azure Event Hubs의 개요를 제공 합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 52e092e6e48f004656860cb5d078e780039584ab
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730232"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated의 개요

*이벤트 허브 클러스터* 스트리밍 가장 까다로운 요구를 사용 하 여 고객에 대 한 단일 테 넌 트 배포를 제공 합니다. 이 단일 테 넌 트 제품 보장 된 99.99 %SLA 있으며이 전용 가격 책정 계층 에서만 사용할 수 있습니다. 클러스터를 Event Hubs 이벤트는 보장 된 용량 및 1 초 미만의 대기 시간으로 초당 수백만 개의 수신 수 있습니다. 전용된 클러스터 내에서 만든 네임 스페이스 및 이벤트 허브 수신에 제한이 없이 Standard 제품 등의 모든 기능을 포함 합니다. 또한 인기 있는 [Event Hubs 캡처](event-hubs-capture-overview.md) 기능은 추가 비용 없이 자동으로 일괄 처리 및 로그 데이터 스트림을 Azure Storage 또는 Azure Data Lake에 있습니다. 

클러스터 프로 비전 되 고 기준으로 청구 됩니다 **용량 단위 (Cu)** , CPU 및 메모리 리소스를 미리 할당 된 크기입니다. 각 클러스터에 1, 2, 4, 8, 12, 16 또는 20개 CU를 구매할 수 있습니다. 다양 한 요인에 따라 달라 집니다 수집 하 고 CU 당 스트림 수 얼마나 생산자와 소비자에 게 제공 되는 페이로드 셰이프에 송신 수와 같은 (자세한 내용은 아래 벤치 마크 결과 참조)을 평가 합니다. 

> [!NOTE]
> 모든 Event Hubs 클러스터 기본적으로 Kafka를 사용할 수 있고 사용할 수 있는 Kafka 끝점을 지원 하 여 기존 Kafka 기반 응용 프로그램입니다. Kafka에서 사용할 수 있는 클러스터에 Kafka 아닌 사용 사례; 영향을 주지 않습니다 옵션 또는 클러스터에서 Kafka를 사용 하지 않도록 설정할 필요가 없습니다 있습니다.

## <a name="why-dedicated"></a>전용 하는 이유?

Dedicated Event Hubs는 엔터프라이즈 수준 용량 필요한 고객에 대 한 세 가지 뛰어난 이점을 제공 합니다.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>성능 향상을 위해 용량을 보장 하는 단일 테 넌 트

전체 규모의 용량을 보장 하는 전용된 클러스터 및 트래픽에서 수신 완전 내구성이 있는 저장소 및 1 초 미만의 대기 시간을 수용 하기를 사용 하 여 스트리밍 데이터의 기가바이트까지 버스트할 수 있습니다. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>기능에 대 한 포괄 / 전용 액세스 
전용된 제품 BYOK 같은 예정 된 기능에 대 한 단독 액세스 뿐 아니라 추가 비용 없는 캡처 같은 기능을 포함 합니다. 서비스 또한 관리 부하 분산, OS 업데이트, 보안 패치 및 분할 고객을 위한 인프라 유지 관리 및 클라이언트 쪽 기능을 구축에 더 많은 시간에 시간을 줄일 수 있도록 합니다.  

#### <a name="cost-savings"></a>비용 절감
높은 수신 볼륨이 (> 100 Tu), 클러스터 비용 보다 훨씬 적습니다 시간당 비슷한 양의 Standard 제품의 처리량 단위를 구입 합니다.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Dedicated 할당량 및 제한

Event Hubs Dedicated 제품은 최소 4 시간의 사용량을 사용 하 여 고정된 된 월간 가격으로 청구 됩니다. Dedicated 계층 까다로운 워크 로드를 사용 하 여 고객을 위한 엔터프라이즈급 용량 및 제한 하지만 표준 요금제의 모든 기능을 제공합니다. 

| 기능 | Standard | 전용 |
| --- |:---:|:---:|
| 대역폭 | 20 Tu (최대 40 Tu) | 20 Cu |
| 네임스페이스 |  1 | CU 당 50 |
| Event Hubs |  네임 스페이스 당 10 | 네임 스페이스 당 1000 |
| 수신 이벤트 | 100만 이벤트당 요금 부과 | 포함 |
| 메시지 크기 | 1 백만 바이트 | 1 백만 바이트 |
| 파티션 | 네임 스페이스 당 40 | CU 당 2000 |
| 소비자 그룹 | 이벤트 허브 당 20 | 제한 없음, CU 당 event hub 당 1000 |
| 조정된 연결 | 1,000 포함, 최대 5,000 | 100k 포함 및 max |
| 메시지 보존 | 7 일, 84GB TU 당 포함 된 | CU 당 포함 된 10TB 90 일 |
| 캡처 | 시간당 요금 부과 | 포함 |

## <a name="how-to-onboard"></a>등록 방법

여 셀프 서비스 환경을 [Event Hubs 클러스터를 만드는](event-hubs-dedicated-cluster-create-portal.md) 를 통해 합니다 [Azure Portal](https://aka.ms/eventhubsclusterquickstart) 미리 보기로 제공 됩니다. 질문이 있거나 도움이 필요 하세요 온 보 딩을 Event Hubs Dedicated를 하는 경우 문의 합니다 [Event Hubs 팀](mailto:askeventhubs@microsoft.com)합니다.

## <a name="faqs"></a>FAQ

#### <a name="what-can-i-achieve-with-a-cluster"></a>클러스터를 얻을 수 있습니까?

Event Hubs 클러스터의 경우 수집 하 고 스트림 수 얼마나에 생산자, 소비자, 속도는 수집 되며, 처리 등 같은 다양 한 요인에 따라 다릅니다. 

다음 표에는 테스트를 통해 실현한 벤치마크 결과가 나와 있습니다.

| 페이로드 셰이프 | 수신기 | 수신 대역폭| 수신 메시지 | 송신 대역폭 | 송신 메시지 | 총 TU | CU당 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB의 일괄 처리 | 2 | 400MB/초 | 400 k 메시지/초 | 800MB/초 | 800 k 메시지 수/초 | 400TU | 100TU | 
| 10x10KB의 일괄 처리 | 2 | 666MB/초 | 66.6 k 메시지 수/초 | 1.33GB/초 | 133k messages/sec | 666TU | 166TU |
| 6x32KB의 일괄 처리 | 1 | 1.05GB/초 | 34 k 메시지 수 / 초 | 1.05GB/초 | 34k messages/sec | 1,000TU | 250TU |

테스트에 사용된 조건은 다음과 같습니다.

- 용량 단위 (Cu) 4 개를 사용 하 여 Event Hubs dedicated 계층이 클러스터 사용 되었습니다. 
- 수집에 사용된 이벤트 허브에는 200개의 파티션이 있었습니다. 
- 데이터는 모든 파티션으로부터 받는 두 개의 수신기 애플리케이션에서 수집되었습니다.

#### <a name="can-i-scale-updown-my-cluster"></a>위쪽/아래쪽 내 클러스터를 확장할 수 있나요?

클러스터를 만든 후 4 시간 동안의 사용량이 최소 요금이 청구 됩니다. 셀프 서비스 경험의 미리 보기 릴리스에서 제출할 수 있습니다는 [지원 요청](https://ms.portal.azure.com/#create/Microsoft.Support) 아래에서 Event Hubs 팀 *기술 > 할당량 > 강화 또는 전용 클러스터 아래쪽 크기 조정 요청을* 확장 클러스터 또는 축소 합니다. 클러스터 크기를 조정 하는 요청을 완료 하려면 최대 7 일이 걸릴 수 있습니다. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>지리적 DR은 클러스터가 사용?

지역 쌍에서 전용 계층 클러스터를 다른 네임 스페이스를 사용 하 여 전용 계층 클러스터에서 네임 스페이스 수 있습니다. 당사의 표준 제품, 처리량 한도 호환 되지 않을 수 있는 오류가 발생 하므로 네임 스페이스를 사용 하 여 전용 계층 네임 스페이스 쌍이 좋습니다 하지 않습니다. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>전용 계층 클러스터에 속한 내 표준 네임 스페이스를 마이그레이션할 수 있나요?
에서는 현재 지원 하지 않는 자동된 마이그레이션 프로세스는 하나의 전용으로 표준 네임 스페이스에서 event hubs 데이터를 마이그레이션하기 위한 합니다. 

## <a name="next-steps"></a>다음 단계

Event Hubs Dedicated에 대 한 추가 정보를 얻으려면 Microsoft 영업 담당자 또는 Microsoft 지원부에 문의 합니다. 클러스터 생성 하거나 다음 링크를 방문 하 여 가격 책정 계층 Event Hubs에 대 한 자세한 수도 있습니다.

- [Azure Portal을 통해 Event Hubs 클러스터 만들기](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Dedicated 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/) Microsoft 영업 담당자 또는 Microsoft 지원 서비스에서 Event Hubs Dedicated 용량에 대한 추가 정보를 얻을 수도 있습니다.
- [Event Hubs FAQ](event-hubs-faq.md)에서는 가격 책정 정보를 설명하고 이벤트 허브에 대해 일부 자주 묻는 질문에 대한 답변을 제공합니다.
