---
title: 전용 이벤트 허브 개요 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 이벤트 허브의 단일 테넌트 배포를 제공하는 전용 Azure 이벤트 허브에 대한 개요를 제공합니다.
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
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516752"
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated의 개요

*Event Hubs 클러스터는* 스트리밍 요구가 가장 까다로운 고객을 위해 단일 테넌트 배포를 제공합니다. 이 단일 테넌트 오퍼링은 99.99% SLA를 보장하며 전용 가격 책정 계층에서만 사용할 수 있습니다. 이벤트 허브 클러스터는 보장된 용량 및 초 이하 대기 시간으로 초당 수백만 개의 이벤트를 발생시킬 수 있습니다. 전용 클러스터 내에서 만든 네임스페이스 및 이벤트 허브에는 표준 오퍼링의 모든 기능이 포함되지만 인슈어런스 제한이 없습니다. 또한 추가 비용 없이 인기 있는 [이벤트 허브 캡처](event-hubs-capture-overview.md) 기능이 포함되어 있으므로 Azure Storage 또는 Azure Data Lake에 데이터 스트림을 자동으로 일괄 처리하고 로그할 수 있습니다. 

클러스터는 CPU 및 메모리 리소스의 사전 할당된 양인 **용량 단위(CPU)별로**프로비저닝되고 요금이 청구됩니다. 각 클러스터에 1, 2, 4, 8, 12, 16 또는 20개 CU를 구매할 수 있습니다. CU당 섭취 및 스트리밍할 수 있는 양에는 생산자 및 소비자 수, 페이로드 모양, 송신률 등 다양한 요인에 따라 달라집니다(자세한 내용은 아래 벤치마크 결과 참조). 

> [!NOTE]
> 모든 이벤트 허브 클러스터는 기본적으로 카프카를 사용할 수 있으며 기존 Kafka 기반 응용 프로그램에서 사용할 수 있는 Kafka 끝점을 지원합니다. 클러스터에서 Kafka를 사용하도록 설정해도 Kafka가 아닌 사용 사례에는 영향을 미치지 않습니다. 클러스터에서 Kafka를 비활성화할 수 있는 옵션이 없거나 필요하지 않습니다.

## <a name="why-dedicated"></a>왜 전용인가요?

전용 이벤트 허브는 엔터프라이즈급 용량이 필요한 고객에게 다음과 같은 세 가지 강력한 이점을 제공합니다.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>단일 테넌시로 용량 보장하여 성능 향상

전용 클러스터는 전체 용량으로 용량을 보장하며, 최대 기가바이트의 스트리밍 데이터를 완전히 내구성이 뛰어난 스토리지및 초이하의 대기 시간으로 유입하여 트래픽의 급증을 수용할 수 있습니다. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>기능에 대한 포괄적이고 독점적인 액세스 
전용 오퍼링에는 추가 비용 없이 캡처와 같은 기능뿐만 아니라 BYOK(Bring Your Own Key)와 같은 향후 기능에 대한 독점 액세스가 포함되어 있습니다. 또한 이 서비스는 부하 분산, OS 업데이트, 보안 패치 및 고객을 위한 분할을 관리하므로 인프라 유지 관리에 소요되는 시간을 줄이고 클라이언트 측 기능을 구축하는 데 더 많은 시간을 할애할 수 있습니다.  

#### <a name="cost-savings"></a>비용 절감
높은 수입 볼륨(>100TUs)에서 클러스터비용은 표준 오퍼링에서 유사한 수량의 처리량을 구입하는 것보다 시간당 훨씬 적습니다.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>이벤트 허브 전용 할당량 및 제한

이벤트 허브 전용 오퍼링은 매월 고정 요금으로 청구되며 최소 4시간 사용시 청구됩니다. 전용 계층은 표준 계획의 모든 기능을 제공하지만 까다로운 워크로드를 가진 고객을 위한 엔터프라이즈 규모 용량 및 제한 기능을 제공합니다. 

| 기능 | Standard | 전용 |
| --- |:---:|:---:|
| 대역폭 | 20 개의 TUS (최대 40 개의 TUS) | 20개의 쿠스 |
| 네임스페이스 |  1 | CU당 50개 |
| Event Hubs |  네임스페이스당 10개 | 네임스페이스당 1000개 |
| 수신 이벤트 | 100만 이벤트당 요금 부과 | 포함 |
| 메시지 크기 | 1백만 바이트 | 1백만 바이트 |
| 파티션 | 이벤트 허브당 32개 | 이벤트 허브당 1024 |
| 소비자 그룹 | 이벤트 허브당 20개 | CU당 제한 없음, 이벤트 허브당 1000개 |
| 조정된 연결 | 포함 1,000개, 최대 5,000개 | 100 K 포함 및 최대 |
| 메시지 보존 | 7일, TU당 84GB 포함 | 90일, CU당 10TB 포함 |
| 캡처 | 시간당 요금 부과 | 포함 |

## <a name="how-to-onboard"></a>등록 방법

[Azure 포털을](https://aka.ms/eventhubsclusterquickstart) 통해 [이벤트 허브 클러스터를 만드는](event-hubs-dedicated-cluster-create-portal.md) 셀프 서비스 환경은 이제 미리 보기로 표시됩니다. 이벤트 허브 전용 온보딩에 대한 질문이 있거나 도움이 필요하면 [이벤트 허브 팀에](mailto:askeventhubs@microsoft.com)문의하십시오.

## <a name="faqs"></a>FAQ

#### <a name="what-can-i-achieve-with-a-cluster"></a>클러스터를 통해 무엇을 달성할 수 있습니까?

이벤트 허브 클러스터의 경우 생산자, 소비자, 인제및 처리 속도 등 다양한 요인에 따라 섭취 및 스트리밍할 수 있는 양에 따라 달라집니다. 

다음 표에는 테스트를 통해 실현한 벤치마크 결과가 나와 있습니다.

| 페이로드 셰이프 | 수신기 | 수신 대역폭| 수신 메시지 | 송신 대역폭 | 송신 메시지 | 총 TU | CU당 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB의 일괄 처리 | 2 | 400MB/초 | 초당 400k 메시지 | 800MB/초 | 초당 800k 메시지 | 400TU | 100TU | 
| 10x10KB의 일괄 처리 | 2 | 666MB/초 | 초당 66.6k 메시지 | 1.33GB/초 | 초당 133k 메시지 | 666TU | 166TU |
| 6x32KB의 일괄 처리 | 1 | 1.05GB/초 | 34k 메시지 / 초 | 1.05GB/초 | 초당 34k 메시지 | 1,000TU | 250TU |

테스트에 사용된 조건은 다음과 같습니다.

- 4개의 용량 단위(US)가 있는 전용 계층 이벤트 허브 클러스터가 사용되었습니다. 
- 수집에 사용된 이벤트 허브에는 200개의 파티션이 있었습니다. 
- 데이터는 모든 파티션으로부터 받는 두 개의 수신기 애플리케이션에서 수집되었습니다.

#### <a name="can-i-scale-updown-my-cluster"></a>클러스터를 확장/축소할 수 있습니까?

생성 후 클러스터는 최소 4시간 동안 사용 요금이 청구됩니다. 셀프 서비스 환경의 미리 보기 릴리스에서 기술 > 할당량 > 확장 *또는 확장 전용 클러스터* 요청에 따라 이벤트 허브 팀에 [지원 요청을](https://ms.portal.azure.com/#create/Microsoft.Support) 제출하여 클러스터를 확장 또는 축소할 수 있습니다. 클러스터 축소 요청을 완료하는 데 최대 7일이 걸릴 수 있습니다. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Geo-DR이 클러스터에서 어떻게 작동합니까?

전용 계층 클러스터 아래에 네임스페이스를 지리적으로 페어링하고 전용 계층 클러스터 아래에 다른 네임스페이스를 페어링할 수 있습니다. 처리량 제한이 호환되지 않아 오류가 발생하므로 전용 계층 네임스페이스를 표준 오퍼링의 네임스페이스와 페어링하는 것은 권장되지 않습니다. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>표준 네임스페이스를 전용 계층 클러스터에 속하도록 마이그레이션할 수 있습니까?
현재 이벤트 허브 데이터를 표준 네임스페이스에서 전용 데이터로 마이그레이션하기 위한 자동화된 마이그레이션 프로세스를 지원하지 않습니다. 

## <a name="next-steps"></a>다음 단계

이벤트 허브 전용에 대한 자세한 내용은 Microsoft 영업 담당자 또는 Microsoft 지원팀에 문의하십시오. 다음 링크를 방문하여 클러스터를 만들거나 이벤트 허브 가격 책정 계층에 대해 자세히 알아볼 수도 있습니다.

- [Azure 포털을 통해 이벤트 허브 클러스터 만들기](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Dedicated 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/) Microsoft 영업 담당자 또는 Microsoft 지원 서비스에서 Event Hubs Dedicated 용량에 대한 추가 정보를 얻을 수도 있습니다.
- [Event Hubs FAQ](event-hubs-faq.md)에서는 가격 책정 정보를 설명하고 이벤트 허브에 대해 일부 자주 묻는 질문에 대한 답변을 제공합니다.
