---
title: Azure HDInsight 비즈니스 연속성 아키텍처
description: 이 문서에서는 HDInsight에 대해 가능한 여러 가지 비즈니스 연속성 아키텍처를 설명 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop high availability
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 9eb0cd3fd327a53dd0761779916caa096153a010
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856435"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Azure HDInsight 비즈니스 연속성 아키텍처

이 문서에서는 Azure HDInsight에 대해 고려할 수 있는 비즈니스 연속성 아키텍처의 몇 가지 예를 제공 합니다. 재해 중의 축소 된 기능에 대 한 허용 오차는 응용 프로그램 마다 달라 지는 비즈니스 의사 결정입니다. 일부 응용 프로그램을 사용할 수 없거나 일부 응용 프로그램을 부분적으로 사용할 수 있도록 하는 것이 적합할 수 있습니다. 다른 응용 프로그램의 경우에는 축소 된 모든 기능을 사용할 수 없습니다.

> [!NOTE]
> 이 문서에 나와 있는 아키텍처는 완전 한 방법이 아닙니다. 예상 비즈니스 연속성, 운영 복잡성 및 소유 비용을 목표로 결정 한 후 고유한 아키텍처를 디자인 해야 합니다.

## <a name="apache-hive-and-interactive-query"></a>Apache Hive 및 대화형 쿼리

[Hive 복제 V2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) 는 HDInsight Hive 및 대화형 쿼리 클러스터의 비즈니스 연속성에 권장 됩니다. 복제 해야 하는 독립 실행형 Hive 클러스터의 영구적 섹션은 저장소 계층과 Hive metastore입니다. Enterprise Security Package를 사용 하는 다중 사용자 시나리오의 Hive 클러스터는 Azure Active Directory Domain Services 및 레인저 Metastore 필요 합니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

Hive 이벤트 기반 복제는 기본 클러스터와 보조 클러스터 간에 구성 됩니다. 이는 두 가지 고유한 단계인 부트스트래핑 및 증분 실행으로 구성 됩니다.

* 부트스트랩은 Hive metastore 정보를 포함 하 여 전체 Hive 웨어하우스를 기본에서 보조로 복제 합니다.

* 증분 실행은 주 클러스터에서 자동화 되며 증분 실행 중에 생성 된 이벤트는 보조 클러스터에서 다시 재생 됩니다. 보조 클러스터는 기본 클러스터에서 생성 된 이벤트를 사용 하 여 복제를 실행 한 후 보조 클러스터가 주 클러스터의 이벤트와 일치 하는지 확인 합니다.

보조 클러스터는 분산 복사를 실행 하는 동안에만 필요 `DistCp` 하지만 저장소와 metastore은 영구적 이어야 합니다. 복제 하기 전에 주문형으로 스크립팅된 보조 클러스터를 실행 하도록 선택 하 고 복제 스크립트를 실행 한 다음 복제를 성공적으로 완료 한 후에이 클러스터를 종료할 수 있습니다.

보조 클러스터는 일반적으로 읽기 전용입니다. 보조 클러스터를 읽기/쓰기로 만들 수 있지만 이렇게 하면 보조 클러스터에서 주 클러스터로 변경 내용을 복제 하는 작업이 더 복잡해 집니다.

### <a name="hive-event-based-replication-rpo--rto"></a>Hive 이벤트 기반 복제 RPO & RTO

* RPO: 데이터 손실이 주에서 보조로 마지막으로 성공한 증분 복제 이벤트로 제한 됩니다.

* RTO: 보조 데이터베이스를 사용 하 여 업스트림 및 다운스트림 트랜잭션 간의 실패와 다시 시작 사이의 시간입니다.

### <a name="apache-hive-and-interactive-query-architectures"></a>Apache Hive 및 대화형 쿼리 아키텍처

#### <a name="hive-active-primary-with-on-demand-secondary"></a>주문형 보조를 사용 하는 Hive 활성 주

*주문형 보조 아키텍처가 포함 된 활성 기본* 에서 응용 프로그램은 정상 작업 중에 보조 지역에 클러스터가 프로 비전 되지 않은 상태에서 활성 주 지역에 기록 합니다. 보조 지역의 SQL Metastore 및 저장소는 영구적 이며, HDInsight 클러스터는 예약 된 Hive 복제가 실행 되기 전에만 주문형으로 스크립팅된 방식으로 배포 됩니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

#### <a name="hive-active-primary-with-standby-secondary"></a>대기 보조를 사용 하는 Hive 활성 주

대기 보조 데이터베이스를 사용 하는 *활성 주*에서 응용 프로그램은 활성 주 지역에 기록 하는 반면, 정상 작업 중에는 읽기 전용 모드의 대기 상태의 보조 클러스터가 실행 됩니다. 정상적인 작업 중에는 지역별 특정 읽기 작업을 보조 복제본으로 오프 로드 하도록 선택할 수 있습니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

## <a name="apache-spark"></a>Apache Spark

Spark 워크 로드에는 Hive 구성 요소가 포함 될 수도 있고 그렇지 않을 수도 있습니다. HDInsight Spark 클러스터는 hive에서 데이터를 읽고 쓸 수 있도록 하기 위해 동일한 지역에 있는 Hive/대화형 쿼리 클러스터의 Hive 사용자 지정 metastore을 공유 합니다. 이러한 시나리오에서는 Spark 워크 로드의 지역 간 복제도 Hive metastore 및 저장소의 복제와 함께 제공 되어야 합니다. 이 섹션의 장애 조치 (failover) 시나리오는 두 가지 모두에 적용 됩니다.

* HDInsight 대화형 쿼리 클러스터를 사용 하 여 [HWC (Hive 웨어하우스 커넥터) 설치를 사용 하는 ACID 테이블의 SPARK SQL](./interactive-query/apache-hive-warehouse-connector.md)
* HDInsight Hadoop 클러스터를 사용 하는 비 ACID 테이블의 Spark SQL 워크 로드.

Spark가 독립 실행형 모드에서 작동 하는 시나리오의 경우 큐 레이트 데이터 및 저장 된 Spark Jar (Livy 작업의 경우)는 Azure Data Factory의를 사용 하 여 정기적으로 주 지역에서 보조 지역으로 복제 해야 `DistCP` 합니다.

주 또는 보조 클러스터에 쉽게 배포할 수 있는 Spark 노트북 및 라이브러리를 저장 하려면 버전 제어 시스템을 사용 하는 것이 좋습니다. 노트북 기반 및 노트북 기반이 아닌 솔루션이 기본 또는 보조 작업 영역에서 올바른 데이터 탑재를 로드 하도록 준비 되어 있는지 확인 합니다.

HDInsight에서 기본적으로 제공 하는 것 이상의 사용자 지정 라이브러리가 있는 경우 추적 하 고 주기적으로 대기 보조 클러스터에 로드 해야 합니다.  

### <a name="apache-spark-replication-rpo--rto"></a>Apache Spark 복제 RPO & RTO

* RPO: 데이터 손실은 주에서 보조로 마지막으로 성공한 증분 복제 (Spark 및 Hive)로 제한 됩니다.

* RTO: 보조 데이터베이스를 사용 하 여 업스트림 및 다운스트림 트랜잭션 간의 실패와 다시 시작 사이의 시간입니다.

### <a name="apache-spark-architectures"></a>Apache Spark 아키텍처

#### <a name="spark-active-primary-with-on-demand-secondary"></a>주문형 보조를 사용 하는 Spark 활성 주

응용 프로그램은 기본 지역에서 Spark 및 Hive 클러스터를 읽고 쓰며, 정상 작업 중에는 보조 지역에 클러스터가 프로 비전 되지 않습니다. SQL Metastore, Hive 저장소 및 Spark 저장소는 보조 지역에서 지속 됩니다. Spark 및 Hive 클러스터는 주문형으로 스크립팅 및 배포 됩니다. Hive 복제는 Azure Data Factory를 사용 하 여 `DistCP` 독립 실행형 Spark 저장소를 복사할 수 있는 동안 Hive 저장소 및 hive metastore를 복제 하는 데 사용 됩니다. Hive 클러스터는 종속성 계산으로 인해 모든 Hive 복제가 실행 되기 전에 배포 해야 `DistCp` 합니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

#### <a name="spark-active-primary-with-standby-secondary"></a>대기 보조를 사용 하는 Spark 활성 주

응용 프로그램은 주 지역에서 Spark 및 Hive 클러스터를 읽고 쓰며, 읽기 전용 모드의 대기 확장 Hive 및 Spark 클러스터는 정상 작업 중에 보조 지역에서 실행 됩니다. 정상적인 작업 중에는 지역별 Hive 및 Spark 읽기 작업을 보조 복제본으로 오프 로드 하도록 선택할 수 있습니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

## <a name="apache-hbase"></a>Apache HBase

HBase 내보내기 및 HBase 복제는 HDInsight HBase 클러스터 간에 비즈니스 연속성을 사용 하도록 설정 하는 일반적인 방법입니다.

HBase 내보내기는 HBase 내보내기 유틸리티를 사용 하 여 기본 HBase 클러스터에서 기본 Azure Data Lake Storage Gen 2 저장소로 테이블을 내보내는 일괄 처리 복제 프로세스입니다. 그러면 보조 HBase 클러스터에서 내보낸 데이터에 액세스 하 여 보조 데이터베이스에 미리 존재 해야 하는 테이블로 가져올 수 있습니다. HBase 내보내기는 테이블 수준 세분성을 제공 하지만 증분 업데이트 상황에서 내보내기 자동화 엔진은 각 실행에 포함할 증분 행의 범위를 제어 합니다. 자세한 내용은 [HDInsight HBase 백업 및 복제](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#export-then-import)를 참조 하세요.

HBase 복제는 완전히 자동화 된 방식으로 HBase 클러스터 간의 거의 실시간 복제를 사용 합니다. 복제는 테이블 수준에서 수행 됩니다. 모든 테이블 또는 특정 테이블은 복제 대상으로 지정할 수 있습니다. HBase 복제는 궁극적으로 일치 하므로 주 지역의 테이블에 대 한 최근 편집은 모든 보조 데이터베이스에서 즉시 사용 하지 못할 수 있습니다. 보조 복제본은 궁극적으로 주 데이터베이스와 일치 하 게 됩니다. 다음과 같은 경우 두 개 이상의 HDInsight HBase 클러스터 간에 HBase 복제를 설정할 수 있습니다.

* 주 및 보조 복제본이 동일한 가상 네트워크에 있습니다.
* 주 및 보조 데이터베이스는 동일한 지역에 있는 다른 피어 링 Vnet에 있습니다.
* 주 데이터베이스와 보조 데이터베이스는 서로 다른 지역에 있는 다른 피어 링 Vnet에 있습니다.

자세한 내용은 [Azure virtual networks에서 Apache HBase 클러스터 복제 설정](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-replication)을 참조 하세요.

[Hbase 폴더 복사](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-the-hbase-folder), 테이블 및 [스냅숏](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#snapshots) [복사](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-tables) 와 같은 hbase 클러스터의 백업을 수행 하는 몇 가지 다른 방법이 있습니다.

### <a name="hbase-rpo--rto"></a>HBase RPO & RTO

#### <a name="hbase-export"></a>HBase 내보내기

* RPO: 주 복제본에서 보조 복제본으로 데이터 손실이 마지막으로 성공한 일괄 증분 가져오기로 제한 됩니다.
* RTO: 보조 데이터베이스에 대 한 i/o 작업의 기본 및 다시 시작 오류 사이의 시간입니다.

#### <a name="hbase-replication"></a>HBase 복제

* RPO: 데이터 손실은 보조 복제본에서 받은 마지막 WalEdit 배송으로 제한 됩니다.
* RTO: 보조 데이터베이스에 대 한 i/o 작업의 기본 및 다시 시작 오류 사이의 시간입니다.

### <a name="hbase-architectures"></a>HBase 아키텍처

HBase 복제는 채움선-종동체, Leader-Leader 및 순환의 세 가지 모드에서 설정할 수 있습니다.

#### <a name="hbase-replication--leader--follower-model"></a>HBase 복제: 리더 – 종동체 모델

이 지역 간 설정에서 복제는 주 지역에서 보조 지역으로의 단방향입니다. 단방향 복제에 대해 주 서버의 모든 테이블 또는 특정 테이블을 식별할 수 있습니다. 정상적인 작업 중에 보조 클러스터를 사용 하 여 자체 지역에서 읽기 요청을 처리할 수 있습니다.

보조 클러스터는 자체 테이블을 호스트할 수 있고 지역 응용 프로그램에서 읽기와 쓰기를 제공할 수 있는 일반적인 HBase 클러스터로 작동 합니다. 그러나 복제 된 테이블이 나 보조에 대 한 기본 테이블의 쓰기는 주 복제본으로 다시 복제 되지 않습니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

#### <a name="hbase-replication--leader--leader-model"></a>HBase 복제: 리더-리더 모델

이 지역 간 설정은 복제가 주 지역 및 보조 지역 간에 양방향으로 발생 한다는 점을 제외 하 고 설정 된 단방향과 매우 비슷합니다. 응용 프로그램은 읽기-쓰기 모드에서 두 클러스터를 모두 사용할 수 있으며 업데이트는 비동기 방식으로 교환 됩니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>HBase 복제: 다중 지역 또는 순환

다중 지역/순환 복제 모델은 HBase 복제의 확장 이며, 지역 특정 HBase 클러스터를 읽고 쓰는 여러 응용 프로그램을 사용 하 여 전역적으로 중복 된 HBase 아키텍처를 만드는 데 사용할 수 있습니다. 비즈니스 요구 사항에 따라 리더/리더 또는 리더/종동체의 다양 한 조합으로 클러스터를 설정할 수 있습니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

## <a name="apache-kafka"></a>Apache Kafka

지역 간 가용성을 사용 하도록 설정 하기 위해 HDInsight 4.0는 다른 지역에 있는 기본 Kafka 클러스터의 보조 복제본을 유지 관리 하는 데 사용할 수 있는 Kafka MirrorMaker을 지원 합니다. MirrorMaker는 상위 수준의 소비자 생산자 쌍 역할을 하며, 주 클러스터의 특정 항목에서 사용 하 고, 보조에서 동일한 이름의 토픽을 생성 합니다. MirrorMaker를 사용한 고가용성 재해 복구를 위한 클러스터 간 복제는 생산자와 소비자가 복제 클러스터로 장애 조치 (failover) 해야 한다고 가정 하 고 제공 됩니다. 자세한 내용은 [MirrorMaker를 사용 하 여 HDInsight의 Kafka으로 Apache Kafka 항목 복제](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-mirroring) 를 참조 하세요.

복제 시작 시 항목 수명에 따라 MirrorMaker 토픽 복제에서 원본 및 복제본 항목 간에 다른 오프셋이 발생할 수 있습니다. HDInsight Kafka 클러스터는 개별 클러스터 수준에서 고가용성 기능인 토픽 파티션 복제도 지원 합니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

### <a name="apache-kafka-architectures"></a>Apache Kafka 아키텍처

#### <a name="kafka-replication-active--passive"></a>Kafka 복제: 활성 – 수동

Active-Passive 설치 프로그램을 사용 하면 활성에서 수동으로의 비동기 단방향 미러링이 가능 합니다. 생산자와 소비자는 활성 및 수동 클러스터의 존재 여부를 알고 있어야 하며, 활성이 실패할 경우에는 Passive로 장애 조치 (failover) 할 준비가 되어 있어야 합니다. 다음은 Active-Passive 설정의 장점 및 단점입니다.

장점

* 클러스터 간의 네트워크 대기 시간은 활성 클러스터의 성능에 영향을 주지 않습니다.
* 단방향 복제의 단순성.

단점

* 수동 클러스터가 미달 사용 상태로 유지 될 수 있습니다.
* 응용 프로그램 생산자와 소비자에 장애 조치 인식 기능을 통합 하는 설계 복잡성.
* 활성 클러스터에 오류가 발생 한 경우 데이터가 손실 될 수 있습니다.
* 활성 클러스터와 수동 클러스터 간에 항목 간의 최종 일관성
* 장애 조치 (주)를 주 복제본으로 백업 하면 항목에서 메시지 불일치가 발생할 수 있습니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

#### <a name="kafka-replication-active--active"></a>Kafka 복제: 활성-활성

Active-Active 설정에는 MirrorMaker를 사용 하는 양방향 비동기 복제를 사용 하는 두 개의 지역적으로 구분 된 VNet 피어 링 HDInsight Kafka 클러스터가 포함 됩니다. 이 설계에서 주 데이터베이스의 소비자가 사용 하는 메시지는 보조의 소비자가 사용할 수도 있고 그 반대의 경우도 마찬가지입니다. 다음은 Active-Active 설정의 장점 및 단점입니다.

장점

* 중복 된 상태 때문에 장애 조치 (failover) 및 장애 복구는 실행 하기가 더 쉽습니다.

단점

* 설정, 관리 및 모니터링은 활성-수동 보다 더 복잡 합니다.
* 순환 복제의 문제를 해결 해야 합니다.  
* 양방향 복제는 더 높은 지역 데이터 송신 비용을 초래 합니다.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

## <a name="hdinsight-enterprise-security-package"></a>HDInsight Enterprise Security Package

이 설정을 사용 하 여 사용자가 두 클러스터 모두에 인증할 수 있도록 기본 및 보조 뿐만 아니라 [Azure AD DS 복제본 집합](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-replica-set) 에서 다중 사용자 기능을 사용 하도록 설정할 수 있습니다. 정상적인 작업 중에는 사용자가 읽기 작업으로 제한 되도록 보조에 레인저 정책을 설정 해야 합니다. 아래 아키텍처는 ESP 사용 Hive 활성 주 – 대기 보조 설정의 표시 방법을 설명 합니다.

레인저 Metastore 복제:

레인저 Metastore는 데이터 권한 부여를 제어 하기 위한 레인저 정책을 영구적으로 저장 하 고 제공 하는 데 사용 됩니다. 기본 및 보조에서 독립 된 레인저 정책을 유지 하 고 보조 복제본을 읽기 복제본으로 유지 하는 것이 좋습니다.
  
기본 및 보조 간에 레인저 정책을 동기화 상태로 유지 해야 하는 경우에는 [레인저 import/Export](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export#:~:text=Ranger%20has%20introduced%20a%20new,can%20import%20and%20export%20policies.&text=Also%20can%20export%2Fimport%20a,repositories\)%20via%20Ranger%20Admin%20UI) 를 사용 하 여 정기적으로 백업 하 고 기본에서 보조로의 레인저 정책을 가져옵니다.

주 데이터베이스와 보조 데이터베이스 간에 레인저 정책을 복제 하면 보조 복제본이 쓰기 가능 해질 수 있으며,이로 인해 보조 복제본에서 데이터 불일치를 발생 시킬 수 있습니다.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="Hive 및 대화형 쿼리 아키텍처":::

## <a name="next-steps"></a>다음 단계

이 문서에서 설명 하는 항목에 대 한 자세한 내용은 다음을 참조 하세요.

* [Azure HDInsight 비즈니스 연속성](./hdinsight-business-continuity.md)
* [Azure HDInsight 항상 사용 가능한 솔루션 아키텍처 사례 연구](./hdinsight-high-availability-case-study.md)
* [Azure HDInsight의 Apache Hive 및 HiveQL 무엇 인가요?](./hadoop/hdinsight-use-hive.md)