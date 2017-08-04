---
title: "Spark 문제 해결 - Azure HDInsight | Microsoft Docs"
description: "Spark FAQ를 사용하여 Azure HDInsight 플랫폼의 Spark에 대한 질문과 대답을 알아봅니다."
keywords: "Azure HDInsight, Spark, FAQ, 문제 해결 가이드, 일반적인 문제, 응용 프로그램 구성, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 7655acd689c3f5b869eb0ddb5f186cff032ca8b6
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="spark-troubleshooting"></a>Spark 문제 해결

이 문서에서는 Apache Ambari에서 Spark 페이로드를 사용할 때의 주요 문제 및 해결 방법에 대해 설명합니다.

## <a name="how-do-i-configure-a-spark-application-through-ambari-on-clusters"></a>클러스터에서 Ambari를 통해 Spark 응용 프로그램을 구성하는 방법

### <a name="issue"></a>문제:

Ambari에서 Spark 응용 프로그램이 사용할 수 있는 메모리 및 코어의 수를 구성해야 합니다.  

### <a name="resolution-steps"></a>해결 단계: 

이 프로시저 구성 값은 이전에 HDInsight Spark 클러스터에 설정한 값입니다. 설정해야 하는 Spark 구성과 값을 결정하려면 [Spark 응용 프로그램이 OutOfMemoryError로 실패한 이유는 무엇입니까?](#why-did-my-spark-application-fail-with-an-outofmemoryerror)를 참조하세요.

1. 클러스터 목록에서 **Spark2**를 선택합니다.

    ![목록에서 클러스터 선택](media/hdinsight-troubleshoot-spark/update-config-1.png)

1. **Configs** 탭을 클릭합니다.

    ![Configs 탭 선택](media/hdinsight-troubleshoot-spark/update-config-2.png)

1. 구성 목록에서 **Custom-spark2-defaults**를 선택합니다.

    ![custom-spark-defaults 선택](media/hdinsight-troubleshoot-spark/update-config-3.png)

1. **spark.executor.memory**와 같이 조정해야 하는 값 설정을 찾습니다. 여기서 4608m의 값은 너무 높습니다.

    ![spark.executor.memory 필드 선택](media/hdinsight-troubleshoot-spark/update-config-4.png)

1. 값을 권장되는 값으로 설정합니다. 여기서는 이 설정에 2048이 권장됩니다.

    ![2048m로 값 변경](media/hdinsight-troubleshoot-spark/update-config-5.png)

1. 설정 값을 저장하고 구성을 저장합니다. 

    도구 모음에서 **저장**을 클릭합니다.

    ![설정 및 구성 저장](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    주의할 필요가 있는 구성이면 알림이 표시됩니다. 해당 구성을 기록해 두고 **계속 진행**을 클릭합니다. 

    ![계속 진행 클릭](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    구성 변경 내용에 대한 메모를 작성하고 **저장**을 클릭합니다.

    ![변경 내용에 대한 메모를 입력합니다.](media/hdinsight-troubleshoot-spark/update-config-6c.png)

1. 구성이 저장될 때마다 서비스를 다시 시작하라는 메시지가 표시됩니다. **다시 시작**을 클릭합니다.

    ![다시 시작 클릭](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    다시 시작을 확인합니다.

    ![Confirm Reatart All(다시 시작 모두 확인) 클릭](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    실행 중인 프로세스를 검토할 수 있습니다.

    ![실행 중인 프로세스 검토](media/hdinsight-troubleshoot-spark/update-config-7c.png)

1. 또한 구성을 추가할 수도 있습니다. 구성 목록에서 3단계에서 수행한 대로 **Custom-spark2-defaults**를 선택한 다음 **속성 추가**를 선택합니다.

    ![속성 추가 클릭](media/hdinsight-troubleshoot-spark/update-config-8.png)

1. 새 속성을 정의합니다. 데이터 형식과 같은 특정 설정을 위한 대화 상자로 단일 속을 정의하거나 한 줄에 하나의 정의로 여러 속성을 정의할 수 있습니다. 

    이 예제에서 **spark.driver.memory** 속성의 값은 4g로 정의되었습니다.

    ![새 속성 정의](media/hdinsight-troubleshoot-spark/update-config-9.png)

1. 6단계와 7단계에서 설명한 대로 구성을 저장하고 서비스를 다시 시작합니다.

이러한 변경 내용은 클러스터 전체를 대상으로 하지만 실제 Spark 작업 제출 시간에는 무시될 수 있습니다.

### <a name="further-reading"></a>추가 참고 자료:

[HDInsight 클러스터에서 Spark 작업 제출(영문)](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters"></a>클러스터에서 Jupyter Notebook을 통해 Spark 응용 프로그램을 구성하는 방법

### <a name="issue"></a>문제:

HDInsight 클러스터에서 Jupyter Notebook을 사용할 때 Spark 응용 프로그램에서 사용할 수 있는 메모리 양 및 코어 수를 구성해야 합니다. 

1. 설정해야 하는 Spark 구성과 값을 결정하려면 [Spark 응용 프로그램이 OutOfMemoryError로 실패한 이유는 무엇입니까?](#spark-application-failure-outofmemory) 항목을 참조하세요.
1.  Jupyter Notebook의 첫 번째 셀에서 유효한 JSON 형식의 Spark 구성을 %%configure 지시문 뒤에 지정합니다(해당되는 경우 실제 값 변경). 

>![구성 추가](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="further-reading"></a>추가 참고 자료:

[HDInsight 클러스터에서 Spark 작업 제출(영문)](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-livy-on-clusters"></a>클러스터에서 LIVY를 통해 Spark 응용 프로그램을 구성하는 방법

### <a name="issue"></a>문제:

LIVY를 통해 제출할 때 Spark 응용 프로그램이 HDInsight 클러스터에서 사용할 수 있는 메모리 양 및 코어 수를 구성해야 합니다. 

1. 설정해야 하는 Spark 구성과 값을 결정하려면 [Spark 응용 프로그램이 OutOfMemoryError로 실패한 이유는 무엇입니까?](#spark-application-failure-outofmemory) 항목을 참조하세요.
1. 다음과 비슷한 명령으로 CURL과 같은 REST 클라이언트를 사용하여 LIVY에 Spark 응용 프로그램을 제출합니다(해당되는 경우 실제 값 변경).

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

### <a name="further-reading"></a>추가 참고 자료:

[HDInsight 클러스터에서 Spark 작업 제출(영문)](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-spark-submit-on-clusters"></a>클러스터에서 spark-submit을 통해 Spark 응용 프로그램을 구성하는 방법

### <a name="issue"></a>문제: 

spark-submit을 통해 제출할 때 Spark 응용 프로그램이 HDInsight 클러스터에서 사용할 수 있는 메모리 양 및 코어 수를 구성해야 합니다.

1. 설정해야 하는 Spark 구성과 값을 결정하려면 [Spark 응용 프로그램이 OutOfMemoryError로 실패한 이유는 무엇입니까?](#spark-application-failure-outofmemory) 항목을 참조하세요.
1. 다음과 비슷한 명령으로 spark-shell을 시작합니다(해당되는 경우 구성의 실제 값 변경). 

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="further-reading"></a>추가 참고 자료:

[HDInsight 클러스터에서 Spark 작업 제출(영문)](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)



## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Spark 응용 프로그램 OutOfMemoryError 예외가 발생하는 원인

### <a name="error"></a>오류:

Spark 응용 프로그램이 OutOfMemoryError 예외로 인해 실패함

### <a name="detailed-description"></a>자세한 설명:

Spark 응용 프로그램이 다음과 같은 유형의 확인(catch)할 수 없는 예외로 인해 실패합니다.  
```apache
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

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

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

### <a name="probable-cause"></a>가능한 원인:

이 예외의 가장 가능성이 높은 원인은 Spark 응용 프로그램에 포함된 실행기 또는 드라이버로 시작되는 JVM(Java Virtual Machine)에 할당된 힙 메모리 부족입니다. 

### <a name="resolution-steps"></a>해결 단계:

1. Spark 응용 프로그램에서 처리할 데이터의 최대 크기를 결정합니다. 입력 데이터 크기의 최대값, 입력 데이터 변환을 통해 생성된 중간 데이터와 중간 데이터의 추가적인 변환을 통해 생성된 출력 데이터에 따라 이러한 크기를 추측해 볼 수 있습니다. 정형 초기 추측이 가능하지 않을 경우에도 반복적인 프로세스가 될 수 있습니다. 
1. 사용할 HDInsight 클러스터가 Spark 응용 프로그램을 수용할 수 있는 메모리와 코어 등 충분한 리소스를 갖추고 있는지 확인하세요. 이는 클러스터의 YARN UI에서 Cluster Metrics 섹션에 있는 Memory Used 값과 Memory Total 값, VCores Used 값과 VCores Total 값을 통해 확인할 수 있습니다.

1. Spark 응용 프로그램의 메모리 요구 사항에 잘 부합하며 YARN에서 확인한 사용 가능한 메모리와 코어의 90%를 초과하지 않는 적절한 값으로 다음 Spark 구성을 설정합니다. 

```apache
spark.executor.instances (Example: 8 for 8 executor count) 
spark.executor.memory (Example: 4g for 4 GB) 
spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
spark.executor.cores (Example: 2 for 2 cores per executor) 
spark.driver.memory (Example: 8g for 8GB) 
spark.driver.cores (Example: 4 for 4 cores)   
spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
```

다음 구성은 모든 실행기에서 사용하는 총 메모리 양입니다. 
```apache
spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
```
다음 구성은 드라이버에서 사용하는 총 메모리 양입니다. 
```apache
spark.driver.memory + spark.yarn.driver.memoryOverhead
```

### <a name="further-reading"></a>추가 참고 자료:

- [Spark 메모리 관리 개요(영문)](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [HDInsight 클러스터에서 Spark 응용 프로그램 디버깅(영문)](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)










