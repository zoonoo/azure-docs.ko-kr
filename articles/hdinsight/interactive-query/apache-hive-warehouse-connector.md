---
title: 아파치 스파크 & 하이브 - 하이브 창고 커넥터 - Azure HDInsight
description: 아파치 스파크와 아파치 하이브를 Azure HDInsight의 하이브 웨어하우스 커넥터와 통합하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252417"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Hive Warehouse 커넥터를 사용하여 Apache Spark 및 Apache Hive 통합

아파치 하이브 웨어하우스 커넥터(HWC)는 스파크 데이터프레임과 하이브 테이블 간에 데이터를 이동하고 스파크 스트리밍 데이터를 하이브 테이블로 유도하는 등의 작업을 지원하여 아파치 스파크 및 아파치 하이브로 보다 쉽게 작업할 수 있는 라이브러리입니다. 하이브 웨어하우스 커넥터는 스파크와 하이브 사이의 다리처럼 작동합니다. 그것은 개발을위한 스칼라, 자바, 파이썬을 지원합니다.

Hive 웨어하우스 커넥터를 사용하면 Hive 및 Spark의 고유한 기능을 활용하여 강력한 빅 데이터 애플리케이션을 구축할 수 있습니다. 아파치 하이브는 원자성, 일관된, 격리된 및 내구성(ACID)인 데이터베이스 트랜잭션을 지원합니다. Hive의 ACID 및 트랜잭션에 대한 자세한 내용은 [Hive 트랜잭션을](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)참조하십시오. 하이브는 또한 아파치 스파크에서 사용할 수없는 아파치 레인저와 낮은 대기 시간 분석 처리를 통해 자세한 보안 제어를 제공합니다.

아파치 스파크는 아파치 하이브에서 사용할 수없는 스트리밍 기능을 제공하는 구조화 된 스트리밍 API를 가지고 있습니다. HDInsight 4.0부터 아파치 스파크 2.3.1과 아파치 하이브 3.1.0에는 별도의 메타스토어가 있어 상호 운용성이 어려워질 수 있습니다. Hive Warehouse Connector를 통해 보다 쉽게 Spark 및 Hive를 함께 사용할 수 있습니다. HWC 라이브러리는 LLAP 데몬에서 스파크 실행기까지 데이터를 병렬로 로드하므로 스파크에서 하이브까지 표준 JDBC 연결을 사용하는 것보다 더 효율적이고 확장성이 있습니다.

![하이브 창고 커넥터 아키텍처](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive Warehouse Connector에서 지원하는 일부 작업은 다음과 같습니다.

* 테이블 설명
* ORC 형식의 데이터에 대한 테이블 만들기
* Hive 데이터 선택 및 DataFrame 검색
* Hive에 DataFrame 일괄 쓰기
* Hive update 문 실행
* Hive에서 테이블 데이터 읽기, Spark에서 데이터 변환 및 새 Hive 테이블에 데이터 쓰기
* HiveStreaming를 사용하여 Hive에 DataFrame 또는 Spark 스트림 쓰기

## <a name="hive-warehouse-connector-setup"></a>하이브 창고 커넥터 설정

다음 단계에 따라 Azure HDInsight에서 스파크 및 대화형 쿼리 클러스터 간에 Hive 웨어하우스 커넥터를 설정합니다.

### <a name="create-clusters"></a>클러스터 만들기

1. 스토리지 계정과 사용자 지정 Azure 가상 네트워크를 사용하여 HDInsight Spark **4.0** 클러스터를 만듭니다. Azure 가상 네트워크에서 클러스터를 만드는 방법에 대한 자세한 내용은 [기존 가상 네트워크에 HDInsight 추가](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)를 참조하십시오.

1. 스파크 클러스터와 동일한 저장소 계정 및 Azure 가상 네트워크를 사용하여 HDInsight 대화형 쿼리(LLAP) **4.0** 클러스터를 만듭니다.

### <a name="modify-hosts-file"></a>호스트 파일 수정

대화형 쿼리 클러스터의 `/etc/hosts` headnode0에 있는 파일에서 노드 정보를 복사하고 `/etc/hosts` Spark 클러스터의 헤드노드0에 있는 파일에 정보를 연결합니다. 이 단계를 통해 Spark 클러스터는 대화형 쿼리 클러스터에서 노드의 IP 주소를 확인할 수 있습니다. 을 통해 업데이트된 파일의 `cat /etc/hosts`내용을 봅니다. 최종 출력은 아래 스크린 샷에 표시된 것과 같아야합니다.

![하이브 웨어하우스 커넥터 호스트 파일](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>예비 정보 수집

#### <a name="from-your-interactive-query-cluster"></a>대화형 쿼리 클러스터에서

1. 대화형 쿼리 클러스터의 이름을 사용하여 `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `LLAPCLUSTERNAME` 클러스터의 아파치 암바리 하이브 페이지로 이동합니다.

1. **고급** > **일반** > **hive.metastore.uris로** 이동 하 고 값을 기록 합니다. 값은 다음과 `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`유사할 수 있습니다.

1. **고급** > **하이브 사이트** > **hive.zookeeper.quorum로** 이동하여 값을 기록합니다. 값은 다음과 `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`유사할 수 있습니다.

#### <a name="from-your-apache-spark-cluster"></a>아파치 스파크 클러스터에서

1. 아파치 스파크 클러스터의 이름은 위치를 `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `SPARKCLUSTERNAME` 사용하여 클러스터의 아파치 암바리 하이브로 이동합니다.

1. **고급** > **하이브 대화형 사이트** > **hive.llap.daemon.service.hosts로** 이동하여 값을 기록합니다. 값은 다음과 `@llap0`유사할 수 있습니다.

### <a name="configure-spark-cluster-settings"></a>스파크 클러스터 설정 구성

스파크 암바리 웹 UI에서 **Spark2** > **CONFIGS** > **사용자 지정 spark2 기본값으로 이동합니다.**

![아파치 암바리 스파크2 구성](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

필요에 따라 **속성 추가를 선택합니다.**

| Key | 값 |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|**앞서 hive.llap.daemon.service.hosts에서**얻은 값입니다.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. 대화형 쿼리 클러스터에서 Hiveserver2에 연결하는 JDBC 연결 문자열로 설정합니다. 대화형 `LLAPCLUSTERNAME` 쿼리 클러스터의 이름으로 바꿉니다. 실제 `PWD` 암호로 바꿉습니다.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. 적절한 HDFS 호환 스테이징 디렉토리로 설정합니다. 두 개의 서로 다른 클러스터가 있는 경우 준비 디렉터리가 LLAP 클러스터의 저장소 계정의 스테이징 디렉터리의 폴더여야 HiveServer2에 액세스할 수 있습니다.  클러스터에서 `STORAGE_ACCOUNT_NAME` `STORAGE_CONTAINER_NAME` 사용 중인 저장소 계정의 이름과 저장소 컨테이너의 이름으로 바꿉니다.|
|`spark.datasource.hive.warehouse.metastoreUri`|**당신이 hive.metastore.uris에서**이전에 얻은 값 .|
|`spark.security.credentials.hiveserver2.enabled`|`false`YARN 클라이언트 배포 모드에 대한 것입니다.|
|`spark.hadoop.hive.zookeeper.quorum`|**당신이 hive.zookeeper.quorum에서**이전에 얻은 값.|

필요에 따라 변경 내용을 저장하고 구성 요소를 다시 시작합니다.

## <a name="using-the-hive-warehouse-connector"></a>하이브 창고 커넥터 사용

### <a name="connecting-and-running-queries"></a>쿼리 연결 및 실행

대화형 쿼리 클러스터에 연결하고 Hive 웨어하우스 커넥터를 사용하여 쿼리를 실행하는 몇 가지 다른 방법 중에서 선택할 수 있습니다. 지원되는 방법에는 다음 도구가 포함됩니다.

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

이 문서에 제공된 모든 예제는 스파크 셸을 통해 실행됩니다.

스파크 셸 세션을 시작하려면 다음 단계를 수행합니다.

1. 아파치 스파크 클러스터의 헤드 노드에 SSH. SSH를 사용하여 클러스터에 연결하는 것에 대한 자세한 내용은 [SSH를 사용하여 HDInsight(아파치 하도롭)에 연결하기](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)를 참조하십시오.

1. 스파크 셸을 시작하려면 다음 명령을 입력합니다.

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    환영 메시지와 명령을 입력할 `scala>` 수 있는 프롬프트가 표시됩니다.

1. 스파크 셸을 시작한 후 하이브 웨어하우스 커넥터 인스턴스는 다음 명령을 사용하여 시작할 수 있습니다.

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>ESP(엔터프라이즈 보안 패키지) 클러스터에서 쿼리 연결 및 실행

ESP(엔터프라이즈 보안 패키지)는 Azure HDInsight의 아파치 하두롭 클러스터에 대한 Active Directory 기반 인증, 다중 사용자 지원 및 역할 기반 액세스 제어와 같은 엔터프라이즈급 기능을 제공합니다. ESP에 대한 자세한 내용은 [HDInsight의 엔터프라이즈 보안 패키지 사용을](../domain-joined/apache-domain-joined-architecture.md)참조하십시오.

1. 아파치 스파크 클러스터의 헤드 노드에 SSH. SSH를 사용하여 클러스터에 연결하는 것에 대한 자세한 내용은 [SSH를 사용하여 HDInsight(아파치 하도롭)에 연결하기](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)를 참조하십시오.

1. 도메인 `kinit` 사용자를 입력하고 로그인합니다.

1. 아래와 같이 구성 매개 변수의 전체 목록으로 스파크 셸을 시작합니다. 각 대괄호 사이의 모든 대문자의 모든 값은 클러스터에 따라 지정되어야 합니다. 아래 매개 변수에 대해 입력할 값을 찾아야 하는 경우 [Hive 웨어하우스 커넥터 설정의](#hive-warehouse-connector-setup)섹션을 참조하십시오.

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>하이브 쿼리에서 스파크 데이터 프레임 만들기

HWC 라이브러리를 사용하는 모든 쿼리의 결과는 DataFrame으로 반환됩니다. 다음 예제에서는 기본 쿼리를 만드는 방법을 보여 줍니다.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

쿼리의 결과는 MLIB 및 SparkSQL과 같은 스파크 라이브러리에서 사용할 수 있는 스파크 데이터프레임입니다.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>하이브 테이블에 스파크 데이터 프레임 쓰기

스파크는 기본적으로 Hive의 관리 ACID 테이블에 쓰기를 지원하지 않습니다. 그러나 HWC를 사용하면 모든 DataFrame을 Hive 테이블에 쓸 수 있습니다. 다음 예제에서는 이 기능을 확인할 수 있습니다.

1. 라는 `sampletable_colorado` 테이블을 만들고 다음 명령을 사용 하 여 해당 열을 지정 합니다.

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. 열이 `hivesampletable` `state` 같을 테이블을 `Colorado`필터링합니다. Hive 테이블의 이 쿼리는 스파크 DataFrame으로 반환됩니다. 그런 다음 DataFrame함수를 사용하여 `sampletable_colorado` `write` Hive 테이블에 저장됩니다.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. 다음 명령으로 결과를 봅니다.

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![하이브 창고 커넥터 쇼 하이브 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>구조화 된 스트리밍 쓰기

Hive 웨어하우스 커넥터를 사용하면 Spark 스트리밍을 사용하여 Hive 테이블에 데이터를 쓸 수 있습니다.

아래 단계에 따라 로컬 호스트 포트 9999의 스파크 스트림에서 Hive 테이블로 데이터를 수집하는 Hive 웨어하우스 커넥터 예제를 만듭니다.

1. 쿼리 연결 및 실행 중인 단계 아래의 단계를 [따릅니다.](#connecting-and-running-queries)

1. 다음 명령으로 스파크 스트림을 시작합니다.

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 다음 단계를 수행하여 만든 스파크 스트림에 대한 데이터를 생성합니다.
    1. 동일한 스파크 클러스터에서 두 번째 SSH 세션을 엽니다.
    1. 명령 프롬프트에서 `nc -lk 9999`를 입력합니다. 이 명령은 netcat 유틸리티를 사용하여 명령줄에서 지정된 포트로 데이터를 보냅니다.

1. 첫 번째 SSH 세션으로 돌아가서 스트리밍 데이터를 보유할 새 Hive 테이블을 만듭니다. 스파크 셸에서 다음 명령을 입력합니다.

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 그런 다음 다음 명령을 사용하여 새로 만든 테이블에 스트리밍 데이터를 작성합니다.

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` 아파치 `database` 스파크의 알려진 문제로 인해 및 옵션은 현재 수동으로 설정해야 합니다. 이 문제에 대한 자세한 내용은 [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)을 참조하십시오.

1. 두 번째 SSH 세션으로 돌아가다음 값을 입력합니다.

    ```bash
    foo
    HiveSpark
    bar
    ```

1. 첫 번째 SSH 세션으로 돌아가서 간단한 활동을 기록합니다. 다음 명령을 사용하여 데이터를 봅니다.

    ```scala
    hive.table("stream_table").show()
    ```

**Ctrl + C를** 사용하여 두 번째 SSH 세션에서 netcat을 중지합니다. 첫 `:q` 번째 SSH 세션에서 스파크 셸을 종료하는 데 사용합니다.

### <a name="securing-data-on-spark-esp-clusters"></a>스파크 ESP 클러스터의 데이터 보안

1. 다음 명령을 `demo` 입력하여 일부 샘플 데이터가 있는 테이블을 만듭니다.

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 다음 명령으로 테이블의 내용을 봅니다. 정책을 적용하기 전에 `demo` 테이블에 전체 열이 표시됩니다.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![레인저 정책을 적용하기 전에 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 열의 마지막 네 문자만 표시하는 열 마스킹 정책을 적용합니다.  
    1. 에서 `https://CLUSTERNAME.azurehdinsight.net/ranger/`레인저 관리자 UI로 이동합니다.
    1. 하이브 에서 클러스터에 대한 **하이브**서비스를 클릭합니다.
        ![레인저 서비스 매니저](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. **마스킹** 탭을 클릭한 다음 **새 정책 추가**

        ![하이브 창고 커넥터 레인저 하이브 정책 목록](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. 원하는 정책 이름을 제공합니다. 데이터베이스 선택: **기본**, 하이브 테이블: **데모,** 하이브 열: **이름,** 사용자: **rsadmin2,** 액세스 유형: **선택**및 부분 마스크: **마스킹 옵션 선택** 메뉴에서 **마지막 4를 표시합니다.** **추가**를 클릭합니다.
                ![정책 만들기](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 테이블의 내용을 다시 봅니다. 레인저 정책을 적용한 후 열의 마지막 네 문자만 볼 수 있습니다.

    ![레인저 정책을 적용한 후 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 대화형 쿼리 사용](./apache-interactive-query-get-started.md)
* [제플린, 리비, 스파크 제출 및 피스파크를 사용하여 하이브 창고 커넥터와 상호 작용하는 예](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
