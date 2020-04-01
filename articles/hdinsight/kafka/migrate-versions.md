---
title: 아파치 카프카 워크로드를 Azure HDInsight 4.0으로 마이그레이션
description: HDInsight 3.6에서 아파치 카프카 워크로드를 HDInsight 4.0으로 마이그레이션하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 0a31c6cf32222277e033aacf7d04622c54aef9ea
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437007"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>아파치 카프카 워크로드를 Azure HDInsight 4.0으로 마이그레이션

Azure HDInsight 4.0은 성능, 연결 및 보안이 크게 향상된 최신 오픈 소스 구성 요소를 제공합니다. 이 문서에서는 HDInsight 3.6에서 아파치 카프카 워크로드를 HDInsight 4.0으로 마이그레이션하는 방법을 설명합니다. 워크로드를 HDInsight 4.0으로 마이그레이션한 후 HDInsight 3.6에서 사용할 수 없는 많은 새로운 기능을 사용할 수 있습니다.

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 카프카 마이그레이션 경로

HDInsight 3.6은 카프카의 두 가지 버전을 지원합니다: 1.0.0 과 1.1.0. HDInsight 4.0은 버전 1.1.0 및 2.1.0을 지원합니다. 어떤 버전의 Kafka 및 실행하려는 HDInsight 버전에 따라 지원되는 마이그레이션 경로가 여러 개 있습니다. 이러한 경로는 아래에 설명되어 있으며 다음 다이어그램에 나와 있습니다.

* **최신 버전에서 카프카와 HDInsight를 모두 실행 (권장)**: 카프카 2.1.0 (아래 경로 D와 E)와 HDInsight 4.0에 HDInsight 3.6 및 카프카 1.0 또는 1.1.0 응용 프로그램을 마이그레이션합니다.
* **최신 버전에서 HDInsight를 실행하지만, 최신 버전에서 만 카프카**: 카프카 1.1.0 (아래 경로 B)와 HDInsight 3.6 및 카프카 1.0 응용 프로그램을 HDInsight 4.0으로 마이그레이션합니다.
* **최신 버전에서 HDInsight를 실행, 카프카 버전을 유지**: 카프카 1.1.0 (아래 경로 C)와 HDInsight 4.0에 HDInsight 3.6 및 카프카 1.0 응용 프로그램을 마이그레이션합니다.
* **최신 버전에서 카프카를 실행, HDInsight 버전을 유지**: 카프카 1.0.0 응용 프로그램을 마이그레이션 1.1.0 과 HDInsight에 머물 3.6 (아래 경로 A). 이 옵션을 사용하면 새 클러스터를 배포해야 합니다. 기존 클러스터에서 Kafka 버전을 업그레이드하는 것은 지원되지 않습니다. 원하는 버전으로 클러스터를 만든 후 Kafka 클라이언트를 마이그레이션하여 새 클러스터를 사용합니다.

![아파치 카프카의 경로 업그레이드 3.6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>아파치 카프카 버전

### <a name="kafka-110"></a>카프카 1.1.0
  
Kafka 1.0.0에서 1.1.0으로 마이그레이션하는 경우 다음과 같은 새로운 기능을 활용할 수 있습니다.

* Kafka 컨트롤러의 개선 사항으로 제어된 종료 속도가 빨라지므로 브로커를 다시 시작하고 문제에서 더 빠르게 복구할 수 있습니다. 
* 클러스터에 더 많은 파티션 (그리고 따라서 더 많은 항목)을 가질 수 있도록 [FetchRequests 논리가](https://issues.apache.org/jira/browse/KAFKA-6254) 개선되었습니다. 
* Kafka [Connect는](https://issues.apache.org/jira/browse/KAFKA-5142) 주제에 대한 레코드 헤더 및 [정규식을](https://issues.apache.org/jira/browse/KAFKA-3073) 지원합니다. 

업데이트의 전체 목록은 [아파치 카프카 1.1 릴리스 노트를](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)참조하십시오.

### <a name="apache-kafka-210"></a>아파치 카프카 2.1.0

Kafka 2.1로 마이그레이션하는 경우 다음 기능을 활용할 수 있습니다.

* 향상된 복제 프로토콜로 인해 브로커 복원력 향상.
* 카프카AdminClient API의 새로운 기능.
* 구성 가능한 할당량 관리.
* Zstandard 압축을 지원합니다.

업데이트의 전체 목록은 [아파치 카프카 를 참조하십시오 2.0 릴리스 노트와](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) [아파치 카프카 2.1 릴리스 노트.](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)

## <a name="kafka-client-compatibility"></a>카프카 클라이언트 호환성

새로운 카프카 브로커는 이전 고객을 지원합니다. [KIP-35 - 검색 프로토콜 버전은](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) Kafka 브로커와 KIP-97의 기능을 동적으로 결정하는 메커니즘을 도입했습니다: 향상된 [Kafka 클라이언트 RPC 호환성 정책은](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) Java 클라이언트에 대한 새로운 호환성 정책과 보장을 도입했습니다. 이전에는 Kafka 클라이언트가 동일한 버전또는 최신 버전의 브로커와 상호 작용해야 했습니다. 이제 최신 버전의 Java 클라이언트및 KIP-35를 지원하는 다른 `librdkafka` 클라이언트는 이전 요청 유형으로 대체되거나 기능을 사용할 수 없는 경우 적절한 오류를 throw할 수 있습니다.

![카프카 클라이언트 호환성 업그레이드](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

클라이언트가 이전 브로커를 지원한다는 의미는 아닙니다.  자세한 내용은 [호환성 매트릭스](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)를 참조하십시오.

## <a name="general-migration-process"></a>일반 마이그레이션 프로세스

다음 마이그레이션 지침은 단일 가상 네트워크에서 HDInsight 3.6에 배포된 아파치 카프카 1.0 또는 1.1.0 클러스터를 가정합니다. 기존 브로커는 몇 가지 주제를 가지고 있으며 생산자와 소비자가 적극적으로 사용하고 있습니다.

![현재 카프카 추정 환경](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

마이그레이션을 완료하려면 다음 단계를 수행합니다.

1. **테스트를 위해 새로운 HDInsight 4.0 클러스터 및 클라이언트를 배포합니다.** 새로운 HDInsight 4.0 카프카 클러스터를 배포합니다. 여러 Kafka 클러스터 버전을 선택할 수 있는 경우 최신 버전을 선택하는 것이 좋습니다. 배포 후 필요에 따라 일부 매개 변수를 설정하고 기존 환경과 이름이 같은 토픽을 만듭니다. 또한 필요에 따라 TLS를 설정하고 BYOK(사용자 고유키) 암호화를 설정합니다. 그런 다음 새 클러스터에서 제대로 작동하는지 확인합니다.

    ![새로운 HDInsight 4.0 클러스터 배포](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **생산자 응용 프로그램에 대한 클러스터를 전환하고 현재 소비자가 모든 큐 데이터를 사용할 때까지 기다립니다.** 새 HDInsight 4.0 Kafka 클러스터가 준비되면 기존 생산자 대상을 새 클러스터로 전환합니다. 기존 소비자 앱이 기존 클러스터의 모든 데이터를 사용할 때까지 그대로 둡니다.

    ![생산자 앱용 클러스터 전환](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **소비자 응용 프로그램에서 클러스터를 전환합니다.** 기존 소비자 응용 프로그램이 기존 클러스터의 모든 데이터 사용이 완료되었음을 확인한 후 새 클러스터로 연결을 전환합니다.

    ![소비자 앱의 클러스터 전환](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **필요에 따라 이전 클러스터를 제거하고 응용 프로그램을 테스트합니다.** 스위치가 완료되고 제대로 작동하면 필요에 따라 테스트에 사용된 기존 HDInsight 3.6 Kafka 클러스터와 생산자 및 소비자를 제거합니다.

## <a name="next-steps"></a>다음 단계

* [Apache Kafka HDInsight 클러스터의 성능 최적화](apache-kafka-performance-tuning.md)
* [빠른 시작: Azure 포털을 사용하여 Azure HDInsight에서 아파치 카프카 클러스터 만들기](apache-kafka-get-started.md)
