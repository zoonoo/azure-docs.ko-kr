---
title: Azure SQL 및 SQL Server
description: 이 문서에서는 Azure MS SQL과 서버리스 Apache Spark 풀 간에 데이터를 이동하기 위해 커넥터를 사용하는 방법에 대한 정보를 제공합니다.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 05/19/2020
ms.openlocfilehash: 82518e6c8f4901ffe6c5503d902359a79c12e0b0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110081679"
---
# <a name="azure-sql-database-and-sql-server-connector-for-apache-spark"></a>Azure SQL Database 및 SQL Server용 Apache Spark 커넥터
Azure SQL Database 및 SQL Server용 Apache Spark 커넥터는 이러한 데이터베이스가 Apache Spark 작업에 대한 입력 데이터 원본 및 출력 데이터 싱크 역할을 할 수 있도록 합니다. 이를 통해 빅 데이터 분석에서 실시간 트랜잭션 데이터를 사용하고 임시 쿼리 또는 보고에 대한 결과를 유지할 수 있습니다.

기본 제공 JDBC 커넥터에 비해, 이 커넥터는 SQL 데이터베이스에 대량으로 데이터를 삽입할 수 있는 기능을 제공합니다. 10배 ~ 20배 빠른 성능으로 행 단위 삽입 시 뛰어난 성능을 제공할 수 있습니다. SQL Server 및 Azure SQL Database용 Spark 커넥터는 Azure AD(Azure Active Directory) [인증](/sql/connect/spark/connector#azure-active-directory-authentication)을 지원하므로 Azure Synapse Analytics에서 Azure SQL 데이터베이스에 안전하게 연결할 수 있습니다. 

이 문서에서는 DataFrame API를 통해 MS SQL 커넥터를 사용하여 SQL 데이터베이스에 연결하는 방법을 설명합니다. 이 문서에서는 PysPark API를 사용하는 자세한 예를 제공합니다. MS SQL 커넥터를 사용하여 SQL 데이터베이스에 연결하기 위해 지원되는 모든 인수 및 샘플은 [Azure Data SQL 샘플](https://github.com/microsoft/sql-server-samples#azure-data-sql-samples-repository)을 참조하세요.


  
## <a name="connection-details"></a>연결 세부 정보
이 예에서는 Microsoft Spark 유틸리티를 사용하여 미리 구성된 Key Vault에서 비밀을 쉽게 획득합니다. Microsoft Spark 유틸리티에 대한 자세한 내용은 [Microsoft Spark 유틸리티 소개](../microsoft-spark-utilities.md)를 참조하세요.

```python
servername = "<< server name >>"
dbname = "<< database name >>"
url = servername + ";" + "databaseName=" + dbname + ";"
dbtable = "<< table name >> "
user = "<< username >>" 
password = mssparkutils.credentials.getSecret('azure key vault name','secret name')
```

> [!NOTE]
> 현재 Azure SQL 커넥터를 사용한 연결된 서비스 또는 AAD 통과 지원은 없습니다.

## <a name="use-the-azure-sql-and-sql-server-connector"></a>Azure SQL 및 SQL Server 커넥터 사용

### <a name="read-data"></a>데이터 읽기
```python
#Read from SQL table using MS SQL Connector
print("read data from SQL server table  ")
jdbcDF = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("user", user) \
        .option("password", password).load()

jdbcDF.show(5)
```

### <a name="write-data"></a>데이터 쓰기
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("overwrite") \
    .option("url", url) \
    .option("dbtable", dbtable) \
    .option("user", user) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("MSSQL Connector write failed", error)

print("MSSQL Connector write(overwrite) succeeded  ")
```
### <a name="append-data"></a>데이터 추가
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("append") \
    .option("url", url) \
    .option("dbtable", table_name) \
    .option("user", username) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("Connector write failed", error)
```

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 인증

### <a name="python-example-with-service-principal"></a>서비스 주체를 사용하는 Python 예
```python
import adal

# Located in App Registrations from Azure Portal
tenant_id = "<< tenant id >> "

# Located in App Registrations from Azure Portal
resource_app_id_url = "https://database.windows.net/"

# Authority
authority = "https://login.windows.net/" + tenant_id

context = adal.AuthenticationContext(authority)
token = context.acquire_token_with_client_credentials(resource_app_id_url, service_principal_id, service_principal_secret)
access_token = token["accessToken"]

jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("accessToken", access_token) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

### <a name="python-example-with-active-directory-password"></a>Active Directory 암호를 사용하는 Python 예
```python
jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", table_name) \
        .option("authentication", "ActiveDirectoryPassword") \
        .option("user", user_name) \
        .option("password", password) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

## <a name="next-steps"></a>다음 단계
- [SQL Server 및 Azure SQL 커넥터에 대한 자세한 정보](/sql/connect/spark/connector)
- [Azure Data SQL 샘플 보기](https://github.com/microsoft/sql-server-samples)
