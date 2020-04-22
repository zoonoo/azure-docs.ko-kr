---
title: Spark 풀(미리 보기)과 SQL 풀 간에 데이터 가져오기 및 내보내기
description: 이 문서에서는 사용자 지정 커넥터를 사용하여 SQL 풀과 Spark 풀(미리 보기) 간에 데이터를 이동하는 방법에 대한 정보를 제공합니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420427"
---
# <a name="introduction"></a>소개

Spark SQL Analytics Connector는 Azure Synapse에서 Spark 풀(미리 보기)과 SQL 풀 간에 데이터를 효율적으로 전송하도록 설계되었습니다. Spark SQL Analytics Connector는 SQL 풀에서만 작동하며, SQL 주문형에서는 작동하지 않습니다.

## <a name="design"></a>디자인

JDBC를 사용하여 Spark 풀과 SQL 풀 간에 데이터를 전송할 수 있습니다. 그러나 Spark 및 SQL 풀과 같은 두 개의 분산 시스템이 사용되므로 JDBC는 직렬 데이터를 전송할 때 병목 상태가 발생하는 경향이 있습니다.

SQL Analytics Connector에 대한 Spark 풀은 Apache Spark에 대한 데이터 원본 구현입니다. Azure Data Lake Storage Gen 2 및 SQL 풀의 Polybase를 사용하여 Spark 클러스터와 SQL Analytics 인스턴스 간에 데이터를 효율적으로 전송합니다.

![커넥터 아키텍처](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 인증

시스템 간 인증은 Azure Synapse Analytics에서 원활하게 수행됩니다. 스토리지 계정 또는 데이터 웨어하우스 서버에 액세스할 때 사용할 보안 토큰을 얻기 위해 Azure Active Directory와 연결하는 토큰 서비스가 있습니다. 따라서 스토리지 계정 및 데이터 웨어하우스 서버에서 AAD 인증을 구성하면 자격 증명을 만들거나 커넥터 API에서 자격 증명을 지정할 필요가 없습니다. AAD 인증을 구성하지 않는 경우 SQL 인증을 지정할 수 있습니다. 자세한 내용은 [사용](#usage) 섹션에서 확인할 수 있습니다.

## <a name="constraints"></a>제약 조건

- 이 커넥터는 Scala에서만 작동합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 데이터를 주고 받을 데이터베이스/SQL 풀에서 **db_exporter** 역할이 있어야 합니다.

사용자를 만들려면 데이터베이스에 연결하고 다음 예제를 따릅니다.

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

역할을 할당하는 방법은 다음과 같습니다.

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>사용

import 문은 입력할 필요가 없습니다. Notebook 환경용으로 미리 가져와 집니다.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>작업 영역과 연결된 논리 서버(DW 인스턴스)의 SQL 풀과 데이터 전송

> [!NOTE]
> **Notebook 환경에서 가져올 필요 없음**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>읽기 API

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

위의 API는 SQL 풀의 내부(관리형)뿐 아니라 외부 테이블에 대해서도 작동합니다.

#### <a name="write-api"></a>쓰기 API

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

TableType은 Constants.INTERNAL 또는 Constants.EXTERNAL입니다.

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Storage 및 SQL Server에 대한 인증이 수행됩니다.

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>작업 영역 외부의 SQL 풀 또는 논리 서버의 데이터베이스와 데이터를 주고 받는 경우

> [!NOTE]
> Notebook 환경에서 가져올 필요 없음

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>읽기 API

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>쓰기 API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>AAD 대신 SQL 인증 사용

#### <a name="read-api"></a>읽기 API

현재 이 커넥터는 작업 영역 외부에 있는 SQL 풀에 대한 토큰 기반 인증을 지원하지 않습니다. SQL 인증을 사용해야 합니다.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>쓰기 API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>PySpark 커넥터 사용

> [!NOTE]
> 이 예제는 노트북 환경만을 고려하여 제공됩니다.

DW에 작성하려는 데이터 프레임 "pyspark_df"가 있다고 가정합니다.

PySpark에서 데이터 프레임을 사용하여 임시 테이블을 만듭니다.

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

매직을 사용하여 PySpark Notebook에서 Scala 셀을 실행합니다.

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
마찬가지로 읽기 시나리오에서는 Scala를 사용하여 데이터를 읽고 임시 테이블에 쓰고, PySpark에서 Spark SQL을 사용하여 임시 테이블을 데이터 프레임으로 쿼리합니다.

## <a name="next-steps"></a>다음 단계

- [SQL 풀 만들기]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Azure Synapse Analytics 작업 영역에 사용할 새 Apache Spark 풀 만들기](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 