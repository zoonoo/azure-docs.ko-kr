---
title: 메시지 복제 및 지역 간 페더레이션-Azure Service Bus | Microsoft Docs
description: 이 문서에서는 Azure Service Bus를 사용 하 여 이벤트 복제 및 지역 간 페더레이션에 대 한 개요를 제공 합니다.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: e47f633fcd9248eab6f47936aa7c45877decc1fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880830"
---
# <a name="message-replication-and-cross-region-federation"></a>메시지 복제 및 지역 간 페더레이션

네임 스페이스 내에서 Azure Service Bus는 [자동 전달을 사용 하 여 연결 된 큐 및 토픽 구독의 토폴로지를 만들어](service-bus-auto-forwarding.md) 다양 한 라우팅 패턴을 구현할 수 있도록 지원 합니다. 예를 들어, 송신 또는 수신 권한이 있는 전용 큐를 파트너에 게 제공 하 고, 필요한 경우 일시적으로 일시 중단 하 고, 응용 프로그램에 전용으로 사용 되는 다른 엔터티에 유연 하 게 연결할 수 있습니다. 또한 복잡 한 다단계 라우팅 토폴로지를 만들거나 사서함 스타일 큐를 만들어 큐와 같은 항목의 구독을 드레이닝 하 고 구독자 당 추가 저장소 용량을 허용할 수 있습니다. 

또한 많은 정교한 솔루션에서는 이러한 패턴과 기타 패턴을 구현 하기 위해 메시지를 네임 스페이스 경계를 넘어 복제 해야 합니다. 메시지는 여러 다른 응용 프로그램 테 넌 트와 연결 된 네임 스페이스 사이 또는 여러 다른 Azure 지역 간에 이동 해야 할 수 있습니다. 

솔루션은 여러 지역에 여러 Service Bus 네임 스페이스를 유지 하 고 큐와 토픽 간에 메시지를 복제 하며, [Azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure IoT Hub](../iot-fundamentals/iot-introduction.md)또는 [Apache Kafka](https://kafka.apache.org)와 같은 원본 및 대상과 메시지를 교환 합니다. 

이러한 시나리오는이 문서에서 중점적으로 다룹니다. 

## <a name="federation-patterns"></a>페더레이션 패턴

큐 또는 토픽 같은 Service Bus 엔터티 간에 또는 Service Bus와 다른 원본 및 대상 간에 메시지를 이동 해야 하는 이유에는 여러 가지 잠재적 동기 기능이 있습니다. 

[Event Hubs](../service-bus-messaging/service-bus-federation-overview.md)에 대 한 유사한 패턴 집합에 비해, 메시지 큐는 단일 메시지에 대 한 소비자 독점 소유권을 보장 하 고, 메시지 배달 시 도착 순서를 유지 하 고, broker가 [경쟁 소비자](/azure/architecture/patterns/competing-consumers)간에 메시지의 공평 한 분포를 조정 하기 때문에 더 복잡 합니다. 

[CAP 정리](https://en.wikipedia.org/wiki/CAP_theorem)의 제약 조건을 포함 하 여 실제적인 장애 조치 ()는 여러 지역에서 동시에 사용할 수 있는 큐에 대 한 통합 된 보기를 제공 하기 어렵고 지역적으로 분산 된 [소비자](/azure/architecture/patterns/competing-consumers) 가 메시지의 독점 소유권을 가질 수 있도록 합니다. 이러한 지리적으로 분산 된 큐에는 메시지 뿐만 아니라 모든 메시지의 배달 상태에 대해 완전히 일관 된 복제가 필요 하며,이 경우에는 소비자가 메시지를 사용할 수 있습니다. 가상의 지역적으로 분산 큐에 대 한 완전 한 일관성의 목표는 페더레이션 시나리오 (솔루션에 대 한 최대 가용성 및 안정성)를 고려할 때 실제로 모든 Azure Service Bus 고객에 게 제공 되는 주요 목표와 직접적인 충돌입니다. 

여기에 나와 있는 패턴은 가용성과 안정성에 중점을 두 며 정보 손실 및 중복 메시지 처리를 모두 방지 하기 위한 것입니다. 

### <a name="resiliency-against-regional-availability-events"></a>지역 가용성 이벤트에 대 한 복원 력 

최대 가용성 및 안정성은 Service Bus에 대 한 최상위 운영 우선 순위 이지만 생산자 나 소비자가 네트워킹 또는 이름 확인 문제로 인해 할당 된 "기본" Service Bus와 통신 하지 못하게 하거나, Service Bus 엔터티가 일시적으로 응답 하지 않거나 오류를 반환할 수 있는 여러 가지 방법이 있습니다. 지정 된 메시지 프로세서를 사용할 수 없게 될 수도 있습니다.

이러한 조건은 재해 복구 상황에서 수행 하는 것과 같은 방법으로 지역 배포를 완전히 중단 하는 것이 아니라 "재해"는 아니지만 일부 응용 프로그램의 비즈니스 시나리오는 몇 분 또는 몇 초 이상 지속 되는 가용성 이벤트의 영향을 받을 수 있습니다. Azure Service Bus은 종종 하이브리드 클라우드 환경에서 사용 되 고 네트워크에 지에 있는 클라이언트 (예를 들어 소매 매장, 식당, 은행 분기, 제조 사이트, 물류 시설 및 공항)에 사용 됩니다. 네트워크 라우팅 또는 정체 문제로 인해 할당 된 Service Bus 끝점에 도달 하는 한 사이트의 기능에 영향을 줄 수 있지만 다른 지역의 보조 끝점에 연결할 수 있습니다. 동시에 이러한 사이트에서 도착 하는 메시지를 처리 하는 시스템은 기본 및 보조 Service Bus 끝점 모두에 대 한 커뮤니케이션이 액세스를 가질 수 있습니다. 

이러한 하이브리드 클라우드 및에 지 응용 프로그램에는 네트워크 라우팅 문제나 Service Bus 엔터티의 일시적인 가용성 문제에 미치는 영향에 대 한 낮은 비즈니스 공차가 있는 많은 실제적인 예가 있습니다. 이러한 작업에는 소매 사이트의 지불 처리, 공항 게이트에서 보드, 식당의 휴대폰 주문, 모든 것이 즉시 제공 되며 신뢰할 수 있는 통신 경로를 사용할 수 없는 경우에도 완료 됩니다.

이 범주에서는 세 가지 고유한 분산 패턴 인 "모두 활성" 복제, "능동-수동" 복제 및 "spillover" 복제에 대해 설명 합니다. 

#### <a name="all-active-replication"></a>All-Active 복제

"모든 활성" 복제 패턴을 사용 하 여 동일한 논리 토픽 (또는 큐)의 활성 복제본을 여러 네임 스페이스 및 지역에서 사용할 수 있으며 모든 메시지를 큐에 넣은 위치에 관계 없이 모든 복제본에서 사용할 수 있습니다. 패턴은 일반적으로 모든 게시자에 상대적인 메시지의 순서를 유지 합니다. 

![모든 활성 패턴](media/service-bus-federation-overview/mirrored-topics.jpg)

그림에 표시 된 것 처럼 패턴은 일반적으로 Service Bus 항목을 기울어집니다 합니다. 복제 체계에 참여 해야 하는 각 네임 스페이스에 대 한 하나의 항목입니다. 이러한 각 항목에는 메시지를 복제할 다른 항목에 대 한 "복제 구독"이 하나씩 있습니다. 위의 그림에는 항목 쌍이 있으므로 해당 하는 다른 항목에 대 한 단일 복제 구독이 있습니다. 3 개의 네임 스페이스 *{n1, n2, n3}* 를 사용 하는 시나리오에서 *n1* 네임 스페이스의 항목에는 두 개의 복제 구독이 있습니다. 하나는 *n2* 의 해당 항목에 대해 하나이 고 다른 하나는 *n3* 의 해당 토픽에 대 한 것입니다. 

각 복제 구독에는 SQL 필터 식 ( `replicated <> 1` )과 sql 작업 ()을 결합 하는 규칙이 있습니다 `set replicated = 1` . 규칙 필터를 사용 하면 사용자 지정 속성이 `replication` 설정 되어 있지 않거나 `1` 이 구독에 적합 한 값이 없는 메시지만 확인 하 고,이 작업은 정확한 속성을 `1` 선택 된 각 메시지의 값에 대 한 적절 한 값으로 설정 합니다. 즉, 메시지가 해당 토픽에 복사 될 때 더 이상 반대 방향으로 복제에 적합 하지 않으므로 복제본 간에 메시지를 바운스 하지 않도록 합니다.

이러한 규칙을 사용 하는 구독은 다음과 같은 Azure CLI를 사용 하 여 토픽에 쉽게 추가할 수 있습니다.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

큐를 모델링 하기 위해 각 항목은 모든 소비자가 공유 하는 하나의 정기 구독 (복제 구독 제외) 으로만 제한 됩니다.

> 모든 활성 복제 모델은 항목에 전송 된 모든 메시지의 복사본을 각 항목에 넣습니다. 즉, 각 지역의 응용 프로그램 코드에 모든 메시지가 표시 되 고 처리 됩니다.
> 이 패턴은 데이터를 여러 지역으로 공유 하거나 일반적으로 중복 처리가 필요한 시나리오에 적합 합니다. 일반 큐와 마찬가지로 모든 메시지를 한 번만 처리 해야 하는 경우 다음 두 가지 패턴 중 하나를 고려해 야 합니다.  

#### <a name="active-passive-replication"></a>Active-Passive 복제

"능동-수동" 복제 패턴은 응용 프로그램에서 메시지를 보내고 받는 데 사용 하는 토픽 ("주") 중 하나만 사용 하는 이전 패턴의 변형입니다. 기본 항목을 사용할 수 없거나 연결할 수 없는 경우에는 메시지가 보조 항목으로 복제 됩니다. 

![활성 수동 패턴](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

이 패턴과 이전 패턴의 주요 차이점은 복제가 주 항목에서 보조 항목으로의 단방향입니다. 보조 항목은 기본 항목이 아니라 일시적으로 사용할 수 없는 경우에 대 한 백업 옵션입니다. 

이 패턴을 사용 하면 중복 처리를 최소화 하는 데 도움이 됩니다. 복제 하는 동안 `TimeToLive` 메시지 속성은 주 복제본의 실패가 장애 조치 (failover) 될 때 예상 되는 시간을 반영 하는 복제 된 메시지에 대 한 기간으로 설정 됩니다. 예를 들어, 사용 사례 시나리오에서 기본 시작의 메시지를 검색 하는 경우 최대 1 분 이내에 소비자를 보조로 전환 해야 하는 경우 주 데이터베이스에서 액세스할 수 없는 모든 메시지를 보조 데이터베이스에 제공 하는 것이 좋습니다 .이 경우 문제가 나타나기 전에 주 복제본에서 이미 처리 된 메시지의 수는 최소화 됩니다. 복제 하는 `TimeToLive` 동안 (규칙 작업에서) 2 분의 기간을 두 번 설정 하면 보조 데이터베이스는 `set sys.TimeToLive = '0:2:0'` 2 분 동안 메시지만 보존 하 고 오래 된 메시지는 삭제 합니다. 즉, 수신기가 보조로 전환 되 면 마지막으로 처리 한 메시지 보다 오래 된 메시지를 빠르게 읽고 삭제 한 다음 아직 확인 하지 않은 첫 번째 메시지에서 처리할 수 있습니다. 실제 보존 기간은 특정 사용 사례에 따라 다르며, 신속 하 게 원하는 경우 응용 프로그램에서 보조로 전환할 수 있습니다. `TimeToLive`설정은 몇 초에서 며칠 사이에 적용 됩니다. 

응용 프로그램은 보조를 사용 하는 동안 보조 항목에 직접 게시할 수도 있으며,이는 일반 토픽 처럼 작동 합니다. 이 보조로 전환 된 후 소비자는 복제 된 메시지와 보조에 직접 게시 된 메시지를 혼합 하 여 볼 수 있습니다. 따라서 응용 프로그램은 먼저 주 데이터베이스로 다시 게시를 전환 하 고 소비자를 보조로 다시 전환 하기 전에 로컬에서 게시 된 메시지의 드레이닝을 계속 허용 해야 합니다. 주 복제본을 다시 사용할 수 있게 되 면 복제를 자동으로 다시 시작 하기 때문에 소비자는이 시간 동안 주 복제본에 게시 된 새 메시지를 받게 되므로 대기 시간이 다소 길어질 수 있습니다. 

> 이 패턴은 메시지를 한 번만 처리 해야 하는 시나리오에 적합 합니다. 응용 프로그램은 보조 서버의 장애 조치 (failover) 기간 동안 중복 항목을 검색 하 고 다시 전환 하는 동안 중복을 찾기 때문에 주 데이터베이스에서 처리 한 메시지를 추적 하는 데 협력 해야 합니다.
> 중복 제거 기준은 응용 프로그램에서 제공 하는 것이 가장 좋습니다 `MessageId` . `EnqueuedTimeUtc`값은 워터 마크 표시기로도 적합 하지만 응용 프로그램은 분산 시스템에서 기본 및 보조에 대 한 몇 가지 클록 드리프트 (몇 초)를 허용 해야 합니다.


#### <a name="spillover-replication"></a>Spillover 복제

"Spillover" 복제 패턴을 사용 하면 여러 지역에 있는 여러 Service Bus 엔터티의 활성/활성 사용을 통해 Service Bus 정상 상태 이지만 *소비자* 가 보류 중인 메시지의 수 나 완전히 사용할 수 없게 되는 시나리오를 처리할 수 있습니다. 이로 인해 소비자 프로세스를 지 원하는 데이터베이스가 느리거나 사용할 수 없을 수 있습니다. 이 패턴은 일반 큐 및 토픽 구독과 함께 작동 합니다.  

![Spillover 복제](media/service-bus-federation-overview/spillover.jpg)  

그림에 표시 된 것 처럼 spillover 복제 패턴은 큐 또는 구독의 연결 된 [배달 못 한 편지 큐](service-bus-dead-letter-queues.md) 의 메시지를 다른 네임 스페이스의 쌍을 이루는 큐 또는 토픽으로 복제 합니다. 

오류 상황이 발생 하지 않으면 두 개의 네임 스페이스는 병렬로 사용 되며, 각각은 전체 메시지 트래픽의 일부와 해당 하위 집합을 처리 하는 관련 소비자를 수신 합니다. 소비자 중 하나가 높은 실패율을 시작 하거나 완전히 중지 되 면 해당 메시지는 배달 횟수를 초과 하거나 만료 되기 때문에 배달 못 한 편지 큐에 종료 됩니다. 그런 다음 복제 작업에서이를 선택 하 고 쌍을 이루는 큐에서 다시 큐에 넣고, 그런 다음 presumable 정상 소비자에 게 제공 됩니다. 

처리가 특정 최종 기한 이내에 발생 해야 하는 경우에는 `TimeToLive` spillover 보조 데이터베이스에서 처리를 계속 수행할 수 있도록 큐 및/또는 메시지에 대 한를 설정 해야 합니다. 예를 들어 `TimeToLive` 허용 되는 시간의 절반으로 설정 될 수 있습니다.

[모든-활성 패턴과](#all-active-replication)마찬가지로 응용 프로그램은 메시지가 한 번 복제 되어 큐 쌍 간에 바운스 하지 않고 복합 패턴의 배달 못 한 편지 큐 역할을 하는 보조 큐에 게시 되는 메시지에 표시기를 추가할 수 있습니다.

> 이 패턴은 고객이 사용 하는 소비자 또는 리소스의 가용성 문제를 방지 하 고 쌍을 이루는 큐 중 하나에서 트래픽 급증을 재배포할 때 가장 중요 한 문제가 있는 시나리오에 적합 합니다. 또한 소비자가 두 큐 모두에서 읽는 경우에는 네임 스페이스 중 하나에 대 한 보호를 사용할 수 없지만 만료로 인 한 복제 지연으로 `TimeToLive` 인해 해당 기간 내의 메시지가 사용할 수 없는 네임 스페이스에서 strand 수 있습니다. 

### <a name="latency-optimization"></a>대기 시간 최적화 

토픽은 여러 소비자에 게 정보를 배포 하는 데 사용 됩니다. 경우에 따라 지리적으로 분산 된 소비자의 경우 항목의 메시지를 보조 네임 스페이스의 항목으로 복제 하는 것이 유용할 수 있습니다.

![대기 시간 최적화](media/service-bus-federation-overview/latency-optimization.jpg)  

예를 들어, 본토 hubs 지역 간에 데이터를 공유 하는 경우 허브 간에 정보를 한 번만 전송 하는 것이 더 효율적 이며 소비자는 해당 허브에서 데이터의 복사본을 가져옵니다. 

복제 전송은 일괄 처리로 수행할 수 있으며, 소비자는 메시지를 하나씩 메시지를 받아 지급 하는 경우가 많습니다. 북아메리카와 유럽 사이에서 100 밀리초의 기본 네트워크 대기 시간을 사용 하는 경우 각 메시지는 동일한 지역의 엔터티와 비교 하 여 메시지를 획득 하 고 정착 위해 원격 엔터티에 대 한 두 왕복을 처리 하는 데 200 밀리초가 걸립니다. 

### <a name="validation-reduction-and-enrichment"></a>유효성 검사, 감소 및 보강

사용자 고유의 솔루션 외부에 있는 클라이언트에서 Service Bus 큐 또는 토픽에 메시지를 제출할 수 있습니다.

![유효성 검사, 감소, 보강](media/service-bus-federation-overview/validation.jpg)  

이러한 메시지에는 지정 된 스키마를 준수 하는지 확인 해야 할 수 있으며 비규격 메시지는 삭제 하거나 배달 하지 못할 수 있습니다. 일부 메시지는 데이터를 생략 하 여 복잡성을 줄여야 할 수 있으며, 일부 메시지는 참조 데이터 조회에 따라 데이터를 추가 하 여 보강 해야 할 수도 있습니다. 작업은 복제 작업에서 사용자 지정 기능을 사용 하 여 수행할 수 있습니다. 

### <a name="stream-to-queue-replication"></a>큐 복제에 대 한 스트림

Azure Event Hubs는 대량의 들어오는 이벤트를 처리 하는 데 적합 한 솔루션입니다. 그러나 Apache Kafka와 같은 Event Hubs 및 유사한 엔진은 여러 소비자가 중복 처리를 위험 없이 동시에 동일한 소스의 메시지를 처리할 수 있는 서비스 관리 [경쟁 소비자](/azure/architecture/patterns/competing-consumers) 모델을 제공 하 고, 마지막으로 처리 된 후에 해당 메시지를 해결 합니다. 

![통합](media/service-bus-federation-overview/hub-to-queue.jpg)

복제 큐에 대 한 스트림은 단일 이벤트 허브 파티션의 콘텐츠나 전체 이벤트 허브의 콘텐츠를 Service Bus 큐에 전송 합니다. 여기서 메시지를 안전 하 고 트랜잭션 방식으로 처리 하 고 경쟁 소비자를 사용할 수 있습니다. 또한이 복제는 토픽 및 세션 기반 demultiplexing를 포함 하는 라우팅을 포함 하 여 해당 메시지에 대해 다른 모든 Service Bus 기능을 사용할 수 있도록 합니다. 

### <a name="consolidation-and-normalization"></a>통합 및 정규화 

글로벌 솔루션은 일반적으로 자체 처리 기능을 포함 하는 것을 비롯 하 여 거의 독립적인 지역 차지 구성 되어 있지만,이 경우에는 데이터 통합이 필요 하며, 따라서 로컬 관점에서 각 지역 차지에서 평가 되는 것과 동일한 메시지 데이터의 중앙 통합이 필요 합니다. 

![시트가](media/service-bus-federation-overview/merge.jpg)

정규화는 두 개 이상의 들어오는 메시지 시퀀스에서 서로 다른 구조 나 인코딩을 사용 하 여 동일한 종류의 정보를 전달 하 고 메시지를 사용 하기 전에 트랜스 코딩 하거나 변환 해야 하는 통합 시나리오의 한 버전입니다. 

정규화에는 종단 간 암호화 된 페이로드의 암호를 해독 하 고 다운스트림 소비자 대상에 대 한 다른 키와 알고리즘으로 다시 암호화 하는 등의 암호화 작업이 포함 될 수도 있습니다. 

### <a name="splitting-and-routing"></a>분할 및 라우팅

Service Bus 토픽 및 해당 구독 규칙은 종종 특정 대상 그룹에 대 한 메시지 스트림을 필터링 하는 데 사용 되며, 해당 대상이 구독에서 필터링 된 집합을 가져옵니다. 

![분할](media/service-bus-federation-overview/split.jpg)

이러한 메시지의 대상이 전역적으로 분산 되거나 다른 응용 프로그램에 속해 있는 글로벌 시스템에서 복제를 사용 하 여 이러한 구독에서 사용 되는 위치와 다른 네임 스페이스의 큐 또는 토픽으로 메시지를 전송할 수 있습니다.

### <a name="replication-applications-in-azure-functions"></a>Azure Functions의 복제 응용 프로그램

위의 패턴을 구현 하려면 구성 및 실행 하려는 복제 작업에 대 한 확장 가능 하 고 안정적인 실행 환경이 필요 합니다. Azure에서 상태 비저장 작업에 가장 적합 한 런타임 환경을 [Azure Functions](../azure-functions/functions-overview.md)합니다. 

복제 태스크는 복제 경로를 따라 암호를 관리 하지 않고도 원본 및 대상 서비스의 역할 기반 액세스 제어 규칙과 통합 될 수 있도록 [Azure 관리 id](../active-directory/managed-identities-azure-resources/overview.md) 로 실행 될 수 Azure Functions. 명시적 자격 증명을 요구 하는 복제 원본 및 대상의 경우 [Azure Key Vault](../key-vault/general/overview.md)내의 긴밀 한 액세스 제어 저장소에서 해당 자격 증명에 대 한 구성 값을 저장할 수 Azure Functions.

또한 Azure Functions를 사용 하면 복제 태스크가 모든 Azure 메시징 서비스에 대 한 Azure 가상 네트워크 및 [서비스 끝점과](../virtual-network/virtual-network-service-endpoints-overview.md) 직접 통합 될 수 있으며, [Azure Monitor](../azure-monitor/overview.md)와 쉽게 통합 됩니다.

가장 중요 한 점은 [azure Event Hubs](../azure-functions/functions-bindings-service-bus.md), [Azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md), [azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md), [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)에 대 한 사용자 지정 확장, [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)에 대 한 미리 작성 되 고 확장 가능한 트리거 및 출력 바인딩이 Azure Functions입니다. 대부분의 트리거는 문서화 된 메트릭에 따라 동시에 실행 되는 인스턴스 수를 확장 하 여 처리량 요구에 맞게 동적으로 조정 됩니다. 

Azure Functions 소비 계획을 사용 하는 경우 미리 작성 된 트리거는 복제에 사용할 수 있는 메시지가 없는 상태에서 0으로 축소 될 수도 있습니다. 즉, 구성의 크기를 조정할 수 있도록 유지 하는 비용이 들지 않습니다. 소비 계획을 사용 하는 경우의 주요 단점은이 상태에서 "절전 모드 해제" 복제 작업에 대 한 대기 시간이 인프라를 실행 하는 호스팅 계획 보다 훨씬 높기 때문입니다.  

이와 대조적으로 메시징 및 이벤트에 대 한 대부분의 일반적인 복제 엔진 (예: Apache Kafka의 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) )은 호스팅 환경을 제공 하 고 복제 엔진을 직접 확장 해야 합니다. 여기에는 보안 및 네트워킹 기능을 구성 하 고 통합 하 고 모니터링 데이터의 흐름을 원활 하 게 유지 하는 작업이 포함 됩니다. 그러면 사용자 지정 복제 태스크를 흐름에 삽입할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 다양 한 페더레이션 패턴을 설명 하 고 Azure에서 이벤트 및 메시징 복제 런타임으로 Azure Functions 역할에 대해 설명 했습니다. 

다음으로 Azure Functions를 사용 하 여 복제기 응용 프로그램을 설정 하는 방법을 확인 하 고 Event Hubs와 다양 한 이벤트 및 메시징 시스템 간에 이벤트 흐름을 복제 하는 방법을 확인할 수 있습니다.

- [Azure Functions의 복제 응용 프로그램](service-bus-federation-replicator-functions.md)
- [Service Bus 엔터티 간에 이벤트 복제](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Azure Event Hubs로 이벤트 라우팅](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Azure Event Hubs에서 이벤트 가져오기](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif