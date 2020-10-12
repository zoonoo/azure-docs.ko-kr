---
title: Microsoft Azure SQL 및 SQL Server에서 Spark 커넥터 사용
description: Azure SQL Database, Azure SQL Managed Instance 및 SQL Server에서 Spark 커넥터를 사용 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 36010ff0206ddf9dae08391eb6e4c3dd7762cc10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319336"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Spark 커넥터를 사용 하 여 실시간 빅 데이터 분석 가속화
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 9 월 2020 현재이 커넥터는 적극적으로 유지 관리 되지 않습니다. 그러나 이제는 [SQL Server 및 AZURE SQL 용 Apache Spark 커넥터](https://docs.microsoft.com/sql/connect/spark/connector) 를 사용할 수 있으며, Python 및 R 바인딩을 지원 하 고, 사용 하기 쉬운 인터페이스를 사용 하 여 데이터를 대량으로 삽입 하 고, 기타 여러 가지 기능을 향상 시킬 수 있습니다. 이 대신 새 커넥터를 평가 하 고 사용 하는 것이 좋습니다. 이전 커넥터 (이 페이지)에 대 한 정보는 보관 목적 으로만 유지 됩니다.

Spark 커넥터를 사용 하면 Azure SQL Database, Azure SQL Managed Instance 및 SQL Server의 데이터베이스가 Spark 작업에 대 한 입력 데이터 원본 또는 출력 데이터 싱크로 작동할 수 있습니다. 빅 데이터 분석에서 실시간 트랜잭션 데이터를 활용 하 고 임시 쿼리 또는 보고에 대 한 결과를 유지할 수 있습니다. 기본 제공 JDBC 커넥터와 비교할 때이 커넥터는 데이터베이스에 데이터를 대량으로 삽입 하는 기능을 제공 합니다. 10 배를 사용 하 여 행 단위 삽입을 내지만 더 빠른 성능을 20x 수 있습니다. Spark 커넥터 Azure Active Directory (Azure AD) 인증을 지원 하 여 azure AD 계정을 사용 하 여 Azure Databricks에서 데이터베이스를 연결할 수 있도록 Azure SQL Database 및 Azure SQL Managed Instance에 연결 합니다. 기본 제공 JDBC 커넥터와 유사한 인터페이스를 제공합니다. 기존의 Spark 작업을 이 새로운 커넥터를 사용하도록 쉽게 마이그레이션할 수 있습니다.

## <a name="download-and-build-a-spark-connector"></a>Spark 커넥터 다운로드 및 빌드

이전에이 페이지에서 연결 했던 이전 커넥터의 GitHub 리포지토리는 적극적으로 유지 관리 되지 않습니다. 대신 [새 커넥터](https://github.com/microsoft/sql-spark-connector)를 평가 하 고 사용 하는 것이 좋습니다.

### <a name="official-supported-versions"></a>지원 되는 공식 버전

| 구성 요소                             | 버전                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 이상           |
| 스칼라                                 | 2.10 이상            |
| SQL Server용 Microsoft JDBC Driver  | 6.2 이상             |
| Microsoft SQL Server                  | SQL Server 2008 이상 |
| Azure SQL Database                    | 지원됨                |
| Azure SQL Managed Instance            | 지원됨                |

Spark 커넥터는 SQL Server 용 Microsoft JDBC Driver를 활용 하 여 Spark 작업자 노드와 데이터베이스 간에 데이터를 이동 합니다.

데이터 흐름은 다음과 같습니다.

1. Spark 마스터 노드는 SQL Database 또는 SQL Server의 데이터베이스에 연결 하 고 특정 테이블 또는 특정 SQL 쿼리를 사용 하 여 데이터를 로드 합니다.
2. Spark 마스터 노드는 변환을 위해 데이터를 작업자 노드에 배포합니다.
3. 작업자 노드는 SQL Server SQL Database 연결 되는 데이터베이스에 연결 하 고 데이터베이스에 데이터를 씁니다. 사용자는 행 단위 삽입 또는 대량 삽입을 사용할지 선택할 수 있습니다.

아래 다이어그램은 데이터 흐름을 보여 줍니다.

   ![다이어그램에서는 마스터 노드가 데이터베이스에 직접 연결 하 고 세 개의 작업자 노드에 연결 하 여 데이터베이스에 연결 하는 설명 된 흐름을 보여 줍니다.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>Spark 커넥터 빌드

현재 커넥터 프로젝트는 maven을 사용합니다. 종속성 없이 커넥터를 빌드하려면 다음을 실행할 수 있습니다.

- mvn clean package
- 릴리스 폴더에서 최신 버전의 JAR 다운로드
- SQL Database Spark JAR 포함

## <a name="connect-and-read-data-using-the-spark-connector"></a>Spark 커넥터를 사용 하 여 데이터 연결 및 읽기

SQL Database의 데이터베이스에 연결 하 고 Spark 작업에서 SQL Server 하 여 데이터를 읽거나 쓸 수 있습니다. SQL Database 및 SQL Server의 데이터베이스에서 DML 또는 DDL 쿼리를 실행할 수도 있습니다.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Azure SQL 및 SQL Server에서 데이터 읽기

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>지정 된 SQL 쿼리를 사용 하 여 Azure SQL 및 SQL Server에서 데이터 읽기

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>Azure SQL에 데이터 쓰기 및 SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>Azure SQL에서 DML 또는 DDL 쿼리를 실행 하 고 SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Azure AD 인증을 사용 하 여 Spark에서 연결

Azure AD 인증을 사용 하 여 Azure SQL Database 및 SQL Managed Instance에 연결할 수 있습니다. 중앙에서 데이터베이스 사용자의 ID를 관리할 수 있는 Azure AD 인증은 SQL Server 인증 대신으로도 사용할 수 있습니다.

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>ActiveDirectoryPassword 인증 모드를 사용하여 연결

#### <a name="setup-requirement"></a>설치 요구 사항

ActiveDirectoryPassword 인증 모드를 사용하는 경우 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 및 해당 종속성을 다운로드하여 Java 빌드 경로에 포함시켜야 합니다.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-an-access-token"></a>액세스 토큰을 사용 하 여 연결

#### <a name="setup-requirement"></a>설치 요구 사항

액세스 토큰 기반 인증 모드를 사용하는 경우 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 및 해당 종속성을 다운로드하여 Java 빌드 경로에 포함시켜야 합니다.

Azure SQL Database 또는 Azure SQL Managed Instance에서 데이터베이스에 대 한 액세스 토큰을 가져오는 방법을 알아보려면 [인증에 Azure Active Directory 인증 사용](authentication-aad-overview.md) 을 참조 하세요.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-using-bulk-insert"></a>Bulk insert를 사용 하 여 데이터 쓰기

기존 jdbc 커넥터는 행 단위 삽입을 사용 하 여 데이터를 데이터베이스에 씁니다. Spark 커넥터를 사용 하 여 Azure SQL에 데이터를 쓰고 bulk insert를 사용 하 여 SQL Server 수 있습니다. 대량의 데이터 집합을 로드하거나 열 저장소 인덱스가 사용되는 테이블에 데이터를 로드할 때 쓰기 성능이 크게 향상됩니다.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/**
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>다음 단계

아직 없는 경우 [azure-sqldb-Spark GitHub 리포지토리에서](https://github.com/Azure/azure-sqldb-spark) spark 커넥터를 다운로드 하 고 리포지토리의 추가 리소스를 탐색 합니다.

- [Azure Databricks Notebooks 샘플](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [샘플 스크립트(Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

또한 [Apache Spark SQL, DataFrames 및 Datasets 가이드](https://spark.apache.org/docs/latest/sql-programming-guide.html) 및 [Azure Databricks 설명서](https://docs.microsoft.com/azure/azure-databricks/)를 검토할 수도 있습니다.
