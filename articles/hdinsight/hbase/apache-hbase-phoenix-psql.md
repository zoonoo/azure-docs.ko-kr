---
title: psql을 사용하여 Apache Phoenix로 대량 로드 - Azure HDInsight
description: Psql 도구를 사용 하 여 Azure HDInsight에서 대량 로드 데이터를 Apache Phoenix 테이블로 로드
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552613"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>psql을 사용하여 Apache Phoenix로 데이터 대량 로드

[Apache Phoenix](https://phoenix.apache.org/)는 [Apache HBase](../hbase/apache-hbase-overview.md)에서 구축되는 오픈 소스 대규모 병렬 관계형 데이터베이스입니다. Phoenix는 HBase보다는 SQL과 비슷한 쿼리를 제공합니다. Phoenix는 JDBC 드라이버를 사용하여 사용자가 개별적으로 및 대량으로 SQL 테이블, 인덱스, 뷰 및 시퀀스를 생성, 삭제 및 변경하고, 행을 Upsert할 수 있도록 합니다. Phoenix는 MapReduce를 사용하여 쿼리를 컴파일하는 대신, noSQL 네이티브 컴파일을 사용하여 HBase 위에 대기 시간이 짧은 애플리케이션을 만듭니다. Phoenix는 서버의 주소 공간에서 클라이언트 제공 코드를 실행하도록 지원하는 보조 프로세서를 추가하고, 데이터와 함께 있는 코드를 실행합니다. 따라서 클라이언트/서버 데이터 전송이 최소화됩니다.  HDInsight에서 Phoenix를 사용하여 데이터로 작업하려면 먼저 테이블을 만든 다음, 데이터를 로드합니다.

## <a name="bulk-loading-with-apache-phoenix"></a>Apache Phoenix를 사용하여 대량 로드

클라이언트 API를 사용하거나, TableOutputFormat을 통해 MapReduce 작업을 수행하거나, HBase 셸을 통해 데이터를 수동으로 입력하는 경우를 비롯한 다양한 방법으로 데이터를 HBase로 가져올 수 있습니다. Phoenix는 CSV 데이터를 Phoenix 테이블로 로드하는 2가지 방법을 제공합니다. 하나는 `psql`이라는 클라이언트 로드 도구를 사용하는 것이고, 다른 하나는 MapReduce 기반 대량 로드 도구를 사용하는 것입니다.

`psql` 도구는 단일 스레드 도구로, 메가바이트 또는 기가바이트 단위의 데이터를 로드하는 데 가장 적합합니다. 로드할 모든 CSV 파일의 확장명은 '.csv'여야 합니다.  '.sql' 파일 확장명을 사용하여 `psql` 명령줄에 SQL 스크립트 파일을 지정할 수도 있습니다.

MapReduce는 여러 스레드를 사용하므로, 일반적으로 프로덕션 시나리오에서 훨씬 많은 양의 데이터를 대량으로 로드하는 데 사용됩니다.

데이터 로드를 시작하기 전에, Phoenix가 사용되도록 설정되어 있는지와 쿼리 제한 시간 설정이 예상대로 지정되어 있는지 확인합니다.  HDInsight 클러스터 [Apache Ambari](https://ambari.apache.org/) 대시보드에 액세스 하 여 HBase를 선택 하 고 구성 탭을 선택 합니다.  아래로 스크롤하여 Apache Phoenix이 다음과 같이로 설정 되었는지 확인 합니다 `enabled` .

![Apache Phoenix HDInsight 클러스터 설정](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>`psql`을 사용하여 테이블 대량 로드

1. 이라는 파일을 만들고 `createCustomersTable.sql` 아래 코드를 파일에 복사 합니다. 그런 다음 파일을 저장하고 닫습니다.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. 이라는 파일을 만들고 `listCustomers.sql` 아래 코드를 파일에 복사 합니다. 그런 다음 파일을 저장하고 닫습니다.

    ```sql
    SELECT * from Customers;
    ```

1. 이라는 파일을 만들고 `customers.csv` 아래 코드를 파일에 복사 합니다. 그런 다음 파일을 저장하고 닫습니다.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. 이라는 파일을 만들고 `customers2.csv` 아래 코드를 파일에 복사 합니다. 그런 다음 파일을 저장하고 닫습니다.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. 명령 프롬프트를 열고 디렉터리를 새로 만든 파일의 위치로 변경 합니다. 아래의 CLUSTERNAME을 HBase 클러스터의 실제 이름으로 바꿉니다. 그런 다음 코드를 실행 하 여 클러스터의 헤드 노드에 파일을 업로드 합니다.

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ssh 세션에서 **psql** 도구의 위치로 디렉터리를 변경 합니다. 아래 명령을 실행 합니다.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. 데이터를 대량 로드 합니다. 아래 코드에서는 **Customers** 테이블을 만들고 데이터를 업로드 합니다.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    `psql`작업이 완료 되 면 다음과 유사한 메시지가 표시 됩니다.

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. 계속 사용 하 여 `psql` Customers 테이블의 내용을 볼 수 있습니다. 아래 코드를 실행합니다.

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    또는 [HBase shell](./query-hbase-with-hbase-shell.md)또는 [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) 를 사용 하 여 데이터를 쿼리할 수 있습니다.

1. 추가 데이터를 업로드 합니다. 이제 테이블이 이미 있으므로 명령은 테이블을 지정 합니다. 아래 명령을 실행 합니다.

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce를 사용하여 테이블 대량 로드

더 높은 처리량의 부하가 클러스터에 분산되도록 하려면 MapReduce 로드 도구를 사용합니다. 이 로더는 먼저 모든 데이터를 HFiles로 변환하고 만든 HFiles를 HBase에 제공합니다.

1. 이 섹션에서는 ssh 세션과 이전에 만든 개체를 계속 사용 합니다. 위의 단계를 사용 하 여 필요에 따라 **Customers** 테이블 및 **customers.csv** 파일을 만듭니다. 필요한 경우 ssh 연결을 다시 설정 합니다.

1. **Customers** 테이블의 내용을 자릅니다. 열려 있는 ssh 세션에서 아래 명령을 실행 합니다.

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. `customers.csv`헤드 노드에서 Azure Storage으로 파일을 복사 합니다.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. MapReduce 대량 로드 명령에 대한 실행 디렉터리로 변경합니다.

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Phoenix 클라이언트 jar에 `hadoop` 명령을 사용하여 CSV MapReduce 로더를 시작합니다.

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    업로드가 완료 되 면 다음과 유사한 메시지가 표시 됩니다.

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Azure Data Lake Storage에서 MapReduce를 사용하려면 Data Lake Storage 루트 디렉터리(`hbase-site.xml`의 `hbase.rootdir` 값)를 찾습니다. 다음 명령에서 Data Lake Storage 루트 디렉터리는 `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`입니다. 이 명령에서 다음과 같이 Data Lake Storage 입력 및 출력 폴더를 매개 변수로 지정합니다.

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. 데이터를 쿼리하고 보려면 앞에서 설명한 대로 **psql** 을 사용할 수 있습니다. [HBase shell](./query-hbase-with-hbase-shell.md)또는 [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md)을 사용할 수도 있습니다.

## <a name="recommendations"></a>권장 사항

* 입력 및 출력 폴더에 동일한 스토리지 미디어(Azure Storage(WASB) 또는 Azure Data Lake Storage(ADL))를 사용합니다. 데이터를 Azure Storage에서 Data Lake Storage로 전송하려면 `distcp` 명령을 사용합니다.

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* 더 큰 크기의 작업자 노드를 사용합니다. MapReduce 대량 복사의 매핑 프로세스는 사용 가능한 비 DFS 공간을 채우는 대량의 임시 출력을 생성합니다. 많은 양의 데이터에 대한 대량 로드를 위해 더 큰 작업자 노드를 더 많이 사용합니다. 클러스터에 직접 할당하는 작업자 노드 수는 처리 속도를 영향을 줍니다.

* 입력 파일을 ~10GB 청크로 분할합니다. 대량 로드는 스토리지 집약적 작업이므로, 입력 파일을 여러 개의 청크로 분할하면 성능이 더 좋아집니다.

* 지역 서버 핫스폿을 방지합니다. 행 키가 단조롭게 증가하는 경우 HBase 순차 쓰기로 인해 지역 서버 핫스폿이 발생할 수 있습니다. 행 키를 *솔트*하면 순차 쓰기가 줄어듭니다. Phoenix는 아래에 참조된 것처럼, 특정 테이블에 대해 솔트 바이트로 행 키를 투명하게 솔트하는 방법을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Apache Phoenix로 데이터 대량 로드](https://phoenix.apache.org/bulk_dataload.html)
* [HDInsight에서 Linux 기반 Apache HBase 클러스터와 함께 Apache Phoenix 사용](../hbase/apache-hbase-query-with-phoenix.md)
* [솔트된 테이블](https://phoenix.apache.org/salted.html)
* [Apache Phoenix 문법](https://phoenix.apache.org/language/index.html)
