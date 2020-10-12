---
title: Spark 설정 구성 - Azure HDInsight
description: Azure HDInsight 클러스터에 대 한 Apache Spark 설정을 보고 구성 하는 방법
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: cdef21c69e8f05924097d57bbe78b86d38497b86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82188160"
---
# <a name="configure-apache-spark-settings"></a>Apache Spark 설정 구성

HDInsight Spark 클러스터에는 Apache Spark 라이브러리 설치가 포함되어 있습니다.  각 HDInsight 클러스터에는 Spark를 비롯하여 설치된 모든 서비스에 대한 기본 구성 매개 변수가 포함되어 있습니다.  HDInsight Apache Hadoop 클러스터를 관리 하는 주요 측면은 Spark 작업을 비롯 하 여 워크 로드를 모니터링 하는 것입니다. Spark 작업을 가장 잘 실행 하려면 클러스터의 논리적 구성을 결정할 때 실제 클러스터 구성을 고려 하십시오.

기본 HDInsight Apache Spark 클러스터에는 3개의 Apache ZooKeeper 노드, 2개의 헤드 노드 및 하나 이상의 작업자 노드가 포함되어 있습니다.

![Spark HDInsight 아키텍처](./media/apache-spark-settings/spark-hdinsight-arch.png)

HDInsight 클러스터의 노드에 대 한 vm 수 및 VM 크기는 Spark 구성에 영향을 줄 수 있습니다. 기본이 아닌 HDInsight 구성 값을 사용하려면 종종 기본이 아닌 Spark 구성 값이 필요합니다. HDInsight Spark 클러스터를 만들 때 각 구성 요소에 대해 제안 된 VM 크기가 표시 됩니다. 현재, Azure에 대한 [메모리 최적화 Linux VM 크기](../../virtual-machines/linux/sizes-memory.md)는 D12 v2 이상입니다.

## <a name="apache-spark-versions"></a>Apache Spark 버전

클러스터에 최적의 Spark 버전을 사용합니다.  HDInsight 서비스에는 여러 버전의 Spark 및 HDInsight가 포함되어 있습니다.  Spark의 각 버전에는 기본 클러스터 설정 집합이 포함됩니다.  

새 클러스터를 만들 때 여러 Spark 버전 중에서 선택할 수 있습니다. 전체 목록은  [HDInsight 구성 요소 및 버전](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)을 참조 하세요.

> [!NOTE]  
> HDInsight 서비스에서 Apache Spark의 기본 버전은 예고 없이 변경될 수 있습니다. 버전 종속성이 있는 경우 .NET SDK/Azure PowerShell 및 Azure 클래식 CLI를 사용하여 클러스터를 만들 때 특정 버전을 지정하는 것이 좋습니다.

Apache Spark에는 다음과 같은 3가지 시스템 구성 위치가 있습니다.

* Spark 속성은 대부분의 애플리케이션 매개 변수를 제어하며, `SparkConf` 개체 또는 Java 시스템 속성을 통해 설정할 수 있습니다.
* 각 노드의 `conf/spark-env.sh` 스크립트를 통해, IP 주소와 같은 컴퓨터별 설정을 지정하는 데 환경 변수를 사용할 수 있습니다.
* 로깅은 `log4j.properties`를 통해 구성할 수 있습니다.

특정 Spark 버전을 선택하면 클러스터에 기본 구성 설정이 포함됩니다.  사용자 지정 Spark 구성 파일을 사용하여 기본 Spark 구성 값을 변경할 수 있습니다.  아래에 예제가 나와 있습니다.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

위에 표시된 예제는 5개의 Spark 구성 매개 변수에 대한 몇 가지 기본값을 재정의합니다.  이러한 값은 MapReduce 분할 최소 크기 및 parquet 블록 크기를 Apache Hadoop 하는 압축 코덱입니다. 또한 Spar SQL 파티션 및 열려 있는 파일 크기 기본값이 여기에 해당 합니다.  이러한 구성 변경은 관련 데이터 및 작업 (이 예제에서는 게놈 데이터)에 특정 특징이 있기 때문에 선택 됩니다. 이러한 특성은 이러한 사용자 지정 구성 설정을 사용 하는 것이 더 좋습니다.

---

## <a name="view-cluster-configuration-settings"></a>클러스터 구성 설정 보기

클러스터에서 성능 최적화를 수행 하기 전에 현재 HDInsight 클러스터 구성 설정을 확인 하십시오. Spark 클러스터 창에서 **대시보드** 링크를 클릭하여 Azure Portal에서 HDInsight 대시보드를 시작합니다. 클러스터 관리자의 사용자 이름 및 암호를 사용 하 여 로그인 합니다.

Apache Ambari 웹 UI가 표시 되 고 키 클러스터 리소스 사용 메트릭 대시보드가 표시 됩니다.  Ambari 대시보드는 Apache Spark 구성 및 기타 설치 된 서비스를 보여 줍니다. 대시보드에는 Spark를 비롯 하 여 설치 된 서비스에 대 한 정보를 볼 수 있는 **구성 기록** 탭이 포함 되어 있습니다.

Apache Spark에 대한 구성 값을 표시하려면 **구성 기록**을 선택한 후 **Spark2**를 선택합니다.  **구성** 탭을 선택하고 서비스 목록에서 `Spark`(또는 사용 중인 버전에 따라 `Spark2`) 링크를 선택합니다.  클러스터에 대한 구성 값 목록을 확인합니다.

![Spark 구성](./media/apache-spark-settings/spark-configurations.png)

개별 Spark 구성 값을 확인 하 고 변경 하려면 제목에 "spark"가 있는 링크를 선택 합니다.  Spark에 대한 구성에는 다음 범주의 사용자 지정 및 고급 구성 값이 모두 포함됩니다.

* 사용자 지정 Spark2-defaults
* 사용자 지정 Spark2-metrics-properties
* 고급 Spark2-defaults
* 고급 Spark2-env
* 고급 spark2-hive-site-override

기본이 아닌 구성 값 집합을 만드는 경우 업데이트 기록이 표시 됩니다.  이 구성 기록은 기본이 아닌 구성 중에서 최적 성능을 나타내는 구성을 확인하는 데도 유용할 수 있습니다.

> [!NOTE]  
> 일반적인 Spark 클러스터 구성 설정을 변경하지는 않고 보기만 하려면 최상위 **Spark 작업 UI** 인터페이스에서 **환경** 탭을 선택합니다.

## <a name="configuring-spark-executors"></a>Spark 실행기 구성

다음 다이어그램은 핵심 Spark 개체에 해당하는 드라이버 프로그램과 해당 관련 Spark 컨텍스트, 클러스터 관리자 및 해당 *n* 작업자 노드를 나타냅니다.  각 작업자 노드에는 실행기, 캐시 및 *n* 태스크 인스턴스가 포함됩니다.

![클러스터 개체](./media/apache-spark-settings/hdi-spark-architecture.png)

Spark 작업은 작업자 리소스, 특히 메모리를 사용하므로, 작업자 노드 실행기에 대한 Spark 구성 값을 조정하는 것이 일반적입니다.

애플리케이션 요구 사항을 개선하기 위해 Spark 구성 조정을 위해 수정되는 3가지 핵심 매개 변수는 `spark.executor.instances`, `spark.executor.cores` 및 `spark.executor.memory`입니다. 실행자는 Spark 애플리케이션을 위해 시작된 프로세스입니다. 실행기는 작업자 노드에서 실행되며 애플리케이션에 대한 작업을 담당합니다. 작업자 노드 및 작업자 노드 크기의 수에 따라 실행 기 수와 실행자 크기가 결정 됩니다. 이러한 값은 `spark-defaults.conf` 클러스터 헤드 노드의에 저장 됩니다.  Ambari 웹 UI에서 **사용자 지정 spark-기본값** 을 선택 하 여 실행 중인 클러스터에서 이러한 값을 편집할 수 있습니다.  변경한 후에는 UI에 영향 받은 모든 서비스를 **다시 시작**하라는 메시지가 표시됩니다.

> [!NOTE]  
> 이러한 세 가지 구성 매개 변수는 (클러스터에서 실행된는 모든 애플리케이션의 경우) 클러스터 수준에서 구성될 수도 있고, 각 개별 애플리케이션에 대해 지정될 수도 있습니다.

Spark 실행 기에서 사용 되는 리소스에 대 한 다른 정보 원본은 Spark 응용 프로그램 UI입니다.  UI에서  **실행자** 는 구성 및 사용 된 리소스의 요약 및 세부 정보 보기를 표시 합니다.  전체 클러스터 또는 특정 작업 실행에 대 한 실행자 값을 변경할 것인지 여부를 결정 합니다.

![Spark 실행기](./media/apache-spark-settings/apache-spark-executors.png)

또는 Ambari REST API를 사용 하 여 HDInsight 및 Spark 클러스터 구성 설정을 프로그래밍 방식으로 확인할 수 있습니다.  자세한 내용은 [GitHub의 Apache Ambari API 참조](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)를 참조하세요.

Spark 워크로드에 따라 기본이 아닌 Spark 구성을 사용하여 보다 최적화된 Spark 작업 실행이 제공되는지 확인할 수 있습니다.  샘플 워크로드로 벤치마크 테스트를 수행하여 기본이 아닌 클러스터 구성의 유효성을 검사합니다.  조정을 고려할 수 있는 몇 가지 일반적인 매개 변수는 다음과 같습니다.

|매개 변수 |Description|
|---|---|
|--num-executors|실행자 수를 설정 합니다.|
|--executor-cores|각 실행기의 코어 수를 설정합니다. 다른 프로세스도 사용 가능한 메모리 중 일부를 소비하기 때문에 중간 규모의 실행기를 사용하는 것이 좋습니다.|
|--executor-memory|Apache Hadoop YARN에서 각 실행 기의 메모리 크기 (힙 크기)를 제어 하며, 실행 오버 헤드를 위해 일부 메모리를 남겨 두어야 합니다.|

다음은 다른 구성 값을 갖는 두 개의 작업자 노드 예제입니다.

![두 노드 구성](./media/apache-spark-settings/executor-configuration.png)

다음 목록에서는 핵심 Spark 실행기 메모리 매개 변수를 보여 줍니다.

|매개 변수 |설명|
|---|---|
|spark.executor.memory|실행자에 사용할 수 있는 총 메모리 양을 정의 합니다.|
|spark. 저장소. memoryFraction|(기본 ~60%)은 지속된 RDD를 저장하는 데 사용할 수 있는 메모리 양을 정의합니다.|
|spark. 무작위 재생|(기본 ~20%)은 무작위 재생용으로 예약된 메모리 양을 정의합니다.|
|unrollFraction 및 spark. 저장소입니다.|(총 메모리의 합계 ~ 30%)-이러한 값은 Spark에서 내부적으로 사용 되며 변경할 수 없습니다.|

YARN은 각 Spark 노드의 컨테이너에서 사용되는 메모리의 최대 합계를 제어합니다. 다음 다이어그램은 YARN 구성 개체와 Spark 개체 사이의 노드별 관계를 보여 줍니다.

![YARN Spark 메모리 관리](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter 노트에서 실행 중인 애플리케이션에 대한 매개 변수 변경

기본적으로 HDInsight의 Spark 클러스터에는 다양한 구성 요소가 포함되어 있습니다. 이러한 각 구성 요소에는 필요에 따라 재정의될 수 있는 기본 구성 값이 포함되어 있습니다.

|구성 요소 |설명|
|---|---|
|Spark 코어|Spark Core, Spark SQL, Spark 스트리밍 Api, GraphX 및 Apache Spark MLlib.|
|Anaconda|Python 패키지 관리자입니다.|
|Apache Livy|HDInsight Spark 클러스터에 원격 작업을 제출 하는 데 사용 되는 Apache Spark REST API입니다.|
|Jupyter 및 Apache Zeppelin 노트북|Spark 클러스터와 상호 작용 하기 위한 대화형 브라우저 기반 UI입니다.|
|ODBC 드라이버|HDInsight의 Spark 클러스터를 Microsoft Power BI 및 Tableau와 같은 BI (비즈니스 인텔리전스) 도구에 연결 합니다.|

Jupyter 노트에서 실행되는 애플리케이션의 경우 `%%configure` 명령을 사용하여 노트 자체에서 구성을 변경합니다. 이러한 구성 변경 내용은 노트 인스턴스에서 실행되는 Spark 작업에 적용됩니다. 첫 번째 코드 셀을 실행 하기 전에 응용 프로그램의 시작 부분에서 이러한 변경을 수행 합니다. 변경된 구성은 생성된 Livy 세션에 적용됩니다.

> [!NOTE]  
> 애플리케이션의 이후 단계에서 구성을 변경하려면 `-f`(force) 매개 변수를 사용합니다. 그러나 애플리케이션의 모든 진행 상태는 손실됩니다.

다음 코드는 Jupyter 노트에서 실행 중인 애플리케이션에 대한 구성을 변경하는 방법을 보여 줍니다.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>결론

핵심 구성 설정을 모니터링 하 여 Spark 작업이 예측 가능 하 고 성능이 뛰어난 방식으로 실행 되도록 합니다. 이러한 설정은 특정 워크로드에 대해 최상의 Spark 클러스터 구성을 결정하도록 도와줍니다.  또한 장기 실행 및 리소스 소비 Spark 작업 실행의 실행을 모니터링 해야 합니다.  가장 일반적인 문제는 잘못 된 크기의 실행자와 같이 잘못 된 구성에서 발생 하는 메모리 압력을 중심으로 합니다. 또한 장기 실행 작업 및 작업으로, 데카르트 연산이 발생 합니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 사용할 수 있는 Apache Hadoop 구성 요소 및 버전](../hdinsight-component-versioning.md)
* [HDInsight에서 Apache Spark 클러스터용 리소스 관리](apache-spark-resource-manager.md)
* [Apache Spark 구성](https://spark.apache.org/docs/latest/configuration.html)
* [Apache Hadoop YARN에서 Apache Spark 실행](https://spark.apache.org/docs/latest/running-on-yarn.html)
