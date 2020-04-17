---
title: Azure 코스모스 DB에서 피드 디자인 패턴 변경
description: 일반적인 변경 피드 디자인 패턴 개요
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450354"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Azure 코스모스 DB에서 피드 디자인 패턴 변경

Azure Cosmos DB 변경 피드를 사용하면 많은 양의 쓰기를 사용하여 대용량 데이터 집합을 효율적으로 처리할 수 있습니다. 변경 피드는 또한 변경된 내용을 식별하기 위해 전체 데이터 세트를 쿼리하는 대안을 제공합니다. 이 문서에서는 일반적인 변경 피드 디자인 패턴, 설계 절충 및 변경 피드 제한에 중점을 둡니다.

Azure Cosmos DB는 IoT, 게임, 소매 및 운영 로깅 애플리케이션에 적합합니다. 이러한 애플리케이션에서 일반적인 설계 패턴은 데이터 변경 사항을 사용하여 추가 작업을 트리거하는 것입니다. 추가 작업의 예제는 다음을 포함합니다.

* 항목이 삽입 또는 업데이트되는 경우 알림 또는 API에 대한 호출 트리거
* IoT에 대한 실시간 스트림 처리 또는 운영 데이터에 대한 실시간 분석 처리
* 캐시, 검색 엔진, 데이터 웨어하우스 또는 콜드 스토리지와 동기화하는 것과 같은 데이터 이동입니다.

Azure Cosmos DB의 변경 피드를 사용하면 다음 그림과 같이 이러한 패턴 각각에 대해 효율적이고 확장 가능한 솔루션을 구축할 수 있습니다.

![Azure Cosmos DB 변경 피드를 사용하여 실시간 분석 및 이벤트 기반 컴퓨팅 시나리오 작동](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>이벤트 컴퓨팅 및 알림

Azure Cosmos DB 변경 피드는 특정 이벤트를 기반으로 알림 또는 API 호출을 트리거해야 하는 시나리오를 단순화할 수 있습니다. 변경 피드 [프로세스 라이브러리를](change-feed-processor.md) 사용하여 컨테이너에서 변경 내용을 자동으로 폴링하고 쓰기 또는 업데이트가 있을 때마다 외부 API를 호출할 수 있습니다.

특정 기준에 따라 알림을 선택적으로 트리거하거나 API에 대한 호출을 보낼 수도 있습니다. 예를 들어 [Azure Functions를](change-feed-functions.md)사용하여 변경 피드에서 읽는 경우 논리를 함수에 넣어 특정 조건이 충족된 경우에만 알림을 보낼 수 있습니다. Azure Function 코드는 각 쓰기 및 업데이트 중에 실행되지만 특정 조건이 충족된 경우에만 알림이 전송됩니다.

## <a name="real-time-stream-processing"></a>실시간 스트림 처리

Azure Cosmos DB 변경 피드는 IoT를 위한 실시간 스트림 처리 또는 운영 데이터에 대한 실시간 분석 처리에 사용할 수 있습니다.
예를 들어 [Spark를](../hdinsight/spark/apache-spark-overview.md)사용하여 장치, 센서, 인프라 및 응용 프로그램에서 이벤트 데이터를 수신 및 저장하고 이러한 이벤트를 실시간으로 처리할 수 있습니다. 다음 이미지는 변경 피드를 통해 Azure Cosmos DB를 사용하여 람다 아키텍처를 구현하는 방법을 보여 주며 있습니다.

![수집 및 쿼리를 위한 Azure Cosmos DB 기반 lambda 파이프라인](./media/change-feed/lambda.png)

대부분의 경우 스트림 처리 구현은 먼저 Azure Event Hub 또는 Apache Kafka와 같은 임시 메시지 큐에 대량의 들어오는 데이터를 받습니다. 변경 피드는 낮은 읽기 및 쓰기 대기 시간을 보장하여 지속적인 높은 데이터 수집 속도를 지원하는 Azure Cosmos DB의 기능으로 인해 훌륭한 대안입니다. Azure Cosmos DB 변경 피드의 이점은 메시지 큐에 포함됩니다.

### <a name="data-persistence"></a>데이터 지속성

Azure Cosmos DB에 기록된 데이터는 변경 피드에 표시되고 삭제될 때까지 유지됩니다. 메시지 큐에는 일반적으로 최대 보존 기간이 있습니다. 예를 들어 [Azure 이벤트 허브는](https://azure.microsoft.com/services/event-hubs/) 최대 90일의 데이터 보존을 제공합니다.

### <a name="querying-ability"></a>쿼리 능력

Cosmos 컨테이너의 변경 피드에서 읽는 것 외에도 Azure Cosmos DB에 저장된 데이터에 대한 SQL 쿼리를 실행할 수도 있습니다. 변경 피드는 컨테이너에 이미 있는 데이터의 중복이 아니라 데이터를 읽는 다른 메커니즘일 뿐입니다. 따라서 변경 피드에서 데이터를 읽는 경우 항상 동일한 Azure Cosmos DB 컨테이너의 쿼리와 일치 합니다.

### <a name="high-availability"></a>고가용성

Azure Cosmos DB는 최대 99.999%의 읽기 및 쓰기 가용성을 제공합니다. 많은 메시지 큐와 달리 Azure Cosmos DB 데이터는 [RTO(복구 시간 목표)로](consistency-levels-tradeoffs.md#rto) 쉽게 전역적으로 배포되고 구성될 수 있습니다.

변경 피드에서 항목을 처리한 후 구체화된 뷰를 작성하고 Azure Cosmos DB에서 집계된 값을 다시 유지합니다. 예를 들어, Azure Cosmos DB를 사용하여 게임을 빌드하는 경우 변경 피드를 사용하여 완료된 게임의 점수에 따라 실시간 순위표를 구현할 수 있습니다.

## <a name="data-movement"></a>데이터 이동

또한 실시간 데이터 이동을 위해 변경 피드에서 읽을 수도 있습니다.

예를 들어 변경 피드를 사용하면 다음 작업을 효율적으로 수행할 수 있습니다.

* Azure Cosmos DB에 저장된 데이터로 캐시, 검색 인덱스 또는 데이터 웨어하우스를 업데이트합니다.

* 다른 논리 파티션 키를 사용하여 다른 Azure Cosmos 계정 또는 다른 Azure Cosmos 컨테이너에 대한 중단 시간이 없는 마이그레이션을 수행합니다.

* 응용 프로그램 수준 데이터 계층화 및 보관을 구현합니다. 예를 들어 Azure Cosmos DB에 "핫 데이터"를 저장하고 Azure [Blob Storage와](../storage/common/storage-introduction.md)같은 다른 저장소 시스템에 "콜드 데이터"를 사용할 수 있습니다.

[파티션 및 컨테이너 간에 데이터를 비정규화해야](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)하는 경우 컨테이너의 변경 피드에서 이 데이터 복제의 원본으로 읽을 수 있습니다. 변경 피드를 사용하여 실시간 데이터 복제는 최종 일관성을 보장할 수 있습니다. Cosmos 컨테이너의 변경 내용 처리에서 [변경 피드 프로세서가 얼마나 뒤쳐지는지 모니터링할](how-to-use-change-feed-estimator.md) 수 있습니다.

## <a name="event-sourcing"></a>이벤트 소싱

[이벤트 소싱 패턴에는](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) 추가 전용 저장소를 사용하여 해당 데이터에 대한 전체 일련의 작업을 기록하는 작업이 포함됩니다. Azure Cosmos DB의 변경 피드는 모든 데이터 수집이 쓰기로 모델링되는 이벤트 소싱 아키텍처에서 중앙 데이터 저장소로 선택하는 데 적합합니다(업데이트 또는 삭제 없음). 이 경우 Azure Cosmos DB에 대한 각 쓰기는 "이벤트"이며 변경 피드에서 과거 이벤트에 대한 전체 레코드를 갖게 됩니다. 중앙 이벤트 저장소에서 게시한 이벤트의 일반적인 용도는 구체화된 뷰를 유지 관리하거나 외부 시스템과의 통합을 위한 것입니다. 변경 피드의 보존에는 시간 제한이 없으므로 Cosmos 컨테이너의 변경 피드의 시작 부분에서 모두 읽음으로써 모든 과거 이벤트를 재생할 수 있습니다.

[여러 변경 피드 소비자가 동일한 컨테이너의 변경 피드에 가입하도록 할](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers)수 있습니다. [임대 컨테이너의](change-feed-processor.md#components-of-the-change-feed-processor) 프로비저닝된 처리량을 제외하고 변경 피드를 활용하는 비용은 없습니다. 변경 피드는 사용 여부에 관계없이 모든 컨테이너에서 사용할 수 있습니다.

Azure Cosmos DB는 수평 확장성과 고가용성의 강점으로 인해 이벤트 소싱 패턴에서 훌륭한 중앙 부화 전용 영구 데이터 저장소입니다. 또한 변경 피드 프로세서 라이브러리는 ["최소 한 번"](change-feed-processor.md#error-handling) 보증을 제공하여 이벤트 처리를 놓치지 않도록 합니다.

## <a name="current-limitations"></a>현재 제한 사항

변경 피드에는 이해해야 하는 중요한 제한 사항이 있습니다. Cosmos 컨테이너의 항목은 항상 변경 피드에 남아 있지만 변경 피드는 전체 작업 로그가 아닙니다. 변경 피드를 활용하는 응용 프로그램을 디자인할 때 고려해야 할 중요한 영역이 있습니다.

### <a name="intermediate-updates"></a>중간 업데이트

지정된 항목에 대한 가장 최근 변경 사항만 변경 피드에 포함됩니다. 변경 내용을 처리하면 사용 가능한 최신 항목 버전을 읽습니다. 짧은 시간에 동일한 항목에 대한 업데이트가 여러 개 있는 경우 중간 업데이트 처리를 놓칠 수 있습니다. 업데이트를 추적하고 항목에 대한 지난 업데이트를 재생할 수 있도록 하려면 대신 이러한 업데이트를 일련의 쓰기로 모델링하는 것이 좋습니다.

### <a name="deletes"></a>Deletes

변경 피드는 삭제를 캡처하지 않습니다. 컨테이너에서 항목을 삭제하면 변경 피드에서도 제거됩니다. 이 작업을 처리하는 가장 일반적인 방법은 삭제되는 항목에 소프트 마커를 추가하는 것입니다. "삭제됨"이라는 속성을 추가하고 삭제 시 "true"로 설정할 수 있습니다. 이 문서 업데이트는 변경 피드에 표시됩니다. 나중에 자동으로 삭제할 수 있도록 이 항목에 TTL을 설정할 수 있습니다.

### <a name="guaranteed-order"></a>보장된 주문

파티션 키 값 내에서 변경 피드에는 보장되지만 파티션 키 값에는 포함되지 않습니다. 의미 있는 주문 보증을 제공하는 파티션 키를 선택해야 합니다.

예를 들어 이벤트 소싱 디자인 패턴을 사용하여 소매 응용 프로그램을 고려합니다. 이 응용 프로그램에서 다른 사용자 작업은 Azure Cosmos DB에 쓰기로 모델링되는 각 "이벤트"입니다. 다음 순서로 몇 가지 예제 이벤트가 발생했다고 가정해 보십시오.

1. 고객이 장바구니에 항목 A를 추가합니다.
2. 고객이 장바구니에 항목 B를 추가합니다.
3. 고객이 장바구니에서 항목 A를 제거합니다.
4. 고객 체크 아웃 및 장바구니 내용물 발송

각 고객에 대해 현재 장바구니 내용에 대한 구체화된 보기가 유지됩니다. 이 응용 프로그램은 이러한 이벤트가 발생하는 순서대로 처리되도록 해야 합니다. 예를 들어 항목 A를 제거하기 전에 카트 체크아웃을 처리해야 하는 경우 고객이 원하는 항목 B가 아닌 항목 A를 배송했을 가능성이 높습니다. 이러한 네 이벤트가 발생 순서대로 처리되도록 하려면 동일한 파티션 키 값 에 속해야 합니다. **파티션** 키로 사용자 이름(각 고객에게 고유한 사용자 이름 있음)을 선택하면 이러한 이벤트가 Azure Cosmos DB에 기록된 것과 동일한 순서로 변경 피드에 표시되도록 보장할 수 있습니다.

## <a name="examples"></a>예

다음은 Microsoft 문서에서 제공하는 샘플의 범위를 벗어나는 몇 가지 실제 변경 피드 코드 예제입니다.

- [변경 피드 소개](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [변경 피드를 중심으로 한 IoT 사용 사례](https://github.com/AzureCosmosDB/scenario-based-labs)
- [변경 피드를 중심으로 한 소매 사용 사례](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>다음 단계

* [변경 피드 개요](change-feed.md)
* [변경 피드를 읽는 옵션](read-change-feed.md)
* [Azure Functions로 변경 피드 사용](change-feed-functions.md)