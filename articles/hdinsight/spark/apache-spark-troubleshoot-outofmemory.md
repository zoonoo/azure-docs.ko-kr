---
title: 아웃오브메모리애디에이치스스파크에 대한 오류 예외
description: Azure HDInsight의 아파치 스파크 클러스터에 대한 다양한 OutOfMemoryError 예외
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271968"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>아웃오브메모리애디에이치스스파크에 대한 오류 예외

이 문서에서는 Azure HDInsight 클러스터에서 아파치 스파크 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>시나리오: 아파치 스파크에 대 한 OutOfMemory오류 예외

### <a name="issue"></a>문제

아파치 스파크 응용 프로그램이 OutOfMemoryError 처리되지 않은 예외로 실패했습니다. 다음과 유사한 오류 메시지가 나타날 수 있습니다.

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

### <a name="cause"></a>원인

이 예외의 가장 가능성 높은 원인은 JVM(Java Virtual Machine)에 할당된 힙 메모리가 충분하지 않다는 것입니다. 이러한 JVM은 아파치 스파크 응용 프로그램의 일부로 실행기 또는 드라이버로 시작됩니다.

### <a name="resolution"></a>해결 방법

1. Spark 애플리케이션에서 처리할 데이터의 최대 크기를 결정합니다. 입력 데이터의 최대 크기, 입력 데이터를 변환하여 생성된 중간 데이터 및 중간 데이터를 더 변환하는 출력 데이터를 기반으로 크기를 추정합니다. 초기 예상치가 충분하지 않은 경우 크기를 약간 늘리고 메모리 오류가 가라앉을 때까지 반복합니다.

1. 사용할 HDInsight 클러스터가 Spark 애플리케이션을 수용할 수 있는 메모리와 코어 등 충분한 리소스를 갖추고 있는지 확인하세요. 이는 **사용된 메모리** **총값과** 사용된 메모리 총 및 **VCores** 합계에 대한 클러스터 의 YARN UI의 클러스터 메트릭 섹션을 확인하여 결정할 수 **있습니다.**

    ![원사 코어 메모리 보기](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. 다음 Spark 구성을 적절한 값으로 설정합니다. 응용 프로그램 요구 사항과 클러스터의 사용 가능한 리소스 의 균형을 조정합니다. 이러한 값은 YARN에서 볼 수 있는 사용 가능한 메모리 및 코어의 90%를 초과하지 않아야 하며 Spark 응용 프로그램의 최소 메모리 요구 사항도 충족해야 합니다.

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

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>시나리오: 아파치 스파크 기록 서버를 열려고 할 때 자바 힙 공간 오류

### <a name="issue"></a>문제

Spark 기록 서버에서 이벤트를 열 때 다음과 같은 오류가 발생합니다.

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>원인

이 문제는 대용량 스파크 이벤트 파일을 열 때 리소스가 부족하여 발생하는 경우가 많습니다. 스파크 힙 크기는 기본적으로 1GB로 설정되어 있지만 큰 Spark 이벤트 파일에는 이 보다 더 많은 것이 필요할 수 있습니다.

로드하려는 파일의 크기를 확인하려면 다음 명령을 수행할 수 있습니다.

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>해결 방법

Spark 구성에서 `SPARK_DAEMON_MEMORY` 속성을 편집하고 모든 서비스를 다시 시작하여 스파크 기록 서버 메모리를 늘릴 수 있습니다.

Spark2/Config/고급 spark2-env 섹션을 선택하여 Ambari 브라우저 UI 내에서 이 작업을 수행할 수 있습니다.

![고급 스파크2-env 섹션](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

다음 속성을 추가하여 스파크 기록 서버 메모리를 1g에서 `SPARK_DAEMON_MEMORY=4g`4g으로 변경합니다.

![스파크 속성](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Ambari에서 영향을 받는 모든 서비스를 다시 시작해야 합니다.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>시나리오: Livy 서버 아파치 스파크 클러스터에서 시작 하지 못했습니다.

### <a name="issue"></a>문제

리비 서버는 아파치 스파크 [(리눅스 (HDI 3.6)에 스파크 2.1]에서 시작할 수 없습니다. 다시 시작하려고 하면 Livy 로그에서 다음과 같은 오류 스택이 발생합니다.

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>원인

`java.lang.OutOfMemoryError: unable to create new native thread`강조 표시 OS는 JVM에 더 많은 네이티브 스레드를 할당할 수 없습니다. 프로세스별 스레드 수 제한을 위반하여 이 예외가 발생하는지 확인했습니다.

Livy Server가 예기치 않게 종료되면 Spark 클러스터에 대한 모든 연결도 종료되므로 모든 작업 및 관련 데이터가 손실됩니다. HDP 2.6 세션 복구 메커니즘이 도입된 경우 Livy는 리비 서버가 돌아온 후 복구할 수 있도록 사육사에 세션 세부 정보를 저장합니다.

Livy Server의 고가용성의 일부로 Livy Server를 통해 많은 수의 작업이 제출되면 이러한 세션 상태가 ZK(HDInsight 클러스터)에 저장되고 Livy 서비스가 다시 시작될 때 해당 세션을 복구합니다. 예기치 않은 종료 후 다시 시작하면 Livy는 세션당 하나의 스레드를 생성하며, 이로 인해 복구되는 세션수가 너무 많아서 스레드가 너무 많이 생성됩니다.

### <a name="resolution"></a>해결 방법

아래에 자세히 설명된 단계를 사용하여 모든 항목을 삭제합니다.

1. 를 사용하여 사육사 노드의 IP 주소 받기

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. 위의 명령은 내 클러스터에 대한 모든 사육사를 나열

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. 핑을 사용하여 사육사 노드의 모든 IP 주소를 얻거나 zk 이름을 사용하여 헤드 노드에서 사육사에 연결할 수도 있습니다.

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. 사육사에 연결되면 다음 명령을 실행하여 다시 시작하려고 시도하는 모든 세션을 나열합니다.

    1. 대부분의 경우 8000개 이상의 세션이 목록이 될 수 있습니다. ####

        ```bash
        ls /livy/v1/batch
        ```

    1. 다음 명령은 복구할 모든 세션을 제거하는 것입니다. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. 위의 명령이 완료될 때까지 기다렸다가 커서가 프롬프트를 반환한 다음 성공해야 하는 Ambari에서 Livy 서비스를 다시 시작합니다.

> [!NOTE]
> `DELETE`실행이 완료되면 livy 세션이 실행됩니다. Livy 일괄 처리 세션은 스파크 앱이 완료되는 즉시 자동으로 삭제되지 않습니다. Livy 세션은 Livy Rest 서버에 대한 POST 요청에 의해 생성된 엔터티입니다. 해당 `DELETE` 엔터티를 삭제하려면 호출이 필요합니다. 또는 GC가 시작될 때까지 기다려야 합니다.

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [스파크 메모리 관리 개요](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [HDInsight 클러스터에서 스파크 응용 프로그램을 디버깅.](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스( 답변, 지원 및 전문가)에 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
