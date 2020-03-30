---
title: Spark를 사용하여 HBase 데이터 읽기 및 쓰기 - Azure HDInsight
description: Spark HBase 커넥터를 사용하여 Spark 클러스터에서 HBase 클러스터로 데이터를 읽고 씁니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/24/2020
ms.openlocfilehash: 888f24e13ce67c878592068927383dd8cbfefa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623098"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Apache Spark를 사용하여 Apache HBase 데이터 읽기 및 쓰기

Apache HBase는 일반적으로 낮은 수준의 API(scans, gets, puts) 또는 Apache Phoenix를 사용하는 SQL 구문을 사용하여 쿼리됩니다. 또한 Apache는 Apache Spark HBase 커넥터를 제공하며 이 커넥터는 HBase에서 저장한 데이터를 쿼리하고 수정하는 대신 편리하고 효율적인 대안입니다.

## <a name="prerequisites"></a>사전 요구 사항

* 동일한 [가상 네트워크에](./hdinsight-plan-virtual-network-deployment.md)배포된 두 개의 별도 HDInsight 클러스터. 하나의 HBase, 그리고 적어도 스파크 와 하나의 스파크 2.1 (HDInsight 3.6) 설치. 자세한 내용은 [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.

* SSH 클라이언트. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* 클러스터 기본 스토리지에 대한 [URI 체계](hdinsight-hadoop-linux-information.md#URI-and-scheme)입니다. 이 체계는 Azure Blob 저장소, azure 데이터 호수 저장소 세대2에 대 한 abfs:// 또는 Azure 데이터 호수 저장소 Gen1에 대 한 adl:// 대 한 wasb:// 합니다. Blob Storage에 대해 보안 전송을 사용하도록 `wasbs://`설정하면 URI가 됩니다.  [보안 전송](../storage/common/storage-require-secure-transfer.md)도 참조하세요.

## <a name="overall-process"></a>전체 프로세스

Spark 클러스터가 HDInsight 클러스터를 쿼리할 수 ​​있도록 하는 고급 프로세스는 다음과 같습니다.

1. HBase에서 일부 샘플 데이터를 준비합니다.
2. HBase 클러스터 구성 폴더(/etc/hbase/conf)에서 hbase-site.xml 파일을 얻습니다.
3. Spark 2 구성 폴더(/etc/spark2/conf)에 hbase-site.xml 사본을 배치합니다.
4. `packages` 옵션의 Maven 좌표를 기준으로 Spark HBase 커넥터를 참조하는 `spark-shell`을 실행합니다.
5. Spark에서 HBase로 스키마를 매핑하는 카탈로그를 정의합니다.
6. RDD 또는 데이터 프레임 API를 사용하여 HBase 데이터와 상호 작용합니다.

## <a name="prepare-sample-data-in-apache-hbase"></a>Apache HBase에서 샘플 데이터 준비

이 단계에서는 Spark를 사용하여 쿼리할 수 있는 Apache HBase에서 테이블을 만들고 채웁니다.

1. `ssh` 명령을 사용하여 HBase 클러스터에 연결합니다. HBase 클러스터의 이름으로 `HBASECLUSTER` 바꿉니다 아래 명령을 편집한 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. `hbase shell` 명령을 사용하여 HBase 대화형 셸을 시작합니다. SSH 연결에서 다음 명령을 입력합니다.

    ```bash
    hbase shell
    ```

3. `create` 명령을 사용하여 두 개의 열 패밀리가 있는 HBase 테이블을 만듭니다. 다음 명령을 입력합니다.

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. 명령을 `put` 사용하여 특정 테이블의 지정된 행에서 지정된 열에 값을 삽입합니다. 다음 명령을 입력합니다.

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. `exit` 명령을 사용하여 HBase 대화형 셸을 중지합니다. 다음 명령을 입력합니다.

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>hbase-site.xml을 스파크 클러스터로 복사

로컬 저장소에서 Spark 클러스터의 기본 저장소루트로 hbase-site.xml을 복사합니다.  구성을 반영하도록 아래 명령을 편집합니다.  그런 다음 열린 SSH 세션에서 HBase 클러스터로 명령을 입력합니다.

| 구문 값 | 새 값|
|---|---|
|[URI 체계](hdinsight-hadoop-linux-information.md#URI-and-scheme) | 저장소를 반영하도록 수정합니다.  아래 구문은 보안 전송이 활성화된 Blob 저장소에 대한 것입니다.|
|`SPARK_STORAGE_CONTAINER`|Spark 클러스터에 사용되는 기본 저장소 컨테이너 이름으로 바꿉니다.|
|`SPARK_STORAGE_ACCOUNT`|Spark 클러스터에 사용되는 기본 저장소 계정 이름으로 바꿉니다.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

그런 다음 HBase 클러스터에 대한 ssh 연결을 종료합니다.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Spark 클러스터에 hbase-site.xml 배치

1. SSH를 사용하여 Spark 클러스터의 헤드 노드에 연결합니다. Spark 클러스터의 이름으로 `SPARKCLUSTER` 바꿉니다 아래 명령을 편집한 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. 아래 명령을 입력하여 `hbase-site.xml` Spark 클러스터의 기본 저장소에서 클러스터의 로컬 저장소의 Spark 2 구성 폴더로 복사합니다.

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark HBase 커넥터를 참조하는 Spark 셸 실행

1. 열려 있는 SSH 세션에서 스파크 클러스터까지 아래 명령을 입력하여 스파크 셸을 시작합니다.

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. 이 Spark 셸 인스턴스를 열린 상태로 유지하고 다음 단계를 계속합니다.

## <a name="define-a-catalog-and-query"></a>카탈로그 및 쿼리 정의

이 단계에서는 Apache Spark에서 Apache HBase로 스키마를 매핑하는 카탈로그 개체를 정의합니다.  

1. 열린 스파크 셸에서 다음 `import` 문을 입력합니다.

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. 아래 명령을 입력하여 HBase에서 만든 연락처 테이블에 대한 카탈로그를 정의합니다.

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    코드는 다음을 수행합니다.  

     a. 이름이 `Contacts`인 HBase 테이블에 대한 카탈로그 스키마를 정의합니다.  
     b. rowkey를 `key`로 식별하고 Spark에서 사용된 열 이름을 HBase에서 사용되는 열 패밀리, 열 이름 및 열 유형으로 매핑합니다.  
     다. 또한 rowkey는 `rowkey`의 특정 열 패밀리 `cf`가 있는 명명된 열(`rowkey`)로 자세하게 정의되어야 합니다.  

1. 아래 명령을 입력하여 HBase의 테이블 주위에 `Contacts` DataFrame을 제공하는 메서드를 정의합니다.

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. 데이터 프레임의 인스턴스를 만듭니다.

    ```scala
    val df = withCatalog(catalog)
    ```  

1. 데이터 프레임을 쿼리합니다.

    ```scala
    df.show()
    ```

    두 개의 데이터 행이 표시되어야 합니다.

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Spark SQL을 사용하여 HBase 테이블을 쿼리할 수 있도록 임시 테이블을 등록합니다.

    ```scala
    df.createTempView("contacts")
    ```

1. `contacts` 테이블에 대해 SQL 쿼리를 실행합니다.

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    다음과 같은 결과가 표시되어야 합니다.

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>새 데이터 삽입

1. 새 Contact 레코드를 삽입하려면 `ContactRecord` 클래스를 정의합니다.

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. `ContactRecord` 인스턴스를 만들어 배열에 넣습니다.

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. 새 데이터의 배열을 HBase에 저장합니다.

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. 결과를 검사합니다.

    ```scala  
    df.show()
    ```

    다음과 유사한 출력이 표시됩니다.

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. 다음 명령을 입력하여 스파크 셸을 닫습니다.

    ```scala
    :q
    ```

## <a name="next-steps"></a>다음 단계

* [Apache Spark HBase 커넥터](https://github.com/hortonworks-spark/shc)
