---
title: Azure Cosmos DB SQL API로 일대 소 관계형 데이터 마이그레이션
description: SQL API에 대한 일~소수 관계에 대한 복잡한 데이터 마이그레이션을 처리하는 방법 알아보기
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896637"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB SQL API 계정으로 일대 소 관계형 데이터 마이그레이션

관계형 데이터베이스에서 Azure Cosmos DB SQL API로 마이그레이션하려면 최적화를 위해 데이터 모델을 변경해야 할 수 있습니다.

한 가지 일반적인 변환은 하나의 JSON 문서에 관련 하위 항목을 포함시켜 데이터를 비정규화하는 것입니다. 여기서는 Azure 데이터 팩터리 또는 Azure Databricks를 사용하여 이에 대한 몇 가지 옵션을 살펴봅니다. 코스모스 DB의 데이터 모델링에 대한 일반적인 지침은 [Azure Cosmos DB에서 데이터 모델링을](modeling-data.md)검토하십시오.  

## <a name="example-scenario"></a>예제 시나리오

SQL 데이터베이스에 다음 두 테이블, 주문 및 OrderDetails가 있다고 가정합니다.


![Order Details](./media/migrate-relational-to-cosmos-sql-api/orders.png)

마이그레이션 하는 동안 하나의 JSON 문서에이 일~몇 가지 관계를 결합 하려고 합니다. 이렇게 하려면 아래와 같이 "FOR JSON"을 사용하여 T-SQL 쿼리를 만들 수 있습니다.

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

이 쿼리의 결과는 다음과 같습니다. 

![Order Details](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


이상적으로는 단일 ADF(Azure Data Factory) 복사 활동을 사용하여 SQL 데이터를 원본으로 쿼리하고 출력을 Azure Cosmos DB 싱크에 적절한 JSON 개체로 직접 작성하려고 합니다. 현재 는 하나의 복사 활동에서 필요한 JSON 변환을 수행할 수 없습니다. 위의 쿼리 결과를 Azure Cosmos DB SQL API 컨테이너에 복사하려고 하면 OrderDetails 필드가 예상되는 JSON 배열 대신 문서의 문자열 속성으로 표시됩니다.

다음 방법 중 하나로 현재의 제한 을 해결할 수 있습니다.

* **두 개의 복사 활동이 있는 Azure 데이터 팩터리 사용:** 
  1. SQL에서 중간 Blob 저장소 위치에 있는 텍스트 파일로 JSON 형식의 데이터를 가져옵니다. 
  2. JSON 텍스트 파일에서 Azure Cosmos DB의 컨테이너에 데이터를 로드합니다.

* **Azure Databricks를 사용하여 SQL에서 읽고 Azure Cosmos DB에 쓰십시오** - 여기에서 두 가지 옵션을 제공합니다.


이러한 접근 방식을 자세히 살펴보겠습니다.

## <a name="azure-data-factory"></a>Azure 데이터 팩터리

OrderDetails를 대상 Cosmos DB 문서에 JSON 배열로 포함할 수는 없지만 두 개의 별도 복사 활동을 사용하여 문제를 해결할 수 있습니다.

### <a name="copy-activity-1-sqljsontoblobtext"></a>복사 활동 #1: SqlJsonToBlobText

원본 데이터의 경우 SQL 쿼리를 사용하여 SQL Server OPENJSON 및 FOR JSON PATH 기능을 사용하여 행당 하나의 JSON 개체(순서를 나타내는)가 있는 단일 열로 결과 집합을 가져옵니다.

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![ADF 사본](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


SqlJsonToBlobText 복사 작업의 싱크에 대 한 "구분 된 텍스트"를 선택 하 고 동적으로 생성 된 고유 한 파일@concat이름 (예: ' (파이프라인))와 Azure Blob 저장소의 특정 폴더에 가리킬. RunId,'.json').
텍스트 파일이 실제로 "구분"되지 않고 쉼표를 사용하여 별도의 열로 구문 분석하지 않고 큰따옴표 (""")를 유지하려고하기 때문에"열 구분 기호"를 Tab ("\t")로 설정하거나 데이터에서 발생하지 않는 다른 문자 - 및 "따옴표 문자"를 설정합니다. "따옴표 문자 없음"으로 이동합니다.

![ADF 사본](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>복사 활동 #2: BlobJsonToCosmos

다음으로 첫 번째 활동에 의해 만들어진 텍스트 파일에 대해 Azure Blob 저장소에 보이는 두 번째 복사 작업을 추가하여 ADF 파이프라인을 수정합니다. 그것은 "JSON"소스로 처리하여 Cosmos DB 싱크에 텍스트 파일에 있는 JSON 행당 하나의 문서로 삽입합니다.

![ADF 사본](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

선택적으로 파이프라인에 "삭제" 활동을 추가하여 각 실행 전에 /Orders/폴더에 남아 있는 모든 이전 파일을 삭제합니다. 이제 ADF 파이프라인은 다음과 같이 보입니다.

![ADF 사본](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

위의 파이프라인을 트리거한 후 행당 하나의 JSON 개체를 포함하는 중간 Azure Blob Storage 위치에 생성된 파일이 표시됩니다.

![ADF 사본](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

또한 Cosmos DB 컬렉션에 삽입된 OrderDetails가 올바르게 포함된 주문 문서도 표시됩니다.

![ADF 사본](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

또한 [Azure Databricks의 Spark를](https://azure.microsoft.com/services/databricks/) 사용하여 Azure Blob Storage에서 중간 텍스트/JSON 파일을 만들지 않고도 SQL Database 원본의 데이터를 Azure Cosmos DB 대상으로 복사할 수 있습니다. 

> [!NOTE]
> 명확성과 단순성을 위해 아래 코드 조각에는 더미 데이터베이스 암호가 명시적으로 인라인으로 포함되지만 항상 Azure Databricks 암호를 사용해야 합니다.
>

먼저 필요한 [SQL 커넥터](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) 및 Azure [Cosmos DB 커넥터 라이브러리를](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) 만들고 Azure Databricks 클러스터에 연결합니다. 클러스터를 다시 시작하여 라이브러리가 로드되었는지 확인합니다.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

다음으로, 우리는 스칼라와 파이썬에 대한 두 개의 샘플을 제시한다. 

### <a name="scala"></a>스칼라
여기서는 "FOR JSON" 출력을 사용하여 SQL 쿼리의 결과를 DataFrame에 가져옵니다.

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

다음으로, 우리는 우리의 코스모스 DB 데이터베이스 및 컬렉션에 연결 :

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

마지막으로 스키마를 정의하고 from_json 사용하여 CosmosDB 컬렉션에 저장하기 전에 DataFrame을 적용합니다.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

다른 방법으로 Spark에서 JSON 변환을 실행해야 할 수도 있습니다(원본 데이터베이스가 "FOR JSON" 또는 유사한 작업을 지원하지 않는 경우) 매우 큰 데이터 집합에 병렬 작업을 사용할 수 있습니다. 여기서 는 PySpark 샘플을 제시합니다. 첫 번째 셀에서 소스 및 대상 데이터베이스 연결을 구성하여 시작합니다.

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

그런 다음 소스 데이터베이스(이 경우 SQL Server)에서 주문 및 주문 세부 정보 레코드를 쿼리하여 결과를 Spark Dataframes에 넣습니다. 또한 모든 순서 아이디를 포함하는 목록과 병렬 작업을 위한 스레드 풀을 만듭니다.

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

그런 다음 대상 SQL API 컬렉션에 Orders를 작성하는 함수를 만듭니다. 이 함수는 지정된 주문 ID에 대한 모든 주문 세부 정보를 필터링하고 JSON 배열로 변환한 다음 해당 순서에 대한 대상 SQL API 컬렉션에 쓸 JSON 문서에 배열을 삽입합니다.

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

마지막으로 스레드 풀의 맵 함수를 사용하여 위에서 호출하여 병렬로 실행하여 앞에서 만든 순서 의 ID 목록을 전달합니다.

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
두 방법 중 하나에서, 끝에서, 우리는 제대로 코스모스 DB 컬렉션의 각 주문 문서 내에서 포함 된 OrderDetails를 저장해야합니다 :

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>다음 단계
* Azure [코스모스 DB에서 데이터 모델링에](https://docs.microsoft.com/azure/cosmos-db/modeling-data) 대해 알아보기
* [Azure Cosmos DB에서 데이터를 모델링하고 파티션하는 방법](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example) 알아보기
