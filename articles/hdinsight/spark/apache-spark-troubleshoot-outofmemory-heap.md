---
title: Azure HDInsight의 Apache Spark에 대 한 OutOfMemoryError 예외
description: Azure HDInsight의 Apache Spark에 대 한 OutOfMemoryError 예외
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/26/2019
ms.openlocfilehash: ac360312fdb3c4a238e6280872bc8c8b548e8544
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620988"
---
# <a name="scenario-outofmemoryerror-exception-for-apache-spark-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Spark에 대 한 OutOfMemoryError 예외

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

OutOfMemoryError 처리 되지 않은 예외가 발생 하 여 Apache Spark 응용 프로그램이 실패 했습니다. 다음과 유사한 오류 메시지가 표시 될 수 있습니다.

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

## <a name="cause"></a>원인

이 예외의 가장 가능성이 높은 원인은 힙 메모리가 충분 하지 않은 경우입니다. Spark 응용 프로그램은 실행 기 또는 드라이버로 실행 될 때 충분 한 JVM (Java Virtual Machines) 힙 메모리를 필요로 합니다.

## <a name="resolution"></a>해결 방법

1. Spark 애플리케이션에서 처리할 데이터의 최대 크기를 결정합니다. 입력 데이터의 최대 크기를 기준으로 크기를 예상 하 고, 입력 데이터를 변환 하 여 생성 된 중간 데이터와 중간 데이터를 추가로 변환 하 여 출력 데이터를 생성 합니다. 초기 예측이 충분 하지 않으면 크기를 약간 늘리고 메모리 오류가 감소 때까지 반복 합니다.

1. 사용할 HDInsight 클러스터가 Spark 애플리케이션을 수용할 수 있는 메모리와 코어 등 충분한 리소스를 갖추고 있는지 확인하세요. 이는 클러스터의 YARN UI에서 Cluster Metrics 섹션에 있는 Memory Used 값과 Memory Total 값, VCores Used 값과 VCores Total 값을 통해 확인할 수 있습니다.

    ![yarn core 메모리 보기](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. 다음 Spark 구성을 적절 한 값으로 설정 합니다. 응용 프로그램 요구 사항을 클러스터에서 사용 가능한 리소스와 분산 합니다. 이러한 값은 YARN에 의해 표시 되는 사용 가능한 메모리 및 코어의 90%를 초과할 수 없으며 Spark 응용 프로그램의 최소 메모리 요구 사항도 충족 해야 합니다.

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    다음 구성은 모든 실행기에서 사용하는 총 메모리 양입니다.

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    다음 구성은 드라이버에서 사용하는 총 메모리 양입니다.

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Spark 메모리 관리 개요](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [HDInsight 클러스터에서 Spark 응용 프로그램 디버깅](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
