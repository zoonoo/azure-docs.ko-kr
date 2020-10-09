---
title: Azure HDInsight 항상 사용 가능한 솔루션 아키텍처 사례 연구
description: 이 문서는 가능한 Azure HDInsight 고가용성 솔루션 아키텍처에 대 한 가상의 사례 연구입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop high availability
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 4b98b03c2d7eb4a0403b4595c1376656ed42511b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855041"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Azure HDInsight 항상 사용 가능한 솔루션 아키텍처 사례 연구

Azure HDInsight의 복제 메커니즘은 항상 사용 가능한 솔루션 아키텍처에 통합할 수 있습니다. 이 문서에서는 Contoso Retail의 가상 사례 연구를 사용 하 여 가능한 고가용성 재해 복구 방법, 비용 고려 사항 및 해당 디자인을 설명 합니다.

고가용성 재해 복구 권장 사항은 여러 순열 및 조합을 포함할 수 있습니다. 이러한 솔루션은 각 옵션의 장단점을 deliberating 후에 도착 합니다. 이 문서에서는 하나의 가능한 솔루션에 대해서만 설명 합니다.

## <a name="customer-architecture"></a>고객 아키텍처

다음 그림은 Contoso Retail 주 아키텍처를 보여 줍니다. 아키텍처는 스트리밍 워크 로드, 일괄 작업, 서비스 계층, 소비 계층, 저장소 계층 및 버전 제어로 구성 됩니다.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Contoso Retail 아키텍처":::

### <a name="streaming-workload"></a>스트리밍 작업

장치 및 센서는 메시징 프레임 워크를 구성 하는 HDInsight Kafka에 게 데이터를 생성 합니다. An HDInsight Spark 소비자는 Kafka 토픽에서 읽습니다. Spark는 들어오는 메시지를 변환 하 고 서비스 계층의 HDInsight HBase 클러스터에 씁니다.

### <a name="batch-workload"></a>일괄 처리 작업

온-프레미스 트랜잭션 시스템에서 Hive 및 MapReduce 수집 데이터를 실행 하는 Hadoop 클러스터를 An HDInsight 합니다. Hive 및 MapReduce로 변환 된 원시 데이터는 Azure Data Lake Storage Gen2에 의해 지원 되는 data lake의 논리적 파티션의 Hive 테이블에 저장 됩니다. Hive 테이블에 저장 된 데이터는 Spark SQL 에서도 사용할 수 있습니다 .이는 서비스를 제공 하기 위해 HBase에 큐 레이트 데이터를 저장 하기 전에 일괄 변환을 수행 합니다.

### <a name="serving-layer"></a>서비스 계층

Apache Phoenix를 사용 하 여 HBase 클러스터 An HDInsight 웹 응용 프로그램 및 시각화 대시보드에 데이터를 제공 하는 데 사용 됩니다. An HDInsight LLAP 클러스터는 내부 보고 요구 사항을 충족 하는 데 사용 됩니다.

### <a name="consumption-layer"></a>소비 계층

Azure API Apps 및 API Management 계층은 공용 웹 페이지를 다시 만듭니다. 내부 보고 요구 사항은 Power BI에 의해 충족 됩니다.

### <a name="storage-layer"></a>스토리지 계층

논리적으로 분할 된 Azure Data Lake Storage Gen2 엔터프라이즈 Data Lake로 사용 됩니다. HDInsight metastore는 Azure SQL DB에서 지원 됩니다.

### <a name="version-control-system"></a>버전 제어 시스템

Azure Pipelines에 통합 되 고 Azure 외부에 호스트 되는 버전 제어 시스템입니다.

## <a name="customer-business-continuity-requirements"></a>고객 비즈니스 연속성 요구 사항

재해가 발생 하는 경우 필요한 최소한의 비즈니스 기능을 결정 하는 것이 중요 합니다.

### <a name="contoso-retails-business-continuity-requirements"></a>Contoso Retail의 비즈니스 연속성 요구 사항

* 지역 장애 또는 지역별 서비스 상태 문제를 방지 해야 합니다.
* 고객에 게 404 오류가 표시 되어서는 안 됩니다. 공용 콘텐츠는 항상 제공 되어야 합니다. (RTO = 0)  
* 대부분의 경우에는 5 시간 동안 오래 된 공용 콘텐츠를 표시할 수 있습니다. (RPO = 5 시간)
* 명절 시즌 중에는 공용 콘텐츠를 항상 최신 상태로 유지 해야 합니다. (RPO = 0)
* 내부 보고 요구 사항은 비즈니스 연속성에 중요 한 것으로 간주 되지 않습니다.
* 비즈니스 연속성 비용을 최적화 합니다.

## <a name="proposed-solution"></a>제안된 솔루션

다음 이미지는 Contoso Retail 고가용성 재해 복구 아키텍처를 보여 줍니다.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Contoso Retail 아키텍처":::

**Kafka** 은 [활성 – 수동](hdinsight-business-continuity-architecture.md#apache-kafka) 복제를 사용 하 여 주 지역에서 보조 지역으로 kafka 주제를 미러링합니다. Kafka 복제에 대 한 대안은 두 지역의 Kafka에 게 생성 하는 것일 수 있습니다.

**Hive 및 Spark** 는 정상적인 시간 동안 [활성 주 주문형 보조](hdinsight-business-continuity-architecture.md#apache-spark) 복제 모델을 사용 합니다. Hive 복제 프로세스는 주기적으로 실행 되며 Hive Azure SQL metastore 및 Hive 저장소 계정 복제를 함께 제공 합니다. Spark 저장소 계정은 ADF DistCP를 사용 하 여 주기적으로 복제 됩니다. 이러한 클러스터의 일시적인 특성은 비용을 최적화 하는 데 도움이 됩니다. 복제는 5 시간 요구 사항에 해당 하는 RPO에 도착 하도록 4 시간 마다 예약 됩니다.

**HBase** 복제는 보통 시간 동안 [리더 – 종동체](hdinsight-business-continuity-architecture.md#apache-hbase) 모델을 사용 하 여 지역에 관계 없이 데이터가 항상 제공 되 고 RPO가 0 인지 확인 합니다.

주 지역에 지역 실패가 있는 경우 웹 페이지와 백 엔드 콘텐츠는 일정 수준으로 5 시간 동안 보조 지역에서 제공 됩니다. Azure 서비스 상태 대시보드가 5 시간 기간 내에 복구 에타를 나타내지 않는 경우 Contoso Retail은 보조 지역에 Hive 및 Spark 변환 계층을 만든 다음 모든 업스트림 데이터 원본을 보조 지역으로 가리킵니다. 보조 영역을 쓰기 가능 하도록 설정 하면 장애 복구 프로세스가 주 데이터베이스로 다시 복제 됩니다.

최고 쇼핑 시즌 중에는 전체 보조 파이프라인이 항상 활성 상태이 고 실행 중입니다. 두 지역 및 HBase 복제가 모두 Leader-Follower에서 Leader-Leader으로 변경 되어 공용 콘텐츠를 항상 최신 상태로 유지 합니다.

장애 조치 (failover) 솔루션은 비즈니스 연속성에 중요 하지 않기 때문에 내부 보고용으로 디자인할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 설명 하는 항목에 대 한 자세한 내용은 다음을 참조 하세요.

* [Azure HDInsight 비즈니스 연속성](./hdinsight-business-continuity.md)
* [Azure HDInsight 비즈니스 연속성 아키텍처](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight의 Apache Hive 및 HiveQL 무엇 인가요?](./hadoop/hdinsight-use-hive.md)