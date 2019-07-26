---
title: Hive 웨어하우스 커넥터를 사용 하 여 Apache Spark 및 Apache Hive 통합
description: Azure HDInsight의 Hive 웨어하우스 커넥터와 Apache Spark 및 Apache Hive를 통합 하는 방법에 대해 알아봅니다.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: f3a0fa1ecdb2db94b43a5380f9497b4b1c266e47
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441937"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Hive 웨어하우스 커넥터를 사용 하 여 Apache Spark 및 Apache Hive 통합

HWC (Apache Hive 웨어하우스 커넥터)는 Spark 데이터 프레임와 Hive 테이블 간에 데이터를 이동 하는 등의 작업을 지원 하 고 Spark 스트리밍 데이터를 Hive 테이블로 전송 하는 등의 작업을 지원 하 여 Apache Spark 및 Apache Hive을 보다 쉽게 사용할 수 있도록 하는 라이브러리입니다. Hive 웨어하우스 커넥터는 Spark와 Hive 간의 브리지 처럼 작동 합니다. 개발을 위한 Scala, Java 및 Python을 지원 합니다.

Hive 웨어하우스 커넥터를 사용 하면 Hive 및 Spark의 고유한 기능을 활용 하 여 강력한 빅 데이터 응용 프로그램을 빌드할 수 있습니다. Apache Hive는 ACID (원자성, 일관성, 격리성 및 내구성) 인 데이터베이스 트랜잭션에 대 한 지원을 제공 합니다. Hive의 ACID 및 트랜잭션에 대 한 자세한 내용은 [Hive 트랜잭션](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)을 참조 하세요. 또한 Hive는 Apache Spark에서 사용할 수 없는 Apache 레인저 및 짧은 대기 시간 분석 처리를 통해 자세한 보안 제어를 제공 합니다.

Apache Spark에는 Apache Hive에서 사용할 수 없는 스트리밍 기능을 제공 하는 구조적 스트리밍 API가 있습니다. HDInsight 4.0부터 Apache Spark 2.3.1 및 Apache Hive 3.1.0에는 별도의 metastore 있으므로 상호 운용성이 어려워질 수 있습니다. Hive 웨어하우스 커넥터를 사용 하면 Spark와 Hive를 함께 더 쉽게 사용할 수 있습니다. HWC 라이브러리는 LLAP 디먼에서 Spark 실행 기에 병렬로 데이터를 로드 하 여 Spark에서 Hive로 표준 JDBC 연결을 사용 하는 것 보다 효율적이 고 확장 가능 합니다.

![아키텍처](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive 웨어하우스 커넥터에서 지원 되는 작업은 다음과 같습니다.

* 테이블 설명
* ORC 형식의 데이터에 대 한 테이블 만들기
* Hive 데이터 선택 및 데이터 프레임 검색
* 데이터 프레임을 Hive에 일괄 처리로 쓰기
* Hive update 문 실행
* Hive에서 테이블 데이터를 읽고 Spark에서 변환한 다음 새 Hive 테이블에 기록
* HiveStreaming를 사용 하 여 Hive에 데이터 프레임 또는 Spark 스트림 작성

## <a name="hive-warehouse-connector-setup"></a>Hive 웨어하우스 커넥터 설정

Azure HDInsight에서 Spark 및 대화형 쿼리 클러스터 간에 Hive 웨어하우스 커넥터를 설정 하려면 다음 단계를 수행 합니다.

1. 저장소 계정 및 사용자 지정 Azure virtual network와 함께 Azure Portal를 사용 하 여 HDInsight Spark 4.0 클러스터를 만듭니다. Azure 가상 네트워크에서 클러스터를 만드는 방법에 대 한 자세한 내용은 [기존 가상 네트워크에 HDInsight 추가](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)를 참조 하세요.
1. Spark 클러스터와 동일한 저장소 계정 및 Azure 가상 네트워크를 사용 하 여 Azure Portal를 사용 하 여 HDInsight 대화형 쿼리 (LLAP) 4.0 클러스터를 만듭니다.
1. 대화형 쿼리 클러스터의 headnode0 `/etc/hosts` 에 있는 파일의 내용을 Spark 클러스터의 headnode0 `/etc/hosts` 파일에 복사 합니다. 이 단계에서는 Spark 클러스터가 대화형 쿼리 클러스터 노드의 IP 주소를 확인할 수 있습니다. 을 사용 하 `cat /etc/hosts`여 업데이트 된 파일의 내용을 봅니다. 출력은 아래 스크린샷에 표시 된 것과 같아야 합니다.

    ![hosts 파일 보기](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. 다음 단계를 수행 하 여 Spark 클러스터 설정을 구성 합니다. 
    1. Azure Portal로 이동 하 여 HDInsight 클러스터를 선택 하 고 클러스터 이름을 클릭 합니다.
    1. 오른쪽의 **클러스터 대시보드**아래에서 **Ambari home**을 선택 합니다.
    1. Ambari 웹 UI에서 **SPARK2** > **CONFIGS** > **Custom SPARK2-defaults**를 클릭 합니다.

        ![Spark2 Ambari 구성](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. `spark.hadoop.hive.llap.daemon.service.hosts` **고급 hive-대화형-env**에서 **llap 앱 이름** 속성의 값과 동일한 값으로 설정 합니다. 예를 들면 `@llap0`

    1. 대화형 `spark.sql.hive.hiveserver2.jdbc.url` 쿼리 클러스터의 Hiveserver2에 연결 하는 JDBC 연결 문자열로 설정 합니다. 클러스터에 대 한 연결 문자열은 아래 URI 처럼 보입니다. `CLUSTERNAME`는 Spark 클러스터 `user` 의 이름이 고 및 `password` 매개 변수는 클러스터에 대 한 올바른 값으로 설정 됩니다.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > JDBC URL은 사용자 이름 및 암호를 포함 하 여 Hiveserver2에 연결 하기 위한 자격 증명을 포함 해야 합니다.

    1. 적절 `spark.datasource.hive.warehouse.load.staging.dir` 한 HDFS 호환 준비 디렉터리로 설정 합니다. 서로 다른 두 개의 클러스터가 있는 경우 스테이징 디렉터리는 HiveServer2에서 액세스할 수 있도록 LLAP 클러스터의 저장소 계정의 준비 디렉터리에 있는 폴더 여야 합니다. 예 `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` 를 들어 여기서 `STORAGE_ACCOUNT_NAME` 는 클러스터에서 사용 되는 저장소 계정의 이름이 고 `STORAGE_CONTAINER_NAME` 은 저장소 컨테이너의 이름입니다.

    1. 대화형 `spark.datasource.hive.warehouse.metastoreUri` 쿼리 클러스터의 metastore URI 값으로 설정 합니다. Llap 클러스터에 대 한 metastoreUri을 찾으려면 **hive** > **고급** > **일반**에서 llap 클러스터에 대 한 Ambari UI의 **metastore** 속성을 찾습니다. 값은 다음 URI와 유사 하 게 표시 됩니다.

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. YARN `spark.security.credentials.hiveserver2.enabled` client `false` 배포 모드의 경우로 설정 합니다.
    1. Llap 클러스터의 사육 사 쿼럼으로 설정 `spark.hadoop.hive.zookeeper.quorum` 합니다. Llap 클러스터에 대 한 사육 사 쿼럼을 찾으려면 **hive** > **고급** > **고급 hive 사이트**에서 llap 클러스터에 대 한 Ambari UI의 **hive** 속성을 찾습니다. 값은 다음 문자열과 유사 하 게 표시 됩니다.

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Hive 웨어하우스 커넥터의 구성을 테스트 하려면 [쿼리 연결 및 실행](#connecting-and-running-queries)섹션의 단계를 따르세요.

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

1. 클러스터에 대 한 헤드 노드로 SSH를 합니다. SSH를 사용 하 여 클러스터에 연결 하는 방법에 대 한 자세한 내용은 [ssh를 사용 하 여 HDInsight에 연결 (Apache Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조 하세요.
1. 를 입력 `cd /usr/hdp/current/hive_warehouse_connector` 하 여 올바른 디렉터리로 변경 하거나 spark-shell 명령에서 매개 변수로 사용 되는 모든 jar 파일의 전체 경로를 제공 합니다.
1. 다음 명령을 입력 하 여 spark 셸을 시작 합니다.

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. 명령을 입력할 수 있는 시작 메시지와 `scala>` 프롬프트가 표시 됩니다.

1. Spark-셸을 시작한 후 다음 명령을 사용 하 여 Hive 웨어하우스 커넥터 인스턴스를 시작할 수 있습니다.

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Enterprise Security Package (ESP) 클러스터에서 쿼리 연결 및 실행

Enterprise Security Package (ESP)는 Active Directory 기반 인증, 다중 사용자 지원 및 Azure HDInsight의 Apache Hadoop 클러스터에 대 한 역할 기반 액세스 제어와 같은 엔터프라이즈급 기능을 제공 합니다. ESP에 대 한 자세한 내용은 [HDInsight에서 Enterprise Security Package 사용](../domain-joined/apache-domain-joined-architecture.md)을 참조 하세요.

1. [쿼리 연결 및 실행](#connecting-and-running-queries)아래에서 1 단계와 2 단계를 수행 합니다.
1. 을 `kinit` 입력 하 고 도메인 사용자로 로그인 합니다.
1. 아래와 같이 구성 매개 변수의 전체 목록을 사용 하 여 spark-shell을 시작 합니다. 꺾쇠 괄호 사이의 모든 대문자에 있는 모든 값은 클러스터에 따라 지정 해야 합니다. 아래 매개 변수에 대 한 입력 값을 확인 해야 하는 경우 [Hive 웨어하우스 커넥터 설정](#hive-warehouse-connector-setup)에 대 한 섹션을 참조 하세요.

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
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

1. 이라는 `sampletable_colorado` 테이블을 만들고 다음 명령을 사용 하 여 해당 열을 지정 합니다.

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. 열 `hivesampletable` 이`state` 같은 테이블`Colorado`을 필터링 합니다. Hive 테이블의이 쿼리는 Spark 데이터 프레임 반환 됩니다. 그런 다음 데이터 프레임은 함수를 `sampletable_colorado` `write` 사용 하 여 Hive 테이블에 저장 됩니다.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

아래 스크린샷에서 결과 테이블을 볼 수 있습니다.

![결과 테이블 표시](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>구조적 스트리밍 쓰기

Hive 웨어하우스 커넥터를 사용 하 여 Spark 스트리밍을 사용 하 여 데이터를 Hive 테이블에 쓸 수 있습니다.

다음 단계를 수행 하 여 localhost 포트 9999의 Spark 스트림에서 데이터를 수집 하는 Hive 웨어하우스 커넥터 예제를 Hive 테이블에 만듭니다.

1. Spark 클러스터에서 터미널을 엽니다.
1. 다음 명령을 사용 하 여 spark 스트림을 시작 합니다.

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. 다음 단계를 수행 하 여 만든 Spark 스트림에 대 한 데이터를 생성 합니다.
    1. 동일한 Spark 클러스터에서 다른 터미널을 엽니다.
    1. 명령 프롬프트에서 `nc -lk 9999`를 입력합니다. 이 명령은 netcat 유틸리티를 사용 하 여 명령줄에서 지정 된 포트로 데이터를 보냅니다.
    1. Spark 스트림에 수집할 단어를 입력 하 고 그 뒤에 캐리지 리턴을 입력 합니다.
        ![spark 스트림에 대 한 입력 데이터](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. 스트리밍 데이터를 보관할 새 Hive 테이블을 만듭니다. Spark-셸에서 다음 명령을 입력 합니다.

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 다음 명령을 사용 하 여 새로 만든 테이블에 스트리밍 데이터를 씁니다.

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` 및`database` 옵션은 현재 Apache Spark의 알려진 문제로 인해 수동으로 설정 해야 합니다. 이 문제에 대 한 자세한 내용은 [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)을 참조 하세요.

1. 다음 명령을 사용 하 여 테이블에 삽입 된 데이터를 볼 수 있습니다.

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Spark ESP 클러스터에서 데이터 보호

1. 다음 명령을 입력 `demo` 하 여 몇 가지 샘플 데이터를 포함 하는 테이블을 만듭니다.

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 다음 명령을 사용 하 여 테이블의 내용을 봅니다. 이 `demo` 표에서는 정책을 적용 하기 전에 전체 열을 보여 줍니다.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![레인저 정책을 적용 하기 전의 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 열의 마지막 4 자만 표시 하는 열 마스킹 정책을 적용 합니다.  
    1. 에서 `https://CLUSTERNAME.azurehdinsight.net/ranger/`레인저 관리 UI로 이동 합니다.
    1. **Hive**에서 클러스터에 대 한 hive 서비스를 클릭 합니다.
        ![레인저 정책을 적용 하기 전의 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. **마스킹** 탭을 클릭 한 다음 **새 정책** ![정책 목록 추가를 클릭 합니다.](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. 원하는 정책 이름을 제공 합니다. 데이터베이스 선택: **기본**, hive 테이블: **demo**, hive 열: **이름**, 사용자: **Rsadmin2**, 액세스 형식: **선택**및 부분 마스크: 선택 **마스킹 옵션** 메뉴에서 **마지막 4 표시** . **추가**를 클릭합니다.
                ![정책 목록](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 테이블의 내용을 다시 봅니다. 레인저 정책을 적용 한 후에는 열의 마지막 4 자만 볼 수 있습니다.

    ![레인저 정책을 적용 한 후의 데모 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 대화형 쿼리 사용](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Zeppelin, Livy, spark-제출 및 pyspark를 사용 하 여 Hive 웨어하우스 커넥터와 상호 작용 하는 예](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
