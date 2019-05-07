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
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138667"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated의 개요

Azure Event Hubs 클러스터 스트리밍 가장 까다로운 요구를 사용 하 여 고객에 대 한 단일 테 넌 트 배포를 제공합니다. 이 단일 테 넌 트 제품에 99.99 %SLA 보장된 합니다. 전용 가격 책정 계층 에서만 제공 됩니다.

클러스터를 Event Hubs 이벤트는 보장 된 용량 및 초 대기 시간으로 초당 수백만 개의 수신 수 있습니다. 전용된 클러스터 내에서 만든 네임 스페이스 및 이벤트 허브 수신에 제한이 없이 Standard 제품 등의 모든 기능을 포함 합니다. 또한 합니다 [Event Hubs 캡처](event-hubs-capture-overview.md) 추가 비용 없이 기능입니다. 자동으로 일괄 처리 및 로그 데이터 스트림을 Azure Storage 또는 Azure Data Lake를 사용할 수 있습니다.

전용된 클러스터 프로 비전 되 고 용량 단위 (Cu) 기준으로 청구 됩니다. Cu는 CPU 및 메모리 리소스를 미리 할당 된 크기입니다. 1, 2, 4, 8, 12, 16, 또는 각 클러스터에 대 한 20 Cu를 구입할 수 있습니다. 얼마나 많은 수 수집 및 스트림 CU 당 생산자 및 소비자, 페이로드 셰이프 및 송신 비율이 수 등의 요인에 따라 달라 집니다.

자세한 내용은 벤치 마크 결과 사용 하 여 테이블을 참조 하세요.

## <a name="why-use-event-hubs-dedicated"></a>Event Hubs Dedicated 사용 하는 이유?

Event Hubs Dedicated는 엔터프라이즈 수준 용량 필요한 고객에 대 한 세 가지 이점을 제공 합니다.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>성능 향상을 위해 용량을 보장 하는 단일 테 넌 트

전용된 클러스터는 전체 규모의 용량을 보장합니다. 트래픽 급증을 수용 하기 위해 완전 내구성이 있는 저장소 및 초 대기 시간을 사용 하 여 스트리밍 데이터의 기가바이트까지 수신할 수 있습니다.

#### <a name="inclusive-and-exclusive-access-to-features"></a>기능에 대 한 포괄 / 전용 액세스 
전용된 제품 추가 비용 없이 캡처 같은 기능이 포함 되어 있습니다. 또한 BYOK 같은 예정 된 기능에 대 한 단독 액세스를 제공합니다. 부하 분산, OS 업데이트, 보안 패치 및 분할에 또한 서비스를 관리합니다. 이러한 기능을 사용 하 여 인프라 유지 관리 및 클라이언트 쪽 기능을 구축에 더 많은 시간에 시간을 줄일 수 있습니다.

#### <a name="cost-savings"></a>비용 절감
높은 수신 볼륨에는 클러스터 비용이 훨씬 적은 경우 보다 시간당 구매한 처리량 단위 (Tu) 비교할 수 있는 수량은 Standard 제품. 높은 볼륨이 > 100 Tu 합니다.


## <a name="event-hubs-standard-vs-dedicated"></a>Event Hubs 표준 vs입니다. 전용

다음은 Event Hubs에 제공되는 서비스 계층을 비교한 표입니다. Event Hubs Dedicated 제품은 사용 표준의 대부분의 기능에 대 한 가격 책정과 비교해 고정된 된 월간 가격으로 요금이 청구 됩니다. Dedicated 계층 까다로운 워크 로드를 사용 하 여 고객을 위한 엔터프라이즈급 용량 하지만 표준 요금제의 모든 기능을 제공합니다.

| 기능 | Standard | 전용 |
| --- |:---:|:---:|
| 수신 이벤트 | 100만 이벤트당 요금 부과 | 포함 |
| 처리량 단위(1MB/초 수신, 2MB/초 송신) | 시간당 요금 부과 | 포함 |
| 메시지 크기 | 1MB | 1MB |
| 파티션 | 네임 스페이스 당 40 | CU 당 2,000 |
| 소비자 그룹 | 이벤트 허브 당 20 | 이벤트 허브 당 1,000 개 |
| 최대 대역폭 | 20 Tu (최대 40 Tu) | 20 Cu |
| 조정된 연결 | 1,000개 포함 | 100,000 포함 |
| 메시지 보존 | 1 일 포함 | 포함 하는 최대 7 일입니다. |
| 캡처 | 시간당 요금 부과 | 포함 |

## <a name="what-can-i-achieve-with-a-cluster"></a>클러스터를 얻을 수 있습니까?

Event Hubs 클러스터의 경우 수집 하 고 스트림 수 얼마나에 생산자, 소비자는 수집 하 고이 처리 하 고, 속도 등에 따라 다릅니다.

다음 표에서 테스트 하는 동안 얻은 결과 벤치 마크 결과 보여 줍니다.

| 페이로드 셰이프 | 수신기 | 수신 대역폭| 수신 메시지 | 송신 대역폭 | 송신 메시지 | 총 TU | CU당 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB의 일괄 처리 | 2 | 400MB/초 | 400,000 메시지/초 | 800MB/초 | 800,000 메시지 수/초 | 400TU | 100TU | 
| 10x10KB의 일괄 처리 | 2 | 666MB/초 | 66,600 메시지 수/초 | 1.33GB/초 | 133,000 메시지 수/초 | 666TU | 166TU |
| 6x32KB의 일괄 처리 | 1 | 1.05GB/초 | 34,000 메시지 수/초 | 1.05GB/초 | 34,000 메시지 수/초 | 1,000 Tu | 250TU |

테스트에서 다음 조건은 사용 되었습니다.

- 용량 단위 4 개를 사용 하 여 Event Hubs Dedicated 계층 클러스터를 사용 했습니다.
- 수집에 사용된 이벤트 허브에는 200개의 파티션이 있었습니다.
- 두 수신기 응용 프로그램에서 수집 된 데이터를 받았습니다. 이러한 모든 파티션에서 데이터를 받았습니다.

## <a name="use-event-hubs-dedicated"></a>Dedicated Event Hubs 사용

Event Hubs Dedicated는 데 [청구 지원에 문의](https://ms.portal.azure.com/#create/Microsoft.Support) 또는 Microsoft 담당자에 게 문의 합니다. 언제든지 요구 사항에 맞게 CU를 추가하거나 제거하여 용량을 늘리거나 줄일 수 있습니다. Event Hubs 제품 팀에는 적합 하도록 유연한 배포가 가능를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

Event Hubs Dedicated 용량에 대 한 추가 정보를 얻으려면 Microsoft 영업 담당자 또는 Microsoft 지원부에 문의 합니다. Event Hubs 가격 책정 계층에 대 한 자세한 내용을 보려면 다음 링크를 사용 합니다.

- [Event Hubs Dedicated 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/) Event Hubs Dedicated 용량에 대 한 추가 정보를 얻으려면 Microsoft 영업 담당자 또는 Microsoft 지원을 문의할 수 있습니다.
- [Event Hubs FAQ](event-hubs-faq.md)에서는 가격 책정 정보를 설명하고 이벤트 허브에 대해 일부 자주 묻는 질문에 대한 답변을 제공합니다.
