---
title: Azure HDInsight 마이그레이션 3.6 아파치 스톰을 HDInsight 4.0 아파치 스파크로 이동
description: 아파치 스톰 워크로드를 스파크 스트리밍 또는 스파크 구조화 스트리밍으로 마이그레이션하기 위한 차이점 및 마이그레이션 흐름입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157793"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Azure HDInsight 마이그레이션 3.6 아파치 스톰을 HDInsight 4.0 아파치 스파크로 이동

이 문서에서는 HDInsight 3.6에서 HDInsight 4.0으로 아파치 스톰 워크로드를 마이그레이션하는 방법에 대해 설명합니다. HDInsight 4.0은 아파치 스톰 클러스터 유형을 지원하지 않으므로 다른 스트리밍 데이터 플랫폼으로 마이그레이션해야 합니다. 두 가지 적절한 옵션은 아파치 스파크 스트리밍 및 스파크 구조화 스트리밍입니다. 이 문서에서는 이러한 플랫폼 간의 차이점을 설명하고 아파치 스톰 워크로드를 마이그레이션하기 위한 워크플로를 권장합니다.

## <a name="storm-migration-paths-in-hdinsight"></a>HDInsight의 폭풍 마이그레이션 경로

HDInsight 3.6에서 아파치 스톰에서 마이그레이션하려는 경우 여러 옵션이 있습니다.

* HDInsight 4.0에서 스파크 스트리밍
* HDInsight 4.0에서 스파크 구조화 스트리밍
* Azure Stream Analytics

이 문서에서는 아파치 스톰에서 스파크 스트리밍 및 스파크 구조화 스트리밍으로 마이그레이션하기 위한 가이드를 제공합니다.

> [!div class="mx-imgBorder"]
> ![HDInsight 스톰 마이그레이션 경로](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>아파치 스톰과 스파크 스트리밍의 비교, 스파크 구조화 스트리밍

Apache Storm은 다양한 수준의 보장된 메시지 처리를 제공할 수 있습니다. 예를 들어 기본적인 Storm 애플리케이션은 최소한 한 번 처리를 보장할 수 있고, [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html)는 정확히 한 번 처리를 보장할 수 있습니다. 스파크 스트리밍 및 스파크 구조화 스트리밍은 노드 오류가 발생하더라도 입력 이벤트가 정확히 한 번 처리되도록 보장합니다. Storm에는 각 단일 이벤트를 처리하는 모델이 있으며 Trident에서 마이크로 배치 모델을 사용할 수도 있습니다. 스파크 스트리밍 및 스파크 구조화 스트리밍은 마이크로 배치 처리 모델을 제공합니다.

|  |Storm |Spark 스트리밍 | 스파크 구조화 스트리밍|
|---|---|---|---|
|**이벤트 처리 보증**|적어도 한 번 <br> 정확히 한 번 (트라이던트) |[정확히 한 번](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[정확히 한 번](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**가공 모델**|실시간 <br> 마이크로 배치 (트라이던트) |마이크로 배치 |마이크로 배치 |
|**이벤트 시간 지원**|[예](https://storm.apache.org/releases/2.0.0/Windowing.html)|예|[예](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**언어**|자바 등|스칼라, 자바, 파이썬|파이썬, R, 스칼라, 자바, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>스파크 스트리밍 vs 스파크 구조화 스트리밍

스파크 구조화 스트리밍이 스파크 스트리밍(DStreams)을 대체하고 있습니다. 구조화 된 스트리밍은 향상된 기능 및 유지 관리를 계속 받는 반면 DStreams는 유지 관리 모드에만 있습니다. **참고 : 이 점을 강조하기 위해 링크가 필요합니다.** 구조화 된 스트리밍은 원본및 즉시 지원하는 싱크에 대한 DStreams만큼 많은 기능을 가지고 있지 않으므로 요구 사항을 평가하여 적절한 Spark 스트림 처리 옵션을 선택하십시오.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>스트리밍(단일 이벤트) 처리 vs 마이크로 일괄 처리

Storm은 각 단일 이벤트를 처리하는 모델을 제공합니다. 즉, 들어오는 모든 레코드가 도착하는 즉시 처리됩니다. Spark Streaming 애플리케이션은 처리를 위해 해당 일괄 처리를 보내기 전에 이벤트의 각 '마이크로 일괄 처리'를 수집하기 위해 잠시 기다려야 합니다. 반면, 이벤트 기반 애플리케이션은 각 이벤트를 즉시 처리합니다. Spark 스트리밍 대기 시간은 일반적으로 몇 초 이하입니다. 마이크로 일괄 처리 방법의 이점은 보다 효율적인 데이터 처리 및 간단한 집계 계산입니다.

> [!div class="mx-imgBorder"]
> ![스트리밍 및 마이크로 배치 처리](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>스톰 아키텍처 및 구성 요소

Storm 토폴로지는 DAG(방향성 비순환 그래프)에서 정렬된 여러 구성 요소로 구성됩니다. 그래프의 구성 요소 간에 데이터가 흐릅니다. 각 구성 요소는 하나 이상의 데이터 스트림을 사용하며, 선택적으로 하나 이상의 스트림을 내보낼 수 있습니다.

|구성 요소 |설명 |
|---|---|
|오 르네|데이터를 토폴로지로 가져옵니다. 하나 이상의 스트림을 토폴로지에 내보냅니다.|
|볼트|주전자 또는 기타 볼트에서 방출되는 스트림을 사용합니다. Bolt는 필요에 따라 스트림을 토폴로지로 내보낼 수 있습니다. 또한 Bolt는 HDFS, Kafka 또는 HBase와 같은 외부 서비스 또는 스토리지에 데이터를 쓰는 역할을 수행합니다.|

> [!div class="mx-imgBorder"]
> ![폭풍 구성 요소의 상호 작용](./media/migrate-storm-to-spark/apache-storm-components.png)

폭풍은 폭풍 군집이 계속 작동하는 세 개의 데몬으로 구성됩니다.

|데몬 |설명 |
|---|---|
|Nimbus|Hadoop JobTracker와 마찬가지로 클러스터 주위에 코드를 배포하고 컴퓨터에 작업을 할당하고 오류를 모니터링합니다.|
|Zookeeper|클러스터 조정에 사용됩니다.|
|감독자|컴퓨터에 할당된 작업을 수신하고 Nimbus의 지시에 따라 작업자 프로세스를 시작하고 중지합니다. 각 작업자 프로세스는 토폴로지의 하위 집합을 실행합니다. 사용자의 응용 프로그램 논리(주유 및 Bolt)는 여기에서 실행됩니다.|

> [!div class="mx-imgBorder"]
> ![님버스, 사육사, 감독자 데몬](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>스파크 스트리밍 아키텍처 및 구성 요소

다음 단계에서는 구성 요소가 스파크 스트리밍(DStreams) 및 스파크 구조화 스트리밍에서 함께 작동하는 방법을 요약합니다.

* Spark 스트리밍이 시작되면 드라이버는 실행기에서 작업을 시작합니다.
* 실행기는 스트리밍 데이터 원본에서 스트림을 수신합니다.
* 실행기는 데이터 스트림을 수신할 때 스트림을 블록으로 분할하고 메모리에 유지합니다.
* 데이터 블록은 다른 실행기에게 복제됩니다.
* 그런 다음 처리된 데이터가 대상 데이터 저장소에 저장됩니다.

> [!div class="mx-imgBorder"]
> ![스파크 스트리밍 경로 출력](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>스파크 스트리밍(DStream) 워크플로우

각 일괄 처리 간격이 지나면 해당 간격에서 모든 데이터를 포함하는 새 RDD가 생성됩니다. RDD의 연속 집합은 DStream으로 수집됩니다. 예를 들어 일괄 처리 간격이 1초 긴 경우 DStream에서 해당 초 동안 수집된 모든 데이터를 포함하는 하나의 RDD를 포함하는 일괄 처리를 매 초 내보냅니다. DStream을 처리할 때 이러한 일괄 처리 중 하나에 온도 이벤트가 나타납니다. Spark 스트리밍 애플리케이션은 이벤트를 포함하는 일괄 처리를 처리하고 각 RDD에 저장된 데이터에서 궁극적으로 작업을 수행합니다.

> [!div class="mx-imgBorder"]
> ![스파크 스트리밍 처리 배치](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Spark 스트리밍에서 사용할 수 있는 다양한 변환에 대한 자세한 내용은 [DStreams의 변환을](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)참조하십시오.

## <a name="spark-structured-streaming"></a>Spark Structured Streaming

스파크 구조화 스트리밍은 심층적으로 바인딩되지 않은 테이블로 데이터 스트림을 나타냅니다. 새 데이터가 도착함에 따라 테이블이 계속 커지고 있습니다. 이 입력 테이블은 장기 실행 쿼리에 의해 지속적으로 처리되며 결과는 출력 테이블로 전송됩니다.

구조적 스트리밍에서 데이터는 시스템에 들어오는 즉시 입력 테이블로 수집됩니다. 이 입력 테이블에 대해 작업을 수행하는 쿼리를 작성합니다(데이터 프레임 및 데이터 세트 API 사용).

쿼리 출력은 *쿼리*결과를 포함하는 결과 테이블을 생성합니다. 외부 데이터 스토어(예: 관계형 데이터베이스)에 대한 결과 테이블에서 데이터를 그릴 수 있습니다.

데이터가 입력 테이블에서 처리되는 타이밍은 트리거 간격으로 제어됩니다. 트리거 간격은 기본적으로 0이며, 이 경우 데이터가 들어오는 즉시 구조적 스트리밍에서 처리하려고 시도합니다. 실제로 구조적 스트리밍은 이전 쿼리의 실행을 처리하는 즉시 새로 받은 데이터에 대해 또 다른 처리를 시작합니다. 트리거가 일정한 간격으로 실행되도록 구성하여 스트리밍 데이터를 시간 기반 일괄 처리로 처리할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![구조화 된 스트리밍에서 데이터 처리](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![구조화 스트리밍을 위한 프로그래밍 모델](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>일반 마이그레이션 흐름

Storm에서 Spark로의 권장 마이그레이션 흐름은 다음과 같은 초기 아키텍처를 가정합니다.

* 카프카는 스트리밍 데이터 소스로 사용됩니다.
* 카프카와 스톰은 동일한 가상 네트워크에 배포됩니다.
* Storm에서 처리한 데이터는 Azure 저장소 또는 Azure 데이터 레이크 저장소 Gen2와 같은 데이터 싱크에 기록됩니다.

    > [!div class="mx-imgBorder"]
    > ![추정된 현재 환경의 다이어그램](./media/migrate-storm-to-spark/presumed-current-environment.png)

응용 프로그램을 Storm에서 Spark 스트리밍 API 중 하나로 마이그레이션하려면 다음을 수행합니다.

1. **새 클러스터를 배포합니다.** 동일한 가상 네트워크에 새로운 HDInsight 4.0 스파크 클러스터를 배포하고 스파크 스트리밍 또는 스파크 구조화 스트리밍 애플리케이션을 배포하고 철저하게 테스트합니다.

    > [!div class="mx-imgBorder"]
    > ![HDInsight의 새로운 스파크 배포](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **이전 폭풍 클러스터에서 소모를 중지합니다.** 기존 Storm에서는 스트리밍 데이터 원본의 데이터 사용을 중지하고 데이터가 대상 싱크에 쓰기가 완료될 때까지 기다립니다.

    > [!div class="mx-imgBorder"]
    > ![현재 클러스터에서 사용 중지](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **새 스파크 클러스터에서 사용 시작을 시작합니다.** 새로 배포된 HDInsight 4.0 스파크 클러스터에서 데이터 스트리밍을 시작합니다. 이 때, 프로세스는 최신 카프카 오프셋에서 소비에 의해 인계된다.

    > [!div class="mx-imgBorder"]
    > ![새 클러스터에서 사용 시작](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **필요에 따라 이전 클러스터를 제거합니다.** 스위치가 완료되고 제대로 작동하면 필요에 따라 이전 HDInsight 3.6 스톰 클러스터를 제거합니다.

    > [!div class="mx-imgBorder"]
    > ![필요에 따라 이전 HDInsight 클러스터 제거](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>다음 단계

스톰, 스파크 스트리밍 및 스파크 구조화 스트리밍에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [Apache Spark Streaming 개요](../spark/apache-spark-streaming-overview.md)
* [Apache Spark 구조적 스트리밍 개요](../spark/apache-spark-structured-streaming-overview.md)