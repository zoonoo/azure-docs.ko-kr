---
title: 다중 사이트 및 다중 지역 페더레이션-Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs를 사용 하 여 다중 사이트 및 다중 지역 페더레이션에 대 한 개요를 제공 합니다.
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861459"
---
# <a name="multi-site-and-multi-region-federation"></a>다중 사이트 및 다중 지역 페더레이션

많은 정교한 솔루션에는 여러 위치에서 사용할 수 있도록 동일한 이벤트 스트림을 사용 해야 하거나 여러 위치에서 이벤트 스트림을 수집 하 여 사용을 위해 특정 위치로 통합 해야 합니다. 또한 단일 지역 및 솔루션 내에서 이벤트 스트림을 보강 하거나 줄이고 이벤트 형식 변환을 수행 해야 하는 경우가 종종 있습니다.

즉, 일반적으로 솔루션은 여러 개의 Event Hubs를 유지 관리 하 고, 여러 지역 및 Event Hubs 네임 스페이스에 있는 경우에는 여러 개의를 유지 한 다음 이들 간에 이벤트를 또한 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), [Azure IoT Hub](../iot-fundamentals/iot-introduction.md), [Apache Kafka](https://kafka.apache.org)등의 원본 및 대상과 이벤트를 교환할 수 있습니다. 

서로 다른 지역에서 여러 활성 Event Hubs를 유지 관리 하면 해당 콘텐츠를 병합 하는 경우 클라이언트에서 해당 콘텐츠를 선택 하 여 전환할 수 있습니다. 그러면 지역 가용성 문제에 대 한 전체 시스템 복원 력이 향상 됩니다.

이 "페더레이션" 장에서는 이벤트 흐름 경로에 고유한 변환 또는 보강 코드를 사용 하는 옵션과 함께 서버 리스 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 또는 [Azure Functions][1] 런타임을 사용 하 여 이러한 패턴을 실현 하는 방법에 대해 설명 합니다. 

## <a name="federation-patterns"></a>페더레이션 패턴

서로 다른 Event Hubs 또는 다른 원본 및 대상 간에 이벤트를 이동 해야 하는 이유에는 여러 가지 잠재적 동기 기능이 있습니다 .이 섹션에서 가장 중요 한 패턴을 열거 하 고 각 패턴에 대 한 자세한 지침을 제공 합니다. 

- [지역 가용성 이벤트에 대 한 복원 력](#resiliency-against-regional-availability-events)
- [대기 시간 최적화](#latency-optimization)
- [유효성 검사, 감소 및 보강](#validation-reduction-and-enrichment)
- [Analytics 서비스와의 통합](#integration-with-analytics-services)
- [이벤트 스트림의 통합 및 정규화](#consolidation-and-normalization-of-event-streams)
- [이벤트 스트림 분할 및 라우팅](#splitting-and-routing-of-event-streams)
- [로그 프로젝션](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>지역 가용성 이벤트에 대 한 복원 력 

![국가별 가용성](media/event-hubs-federation-overview/regional-availability.jpg)

최대 가용성 및 안정성은 Event Hubs에 대 한 최상위 운영 우선 순위 이지만 생산자 나 소비자가 네트워킹 또는 이름 확인 문제로 인해 할당 된 "기본" 이벤트 허브에 대 한 통신을 방지 하거나 이벤트 허브가 일시적으로 응답 하지 않거나 오류를 반환할 수 있는 여러 가지 방법이 있습니다. 

이러한 조건은 [재해 복구](event-hubs-geo-dr.md) 상황에서 수행 하는 것과 같은 방법으로 지역 배포를 완전히 중단 하는 것이 아니라 "재해" 되지 않습니다. 하지만 일부 응용 프로그램의 비즈니스 시나리오는 몇 분 또는 초를 초과 하지 않는 가용성 이벤트의 영향을 받을 수 있습니다. 

이러한 시나리오를 해결 하기 위한 두 가지 기본적인 패턴이 있습니다.

- [복제][4] 패턴은 기본 이벤트 허브의 콘텐츠를 보조 이벤트 허브로 복제 하는 방법에 대 한 것입니다 .이 경우 기본 이벤트 허브는 응용 프로그램에서 생성 및 소비 이벤트 모두에 사용 되 고 보조는 기본 이벤트 허브를 사용할 수 없게 될 때 대체 옵션으로 사용 됩니다. 복제는 주로 주에서 보조로 전환 하는 동시에 사용할 수 없는 주 데이터베이스에서 보조 데이터베이스로 생산자와 소비자를 전환 하면 이전 주 데이터베이스에서 더 이상 새 이벤트를 수신 하지 않으므로 최신 상태가 되지 않습니다.
  따라서 순수 복제는 단방향 장애 조치 (failover) 시나리오에만 적합 합니다. 장애 조치 (failover)를 수행한 후에는 이전 주 복제본이 중단 되 고 다른 대상 지역에 새 보조 이벤트 허브가 생성 되어야 합니다.
- [병합][5] 패턴은 둘 이상의 Event Hubs 콘텐츠를 연속으로 병합 하 여 복제 패턴을 확장 합니다. 구성표에 포함 된 Event Hubs 중 하나로 원래 생성 된 각 이벤트는 다른 Event Hubs에 복제 됩니다. 이벤트가 복제 될 때 복제 대상의 복제 프로세스에서 이후에 무시 되도록 주석이 추가 됩니다. 병합 패턴을 사용한 결과는 궁극적으로 일관 된 방식으로 동일한 이벤트 집합을 포함 하는 둘 이상의 Event Hubs입니다. 
  
두 경우 모두 Event Hubs의 내용이 동일 하지 않습니다. 하나의 생산자에서 발생 한 이벤트와 동일한 파티션 키로 그룹화 된 이벤트는 원래 제출 된 것과 동일한 상대적 순서로 나타나지만 이벤트의 절대 순서는 다를 수 있습니다. 이는 원본 및 대상 Event Hubs의 파티션 수가 다를 경우에 특히 유용 합니다 .이는 여기에서 설명 하는 몇 가지 확장 패턴에 적합 합니다. [분할자 또는 라우터](#splitting-and-routing-of-event-streams) 는 수백 개의 파티션이 있는 훨씬 더 큰 이벤트 허브의 조각을 얻고 몇 개의 파티션만 포함 된 작은 이벤트 허브로 깔때기를 가져올 수 있으며, 제한 된 처리 리소스로 하위 집합을 처리 하는 데 더 적합 합니다. 반대로 [통합](#consolidation-and-normalization-of-event-streams) 은 몇 개의 작은 Event Hubs의 데이터를 더 많은 파티션이 있는 단일 대형 이벤트 허브로 집중 하 여 통합 된 처리량 및 처리 요구를 처리할 수 있습니다.

이벤트를 함께 유지 하기 위한 기준은 파티션 키 이며 원래 파티션 ID가 아닙니다. 동일한 스트림 오프셋 범위를 사용 하지 않고 특정 이벤트 허브에서 다음 이벤트 허브로 장애 조치 (failover)를 수행 하는 방법에 대 한 추가 고려 사항은 [복제][4] 패턴 설명에 설명 되어 있습니다.


지침 
- [복제 패턴][4]
- [Merge 패턴][5]

### <a name="latency-optimization"></a>대기 시간 최적화 

![대기 시간 최적화](media/event-hubs-federation-overview/latency-optimization.jpg)  

이벤트 스트림은 생산자에 의해 한 번만 기록 되지만 이벤트 소비자가 여러 번 읽을 수 있습니다. 여러 소비자가 한 지역의 이벤트 스트림을 공유 하 고 다른 지역에 있는 분석 처리 중에 반복 해 서 액세스 하거나 동시 소비자를 결핍 하는 전체 요구 사항에 따라 반복 해 서 액세스 해야 하는 시나리오의 경우, 왕복 대기 시간을 줄이기 위해 분석 프로세서 근처에 이벤트 스트림의 복사본을 저장 하는 것이 유용할 수 있습니다. 

여러 지역에서 원격으로 이벤트를 소비 하는 것 보다 복제를 선호 하는 경우, 특히 지역이 매우 멀리 떨어져 있는 경우, 유럽 및 오스트레일리아를 거의 방지 하지 않는 지역 및 네트워크 대기 시간에 대 한 좋은 예는 왕복에 대해 250 밀리초를 쉽게 초과할 수 있습니다.
빛 속도는 가속화할 수 없지만 데이터와 상호 작용 하기 위해 대기 시간이 긴 왕복 횟수를 줄일 수 있습니다.

지침 
- [복제 패턴][4]

### <a name="validation-reduction-and-enrichment"></a>유효성 검사, 감소 및 보강

![유효성 검사, 감소, 보강](media/event-hubs-federation-overview/validation-enrichment.jpg)  

사용자 고유의 솔루션 외부에 있는 클라이언트는 이벤트 스트림을 이벤트 허브에 제출할 수 있습니다. 이러한 이벤트 스트림은 외부에서 전송 된 이벤트를 지정 된 스키마와의 호환성을 검사 하 고 비규격 이벤트를 삭제 해야 할 수 있습니다. 

데이터 요금제를 사용 하는 다양 한 "사물 인터넷" 시나리오의 경우 또는 원래 이벤트가 제한 된 패킷 크기를 가진 비 IP 네트워크를 통해 전송 되는 경우와 같이 클라이언트의 대역폭이 매우 높은 시나리오에서 이벤트는 다운스트림 이벤트 프로세서에서 사용할 수 있도록 추가 컨텍스트를 추가 하기 위해 참조 데이터로 보강 될 수 있습니다.

특히 스트림을 통합 하는 경우에는 일부 세부 정보를 생략 하 여 이벤트 데이터를 복잡성 또는 크기의 크기로 줄여야 할 수 있습니다.

이러한 작업은 복제, 통합 또는 병합 흐름의 일부로 발생할 수 있습니다. 

지침 
- [편집기 패턴][6]

### <a name="integration-with-analytics-services"></a>Analytics 서비스와의 통합

![Analytics 서비스와의 통합](media/event-hubs-federation-overview/integration.jpg)

Azure Event Hubs Event Hubs에서 제공 하는 스트리밍 또는 미리 배치 된 데이터를 사용 하 여 Azure Stream Analytics 또는 azure Synapse와 같은 Azure의 클라우드 기본 분석 서비스 중 일부를 사용 하면 Apache Samza, Apache Flink, Apache Spark, Apache Storm 등의 여러 오픈 소스 분석 패키지와 통합할 수 있습니다. 

솔루션에서 주로 Service Bus 또는 Event Grid를 사용 하는 경우 이러한 분석 시스템에서 이러한 이벤트를 쉽게 액세스할 수 있도록 하 고 이벤트 허브로 전환 하는 경우 Event Hubs 캡처를 사용 하 여 보관 합니다. Event Grid는 해당 [이벤트 허브 통합](../event-grid/handler-event-hubs.md)을 사용 하 여 기본적으로 수행할 수 있으며 Service Bus에 대해 [Service Bus 복제 지침](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)을 따릅니다.

Azure Stream Analytics [Event Hubs와 직접 통합](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)됩니다.

지침 
- [복제 패턴][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>이벤트 스트림의 통합 및 정규화

![이벤트 스트림의 통합 및 정규화](media/event-hubs-federation-overview/consolidation.jpg)

글로벌 솔루션은 일반적으로 자체 분석 기능을 포함 하는 것과 거의 독립적인 지역 차지 구성 되어 있지만,이 경우에는 통합 된 관점이 필요 하며, 로컬 관점에서 해당 지역 차지에서 평가 되는 동일한 이벤트 스트림을 중앙에서 통합 하는 이유가 있습니다. 

정규화는 두 개 이상의 들어오는 이벤트 스트림이 동일한 종류의 이벤트를 사용 하지만 다른 구조 또는 다른 인코딩을 사용 하 고 이벤트를 사용 하기 전에 트랜스 코딩 하거나 변환 하는 통합 시나리오의 한 버전입니다. 

정규화에는 종단 간 암호화 된 페이로드의 암호를 해독 하 고 다운스트림 소비자 대상에 대 한 다른 키와 알고리즘으로 다시 암호화 하는 등의 암호화 작업이 포함 될 수도 있습니다. 

지침 
- [Merge 패턴][5]
- [편집기 패턴][6]

### <a name="splitting-and-routing-of-event-streams"></a>이벤트 스트림 분할 및 라우팅

![이벤트 스트림 분할 및 라우팅](media/event-hubs-federation-overview/splitting.jpg)

Azure Event Hubs는 "게시-구독" 스타일 시나리오에서 사용 되는 경우가 있습니다 .이 스타일 시나리오에서는 수집 이벤트의 들어오는 torrent이 Azure Service Bus 또는 Azure Event Grid의 용량을 초과 하 고, 둘 다 기본 게시-구독 필터링 및 배포 기능을 포함 하며이 패턴에 대해 선호 됩니다 

True "게시-구독" 기능을 사용 하 여 원하는 이벤트를 선택할 수 있는 반면 분할 패턴에는 미리 결정 된 배포 모델과 지정 된 소비자의 파티션에 대 한 생산자 맵 이벤트가 포함 되며, 그 다음에는 "해당" 파티션에서 배타적으로 끌어옵니다. 전체 트래픽을 버퍼링 하는 이벤트 허브를 사용 하 여 원본 처리량 볼륨의 비율을 나타내는 특정 파티션의 콘텐츠를 안정적인 트랜잭션, 경쟁 소비자 소비에 대 한 큐에 복제할 수 있습니다.

주로 지역 내의 응용 프로그램 내에서 이벤트를 이동 하는 데 사용 되는 여러 Event Hubs 시나리오에는 단일 파티션에서의 선택 이벤트도 다른 곳에서 사용할 수 있도록 설정 해야 하는 경우가 있습니다. 이 시나리오는 분할 시나리오와 유사 하지만 이벤트 허브에 도착 하는 모든 메시지를 고려 하는 확장 가능한 라우터를 사용할 수 있으며, cherry-pick는 앞으로 라우팅하는 경우를 제외 하 고 라우팅 대상을 이벤트 메타 데이터 또는 콘텐츠와 구분할 수 있습니다. 

지침
- [라우팅 패턴][7]

### <a name="log-projections"></a>로그 프로젝션 

![로그 프로젝션](media/event-hubs-federation-overview/log-projection.jpg)

일부 시나리오에서는 이벤트의 하위 스트림 용으로 전송 된 최신 값에 액세스 하 고 일반적으로 파티션 키로 구분 하는 것이 좋습니다. Apache Kafka에서 "로그 압축"을 사용 하도록 설정 하면 일반적으로 모든 고유 키로 레이블이 지정 된 최신 이벤트를 제외한 모든 항목을 삭제 합니다. 로그 압축 방법에는 세 가지 그래야만 단점이 있습니다. 

- 압축을 사용 하려면 로그를 지속적으로 재구성 해야 하며,이는 추가 전용 워크 로드에 최적화 된 broker에 대해 과도 한 부담이 큰 작업입니다. 
- 압축은 소거식 이며 동일한 스트림의 압축 된와 압축 되지 않은 큐브 뷰를 허용 하지 않습니다.
- 압축 된 스트림은 순차적 액세스 모델을 포함 하 고 있습니다. 즉, 로그에서 원하는 값을 찾는 것이 최악의 경우에는 전체 로그를 읽어야 합니다 .이는 일반적으로 여기에 제공 된 정확한 패턴을 구현 하는 최적화를 야기 하는 데이터베이스 또는 캐시로 로그 내용을 프로젝션 하는 것입니다. 

궁극적으로 압축 된 로그는 키-값 저장소 이며, 이러한 저장소에 대해 가능한 가장 낮은 구현 옵션입니다. 조회 및 쿼리에서 올바른 키-값 저장소 또는 다른 데이터베이스에 대 한 로그의 영구 프로젝션을 만들고 사용 하는 것이 훨씬 더 효율적입니다. 

이벤트는 변경할 수 없으며 순서는 항상 로그에서 유지 되므로 키-값 저장소에 대 한 로그의 모든 프로젝션은 항상 동일한 이벤트 범위에 대해 동일 합니다. 즉, 업데이트를 유지 하는 프로젝션은 항상 신뢰할 수 있는 뷰를 제공 하 고, 작성 한 후에는 로그 콘텐츠에서 다시 작성 하는 것이 좋은 이유가 없습니다. 

지침
- [로그 프로젝션][8]

## <a name="replication-application-technologies"></a>복제 응용 프로그램 기술

위의 패턴을 구현 하려면 구성 및 실행 하려는 복제 작업에 대 한 확장 가능 하 고 안정적인 실행 환경이 필요 합니다. Azure에서 이러한 작업에 가장 적합 한 런타임 환경은 상태 저장 스트림 복제 작업 및 [Azure Functions](../azure-functions/functions-overview.md) 상태 비저장 복제 작업에 대 한 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 입니다.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Azure Stream Analytics의 상태 저장 복제 응용 프로그램

이벤트 간의 관계를 고려해 야 하는 상태 저장 복제 응용 프로그램의 경우, 복합 이벤트를 만들거나 이벤트를 보강 하거나 이벤트를 줄이고 이벤트 페이로드를 변환 하는 것이 가장 좋은 구현 옵션 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 입니다.

Azure Stream Analytics에서 [입력](../stream-analytics/stream-analytics-add-inputs.md) 및 [출력](../stream-analytics/stream-analytics-define-outputs.md) 을 통합 하 고 출력에서 사용할 수 있는 결과를 생성 하는 [쿼리](/stream-analytics-query/stream-analytics-query-language-reference) 를 통해 입력의 데이터를 통합 하는 [작업을 만듭니다](../stream-analytics/stream-analytics-quick-create-portal.md) .

쿼리는 [SQL 쿼리 언어](/stream-analytics-query/stream-analytics-query-language-reference) 를 기반으로 하며 일정 시간 동안 스트리밍 데이터를 쉽게 필터링, 정렬, 집계 및 조인 하는 데 사용할 수 있습니다. [JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) 및 [c # udf (사용자 정의 함수)](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md)를 사용 하 여이 SQL 언어를 확장할 수도 있습니다. 간단한 언어 구성 및/또는 구성을 통해 집계 작업을 수행할 때 이벤트 순서 지정 옵션 및 시간 범위 기간을 손쉽게 조정할 수 있습니다.

각 작업에는 변환된 데이터에 대한 하나 또는 여러 개의 출력이 있으며, 분석한 정보에 대한 응답으로 수행되는 작업을 제어 할 수 있습니다. 예를 들어, 다음을 수행할 수 있습니다.

- Azure Functions, Service Bus 항목 또는 큐와 같은 서비스에 데이터를 보내 통신을 트리거하거나 워크플로 다운스트림을 사용자 지정합니다.
- 실시간 대시보드를 위해 Power BI 대시보드로 데이터를 전송합니다.
- 다른 Azure 저장소 서비스 (예: Azure Data Lake, Azure Synapse Analytics 등)에 데이터를 저장 하 여 일괄 분석을 수행 하거나 기록 데이터의 매우 크고 인덱싱된 풀을 기반으로 기계 학습 모델을 학습 합니다.
- 데이터베이스 ([SQL Database](../stream-analytics/sql-database-output.md), [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) )의 저장소 프로젝션 ("구체화 된 뷰" 라고도 함)

### <a name="stateless-replication-applications-in-azure-functions"></a>Azure Functions의 상태 비저장 복제 응용 프로그램

페이로드를 고려 하지 않고 이벤트를 전달 하거나 상대적인 순서를 제외 하 고 이벤트의 관계를 고려할 필요 없이 단일 프로세스로 처리 하려는 상태 비저장 복제 작업의 경우 상당한 유연성을 제공 하는 Azure Functions를 사용할 수 있습니다.

Azure Functions에는 [azure Event Hubs](../azure-functions/functions-bindings-event-hubs.md), [Azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)및 [azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md)에 대 한 미리 작성 되 고 확장 가능한 트리거 및 출력 바인딩과 [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)및 [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)의 사용자 지정 확장도 있습니다. 대부분의 트리거는 문서화 된 메트릭에 따라 동시에 실행 되는 인스턴스 수를 확장 하 여 처리량 요구에 맞게 동적으로 조정 됩니다. 

로그 프로젝션을 빌드하기 위해 Azure Functions [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) 및 [Azure Table Storage](../azure-functions/functions-bindings-storage-table-output.md)에 대 한 출력 바인딩을 지원 합니다.

Azure Functions은 [Azure 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md) 로 실행 될 수 있으며,이를 통해 [Azure Key Vault](../key-vault/general/overview.md)내부의 긴밀 한 액세스 제어 저장소에서 자격 증명에 대 한 구성 값을 보유할 수 있습니다.

또한 Azure Functions를 사용 하면 복제 태스크가 모든 Azure 메시징 서비스에 대 한 Azure 가상 네트워크 및 [서비스 끝점과](../virtual-network/virtual-network-service-endpoints-overview.md) 직접 통합 될 수 있으며,이는 [Azure Monitor](../azure-monitor/overview.md)와 손쉽게 통합 됩니다.

Azure Functions 소비 계획을 사용 하는 경우 미리 작성 된 트리거는 복제에 사용할 수 있는 메시지가 없는 상태에서 0으로 축소 될 수도 있습니다. 즉, 구성의 크기를 조정할 수 있도록 유지 하기 위해 비용이 발생 하지 않습니다. 소비 계획을 사용 하는 경우의 주요 단점은이 상태에서 "절전 모드 해제" 복제 작업에 대 한 대기 시간이 인프라를 실행 하는 호스팅 계획 보다 훨씬 높기 때문입니다.  

이와 대조적으로 메시징 및 이벤트에 대 한 대부분의 일반적인 복제 엔진 (예: Apache Kafka의 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) )은 호스팅 환경을 제공 하 고 복제 엔진을 직접 확장 해야 합니다. 여기에는 보안 및 네트워킹 기능을 구성 하 고 통합 하 고 모니터링 데이터의 흐름을 원활 하 게 유지 하는 작업이 포함 됩니다. 그러면 사용자 지정 복제 태스크를 흐름에 삽입할 수 없습니다. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Azure Functions 및 Azure Stream Analytics 중에서 선택

Azure Stream Analytics (는)를 복제 하는 동안 이벤트의 페이로드를 처리 해야 할 때마다 가장 좋은 옵션입니다. GLOBAL.ASA는 이벤트를 하나씩 복사 하거나 전달 하기 전에 이벤트 스트림의 정보를 축소 하는 집계를 만들 수 있습니다. 이러한 데이터를 스트림으로 가져올 필요 없이 Azure Blob Storage 또는 Azure SQL Database에 보관 된 [보완 참조 데이터를 쉽게](../stream-analytics/stream-analytics-use-reference-data.md) 사용할 수 있습니다.

이를 통해 대규모로 확장 된 데이터베이스에서 영구적이 고 구체화 된 뷰를 손쉽게 만들 수 있습니다. Clunky의 "로그 압축" 모델 Apache Kafka에 대 한 훨씬 뛰어난 접근 방식으로, Kafka 스트림의 일시적인 클라이언트 쪽 테이블 프로젝션입니다. 

[이는 CSV, JSON 및 Apache Avro 형식](../stream-analytics/stream-analytics-parsing-json.md) 으로 페이로드가 인코딩된 이벤트를 쉽게 처리할 수 있으며, 다른 형식에 대해 [사용자 지정 deserializers](../stream-analytics/custom-deserializer.md) 에 연결할 수 있습니다.

이벤트 스트림을 "있는 그대로" 복사 하 고 페이로드를 발생 시 키 지 않고, 라우터를 구현 하거나, 암호화 작업을 수행 하거나, 페이로드의 인코딩을 변경 하거나, 그렇지 않은 경우 데이터 스트림 내용을 완전히 제어 해야 하는 모든 복제 작업에 대해 Azure Functions가 가장 좋습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다양 한 페더레이션 패턴을 설명 하 고 Azure에서 이벤트 및 메시징 복제 런타임으로 Azure Functions 역할에 대해 설명 했습니다.

다음으로 Azure Stream Analytics 또는 Azure Functions를 사용 하 여 복제기 응용 프로그램을 설정 하는 방법을 확인 하 고 Event Hubs와 기타 다양 한 이벤트 및 메시징 시스템 간에 이벤트 흐름을 복제 하는 방법을 확인할 수 있습니다.

- [이벤트 복제 작업 패턴][10]
- [Azure Stream Analytics를 사용 하 여 데이터 처리][9]
- [Azure Functions의 이벤트 복제기 응용 프로그램][1]
- [Event Hubs 간 이벤트 복제][2]
- [Azure Service Bus에 이벤트 복제][3]
- [Event Hubs에서 Apache Kafka MirrorMaker 사용][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md