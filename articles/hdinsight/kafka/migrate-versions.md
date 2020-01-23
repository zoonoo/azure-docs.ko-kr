---
title: Apache Kafka 워크 로드를 Azure HDInsight 4.0로 마이그레이션
description: HDInsight 3.6에서 Apache Kafka 작업을 HDInsight 4.0로 마이그레이션하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 66ea8a93723c117a364a188ec05b050e1ef60564
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548087"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Apache Kafka 워크 로드를 Azure HDInsight 4.0로 마이그레이션

Azure HDInsight 4.0는 성능, 연결 및 보안이 크게 향상 된 최신 오픈 소스 구성 요소를 제공 합니다. 이 문서에서는 HDInsight 3.6의 Apache Kafka 작업을 HDInsight 4.0로 마이그레이션하는 방법에 대해 설명 합니다. 작업을 HDInsight 4.0로 마이그레이션한 후에는 HDInsight 3.6에서 사용할 수 없는 여러 가지 새로운 기능을 사용할 수 있습니다.

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Kafka 마이그레이션 경로

HDInsight 3.6은 두 가지 버전의 Kafka (1.0.0 및 1.1.0)를 지원 합니다. HDInsight 4.0은 1.1.0 및 2.1.0 버전을 지원 합니다. 어떤 버전의 Kafka 및 실행 하려는 HDInsight 버전에 따라 지원 되는 마이그레이션 경로가 여러 개 있습니다. 이러한 경로는 아래에 설명 되어 있으며 아래 다이어그램에 나와 있습니다.

* **최신 버전에서 kafka 및 HDInsight 모두 실행 (권장)** : kafka 2.1.0 (아래 경로 및 E)를 사용 하 여 hdinsight 3.6 및 kafka 1.0.0 또는 1.1.0 응용 프로그램을 hdinsight 4.0로 마이그레이션 합니다.
* **최신 버전에서 hdinsight를 실행 하 되 최신 버전에만**적용: hdinsight 3.6 및 kafka 1.0.0 응용 프로그램을 kafka 1.1.0 (아래 경로 B)를 사용 하 여 hdinsight 4.0로 마이그레이션합니다.
* **최신 버전에서 hdinsight를 실행 하 고 kafka 버전 유지**: kafka 1.1.0 (아래 경로 C)를 사용 하 여 hdinsight 3.6 및 kafka 1.1.0 응용 프로그램을 hdinsight 4.0로 마이그레이션 합니다.
* **더 최신 버전의 Kafka를 실행 하 고 hdinsight 버전 유지**: kafka 1.0.0 응용 프로그램을 1.1.0로 마이그레이션하고 hdinsight 3.6 (아래 경로)에 유지 합니다. 이 옵션은 여전히 새 클러스터를 배포 해야 합니다. 기존 클러스터에서 Kafka 버전 업그레이드는 지원 되지 않습니다. 원하는 버전으로 클러스터를 만든 후 새 클러스터를 사용 하도록 Kafka 클라이언트를 마이그레이션합니다.

![3\.6의 Apache Kafka에 대 한 업그레이드 경로](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka 버전

### <a name="kafka-110"></a>Kafka 1.1.0
  
Kafka 1.0.0에서 1.1.0로 마이그레이션하는 경우 다음과 같은 새로운 기능을 활용할 수 있습니다.

* Kafka 컨트롤러에 대 한 향상 된 기능을 통해 제어 되는 종료 시간이 단축 되므로 broker를 다시 시작 하 여 문제를 더 빠르게 복구할 수 있습니다. 
* 클러스터에 더 많은 파티션 (그리고 따라서 더 많은 항목)을 포함할 수 있는 [Fetchrequests 논리가](https://issues.apache.org/jira/browse/KAFKA-6254) 개선 되었습니다. 
* Kafka Connect는 토픽에 대 한 [레코드 헤더](https://issues.apache.org/jira/browse/KAFKA-5142) 및 [정규식](https://issues.apache.org/jira/browse/KAFKA-3073) 을 지원 합니다. 

업데이트의 전체 목록은 [Apache Kafka 1.1 릴리스 정보](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)를 참조 하세요.

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Kafka 2.1로 마이그레이션하면 다음과 같은 기능을 활용할 수 있습니다.

* 향상 된 복제 프로토콜 때문에 broker 복원 력이 향상 되었습니다.
* KafkaAdminClient API의 새로운 기능
* 구성 가능한 할당량 관리.
* Zstandard 압축을 지원 합니다.

업데이트의 전체 목록은 [Apache Kafka 2.0 릴리스 정보](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) 및 [Apache Kafka 2.1 릴리스 정보](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)를 참조 하세요.

## <a name="kafka-client-compatibility"></a>Kafka 클라이언트 호환성

새 Kafka broker는 이전 클라이언트를 지원 합니다. [건너뛰기-35-프로토콜 버전을 검색](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) 하면 kafverbroker 및 건너뛰기-97의 기능을 동적으로 결정 하는 메커니즘이 도입 되었습니다 [. 향상 된 Kafverclient RPC 호환성 정책은](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) 새로운 호환성 정책을 도입 하 고 Java 클라이언트를 보장 합니다. 이전에는 Kafka 클라이언트는 동일한 버전이 나 최신 버전의 broker와 상호 작용 해야 했습니다. 이제 최신 버전의 Java 클라이언트 및 `librdkafka`와 같은 건너뛰기-35을 지 원하는 다른 클라이언트는 이전 요청 형식으로 대체 하거나 기능을 사용할 수 없는 경우 적절 한 오류를 throw 할 수 있습니다.

![Kafka 클라이언트 호환성 업그레이드](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

클라이언트에서 이전 broker를 지원함을 의미 하지는 않습니다.  자세한 내용은 [호환성 매트릭스](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)를 참조 하세요.

## <a name="general-migration-process"></a>일반 마이그레이션 프로세스

다음 마이그레이션 지침은 단일 가상 네트워크에서 HDInsight 3.6에 Apache Kafka 1.0.0 또는 1.1.0 클러스터를 배포 했다고 가정 합니다. 기존 broker에는 몇 가지 항목이 있으며 생산자와 소비자가 적극적으로 사용 하 고 있습니다.

![현재 Kafka 가정 환경](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

마이그레이션을 완료 하려면 다음 단계를 수행 합니다.

1. **테스트를 위해 새 HDInsight 4.0 클러스터 및 클라이언트를 배포 합니다.** 새 HDInsight 4.0 Kafka 클러스터를 배포 합니다. 여러 Kafka 클러스터 버전을 선택할 수 있는 경우 최신 버전을 선택 하는 것이 좋습니다. 배포 후 필요에 따라 몇 가지 매개 변수를 설정 하 고 기존 환경과 동일한 이름으로 토픽을 만듭니다. 또한 SSL을 설정 하 고 필요에 따라 BYOK (고유 키) 암호화를 수행 합니다. 그런 다음 새 클러스터에서 올바르게 작동 하는지 확인 합니다.

    ![새 HDInsight 4.0 클러스터 배포](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **생산자 응용 프로그램의 클러스터를 전환 하 고 모든 큐 데이터가 현재 소비자에 의해 사용 될 때까지 기다립니다.** 새 HDInsight 4.0 Kafka 클러스터가 준비 되 면 기존 생산자 대상을 새 클러스터로 전환 합니다. 기존 소비자 앱이 기존 클러스터의 모든 데이터를 사용 하기 전 까지는 그대로 둡니다.

    ![생산자 앱에 대 한 클러스터 전환](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **소비자 응용 프로그램에서 클러스터를 전환 합니다.** 기존 소비자 응용 프로그램에서 기존 클러스터의 모든 데이터 사용을 완료 했는지 확인 한 후 새 클러스터로 연결을 전환 합니다.

    ![소비자 앱에서 클러스터 전환](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **이전 클러스터를 제거 하 고 필요에 따라 응용 프로그램을 테스트 합니다.** 스위치가 완료 되 고 제대로 작동 하면 이전 HDInsight 3.6 Kafka 클러스터와 테스트에 사용 된 생산자와 소비자를 필요에 따라 제거 합니다.

## <a name="next-steps"></a>다음 단계

* [Apache Kafka HDInsight 클러스터의 성능 최적화](apache-kafka-performance-tuning.md)
* [빠른 시작: Azure Portal를 사용 하 여 Azure HDInsight에서 Apache Kafka 클러스터 만들기](apache-kafka-get-started.md)
