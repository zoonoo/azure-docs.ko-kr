---
title: Apache Kafka HDInsight 클러스터의 성능 최적화
description: Azure HDInsight에서 Apache Kafka 작업을 최적화 하는 방법에 대 한 개요를 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75494915"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight 클러스터의 성능 최적화

이 문서에서는 HDInsight에서 Apache Kafka 작업의 성능을 최적화 하기 위한 몇 가지 제안을 제공 합니다. 이에 대 한 초점은 생산자 및 broker 구성을 조정 하는 것입니다. 성능을 측정 하는 다양 한 방법이 있으며, 적용 되는 최적화는 비즈니스 요구 사항에 따라 달라 집니다.

## <a name="architecture-overview"></a>아키텍처 개요

Kafka 토픽은 레코드를 구성 하는 데 사용 됩니다. 생산자에서 레코드를 생성하고, 소비자에서 이 레코드를 소비합니다. 생산자는 Kafka broker에 레코드를 전송 하 여 데이터를 저장 합니다. HDInsight 클러스터의 각 작업자 노드는 Kafka broker입니다.

토픽은 브로커 간에 레코드를 분할합니다. 레코드를 소비할 때 데이터의 병렬 처리를 위해 파티션당 최대 1개의 소비자를 사용할 수 있습니다.

복제는 여러 노드에서 파티션을 복제 하는 데 사용 됩니다. 이렇게 하면 노드 (브로커) 가동 중단 으로부터 보호 됩니다. 복제본 그룹 간의 단일 파티션은 파티션 리더로 지정 됩니다. 생산자 트래픽은 ZooKeeper에서 관리하는 상태를 사용하여 각 노드의 선행부로 라우팅됩니다.

## <a name="identify-your-scenario"></a>시나리오 식별

Apache Kafka 성능에는 처리량과 대기 시간 이라는 두 가지 주요 측면이 있습니다. 처리량은 데이터를 처리할 수 있는 최대 속도입니다. 일반적으로 처리량이 더 높습니다. 대기 시간은 데이터를 저장 하거나 검색 하는 데 소요 되는 시간입니다. 일반적으로 낮은 대기 시간이 더 좋습니다. 처리량, 대기 시간 및 응용 프로그램 인프라 비용 간의 적절 한 균형을 찾는 것은 어려울 수 있습니다. 성능 요구 사항은 높은 처리량, 짧은 대기 시간 또는 둘 다가 필요한 지에 따라 다음과 같은 세 가지 일반적인 상황 중 하 나와 일치할 수 있습니다.

* 높은 처리량, 짧은 대기 시간. 이 시나리오에는 높은 처리량과 짧은 대기 시간 (~ 100 밀리초)이 모두 필요 합니다. 이러한 유형의 응용 프로그램 예는 서비스 가용성 모니터링입니다.
* 높은 처리량, 긴 대기 시간. 이 시나리오에서는 높은 처리량 (~ 1.5 GBps)이 필요 하지만 더 높은 대기 시간 (< 250 밀리초)을 허용할 수 있습니다. 이러한 유형의 응용 프로그램 예제는 보안 및 침입 감지 응용 프로그램과 같은 거의 실시간 프로세스를 위한 원격 분석 데이터 수집입니다.
* 낮은 처리량, 짧은 대기 시간. 이 시나리오에서는 실시간 처리에 짧은 대기 시간 (< 10 밀리초)이 필요 하지만 처리량이 낮아질 수 있습니다. 이러한 유형의 응용 프로그램에 대 한 예는 온라인 맞춤법 및 문법 검사입니다.

## <a name="producer-configurations"></a>생산자 구성

다음 섹션에서는 Kafka 생산자의 성능을 최적화 하기 위한 가장 중요 한 구성 속성 중 일부를 강조 합니다. 모든 구성 속성에 대 한 자세한 설명은 [생산자 구성에 대 한 Apache Kafka 설명서](https://kafka.apache.org/documentation/#producerconfigs)를 참조 하세요.

### <a name="batch-size"></a>Batch 크기

Apache Kafka 생산자는 단일 저장소 파티션에 저장할 단위로 전송 되는 메시지 그룹 (일괄 처리)을 조합 합니다. 일괄 처리 크기는 해당 그룹이 전송 되기 전에 존재 해야 하는 바이트 수를 의미 합니다. `batch.size`매개 변수를 늘리면 네트워크 및 IO 요청에서 처리 오버 헤드가 줄어들기 때문에 처리량이 증가할 수 있습니다. 부하가 낮은 상태에서 일괄 처리 크기를 늘리면 생산자가 일괄 처리 준비가 될 때까지 대기 하므로 Kafka 전송 대기 시간이 늘어날 수 있습니다. 부하가 높은 상태에서 처리량 및 대기 시간을 향상 시키기 위해 일괄 처리 크기를 늘리는 것이 좋습니다.

### <a name="producer-required-acknowledgments"></a>생산자 필수 승인

생산자 필수 `acks` 구성은 쓰기 요청을 완료 된 것으로 간주 하기 전에 파티션 리더에 필요한 승인 수를 결정 합니다. 이 설정은 데이터 안정성에 영향을 주며 `0` , 또는 값을 사용 `1` `-1` 합니다. 값은 `-1` 쓰기를 완료 하기 전에 모든 복제본에서 승인을 받아야 함을 의미 합니다. 설정은 `acks = -1` 데이터 손실에 대해 더 강력한 보장을 제공 하지만 대기 시간이 길어지고 처리량이 낮아집니다. 응용 프로그램 요구 사항에서 더 높은 처리량을 요구 하는 경우 또는을 설정 해 봅니다 `acks = 0` `acks = 1` . 모든 복제본을 승인 하지 않으면 데이터 안정성을 줄일 수 있습니다.

### <a name="compression"></a>압축

메시지를 브로커에 보내기 전에 압축 하도록 Kafka 생산자를 구성할 수 있습니다. `compression.type`설정은 사용할 압축 코덱을 지정 합니다. 지원 되는 압축 코덱은 "gzip", "snappy" 및 "lz4"입니다. 압축은 유용 하며 디스크 용량에 제한이 있는 경우 고려해 야 합니다.

일반적으로 사용 되는 두 가지 압축 코덱 중에서 `gzip` 및는 `snappy` `gzip` 더 높은 압축 비율을 가지 며,이로 인해 더 높은 CPU 부하가 발생 하는 대신 디스크 사용량이 줄어듭니다. `snappy`코덱은 CPU 오버 헤드를 줄여 압축을 줄여 줍니다. Broker 디스크 또는 생산자 CPU 제한을 기반으로 사용할 코덱을 결정할 수 있습니다. `gzip` 는 보다 5 배 높은 속도로 데이터를 압축할 수 있습니다 `snappy` .

데이터 압축을 사용 하면 디스크에 저장 될 수 있는 레코드 수가 늘어납니다. 생산자와 broker에서 사용 되는 압축 형식이 일치 하지 않는 경우에도 CPU 오버 헤드가 늘어날 수 있습니다. 데이터를 전송 하기 전에 데이터를 압축 한 다음 처리 하기 전에 압축 해야 합니다.

## <a name="broker-settings"></a>Broker 설정

다음 섹션에서는 Kafka broker의 성능을 최적화 하기 위한 가장 중요 한 설정 중 일부를 강조 합니다. 모든 broker 설정에 대 한 자세한 설명은 [생산자 구성에 대 한 Apache Kafka 설명서](https://kafka.apache.org/documentation/#producerconfigs)를 참조 하세요.

### <a name="number-of-disks"></a>디스크 수

저장소 디스크는 IOPS (초당 입력/출력 작업) 및 읽기/쓰기 바이트 수를 제한 합니다. 새 파티션을 만들 때 Kafka는 사용 가능한 디스크 간에 균형을 유지 하기 위해 기존 파티션을 최소화 하면서 디스크에 새 파티션을 각각 저장 합니다. 저장소 전략에도 불구 하 고 각 디스크에서 수백 개의 파티션 복제본을 처리할 때 Kafka는 사용 가능한 디스크 처리량을 쉽게 포화 시킬 수 있습니다. 여기에서의 단점은 처리량과 비용 사이입니다. 응용 프로그램에 더 높은 처리량이 필요한 경우 broker 당 더 많은 관리 디스크를 사용 하 여 클러스터를 만듭니다. HDInsight는 현재 실행 중인 클러스터에 관리 디스크를 추가 하는 것을 지원 하지 않습니다. 관리 디스크 수를 구성 하는 방법에 대 한 자세한 내용은 [HDInsight의 Apache Kafka에 대 한 저장소 및 확장성 구성](apache-kafka-scalability.md)을 참조 하세요. 클러스터의 노드에 대 한 저장소 공간을 늘릴 경우의 비용 영향을 이해 합니다.

### <a name="number-of-topics-and-partitions"></a>토픽 및 파티션 수

Kafka 생산자는 토픽에 씁니다. 항목에서 읽은 kafka 소비자입니다. 항목은 디스크의 데이터 구조인 로그와 관련 되어 있습니다. Kafka은 생산자의 레코드를 토픽 로그의 끝에 추가 합니다. 토픽 로그는 여러 파일에 분산 된 많은 파티션으로 구성 됩니다. 이러한 파일은 차례로 여러 Kafka 클러스터 노드에 분산 됩니다. 소비자는 자신의 흐름에서 Kafka 토픽을 읽고 토픽 로그에서 해당 위치 (오프셋)를 선택할 수 있습니다.

각 Kafka partition은 시스템의 로그 파일이 며 생산자 스레드는 여러 로그에 동시에 쓸 수 있습니다. 마찬가지로, 각 소비자 스레드가 하나의 파티션에서 메시지를 읽으므로 여러 파티션에서 사용 하는 것도 병렬로 처리 됩니다.

파티션 밀도 (broker 당 파티션 수)를 증가 시키면 파티션 리더와 팔로 워 사이에서 메타 데이터 작업 및 파티션당 요청/응답과 관련 된 오버 헤드가 추가 됩니다. 로 이동 하는 데이터가 없는 경우에도 파티션 복제본은 계속 해 서 리더의 데이터를 인출 하므로 네트워크를 통해 전송 및 수신 요청이 추가로 처리 됩니다.

HDInsight에서 1.1 이상의 Apache Kafka 클러스터의 경우 복제본을 포함 하 여 최대 1000 개의 파티션 (broker 당)을 보유 하는 것이 좋습니다. Broker 당 파티션 수를 늘려도 처리량이 줄어들고 토픽을 사용할 수 없게 될 수도 있습니다. Kafka 분할 지원에 대 한 자세한 내용은 [버전 1.1.0에서 지원 되는 파티션 수의 증가에 대 한 공식 Apache Kafka 블로그 게시물](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)을 참조 하세요. 항목을 수정 하는 방법에 대 한 자세한 내용은 [Apache Kafka: 항목 수정](https://kafka.apache.org/documentation/#basic_ops_modify_topic)을 참조 하세요.

### <a name="number-of-replicas"></a>복제본 수

복제 요인이 높으면 파티션 리더와 팔로 워 사이에 추가 요청이 발생 합니다. 따라서 더 높은 복제 요인이 추가 요청을 처리 하기 위해 더 많은 디스크와 CPU를 사용 하 여 쓰기 대기 시간을 늘리고 처리량을 줄입니다.

Azure HDInsight의 Kafka에 대해 3 ~ 3 배 이상의 복제를 사용 하는 것이 좋습니다. 대부분의 Azure 지역에는 세 개의 장애 도메인이 있지만 지역에는 두 개의 장애 도메인만 있는 지역에서 4x 복제를 사용 해야 합니다.

복제에 대 한 자세한 내용은 [Apache Kafka: 복제](https://kafka.apache.org/documentation/#replication) 및 [Apache Kafka: 복제 비율 향상](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Apache Kafka를 사용하여 매일 엄청난 수의 이벤트 처리](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [HDInsight의 Apache Kafka란?](apache-kafka-introduction.md)
