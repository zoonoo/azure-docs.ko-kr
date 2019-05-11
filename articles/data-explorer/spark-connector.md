---
title: Azure Data Explorer 및 Spark 클러스터 간 데이터 이동 Apache Spark에 대 한 Azure 데이터 탐색기 커넥터를 사용 합니다.
description: 이 항목에서는 Azure Data Explorer 및 Apache Spark 클러스터 간에 데이터를 이동 하는 방법을 보여 줍니다.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 854e29b67b6e24c583a98b5851bf17551cfcbf61
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441356"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Apache Spark (미리 보기)에 대 한 azure 데이터 탐색기 커넥터

[Apache Spark](https://spark.apache.org/) 는 대규모 데이터 처리를 위한 통합 된 분석 엔진입니다. Azure 데이터 탐색기는 많은 양의 데이터에 대 한 실시간 분석에 대 한 빠르고 완전히 관리 되는 데이터 분석 서비스입니다. 

Spark에 대 한 azure 데이터 탐색기 커넥터는 데이터 원본 및 해당 기능을 모두 사용 하려면 Azure Data Explorer 및 Spark 클러스터 간에 데이터를 이동 하는 것에 대 한 데이터 싱크를 구현 합니다. Azure Data Explorer와 Apache Spark를 사용하면 ML(기계 학습), ETL(추출-변환-로드) 및 Log Analytics와 같은 데이터 기반 시나리오를 대상으로 빠르고 확장성 있는 애플리케이션을 구축할 수 있습니다. 일괄 처리와 스트리밍 모드에서 Azure 데이터 탐색기로 쓰기를 수행할 수 있습니다.
Azure 데이터 탐색기에서 읽는 열 잘라내기 및 Azure 데이터 탐색기에서 데이터를 필터링 하 여 전송 된 데이터의 볼륨을 줄일 수 있는 조건자 푸시 다운을 지원 합니다.

Azure 데이터 탐색기 Spark 커넥터는 [오픈 소스 프로젝트](https://github.com/Azure/azure-kusto-spark) 아무 Spark 클러스터에서 실행할 수 있는 합니다.

> [!NOTE]
> 아래 예제 중 일부를 참조 하지만 [Azure Databricks](https://docs.azuredatabricks.net/) Spark 클러스터를 Azure 데이터 탐색기 Spark 커넥터 Databricks 또는 다른 Spark 배포에 대 한 직접 종속성을 사용 하지 않습니다.

## <a name="prerequisites"></a>필수 조건

* [Azure Data Explorer 클러스터 및 데이터베이스 만들기](/azure/data-explorer/create-cluster-database-portal) 
* Spark 클러스터 만들기
* Azure Data Explorer 커넥터 라이브러리 및 라이브러리에 나열 된 설치 [종속성](https://github.com/Azure/azure-kusto-spark#dependencies) 다음을 비롯 한 [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) 라이브러리:
    * [Kusto 데이터 클라이언트](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto 수집 클라이언트](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* 미리 작성 된 라이브러리에 대 한 [Spark 2.4, Scala 2.11 버전](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Spark 커넥터를 빌드하는 방법

Spark 커넥터를 빌드할 수 있습니다 [원본](https://github.com/Azure/azure-kusto-spark) 아래 설명 된 대로 합니다.

> [!NOTE]
> 이 단계는 선택 사항입니다. 미리 빌드된 라이브러리를 사용 하는 경우로 이동 [Spark 클러스터 설정](#spark-cluster-setup)합니다.

### <a name="build-prerequisites"></a>필수 구성 요소 빌드

* Java 1.8 SDK 설치
* [Maven 3.x](https://maven.apache.org/download.cgi) 설치
* Apache Spark 버전 2.4.0 이상

> [!TIP]
> 2.3.x 버전 에서도 지원 됩니다 있지만 pom.xml 종속성의 일부 변경 해야 할 수 있습니다.

Maven 프로젝트 정의 사용 하는 Scala/Java 응용 프로그램 링크 다음 아티팩트를 사용 하 여 응용 프로그램에 대 한 (최신 버전 다를 수 있습니다).

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>빌드 명령

jar를 빌드하고 모든 테스트를 실행하려면:

```
mvn clean package
```

Jar을 빌드, 모든 테스트를 실행 하 여 로컬 Maven 리포지토리에 jar 설치:

```
mvn clean install
```

자세한 내용은 [커넥터 사용](https://github.com/Azure/azure-kusto-spark#usage)합니다.

## <a name="spark-cluster-setup"></a>Spark 클러스터 설정

> [!NOTE]
> 다음 단계를 수행 하는 경우 최신 Azure 데이터 탐색기 Spark 커넥터 릴리스를 사용 하는 것이 좋습니다.

1. 다음 Spark 클러스터 설정, Spark 2.4 및 Scala 2.11를 사용 하 여 Azure Databricks 클러스터에 따라 설정 합니다. 

    ![Databricks 클러스터 설정](media/spark-connector/databricks-cluster.png)

1. Azure Data Explorer 커넥터 라이브러리를 가져옵니다.

    ![Azure 데이터 탐색기 가져오기 라이브러리](media/spark-connector/db-create-library.png)

1. 추가 종속성을 추가 합니다.

    ![종속성 추가](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > 각 Spark 릴리스에 대 한 올바른 java 릴리스 버전이 [여기](https://github.com/Azure/azure-kusto-spark#dependencies)합니다.

1. 라이브러리가 설치 된 모든 필수 확인 합니다.

    ![설치 된 라이브러리를 확인 합니다.](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>인증

Azure 데이터 탐색기 Spark 커넥터를 사용 하면 Azure Active Directory (Azure AD)를 사용 하 여 인증할 수 있습니다는 [Azure AD 응용 프로그램](#azure-ad-application-authentication)하십시오 [Azure AD 액세스 토큰](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [장치 인증 ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (예: 비-프로덕션 시나리오의 경우) 또는 [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault)합니다. 사용자는 azure key vault 패키지를 설치 하 고 Key Vault 리소스에 액세스 하는 응용 프로그램 자격 증명을 제공 해야 합니다.

### <a name="azure-ad-application-authentication"></a>Azure AD 응용 프로그램 인증

대부분 간단 하 고 일반 인증 방법입니다. Azure 데이터 탐색기 Spark 커넥터 사용에 대 한이 메서드를 사용 하는 것이 좋습니다.

|properties  |설명  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD 응용 프로그램 (클라이언트) 식별자입니다.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 인증 기관입니다. Azure AD 디렉터리 (테 넌 트) id입니다.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    클라이언트에 대 한 azure AD 응용 프로그램 키입니다.     |

### <a name="azure-data-explorer-privileges"></a>Azure 데이터 탐색기 권한

Azure 데이터 탐색기 클러스터에서 다음 권한이 부여 되어야 합니다.

* (데이터 원본)를 읽고 Azure AD 응용 프로그램 있어야 *뷰어* 대상 데이터베이스에 대 한 권한이 나 *관리자* 대상 테이블에 대 한 권한이 합니다.
* Azure AD 응용 프로그램 작성 (데이터 싱크)에 있어야 *수집기* 대상 데이터베이스에 대 한 권한이 있습니다. 또한 있어야 *사용자* 새 테이블을 만들 대상 데이터베이스에 대 한 권한이 있습니다. 대상 테이블에 이미 있으면 *관리자* 대상 테이블에 대 한 권한을 구성할 수 있습니다.
 
Azure 데이터 탐색기 주 역할에 대 한 자세한 내용은 참조 하세요. [역할 기반 권한 부여](/azure/kusto/management/access-control/role-based-authorization)합니다. 보안 역할 관리를 참조 하세요 [보안 역할 관리](/azure/kusto/management/security-roles)합니다.

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark 싱크: Azure 데이터 탐색기로 작성

1. 싱크 매개 변수를 설정 합니다.

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Azure 데이터 탐색기 클러스터에 Spark DataFrame을 일괄 처리로 쓰기:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. 스트리밍 데이터 쓰기:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark 소스: Azure 데이터 탐색기에서 읽기

1. 적은 양의 데이터를 읽을 때 데이터 쿼리를 정의 합니다.

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. 많은 양의 데이터를 읽을 때 임시 blob 저장소를 제공 합니다. 저장소 컨테이너 SAS 키 또는 저장소 계정 이름, 계정 키 및 컨테이너 이름을 제공 합니다. 이 단계는만 Spark 커넥터의 현재 미리 보기 릴리스에 대 한 필요 합니다.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    위의 예에서 connector 인터페이스를 사용 하 여 Key Vault에 액세스 하지 것입니다. 또는 Databricks 암호를 사용 하는 보다 간단한 방법을 사용 합니다.

1. Azure 데이터 탐색기에서 읽기:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
