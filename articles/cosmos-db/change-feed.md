---
title: Azure Cosmos DB에서 변경 피드 지원 사용
description: Azure Cosmos DB 변경 피드 지원을 사용하여 문서 변경 사항을 추적하고 트리거와 같은 이벤트 기반 처리를 추적하고 캐시 및 분석 시스템을 최신 상태로 유지합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984864"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Azure Cosmos DB의 변경 피드

Azure Cosmos DB의 변경 피드 지원은 모든 변경 사항에 대해 Azure Cosmos 컨테이너를 수신하여 작동합니다. 그런 다음 변경된 문서가 수정된 순서로 정렬된 목록이 출력됩니다. 변경 사항은 지속적이며, 비동기적 및 증분적으로 처리할 수 있고 출력을 하나 이상의 소비자 사이에 분산하여 병렬 처리가 가능합니다.

[피드 디자인 패턴 변경에](change-feed-design-patterns.md)대해 자세히 알아보십시오.

## <a name="supported-apis-and-client-sdks"></a>지원되는 API 및 클라이언트 SDK

이 기능은 현재 다음 Azure Cosmos DB API 및 클라이언트 SDK에서 지원됩니다.

| **클라이언트 드라이버** | **SQL API** | **카산드라에 대한 Azure 코스모스 DB의 API** | **Azure Cosmos DB의 API for MongoDB** | **Gremlin API**|**테이블 API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | 예 | 예 | 예 | 예 | 예 |
|Java|예|예|예|예|예|
|Python|예|예|예|예|예|
|Node/JS|예|예|예|예|예|

## <a name="change-feed-and-different-operations"></a>변경 피드 및 다양한 작업

오늘, 변경 피드에 모든 삽입 및 업데이트를 볼 수 있습니다. 특정 유형의 작업에 대한 변경 피드를 필터링할 수 없습니다. 한 가지 가능한 대안은 변경 피드에서 항목을 처리할 때 업데이트및 필터를 위해 항목에 "소프트 마커"를 추가하는 것입니다.

현재 피드 를 변경 해도 삭제를 기록하지 않습니다. 이전 예제와 마찬가지로 삭제되는 항목에 소프트 마커를 추가할 수 있습니다. 예를 들어 "deleted"이라는 항목에 특성을 추가하고 "true"로 설정하고 항목에 TTL을 설정하여 자동으로 삭제할 수 있습니다. 5년 전에 추가된 항목과 같은 기록 항목(항목에 해당하는 가장 최근의 변경 내용, 중간 변경 내용 포함)에 대한 변경 피드를 읽을 수 있습니다. 컨테이너의 원점을 다시 읽을 수 있지만 항목이 삭제되면 변경 피드에서 제거됩니다.

### <a name="sort-order-of-items-in-change-feed"></a>변경 피드에서 항목의 정렬 순서

변경 피드 항목은 수정 시간 순서대로 도착합니다. 이 정렬 순서는 논리 파티션 키에 따라 보장됩니다.

### <a name="consistency-level"></a>일관성 수준

최종 일관성 수준에서 변경 피드를 사용하는 동안 후속 변경 피드 읽기 작업 사이에 중복 이벤트가 있을 수 있습니다(한 읽기 작업의 마지막 이벤트는 다음 작업의 첫 번째 로 표시).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>다중 지역 Azure Cosmos 계정의 변경 피드

다중 지역 Azure Cosmos 계정에서 쓰기 지역이 장애 조치(failover)되는 경우 변경 피드는 수동 장애 조치(failover) 작업에서 작동하고 계속 인접합니다.

### <a name="change-feed-and-time-to-live-ttl"></a>변경 피드 및 TTL(Time to Live)

항목에서 TTL(Time to Live) 속성이 -1로 설정된 경우 변경 피드는 영원히 유지됩니다. 데이터를 삭제하지 않는 이상, 데이터는 변경 피드에 계속 남아 있습니다.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>변경 피드 및 _etag, _lsn 또는 _ts

_etag 형식은 내부적이며 언제든지 변경될 수 있으므로 의존하면 안 됩니다. _ts는 수정 또는 생성 타임스탬프입니다. _ts를 시간순 비교에 사용할 수 있습니다. _lsn 변경 피드에만 추가되는 일괄 ID입니다. 트랜잭션 ID를 나타냅니다. 많은 항목에는 동일한 _lsn이 있을 수 있습니다. FeedResponse의 ETag는 항목에 표시된 _etag와 다릅니다. _etag는 내부 식별자이며 항목의 버전에 대해 알려주는 동시성 컨트롤에 사용되는 반면 ETag는 피드 시퀀싱에 사용됩니다.

## <a name="working-with-change-feed"></a>변경 피드를 사용하여 작업

다음 옵션을 통해 변경 피드를 사용하여 작업할 수 있습니다.

* [Azure Functions로 변경 피드 사용](change-feed-functions.md)
* [변경 피드 프로세서와 함께 변경 피드 사용](change-feed-processor.md) 

변경 피드는 컨테이너 내에서 각 논리 파티션 키에 대해 사용할 수 있으며, 아래 이미지와 같이 한 명 이상의 소비자에게 배포하여 병렬 처리할 수 있습니다.

![Azure Cosmos DB 변경 피드의 분산 처리](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>변경 피드의 기능

* 변경 피드는 모든 Azure Cosmos 계정에 기본적으로 사용됩니다.

* Azure Cosmos 데이터베이스와 연결된 모든 지역에서 [프로비전된 처리량](request-units.md)을 사용하여 Azure Cosmos DB의 다른 작업과 마찬가지로 변경 피드에서 읽을 수 있습니다.

* 변경 피드는 컨테이너 내의 항목에 수행된 삽입 및 업데이트 작업을 포함합니다. 삭제 대신 항목(예: 문서) 내에서 "soft-delete" 플래그를 설정하여 삭제를 캡처할 수 있습니다. 또는 [TTL 기능](time-to-live.md)을 사용하여 항목에 대한 제한된 만료 기간을 설정할 수 있습니다. 예를 들어 24시간 및 삭제를 캡처하는 해당 속성의 값을 사용합니다. 이 솔루션을 사용하여 TTL 만료 기간보다 짧은 시간 간격 내에 변경 내용을 처리해야 합니다.

* 각 항목에 대한 변경 내용이 정확히 한 번 변경 피드에 표시되고, 클라이언트는 검사점 논리를 관리해야 합니다. 검사점 관리의 복잡성을 방지하려면 변경 피드 프로세서에서 자동 검사점 과 "적어도 한 번" 의미 체계를 제공합니다. [변경 피드 프로세서에서 변경 피드를 사용합니다.](change-feed-processor.md)

* 지정된 항목에 대한 가장 최근의 변경 내용만이 변경 로그에 포함됩니다. 중간 변경 내용을 사용할 수 없습니다.

* 변경 피드는 각 논리 파티션 키 값 내에서 수정된 순서로 정렬됩니다. 파티션 키 값에 보장된 순서가 없습니다.

* 특정 시점에서 변경 내용을 동기화할 수 있습니다. 즉, 변경 내용을 사용할 수 있는 고정 데이터 보존 기간이 없습니다.

* Azure Cosmos 컨테이너의 모든 논리 파티션 키에 대해 변경 내용을 동시에 사용할 수 있습니다. 이 기능을 사용하면 대규모 컨테이너의 변경 내용을 여러 소비자에 의해 병렬로 처리할 수 있습니다.

* 응용 프로그램은 동일한 컨테이너에 여러 변경 피드를 동시에 요청할 수 있습니다. 초기 시작점을 제공하기 위해 ChangeFeedOptions.StartTime을 사용할 수 있습니다. 예를 들어 지정된 클록 시간에 해당하는 연속 토큰을 찾을 수 있습니다. 연속 토큰(지정된 경우)이 StartTime 및 StartFromBeginning 값보다 우선합니다. ChangeFeedOptions.StartTime의 정확도는 5초입니다.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>카산드라와 몽고DB에 대한 API의 피드 변경

변경 피드 기능은 MongoDB API및 카산드라 API의 조건자로 쿼리의 변경 스트림으로 표시됩니다. MongoDB API에 대한 구현 세부 정보에 대한 자세한 내용은 [MongoDB에 대한 Azure Cosmos DB API의 변경 스트림을](mongodb-change-streams.md)참조하십시오.

네이티브 아파치 카산드라는 CDC 로그에 대한 구성 가능한 크기-온-디스크에 도달하면 보관을 위해 특정 테이블에 플래그를 지정하고 해당 테이블에 대한 쓰기를 거부하는 메커니즘인 변경 데이터 캡처(CDC)를 제공합니다. Cassandra용 Azure Cosmos DB API의 변경 피드 기능은 CQL을 통해 조건자로 변경 내용을 쿼리하는 기능을 향상시킵니다. 구현 세부 정보에 대한 자세한 내용은 [Cassandra에 대한 Azure Cosmos DB API의 변경 피드를](cassandra-change-feed.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 자세히 알아볼 수 있습니다.

* [변경 피드를 읽는 옵션](read-change-feed.md)
* [Azure Functions로 변경 피드 사용](change-feed-functions.md)
* [변경 피드 프로세서 사용](change-feed-processor.md)
