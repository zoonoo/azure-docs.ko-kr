---
title: Azure HDInsight의 Apache Spark에 대 한 OutOfMemoryError 예외
description: Azure HDInsight의 Apache Spark 클러스터에 대 한 다양 한 OutOfMemoryError 예외
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: f3f89de07e2e17a4dda47ce3650391af38663004
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087197"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Azure HDInsight의 Apache Spark에 대 한 OutOfMemoryError 예외

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>시나리오: Apache Spark에 대 한 OutOfMemoryError 예외

### <a name="issue"></a>문제점

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

### <a name="cause"></a>원인

이 예외의 가장 가능성 높은 원인은 JVM(Java Virtual Machine)에 할당된 힙 메모리가 충분하지 않다는 것입니다. 이러한 Jvm는 실행 기 또는 드라이버를 Apache Spark 응용 프로그램의 일부로 실행 됩니다.

### <a name="resolution"></a>해결 방법

1. Spark 애플리케이션에서 처리할 데이터의 최대 크기를 결정합니다. 입력 데이터의 최대 크기를 기준으로 크기를 예상 하 고, 입력 데이터를 변환 하 여 생성 된 중간 데이터와 중간 데이터를 추가로 변환 하 여 출력 데이터를 생성 합니다. 초기 예측이 충분 하지 않으면 크기를 약간 늘리고 메모리 오류가 감소 때까지 반복 합니다.

1. 사용할 HDInsight 클러스터가 Spark 애플리케이션을 수용할 수 있는 메모리와 코어 등 충분한 리소스를 갖추고 있는지 확인하세요. 이는 클러스터의 YARN UI에 있는 클러스터 메트릭 섹션을 확인 하 여 **사용 된 메모리** 및 **사용 되는** **메모리 총** 및 vcores **VCores Total** 값을 검토하여 이를 확인할 수 있습니다.

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

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>시나리오: Apache Spark 기록 서버를 열려고 할 때 Java 힙 공간 오류가 발생 했습니다.

### <a name="issue"></a>문제점

Spark 기록 서버에서 이벤트를 열 때 다음과 같은 오류가 표시 됩니다.

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>원인

이 문제는 많은 spark 이벤트 파일을 열 때 리소스가 부족 하 여 발생 하는 경우가 많습니다. Spark 힙 크기는 기본적으로 1gb로 설정 되지만 큰 Spark 이벤트 파일에는이 보다 많은 작업이 필요할 수 있습니다.

로드 하려는 파일의 크기를 확인 하려는 경우 다음 명령을 수행할 수 있습니다.

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>해결 방법

Spark 구성에서 속성을 `SPARK_DAEMON_MEMORY` 편집 하 고 모든 서비스를 다시 시작 하 여 spark 기록 서버 메모리를 늘릴 수 있습니다.

Spark2/Config/Advanced Spark2 섹션을 선택 하 여 Ambari 브라우저 UI 내에서이 작업을 수행할 수 있습니다.

![Advanced spark2-env 섹션](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

다음 속성을 추가 하 여 Spark 기록 서버 메모리를 1g에서 4g:로 `SPARK_DAEMON_MEMORY=4g`변경 합니다.

![Spark 속성](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Ambari에서 영향을 받는 모든 서비스를 다시 시작 해야 합니다.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>시나리오: Apache Spark 클러스터에서 Livy 서버를 시작 하지 못함

### <a name="issue"></a>문제점

Livy 서버는 Apache Spark (Linux의 Spark 2.1 (HDI 3.6)]에서 시작할 수 없습니다. 다시 시작을 시도 하면 Livy 로그에서 다음 오류 스택이 생성 됩니다.

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

`java.lang.OutOfMemoryError: unable to create new native thread`운영 체제에서 Jvm에 더 많은 네이티브 스레드를 할당할 수 없는 경우를 강조 표시 합니다. 프로세스별 스레드 수 제한 위반으로 인해이 예외가 발생 한 것으로 확인 되었습니다.

Livy 서버가 예기치 않게 종료 되 면 Spark 클러스터에 대 한 모든 연결이 종료 됩니다. 즉, 모든 작업 및 관련 데이터가 손실 됩니다. HDP 2.6 세션 복구 메커니즘이 도입 되었습니다. Livy는 Livy 서버를 다시 시작한 후에 복구 될 수 있도록 세션 세부 정보를 사육 사육에 저장 합니다.

Livy을 통해 많은 수의 작업을 제출 하는 경우 Livy 서버에 대 한 고가용성의 일부로 ZK (HDInsight 클러스터)에 이러한 세션 상태를 저장 하 고 Livy 서비스가 다시 시작 될 때 해당 세션을 복구 합니다. 예기치 않게 종료 된 후 다시 시작 시 Livy은 세션당 스레드를 하나씩 만들지만이로 인해 너무 많은 스레드가 생성 될 수 있도록 특정 개수의 복구 된 세션이 누적 됩니다.

### <a name="resolution"></a>해결 방법

아래에 설명 되어 있는 단계를 사용 하 여 모든 항목을 삭제 합니다.

1. 를 사용 하 여 사육 사 노드의 IP 주소를 가져옵니다.

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. 위의 명령은 클러스터의 모든 zookeeper 나열 됩니다.

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Ping을 사용 하 여 사육 사 노드의 모든 IP 주소를 가져오거나, zk 이름을 사용 하 여 헤드 노드에서 사육 사에 연결할 수도 있습니다.

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. 사육 사에 연결 되 면 다음 명령을 실행 하 여 다시 시작 하려고 하는 모든 세션을 나열 합니다.

    1. 대부분의 경우 8000 개 이상의 세션을 나열할 수 있습니다. ####

        ```bash
        ls /livy/v1/batch
        ```

    1. 다음 명령은 복구 가능한 모든 세션을 제거 하는 것입니다. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. 위의 명령이 완료 될 때까지 기다리거나 커서를 통해 프롬프트가 반환 되 면 성공 해야 하는 Ambari에서 Livy service를 다시 시작 합니다.

> [!NOTE]
> `DELETE`livy 세션이 실행을 완료 한 후 Livy batch 세션은 spark 앱이 완료 되 자 마자 자동으로 삭제 되지 않습니다. Livy 세션은 Livy Rest 서버에 대 한 POST 요청에 의해 생성 된 엔터티입니다. 해당 엔터티를 삭제 하려면 호출이필요합니다.`DELETE` 또는 GC가 시작 될 때까지 기다려야 합니다.

---

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Spark 메모리 관리 개요](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [HDInsight 클러스터에서 Spark 응용 프로그램 디버깅](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
