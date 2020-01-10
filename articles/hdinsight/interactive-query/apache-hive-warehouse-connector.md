---
title: Apache Spark & Hive-Hive 웨어하우스 커넥터-Azure HDInsight
description: Azure HDInsight의 Hive 웨어하우스 커넥터와 Apache Spark 및 Apache Hive를 통합 하는 방법에 대해 알아봅니다.
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 765bbc352c493124c1adec68eff456f4d0de3d49
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75744881"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Hive 웨어하우스 커넥터를 사용 하 여 Apache Spark 및 Apache Hive 통합

HWC (Apache Hive 웨어하우스 커넥터)는 Spark 데이터 프레임와 Hive 테이블 간에 데이터를 이동 하는 등의 작업을 지원 하 고 Spark 스트리밍 데이터를 Hive 테이블로 전송 하는 등의 작업을 지원 하 여 Apache Spark 및 Apache Hive을 보다 쉽게 사용할 수 있도록 하는 라이브러리입니다. Hive 웨어하우스 커넥터는 Spark와 Hive 간의 브리지 처럼 작동 합니다. 개발을 위한 Scala, Java 및 Python을 지원 합니다.

Hive 웨어하우스 커넥터를 사용 하면 Hive 및 Spark의 고유한 기능을 활용 하 여 강력한 빅 데이터 응용 프로그램을 빌드할 수 있습니다. Apache Hive는 ACID (원자성, 일관성, 격리성 및 내구성) 인 데이터베이스 트랜잭션에 대 한 지원을 제공 합니다. Hive의 ACID 및 트랜잭션에 대 한 자세한 내용은 [Hive 트랜잭션](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)을 참조 하세요. 또한 Hive는 Apache Spark에서 사용할 수 없는 Apache 레인저 및 짧은 대기 시간 분석 처리를 통해 자세한 보안 제어를 제공 합니다.

Apache Spark에는 Apache Hive에서 사용할 수 없는 스트리밍 기능을 제공 하는 구조적 스트리밍 API가 있습니다. HDInsight 4.0부터 Apache Spark 2.3.1 및 Apache Hive 3.1.0에는 별도의 metastore 있으므로 상호 운용성이 어려워질 수 있습니다. Hive Warehouse Connector를 통해 보다 쉽게 Spark 및 Hive를 함께 사용할 수 있습니다. HWC 라이브러리는 LLAP 디먼에서 Spark 실행 기에 병렬로 데이터를 로드 하 여 Spark에서 Hive로 표준 JDBC 연결을 사용 하는 것 보다 효율적이 고 확장 가능 합니다.

![hive 웨어하우스 커넥터 아키텍처](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive Warehouse Connector에서 지원하는 일부 작업은 다음과 같습니다.

* 테이블 설명
* ORC 형식의 데이터에 대 한 테이블 만들기
* Hive 데이터 선택 및 DataFrame 검색
* Hive에 DataFrame 일괄 쓰기
* Hive update 문 실행
* Hive에서 테이블 데이터 읽기, Spark에서 데이터 변환 및 새 Hive 테이블에 데이터 쓰기
* HiveStreaming를 사용하여 Hive에 DataFrame 또는 Spark 스트림 쓰기

## <a name="hive-warehouse-connector-setup"></a>Hive 웨어하우스 커넥터 설정

Azure HDInsight에서 Spark 및 대화형 쿼리 클러스터 간에 Hive 웨어하우스 커넥터를 설정 하려면 다음 단계를 수행 합니다.

### <a name="create-clusters"></a>클러스터 만들기

1. 저장소 계정 및 사용자 지정 Azure virtual network를 사용 하 여 HDInsight Spark **4.0** 클러스터를 만듭니다. Azure 가상 네트워크에서 클러스터를 만드는 방법에 대 한 자세한 내용은 [기존 가상 네트워크에 HDInsight 추가](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)를 참조 하세요.

1. Spark 클러스터와 동일한 저장소 계정 및 Azure 가상 네트워크를 사용 하 여 HDInsight 대화형 쿼리 (LLAP) **4.0** 클러스터를 만듭니다.

### <a name="modify-hosts-file"></a>호스트 파일 수정

대화형 쿼리 클러스터의 headnode0에 있는 `/etc/hosts` 파일에서 노드 정보를 복사 하 고 해당 정보를 Spark 클러스터의 headnode0에 있는 `/etc/hosts` 파일에 연결 합니다. 이 단계에서는 Spark 클러스터가 대화형 쿼리 클러스터 노드의 IP 주소를 확인할 수 있습니다. `cat /etc/hosts`를 사용 하 여 업데이트 된 파일의 내용을 봅니다. 최종 출력은 아래 스크린샷에 표시 된 것과 같아야 합니다.

![hive 웨어하우스 커넥터가 파일을 호스트 합니다.](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>예비 정보 수집

#### <a name="from-your-interactive-query-cluster"></a>대화형 쿼리 클러스터에서

1. `https://LLAPCLUSTERNAME.azurehdinsight.net`를 사용 하 여 클러스터의 Apache Ambari 홈 페이지로 이동 합니다. 여기서 `LLAPCLUSTERNAME`은 대화형 쿼리 클러스터의 이름입니다.

1. **Hive** > **CONFIGS** > **고급** > **고급 hive-사이트** > **hive** 로 이동 하 여 값을 확인 합니다. 값은 `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`와 유사할 수 있습니다.

1. **Hive** > **CONFIGS** > **고급** > **일반** > **metastore** 으로 이동 하 여 값을 확인 합니다. 값은 `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`와 유사할 수 있습니다.

#### <a name="from-your-apache-spark-cluster"></a>Apache Spark 클러스터에서

1. `https://SPARKCLUSTERNAME.azurehdinsight.net`를 사용 하 여 클러스터의 Apache Ambari 홈 페이지로 이동 합니다. 여기서 `SPARKCLUSTERNAME`는 Apache Spark 클러스터의 이름입니다.

1. **Hive** > **CONFIGS** > **고급** > **고급 hive-대화형** > **hive** . a l a. a l a. 값은 `@llap0`와 유사할 수 있습니다.

### <a name="configure-spark-cluster-settings"></a>Spark 클러스터 설정 구성

Spark Ambari 웹 UI에서 **Spark2** > **CONFIGS** > **Custom Spark2-defaults**로 이동 합니다.

![Apache Ambari Spark2 구성](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

다음을 추가/업데이트 하기 위해 필요에 따라 **속성 추가** ...를 선택 합니다.

| 키 | 값 |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|이전에 **hive**. a l a.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다. 대화형 쿼리 클러스터의 Hiveserver2에 연결 하는 JDBC 연결 문자열로 설정 합니다. `LLAPCLUSTERNAME`을 대화형 쿼리 클러스터의 이름으로 바꿉니다. `PWD`를 실제 암호로 바꿉니다.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다. 적절 한 HDFS 호환 준비 디렉터리로 설정 합니다. 서로 다른 두 개의 클러스터가 있는 경우 스테이징 디렉터리는 HiveServer2에서 액세스할 수 있도록 LLAP 클러스터의 저장소 계정의 준비 디렉터리에 있는 폴더 여야 합니다.  `STORAGE_ACCOUNT_NAME`를 클러스터에서 사용 하는 저장소 계정의 이름으로 바꾸고 `STORAGE_CONTAINER_NAME`,을 저장소 컨테이너의 이름으로 바꿉니다.|
|`spark.datasource.hive.warehouse.metastoreUri`|이전에 **metastore**에서 가져온 값입니다.|
|`spark.security.credentials.hiveserver2.enabled`|YARN client 배포 모드에 대 한 `false`입니다.|
|`spark.hadoop.hive.zookeeper.quorum`|이전에 **hive**에서 가져온 값입니다.|

필요에 따라 변경 내용을 저장 하 고 구성 요소를 다시 시작 합니다.

## <a name="using-the-hive-warehouse-connector"></a>Hive 웨어하우스 커넥터 사용

### <a name="connecting-and-running-queries"></a>쿼리 연결 및 실행

몇 가지 방법 중 하나를 선택 하 여 대화형 쿼리 클러스터에 연결 하 고 Hive 웨어하우스 커넥터를 사용 하 여 쿼리를 실행할 수 있습니다. 지원 되는 방법에는 다음 도구가 포함 됩니다.

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

이 문서에 제공 된 모든 예제는 spark-셸을 통해 실행 됩니다.

Spark 셸 세션을 시작 하려면 다음 단계를 수행 합니다.

1. Apache Spark 클러스터의 헤드 노드로 SSH를 합니다. SSH를 사용 하 여 클러스터에 연결 하는 방법에 대 한 자세한 내용은 [ssh를 사용 하 여 HDInsight에 연결 (Apache Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조 하세요.

1. 다음 명령을 입력 하 여 spark 셸을 시작 합니다.

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    명령을 입력할 수 있는 시작 메시지와 `scala>` 프롬프트가 표시 됩니다.

1. Spark-셸을 시작한 후 다음 명령을 사용 하 여 Hive 웨어하우스 커넥터 인스턴스를 시작할 수 있습니다.

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Enterprise Security Package (ESP) 클러스터에서 쿼리 연결 및 실행

Enterprise Security Package (ESP)는 Active Directory 기반 인증, 다중 사용자 지원 및 Azure HDInsight의 Apache Hadoop 클러스터에 대 한 역할 기반 액세스 제어와 같은 엔터프라이즈급 기능을 제공 합니다. ESP에 대 한 자세한 내용은 [HDInsight에서 Enterprise Security Package 사용](../domain-joined/apache-domain-joined-architecture.md)을 참조 하세요.

1. Apache Spark 클러스터의 헤드 노드로 SSH를 합니다. SSH를 사용 하 여 클러스터에 연결 하는 방법에 대 한 자세한 내용은 [ssh를 사용 하 여 HDInsight에 연결 (Apache Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조 하세요.

1. `kinit`를 입력 하 고 도메인 사용자로 로그인 합니다.

1. 아래와 같이 구성 매개 변수의 전체 목록을 사용 하 여 spark-shell을 시작 합니다. 꺾쇠 괄호 사이의 모든 대문자에 있는 모든 값은 클러스터에 따라 지정 해야 합니다. 아래 매개 변수에 대 한 입력 값을 확인 해야 하는 경우 [Hive 웨어하우스 커넥터 설정](#hive-warehouse-connector-setup)에 대 한 섹션을 참조 하세요.

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Hive 쿼리에서 Spark 데이터 프레임 만들기

HWC 라이브러리를 사용 하는 모든 쿼리 결과가 데이터 프레임로 반환 됩니다. 다음 예에서는 기본 쿼리를 만드는 방법을 보여 줍니다.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

쿼리 결과는 Spark 데이터 프레임 이며, MLIB 및 SparkSQL와 같은 Spark 라이브러리와 함께 사용할 수 있습니다.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Hive 테이블에 Spark 데이터 프레임 작성

Spark는 기본적으로 Hive의 관리 되는 ACID 테이블에 쓰기를 지원 하지 않습니다. 그러나 HWC를 사용 하 여 Hive 테이블에 모든 데이터 프레임를 작성할 수 있습니다. 다음 예제의 작업에서이 기능을 확인할 수 있습니다.

1. 다음 명령을 사용 하 여 `sampletable_colorado` 라는 테이블을 만들고 해당 열을 지정 합니다.

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. 열 `state` `Colorado`와 일치 하는 테이블 `hivesampletable`를 필터링 합니다. Hive 테이블의이 쿼리는 Spark 데이터 프레임 반환 됩니다. 그런 다음 `write` 함수를 사용 하 여 `sampletable_colorado` 데이터 프레임이 Hive 테이블에 저장 됩니다.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. 다음 명령을 사용 하 여 결과를 확인 합니다.

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![hive 웨어하우스 커넥터 hive 테이블 표시](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>구조적 스트리밍 쓰기

Hive 웨어하우스 커넥터를 사용 하 여 Spark 스트리밍을 사용 하 여 데이터를 Hive 테이블에 쓸 수 있습니다.

다음 단계를 수행 하 여 localhost 포트 9999의 Spark 스트림에서 데이터를 수집 하는 Hive 웨어하우스 커넥터 예제를 Hive 테이블에 만듭니다.

1. [쿼리 연결 및 실행](#connecting-and-running-queries)의 단계를 따릅니다.

1. 다음 명령을 사용 하 여 spark 스트림을 시작 합니다.

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 다음 단계를 수행 하 여 만든 Spark 스트림에 대 한 데이터를 생성 합니다.
    1. 동일한 Spark 클러스터에서 두 번째 SSH 세션을 엽니다.
    1. 명령 프롬프트에서 `nc -lk 9999`를 입력합니다. 이 명령은 netcat 유틸리티를 사용 하 여 명령줄에서 지정 된 포트로 데이터를 보냅니다.

1. 첫 번째 SSH 세션으로 돌아가서 스트리밍 데이터를 보관할 새 Hive 테이블을 만듭니다. Spark-셸에서 다음 명령을 입력 합니다.

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 그런 다음, 다음 명령을 사용 하 여 새로 만든 테이블에 스트리밍 데이터를 씁니다.

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` 및 `database` 옵션은 현재 Apache Spark의 알려진 문제로 인해 수동으로 설정 해야 합니다. 이 문제에 대 한 자세한 내용은 [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)을 참조 하세요.

1. 두 번째 SSH 세션으로 돌아가서 다음 값을 입력 합니다.

    ```bash
    foo
    HiveSpark
    bar
    ```

1. 첫 번째 SSH 세션으로 돌아가서 brief 작업을 기록 합니다. 다음 명령을 사용 하 여 데이터를 확인 합니다.

    ```scala
    hive.table("stream_table").show()
    ```

**Ctrl + C** 를 사용 하 여 두 번째 SSH 세션에서 netcat을 중지 합니다. `:q`를 사용 하 여 첫 번째 SSH 세션에서 spark-셸을 종료 합니다.

### <a name="securing-data-on-spark-esp-clusters"></a>Spark ESP 클러스터에서 데이터 보호

1. 다음 명령을 입력 하 여 몇 가지 샘플 데이터를 포함 하는 테이블 `demo`를 만듭니다.

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 다음 명령을 사용 하 여 테이블의 내용을 봅니다. 정책을 적용 하기 전에 `demo` 표에는 전체 열이 표시 됩니다.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![레인저 정책을 적용 하기 전의 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 열의 마지막 4 자만 표시 하는 열 마스킹 정책을 적용 합니다.  
    1. `https://CLUSTERNAME.azurehdinsight.net/ranger/`에서 레인저 관리 UI로 이동 합니다.
    1. **Hive**에서 클러스터에 대 한 hive 서비스를 클릭 합니다.
        ![레인저 service manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. **마스킹** 탭을 클릭 한 다음 **새 정책 추가** 를 클릭 합니다.

        ![hive 웨어하우스 커넥터 레인저 hive 정책 목록](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. 원하는 정책 이름을 제공 합니다. **마스킹 옵션 선택** 메뉴에서 데이터베이스: **기본값**, hive 테이블: **데모**, hive 열: **이름**, 사용자: **rsadmin2**, 액세스 형식: **선택**및 **부분 마스크: 마지막 4 표시** 를 선택 합니다. **추가**를 클릭합니다.
                정책 만들기 ![](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 테이블의 내용을 다시 봅니다. 레인저 정책을 적용 한 후에는 열의 마지막 4 자만 볼 수 있습니다.

    ![레인저 정책을 적용 한 후의 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 대화형 쿼리 사용](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Zeppelin, Livy, spark-제출 및 pyspark를 사용 하 여 Hive 웨어하우스 커넥터와 상호 작용 하는 예](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
