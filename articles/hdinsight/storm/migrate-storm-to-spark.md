---
title: HDInsight 4.0 Apache Spark에 Azure HDInsight 3.6 Apache Storm 마이그레이션
description: Apache Storm 워크 로드를 Spark 스트리밍 또는 Spark 구조적 스트리밍으로 마이그레이션하기 위한 차이점과 마이그레이션 흐름입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157793"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>HDInsight 4.0 Apache Spark에 Azure HDInsight 3.6 Apache Storm 마이그레이션

이 문서에서는 HDInsight 3.6의 Apache Storm 작업을 HDInsight 4.0로 마이그레이션하는 방법에 대해 설명 합니다. HDInsight 4.0은 Apache Storm 클러스터 유형을 지원 하지 않으므로 다른 스트리밍 데이터 플랫폼으로 마이그레이션해야 합니다. 두 가지 적절 한 옵션은 스트리밍 및 Spark 구조적 스트리밍을 Apache Spark는 것입니다. 이 문서에서는 이러한 플랫폼 간의 차이점을 설명 하 고 Apache Storm 작업을 마이그레이션하기 위한 워크플로를 권장 합니다.

## <a name="storm-migration-paths-in-hdinsight"></a>HDInsight의 스톰 마이그레이션 경로

HDInsight 3.6의 Apache Storm에서 마이그레이션하려면 다음과 같이 여러 가지 옵션을 사용할 수 있습니다.

* HDInsight 4.0의 Spark 스트리밍
* HDInsight 4.0의 Spark 구조적 스트리밍
* Azure Stream Analytics

이 문서에서는 Apache Storm에서 Spark 스트리밍 및 Spark 구조적 스트리밍으로 마이그레이션하는 방법에 대 한 지침을 제공 합니다.

> [!div class="mx-imgBorder"]
> ![HDInsight 스톰 마이그레이션 경로](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Apache Storm와 Spark 스트리밍 간의 비교, Spark 구조적 스트리밍

Apache Storm은 다양한 수준의 보장된 메시지 처리를 제공할 수 있습니다. 예를 들어 기본적인 Storm 애플리케이션은 최소한 한 번 처리를 보장할 수 있고, [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html)는 정확히 한 번 처리를 보장할 수 있습니다. Spark 스트리밍 및 Spark 구조적 스트리밍은 노드 오류가 발생 하더라도 입력 이벤트가 정확히 한 번만 처리 되도록 보장 합니다. 스톰에는 각 단일 이벤트를 처리 하는 모델이 있으며 Trident에서 마이크로 배치 모델을 사용할 수도 있습니다. Spark 스트리밍 및 Spark 구조적 스트리밍은 마이크로 일괄 처리 처리 모델을 제공 합니다.

|  |Storm |Spark 스트리밍 | Spark 구조적 스트리밍|
|---|---|---|---|
|**이벤트 처리 보장**|최소 한 번 <br> 정확히 한 번 (Trident) |[정확히 한 번](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[정확히 한 번](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**처리 모델**|실시간 <br> 마이크로 일괄 처리 (Trident) |마이크로 일괄 처리 |마이크로 일괄 처리 |
|**이벤트 시간 지원**|[예](https://storm.apache.org/releases/2.0.0/Windowing.html)|아닙니다.|[예](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**언어**|Java 등|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark 스트리밍 및 Spark 구조적 스트리밍

Spark 구조적 스트리밍은 Spark 스트리밍 (i 스트림)을 대체 합니다. 구조적 스트리밍은 향상 된 기능 및 유지 관리를 계속 받고 있지만, d 스트림은 유지 관리 모드 에서만 가능 합니다. **참고:이 점을 강조 하려면 링크가 필요**합니다. 구조적 스트리밍에는 지원 되는 원본 및 싱크에 대 한 많은 기능이 포함 되지 않으므로 요구 사항을 평가 하 여 적절 한 Spark 스트림 처리 옵션을 선택 합니다.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>스트리밍 (단일 이벤트) 처리 vs 마이크로 일괄 처리

스톰은 각 단일 이벤트를 처리 하는 모델을 제공 합니다. 즉, 들어오는 모든 레코드는 도착 하는 즉시 처리 됩니다. Spark 스트리밍 응용 프로그램은 처리를 위해 해당 일괄 처리를 보내기 전에 이벤트의 각 마이크로 일괄 처리를 수집 하는 데 몇 초 정도 기다려야 합니다. 반면, 이벤트 기반 애플리케이션은 각 이벤트를 즉시 처리합니다. Spark 스트리밍 대기 시간은 일반적으로 몇 초 이하입니다. 마이크로 일괄 처리 방법의 이점은 보다 효율적인 데이터 처리 및 간단한 집계 계산입니다.

> [!div class="mx-imgBorder"]
> ![스트리밍 및 마이크로 일괄 처리](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>폭풍 아키텍처 및 구성 요소

Storm 토폴로지는 DAG(방향성 비순환 그래프)에서 정렬된 여러 구성 요소로 구성됩니다. 그래프의 구성 요소 간에 데이터가 흐릅니다. 각 구성 요소는 하나 이상의 데이터 스트림을 사용하며, 선택적으로 하나 이상의 스트림을 내보낼 수 있습니다.

|구성 요소 |Description |
|---|---|
|Spout|데이터를 토폴로지로 가져옵니다. 하나 이상의 스트림을 토폴로지에 내보냅니다.|
|화살표|Spout 또는 다른 볼트에서 내보낸 스트림을 사용 합니다. Bolt는 필요에 따라 스트림을 토폴로지로 내보낼 수 있습니다. 또한 Bolt는 HDFS, Kafka 또는 HBase와 같은 외부 서비스 또는 스토리지에 데이터를 쓰는 역할을 수행합니다.|

> [!div class="mx-imgBorder"]
> 스톰 구성 요소의 상호 작용을 ![](./media/migrate-storm-to-spark/apache-storm-components.png)

스톰은 다음 세 가지 디먼 구성 되어 있으며,이로 인해 스톰 클러스터가 작동 합니다.

|데몬 |Description |
|---|---|
|Nimbus|Hadoop JobTracker와 마찬가지로 클러스터 주위에 코드를 배포 하 고 컴퓨터에 작업을 할당 하 고 오류를 모니터링 하는 일을 담당 합니다.|
|Zookeeper|클러스터 조정에 사용 됩니다.|
|Supervisor|는 해당 컴퓨터에 할당 된 작업을 수신 하 고 Nimbus의 지시문에 따라 작업자 프로세스를 시작 및 중지 합니다. 각 작업자 프로세스는 토폴로지의 하위 집합을 실행 합니다. 여기에서 사용자의 응용 프로그램 논리 (Spouts 및 볼트)를 실행 합니다.|

> [!div class="mx-imgBorder"]
> ![nimbus, 사육 아웃 및 감독자 디먼](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark 스트리밍 아키텍처 및 구성 요소

다음 단계는 Spark 스트리밍 () 및 Spark 구조적 스트리밍에서 구성 요소가 함께 작동 하는 방법을 요약 합니다.

* Spark 스트리밍이 시작 되 면 드라이버는 실행 기에서 작업을 시작 합니다.
* 실행자는 스트리밍 데이터 원본에서 스트림을 받습니다.
* 실행자는 데이터 스트림을 받을 때 스트림을 블록으로 분할 하 고 메모리에 보관 합니다.
* 데이터 블록이 다른 실행자에 복제 됩니다.
* 처리 된 데이터는 대상 데이터 저장소에 저장 됩니다.

> [!div class="mx-imgBorder"]
> 출력](./media/migrate-storm-to-spark/spark-streaming-to-output.png)에 대 한 spark 스트리밍 경로 ![

## <a name="spark-streaming-dstream-workflow"></a>Spark 스트리밍 (d 스트림) 워크플로

각 일괄 처리 간격이 지나면 해당 간격에서 모든 데이터를 포함하는 새 RDD가 생성됩니다. Rds의 연속 집합은 DStream으로 수집 됩니다. 예를 들어 일괄 처리 간격이 1초 긴 경우 DStream에서 해당 초 동안 수집된 모든 데이터를 포함하는 하나의 RDD를 포함하는 일괄 처리를 매 초 내보냅니다. DStream을 처리할 때 이러한 일괄 처리 중 하나에 온도 이벤트가 나타납니다. Spark 스트리밍 애플리케이션은 이벤트를 포함하는 일괄 처리를 처리하고 각 RDD에 저장된 데이터에서 궁극적으로 작업을 수행합니다.

> [!div class="mx-imgBorder"]
> ![spark 스트리밍 일괄 처리](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Spark Streaming에서 사용할 수 있는 다양 한 변환에 대 한 자세한 [내용은 참조 하세요](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Spark Structured Streaming

Spark 구조적 스트리밍은 데이터 스트림을 깊이 있게 바인딩되지 않는 테이블로 나타냅니다. 새 데이터가 도착할 때 테이블이 계속 증가 합니다. 이 입력 테이블은 장기 실행 쿼리를 통해 지속적으로 처리 되며 결과가 출력 테이블로 전송 됩니다.

구조적 스트리밍에서 데이터는 시스템에 들어오는 즉시 입력 테이블로 수집됩니다. 이 입력 테이블에 대해 작업을 수행하는 쿼리를 작성합니다(데이터 프레임 및 데이터 세트 API 사용).

쿼리 출력은 쿼리 결과를 포함 하는 *결과 테이블*을 생성 합니다. 관계형 데이터베이스와 같은 외부 데이터 저장소에 대 한 결과 테이블에서 데이터를 그릴 수 있습니다.

입력 테이블에서 데이터가 처리 되는 시기는 트리거 간격에 의해 제어 됩니다. 트리거 간격은 기본적으로 0이며, 이 경우 데이터가 들어오는 즉시 구조적 스트리밍에서 처리하려고 시도합니다. 실제로 구조적 스트리밍은 이전 쿼리의 실행을 처리하는 즉시 새로 받은 데이터에 대해 또 다른 처리를 시작합니다. 트리거가 일정한 간격으로 실행되도록 구성하여 스트리밍 데이터를 시간 기반 일괄 처리로 처리할 수 있습니다.

> [!div class="mx-imgBorder"]
> 구조적 스트리밍의 데이터 처리 ![](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> 구조적 스트리밍](./media/migrate-storm-to-spark/structured-streaming-model.png)에 대 한 ![프로그래밍 모델

## <a name="general-migration-flow"></a>일반 마이그레이션 흐름

스톰에서 Spark로 권장 되는 마이그레이션 흐름은 다음과 같은 초기 아키텍처를 가정 합니다.

* Kafka은 스트리밍 데이터 원본으로 사용 됩니다.
* Kafka 및 스톰이 동일한 가상 네트워크에 배포 됩니다.
* 스톰에서 처리 하는 데이터는 Azure Storage 또는 Azure Data Lake Storage Gen2 같은 데이터 싱크에 기록 됩니다.

    > [!div class="mx-imgBorder"]
    > 현재 가정 된 환경의 ![다이어그램](./media/migrate-storm-to-spark/presumed-current-environment.png)

응용 프로그램을 폭풍에서 Spark 스트리밍 Api 중 하나로 마이그레이션하려면 다음을 수행 합니다.

1. **새 클러스터를 배포 합니다.** 동일한 가상 네트워크에 새 HDInsight 4.0 Spark 클러스터를 배포 하 고 Spark 스트리밍 또는 Spark 구조적 스트리밍 응용 프로그램을 배포 하 고 철저히 테스트 합니다.

    > [!div class="mx-imgBorder"]
    > HDInsight에서 새 spark 배포 ![](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **이전 스톰 클러스터에서 사용을 중지 합니다.** 기존 스톰에서 스트리밍 데이터 원본의 데이터 사용을 중지 하 고 데이터가 대상 싱크에 쓰기를 완료할 때까지 기다립니다.

    > [!div class="mx-imgBorder"]
    > 현재 클러스터에 대 한 사용 중지 ![](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **새 Spark 클러스터에서 사용을 시작 합니다.** 새로 배포 된 HDInsight 4.0 Spark 클러스터에서 데이터 스트리밍을 시작 합니다. 이번에는 최신 Kafka 오프셋에서를 사용 하 여 프로세스를 수행 합니다.

    > [!div class="mx-imgBorder"]
    > 새 클러스터에서 사용을 시작 ![](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **필요에 따라 이전 클러스터를 제거 합니다.** 스위치가 완료 되 고 제대로 작동 하는 경우 필요에 따라 이전 HDInsight 3.6 스톰 클러스터를 제거 합니다.

    > [!div class="mx-imgBorder"]
    > 필요에 따라 이전 HDInsight 클러스터를 제거 ![](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>다음 단계

스톰, Spark 스트리밍 및 Spark 구조적 스트리밍에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [Apache Spark 스트리밍 개요](../spark/apache-spark-streaming-overview.md)
* [Apache Spark 구조적 스트리밍 개요](../spark/apache-spark-structured-streaming-overview.md)