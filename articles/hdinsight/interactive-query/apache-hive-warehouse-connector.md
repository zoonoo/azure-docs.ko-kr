---
title: Apache Spark 및 Hive - Hive Warehouse Connector - Azure HDInsight
description: Azure HDInsight의 Hive Warehouse Connector와 Apache Spark 및 Apache Hive를 통합하는 방법을 알아봅니다.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: e9438e2e82a6d903b74973fe489b0a67d66c9a72
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84296955"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight의 Hive Warehouse Connector와 Apache Spark 및 Apache Hive 통합

Apache HWC(Hive Warehouse Connector)는 Apache Spark 및 Apache Hive에서 보다 쉽게 작업할 수 있도록 지원하는 라이브러리입니다. Spark DataFrames와 Hive 테이블 간에 데이터를 이동하는 것과 작업을 지원합니다. 또한 Spark 스트리밍 데이터를 Hive 테이블로 전송합니다. Hive Warehouse Connector는 Spark와 Hive 간의 브리지처럼 작동합니다. 또한 개발을 위한 프로그래밍 언어로 Scala, Java 및 Python을 지원합니다.

Hive Warehouse Connector를 사용하면 Hive 및 Spark의 고유한 기능을 활용하여 강력한 빅 데이터 애플리케이션을 빌드할 수 있습니다.

Apache Hive는 ACID(원자성, 일관성, 격리성 및 내구성)에 해당하는 데이터베이스 트랜잭션을 지원합니다. Hive의 ACID 및 트랜잭션에 대한 자세한 내용은 [Hive 트랜잭션](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)을 참조하세요. 또한 Hive는 Apache Spark에서 사용할 수 없는 Apache Ranger 및 LLAP(Low Latency Analytical Processing)를 통해 자세한 보안 제어를 제공합니다.

Apache Spark에는 Apache Hive에서 사용할 수 없는 스트리밍 기능을 제공하는 구조적 스트리밍 API가 있습니다. HDInsight 4.0부터 Apache Spark 2.3.1 및 Apache Hive 3.1.0에는 별도의 metastore가 있습니다. 별도의 metastore는 상호 운용성을 어렵게 만들 수 있습니다. Hive Warehouse Connector를 사용하면 좀 더 쉽게 Spark와 Hive를 함께 사용할 수 있습니다. HWC 라이브러리는 LLAP 디먼에서 Spark 실행기로 데이터를 병렬로 로드합니다. 이 프로세스를 통해 Spark에서 Hive로의 표준 JDBC 연결보다 좀 더 효율적이고 편리하게 연결될 수 있습니다.

![Hive Warehouse Connector 아키텍처](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive Warehouse Connector에서 지원되는 작업은 다음과 같습니다.

* 테이블 설명
* ORC 형식의 데이터에 대한 테이블 만들기
* Hive 데이터 선택 및 DataFrame 검색
* DataFrame을 Hive에 일괄로 쓰기
* Hive update 문 실행
* Hive에서 테이블 데이터를 읽고 Spark에서 변환한 다음, 새 Hive 테이블에 쓰기
* HiveStreaming을 사용하여 Hive에 DataFrame 또는 Spark 스트림 쓰기

## <a name="hive-warehouse-connector-setup"></a>Hive Warehouse Connector 설정

Hive Warehouse Connector에는 Spark 및 Interactive Query 워크로드를 위한 별도의 클러스터가 필요합니다. Azure HDInsight에서 이러한 클러스터를 설정하려면 다음 단계를 수행합니다.

### <a name="create-clusters"></a>클러스터 만들기

1. 스토리지 계정 및 사용자 지정 Azure Virtual Network를 사용하여 HDInsight Spark **4.0** 클러스터를 만듭니다. Azure Virtual Network에서 클러스터를 만드는 방법에 대한 자세한 내용은 [기존 가상 네트워크에 HDInsight 추가](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)를 참조하세요.

1. Spark 클러스터와 동일한 스토리지 계정 및 Azure Virtual Network를 사용하HDInsight Interactive Query(LLAP) **4.0** 클러스터를 만듭니다.

### <a name="configure-hwc-settings"></a>HWC 설정 구성

#### <a name="gather-preliminary-information"></a>예비 정보 수집

1. 웹 브라우저에서 `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE`로 이동합니다. 여기서 LLAPCLUSTERNAME은 Interactive Query 클러스터의 이름입니다.

1. **요약** > **HiveServer2 Interactive JDBC URL**로 이동하고 값을 적어 둡니다. 값은 `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive`과 유사할 수 있습니다.

1. **구성** > **고급** > **고급 hive-site** > **hive.zookeeper.quorum**으로 이동한 후 값을 적어 둡니다. 값은 `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`과 유사할 수 있습니다.

1. **구성** > **고급** > **일반** > **hive.metastore.uris**로 이동한 후 값을 적어 둡니다. 값은 `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`과 유사할 수 있습니다.

1. **구성** > **고급** > **고급 hive-interactive-site** > **hive.llap.daemon.service.hosts**로 이동한 후 값을 적어 둡니다. 값은 `@llap0`과 유사할 수 있습니다.

#### <a name="configure-spark-cluster-settings"></a>Spark 클러스터 설정 구성

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`로 이동합니다. 여기서 CLUSTERNAME은 Apache Spark 클러스터의 이름입니다.

1. **사용자 지정 spark2-defaults**를 확장합니다.

    ![Apache Ambari Spark2 구성](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

1. **속성 추가...** 를 선택하여 다음 구성을 추가합니다.

    | 구성 | 값 |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`입니다. <br> 적절한 HDFS 호환 스테이징 디렉터리로 설정합니다. 다른 두 개의 클러스터가 있는 경우 스테이징 디렉터리는 HiveServer2에서 액세스할 수 있도록 LLAP 클러스터 스토리지 계정의 스테이징 디렉터리에 있는 폴더여야 합니다.  `STORAGE_ACCOUNT_NAME`을 클러스터에서 사용하는 스토리지 계정의 이름으로 바꾸고 `STORAGE_CONTAINER_NAME`을 스토리지 컨테이너의 이름으로 바꿉니다. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| **HiveServer2 Interactive JDBC URL**에서 이전에 가져온 값입니다. |
    |`spark.datasource.hive.warehouse.metastoreUri`| 이전에 **hive.metastore.uris**에서 가져온 값입니다. |
    |`spark.security.credentials.hiveserver2.enabled`|YARN 클러스터 모드의 경우 `true`, YARN 클라이언트 모드의 경우 `false`입니다. |
    |`spark.hadoop.hive.zookeeper.quorum`| 이전에 **hive.zookeeper.quorum**에서 가져온 값입니다. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| 이전에 **hive.llap.daemon.service.hosts**에서 가져온 값입니다. |

1. 변경 내용을 저장하고 영향을 받는 모든 구성 요소를 다시 시작합니다.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>ESP(Enterprise Security Package) 클러스터에 대한 HWC 구성

ESP(Enterprise Security Package)는 Azure HDInsight의 Apache Hadoop 클러스터에 대한 Active Directory 기반 인증, 다중 사용자 지원 및 역할 기반 액세스 제어와 같은 엔터프라이즈급 기능을 제공합니다. ESP에 대한 자세한 내용은 [HDInsight에서 Enterprise Security Package 사용](../domain-joined/apache-domain-joined-architecture.md)을 참조하세요.

이전 섹션에서 설명한 구성과는 별도로 다음 구성을 추가하여 ESP 클러스터에서 HWC를 사용합니다.

1. Spark 클러스터의 Ambari 웹 UI에서 **Spark2** > **구성** > **사용자 지정 Spark2-defaults**로 이동합니다.

1. 다음 속성을 업데이트합니다.

    | 구성 | 값 |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * 웹 브라우저에서로 이동 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` 합니다. 여기서 CLUSTERNAME은 대화형 쿼리 클러스터의 이름입니다. **HiveServer2 Interactive**를 클릭 합니다. 스크린샷에 표시 된 것 처럼 LLAP이 실행 되는 헤드 노드의 FQDN (정규화 된 도메인 이름)이 표시 됩니다. `<llap-headnode>`이 값으로 대체 합니다.

        ![hive 웨어하우스 커넥터 헤드 노드](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * [Ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용 하 여 대화형 쿼리 클러스터에 연결 합니다. `default_realm`파일에서 매개 변수를 찾습니다 `/etc/krb5.conf` . `<AAD-DOMAIN>`이 값을 대문자 문자열로 바꾸고, 그렇지 않으면 자격 증명을 찾을 수 없습니다.

        ![hive 웨어하우스 커넥터 AAD 도메인](./media/apache-hive-warehouse-connector/aad-domain.png)

    * 예를 들면 `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` 입니다.
    
1. 변경 내용을 저장하고 필요하면 구성 요소를 다시 시작합니다.

## <a name="hive-warehouse-connector-usage"></a>Hive Warehouse Connector 사용

몇 가지 방법 중 하나를 선택하여 Interactive Query 클러스터에 연결하고 Hive Warehouse Connector를 사용하여 쿼리를 실행할 수 있습니다. 지원되는 방법에는 다음 도구가 포함됩니다.

* [Spark-shell / PySpark](../spark/apache-spark-shell.md)
* [Spark-submit](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Spark에서 HWC에 연결하는 몇 가지 예제는 다음과 같습니다.

### <a name="spark-shell"></a>Spark-shell

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 Apache Spark 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. ssh 세션에서 다음 명령을 실행하여 `hive-warehouse-connector-assembly` 버전을 확인합니다.

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. 위에서 식별된 `hive-warehouse-connector-assembly` 버전을 사용하여 아래 코드를 편집합니다. 그런 다음 명령을 실행하여 Spark 셸을 시작합니다.

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Spark 셸을 시작한 뒤 다음 명령을 사용하여 Hive Warehouse Connector 인스턴스를 시작할 수 있습니다.

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-submit

어셈블리 jar에 대한 종속성에 따라 scala/java 코드를 빌드한 후 아래 명령을 사용하여 Spark 애플리케이션을 시작합니다. `<VERSION>` 및 `<APP_JAR_PATH>`를 실제 값으로 바꿉니다.

* YARN 클라이언트 모드
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* YARN 클러스터 모드
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

Python의 경우 다음 구성도 추가합니다. 

    ```python
    --py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
    ```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>ESP(Enterprise Security Package) 클러스터에 대해 쿼리 실행

spark-shell 또는 spark-submit을 시작하기 전에 `kinit`를 사용합니다. USERNAME을 클러스터에 액세스할 수 있는 권한이 있는 도메인 계정의 이름으로 바꾸고 다음 명령을 실행합니다.

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Spark ESP 클러스터에서 데이터 보호

1. 다음 명령을 입력하여 몇 가지 샘플 데이터를 포함하는 테이블 `demo`를 만듭니다.

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 다음 명령을 사용하여 테이블의 내용을 봅니다. 정책을 적용하기 전에 `demo` 테이블은 전체 열을 표시합니다.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![Ranger 정책을 적용하기 전의 demo 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 열의 마지막 4자만 표시하는 열 마스킹 정책을 적용합니다.  
    1. `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/`에서 Ranger 관리 UI로 이동합니다.
    1. **Hive**에서 클러스터에 대한 Hive 서비스를 클릭합니다.
        ![Ranger Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. **마스킹** 탭을 클릭하고 **새 정책 추가**를 클릭합니다.

        ![hive warehouse connector ranger hive 정책 목록](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    1. 원하는 정책 이름을 제공합니다. 데이터베이스를 선택합니다. **마스킹 옵션 선택** 메뉴에서 **기본값**, Hive 테이블: **demo**, Hive 열: **name**, 사용자: **rsadmin2**, 액세스 형식: **select** 및 **부분 마스크: 마지막 4 표시**를 선택합니다. **추가**를 클릭합니다.
                ![정책 만들기](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 테이블의 내용을 다시 봅니다. Ranger 정책을 적용한 후에는 열의 마지막 4자만 볼 수 있습니다.

    ![Ranger 정책을 적용한 후의 demo 테이블](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>다음 단계

* [HWC 및 Apache Spark 작업](./apache-hive-warehouse-connector-operations.md)
* [HDInsight에서 대화형 쿼리 사용](./apache-interactive-query-get-started.md)
* [Apache Zeppelin와 HWC 통합](./apache-hive-warehouse-connector-zeppelin.md)
* [Zeppelin, Livy, spark-submit 및 pyspark를 사용하여 Hive Warehouse Connector와의 상호 작용 예제](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)