---
title: Azure Cosmos DB에 Apache Spark 연결
description: Azure Cosmos DB에 Apache Spark를 연결할 수 있도록 하는 Azure Cosmos DB Spark 커넥터에 대해 알아봅니다. Microsoft의 다중 테넌트, 글로벌하게 분산된 데이터베이스 시스템에서 분산된 집계를 수행할 수 있습니다.
keywords: Apache Spark
services: cosmos-db
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: fd1fa105a880acb0d2b18a3b69ba8d475d8f82a3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081594"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Apache Spark-Azure Cosmos DB 커넥터를 사용하여 빅 데이터 분석 가속화
 
Apache Spark-Azure Cosmos DB 커넥터를 사용하면 Azure Cosmos DB가 Apache Spark 작업의 입력 또는 출력이 될 수 있습니다. [Spark](https://spark.apache.org//)를 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 연결하면 빠르게 움직이는 데이터 과학 문제를 해결하는 능력을 가속화합니다. Azure Cosmos DB를 사용하여 데이터를 신속하게 저장하고 쿼리할 수 있습니다. 커넥터는 네이티브 Azure Cosmos DB 관리 인덱스를 효율적으로 사용합니다. 인덱스는 급변하는 글로벌 분산 데이터에 대한 분석 및 푸시다운 조건자 필터링을 수행할 때 업데이트할 수 있는 열을 사용할 수 있도록 합니다. 이러한 종류의 데이터는 IoT(사물 인터넷)에서 데이터 과학 및 분석 시나리오에 이를 수 있습니다.

## <a name="connector-components"></a>커넥터 구성 요소

Spark-Azure Cosmos DB 커넥터는 다음 구성 요소가 있습니다.

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)를 사용하면 여러 지리적 영역에서 처리량과 저장소를 프로비전하고 탄력적으로 확장할 수 있습니다.  

* [Apache Spark](https://spark.apache.org/)는 속도, 사용 편의성 및 정교한 분석에 기반하여 구축된 강력한 오픈 소스 처리 엔진입니다.  

* [Azure Databricks의 Apache Spark 클러스터](https://docs.azuredatabricks.net/getting-started/index.html)를 사용하여 Spark 클러스터에서 Spark 작업을 실행할 수 있습니다.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Azure Cosmos DB에 Apache Spark 연결

Apache Spark와 Azure Cosmos DB를 연결하는 방법에는 두 가지가 있습니다.

- *pyDocumentDB*라고도 하는 Python 기반 커넥터인 [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python)  

- Java 기반 커넥터인 [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java)


**지원되는 버전**

| 구성 요소 | 버전 |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| 스칼라|2.11|
| Azure Databricks 런타임 버전 | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Python 또는 pyDocumentDB SDK를 사용하여 연결

다음 다이어그램은 pyDocumentDB SDK 구현의 아키텍처를 보여줍니다.

![pyDocumentDB DB를 통한 Spark-Azure Cosmos DB 데이터 흐름의 다이어그램](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>데이터 흐름

pyDocumentDB 구현의 데이터 흐름은 다음과 같습니다.

* Spark의 마스터 노드는 pyDocumentDB를 통해 Azure Cosmos DB 게이트웨이 노드에 연결합니다. Spark 및 Azure Cosmos DB 연결만 지정합니다. 해당 마스터 및 게이트웨이 노드에 대한 연결은 투명합니다.  

* 게이트웨이 노드를 통해 Azure Cosmos DB에 대해 쿼리가 수행되며, 이후에 여기서 데이터 노드의 컬렉션 파티션에 대해 쿼리가 실행됩니다. 이러한 쿼리에 대한 응답은 게이트웨이 노드로 다시 보내지고, 결과 집합은 Spark 마스터 노드로 반환됩니다.  

* 이후의 쿼리(예: Spark 데이터 프레임 대상)는 처리를 위해 Spark 작업자 노드로 보내집니다.  

Spark와 Azure Cosmos DB 간의 통신은 Spark 마스터 노드와 Azure Cosmos DB 게이트웨이 노드로 제한됩니다. 쿼리는 이 두 노드 사이의 전송 계층만큼 빠릅니다.

다음 단계를 실행하여 pyDocumentDB SDK를 통해 Spark를 Azure Cosmos DB에 연결합니다.

1. [Azure Databricks 작업 영역](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) 및 [Spark 클러스터](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)를 만듭니다. Azure Databricks 런타임 버전 4.0은 해당 작업 영역 내에 Apache Spark 2.3.0 및 Scala 2.11을 포함합니다.  

2. 클러스터가 만들어지고 실행 중이면 **작업 영역** > **만들기** > **라이브러리**로 이동합니다.  
3. 새 라이브러리 대화 상자에서 원본으로 **Python Egg 또는 PyPi 업로드**를 선택합니다. 이름으로 **pydocumentdb**를 제공하고, **라이브러리 설치**를 선택합니다. pyDocumentdb SDK가 이미 pip 패키지에 게시되어 있으므로 이를 찾고 설치할 수 있습니다. 

   ![새 라이브러리 대화 상자의 스크린샷](./media/spark-connector/create-library.png)

4. 라이브러리가 설치된 후 이전에 만든 클러스터에 라이브러리를 연결합니다.  

5. **작업 영역** > **만들기** > **Notebook**으로 이동합니다.  

6. **Notebook 만들기** 대화 상자에서 사용자에게 익숙한 이름을 입력하고, **Python**을 언어로 선택합니다. 드롭다운 목록에서 이전에 만든 클러스터를 선택하고, **만들기**를 선택합니다.  

7. “doctorwho” Azure Cosmos DB 계정에서 호스트되는 항공편 샘플 데이터를 사용하여 몇 가지 Spark 쿼리를 실행합니다. 이 계정은 공개적으로 액세스할 수 있습니다. [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 리포지토리에서 [Read_Batch_PyDocumentDB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Batch_PyDocumentDB.ipynb) 노트를 호스트합니다. Azure Databricks 계정으로 Notebook을 가져오고 실행할 수 있습니다. 다음 섹션에서는 코드 블록의 기능을 자세히 설명합니다.

다음 코드 조각은 pyDocumentDB SDK를 가져오고 Spark 컨텍스트에서 쿼리를 실행하는 방법을 보여줍니다. 코드 조각에 표시된 대로 pyDocumentDB SDK에는 Azure Cosmos DB 계정에 연결하는 데 필요한 연결 매개 변수가 포함되어 있습니다. 필수 라이브러리를 가져오고, 마스터 키와 호스트를 구성하여 Azure Cosmos DB 클라이언트(pydocumentdb.document_client)를 만듭니다.


```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]

# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)

```

다음으로 쿼리를 실행할 수 있습니다. 다음 코드 조각은 doctorwho 계정의 airports.codes 컬렉션에 연결하고, 워싱턴 주에 있는 공항 도시를 추출하는 쿼리를 실행합니다. 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

쿼리를 실행하면 결과는 Python 목록으로 변환된 “query_iterable.QueryIterable”입니다. 따라서 이 목록은 Spark 데이터 프레임으로 변환됩니다. 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

### <a name="considerations-when-using-pydocumentdb-sdk"></a>pyDocumentDB SDK 사용 시 고려 사항
다음 시나리오에서는 pyDocumentDB SDK를 사용하여 Spark를 Azure Cosmos DB에 연결하는 것이 좋습니다.

* Python을 사용하려고 합니다.  

* 비교적 작은 결과 집합을 Azure Cosmos DB에서 Spark로 반환합니다. Azure Cosmos DB의 기본 데이터 세트가 매우 클 수 있고, Azure Cosmos DB 원본에 필터를 적용하거나 조건자 필터를 실행하고 있습니다.

## <a name="connect-by-using-the-java-sdk"></a>Java SDK를 사용하여 연결

다음 다이어그램은 Azure Cosmos DB SQL Java SDK 구현의 아키텍처를 보여주고, 데이터는 Spark 작업자 노드 간에 이동합니다.

![Spark-Azure Cosmos DB 커넥터에서 데이터 흐름의 다이어그램](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>데이터 흐름

Java SDK 구현의 데이터 흐름은 다음과 같습니다.

* Spark 마스터 노드는 Azure Cosmos DB 게이트웨이 노드에 연결하여 파티션 맵을 가져옵니다. Spark 및 Azure Cosmos DB 연결만 지정합니다. 해당 마스터 및 게이트웨이 노드에 대한 연결은 투명합니다.  

* 이 정보는 Spark 마스터 노드에 다시 제공됩니다. 이 시점에서 쿼리를 구문 분석하여 액세스해야 하는 Azure Cosmos DB의 파티션 및 해당 위치를 확인할 수 있어야 합니다.  

* 이 정보는 Spark 작업자 노드로 전송됩니다.  

* Spark 작업자 노드는 Azure Cosmos DB 파티션에 직접 연결하여 추출하고 작업자 노드의 Spark 파티션으로 데이터를 반환합니다.  

Spark 작업자 노드와 Azure Cosmos DB 데이터 노드(파티션) 간에 데이터가 이동하므로 Spark와 Azure Cosmos DB 간의 통신이 훨씬 더 빠릅니다. 이 예제에서는 Azure Cosmos DB 계정에 일부 샘플 Twitter 데이터를 보내고, 해당 데이터를 사용하여 Spark 쿼리를 실행합니다. 다음 단계를 사용하여 Azure Cosmos DB에 샘플 Twitter 데이터를 기록합니다.

1. [Azure Cosmos DB SQL API 계정](create-sql-api-dotnet.md#create-a-database-account)을 만들고 계정에 [컬렉션을 추가](create-sql-api-dotnet.md#add-a-collection)합니다.  

2. GitHub에서 [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) 샘플을 다운로드합니다. 이 피드를 사용하여 Azure Cosmos DB에 샘플 Twitter 데이터를 기록합니다.  

3. 명령 프롬프트를 열고, 다음 명령을 실행하여 Tweepy 및 pyDocumentdb 모듈을 설치합니다.

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Twitter 피드 샘플의 콘텐츠를 추출하고 config.py 파일을 엽니다. masterKey, host, databaseId, collectionId 및 preferredLocations 값을 업데이트합니다.  

5. `https://apps.twitter.com/`으로 이동하고 Twitter 피드 응용 프로그램을 등록합니다. 응용 프로그램 이름을 선택하면 **소비자 키, 소비자 암호, 액세스 토큰 및 액세스 토큰 암호**가 제공됩니다. 이 값을 복사하고 config.py 파일에서 업데이트하여 Twitter에 대한 응용 프로그램의 프로그래밍 방식 액세스를 Twitter 피드에 제공합니다.   

6. config.py 파일을 저장합니다. 명령 프롬프트를 열고, 다음 명령을 사용하여 Python 응용 프로그램을 실행합니다.

   ```bash
   Python driver.py
   ```

7. 포털에서 Azure Cosmos DB 컬렉션으로 이동하고, Twitter 데이터가 컬렉션에 기록되어 있는지 확인합니다.

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>Java SDK를 찾아 Spark 클러스터에 연결합니다.

1. [Azure Databricks 작업 영역](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) 및 [Spark 클러스터](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)를 만듭니다. Azure Databricks 런타임 버전 4.0은 해당 작업 영역 내에 Apache Spark 2.3.0 및 Scala 2.11을 포함합니다.  

2. 클러스터가 만들어지고 실행 중이면 **작업 영역** > **만들기** > **라이브러리**로 이동합니다.  

3. **새 라이브러리** 대화 상자에서 원본으로 **Maven 좌표**를 선택합니다. 좌표 값 **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**을 제공하고, **라이브러리 만들기**를 선택합니다. Maven 종속성이 확인되고 패키지가 작업 영역에 추가됩니다. 위의 Maven 좌표 형식에서 2.3.0은 Spark 버전을 나타내고, 2.11은 Scala 버전을 나타내고, 1.2.0은 Azure Cosmos DB 커넥터 버전을 나타냅니다. 

4. 라이브러리가 설치된 후 이전에 만든 클러스터에 라이브러리를 연결합니다. 

이 문서에서는 다음 시나리오에서 Spark 커넥터 Java SDK를 사용하는 방법을 보여줍니다.

* Azure Cosmos DB에서 Twitter 데이터 읽기  

* Azure Cosmos DB에 스트리밍되는 Twitter 데이터 읽기  

* Azure Cosmos DB에 Twitter 데이터 쓰기 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Azure Cosmos DB에서 Twitter 데이터 읽기
 
이 섹션에서는 Spark 쿼리를 실행하여 Azure Cosmos DB에서 Twitter 데이터 일괄 처리를 읽습니다. [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 리포지토리에서 [Read_Batch_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Batch_Twitter_Data.ipynb) 노트를 호스트합니다. Azure Databricks 계정에 Notebook을 가져오고, 계정 URI, 마스터 키, 데이터베이스 및 컬렉션 이름을 업데이트할 수 있습니다. Notebook을 실행하거나 다음과 같이 만들 수 있습니다.

1. Azure Databricks 계정으로 이동하고, **작업 영역** > **만들기** > **Notebook**을 선택합니다. 

2. **Notebook 만들기** 대화 상자에서 사용자에게 익숙한 이름을 입력하고, **Python**을 언어로 선택합니다. 드롭다운 목록에서 이전에 만든 클러스터를 선택하고, **만들기**를 선택합니다.  

3. 엔드포인트, 마스터 키, 데이터베이스 및 컬렉션 값을 업데이트하여 계정에 연결합니다. spark.read.format() 명령을 사용하여 트윗을 읽습니다.

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. 캐시된 데이터에서 여러 가지 해시태그별로 트윗 수를 가져오는 쿼리를 실행합니다. 

   ```python
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

Java SDK는 구성 매핑에 다음 값을 지원합니다. 

|설정  |설명  |
|---------|---------|
|query_maxdegreeofparallelism  | 병렬 쿼리를 실행하는 동안 클라이언트 쪽에서 실행되는 동시 작업 수를 설정합니다. 0보다 큰 값으로 설정하면 동시 작업 수가 할당된 값으로 제한됩니다. 0보다 작은 값으로 설정할 경우 시스템이 실행할 동시 작업 수를 자동으로 결정합니다. 커넥터가 각 컬렉션 파티션을 실행기와 매핑하므로 이 값은 읽기 작업에 아무 영향도 주지 않습니다.        |
|query_maxbuffereditemcount     |    병렬 쿼리를 실행하는 동안 클라이언트 쪽에서 버퍼링될 수 있는 최대 항목 수를 설정합니다. 0보다 큰 값으로 설정하면 버퍼링된 항목 수가 할당된 값으로 제한됩니다. 0보다 작은 값으로 설정할 경우, 시스템이 버퍼링할 항목 수를 자동으로 결정합니다.     |
|query_enablescan    |   요청된 경로에서 인덱싱이 옵트아웃되었으므로 서비스를 제공할 수 없는 쿼리에서 검사를 사용할 수 있는 옵션을 설정합니다.       |
|query_disableruperminuteusage  |  일반 프로비전된 RU/초가 모두 소모된 경우, 쿼리를 제공하는 RU(요청 단위)/분 용량을 사용하지 않도록 설정합니다.       |
|query_emitverbosetraces   |   쿼리에서 조사를 위해 세부 정보 표시 추적을 내보낼 수 있는 옵션을 설정합니다.      |
|query_pagesize  |   각 쿼리 요청에 대한 쿼리 결과 페이지의 크기를 설정합니다. 처리량을 최적화하려면 큰 페이지 크기를 사용하여 결과를 페치할 왕복 수를 줄입니다.      |
|query_custom  |  Azure Cosmos DB에서 데이터를 페치할 때 기본 쿼리를 재정의하도록 Azure Cosmos DB 쿼리를 설정합니다. 이 값을 제공하면 이 값은 푸시다운된 조건자가 있는 쿼리 대신 사용되기도 합니다.     |

시나리오에 따라 성능 및 처리량을 최적화하는 데 여러 구성 값을 사용해야 합니다. 구성 키는 현재 대/소문자를 구분하지 않으며 구성 값은 항상 문자열입니다.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Azure Cosmos DB에 스트리밍되는 Twitter 데이터 읽기

이 섹션에서는 Spark 쿼리를 실행하여 스트리밍 Twitter 데이터의 변경 피드를 읽습니다. 이 섹션에서 쿼리를 실행하는 동안 Twitter 피드 앱이 실행 중이고 Azure Cosmos DB에 데이터를 펌프하는지 확인합니다. [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 리포지토리에서 [Read_Stream_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Stream_Twitter_Data.scala) 노트를 호스트합니다. Azure Databricks 계정에 Notebook을 가져오고, 계정 URI, 마스터 키, 데이터베이스 및 컬렉션 이름을 업데이트할 수 있습니다. Notebook을 실행하거나 다음과 같이 만들 수 있습니다.

1. Azure Databricks 계정으로 이동하고, **작업 영역** > **만들기** > **Notebook**을 선택합니다.  

2. **Notebook 만들기** 대화 상자에서 사용자에게 익숙한 이름을 입력하고, **Scala**를 언어로 선택합니다. 드롭다운 목록에서 이전에 만든 클러스터를 선택하고, **만들기**를 선택합니다.  

3. 엔드포인트, 마스터 키, 데이터베이스 및 컬렉션 값을 업데이트하여 계정에 연결합니다.

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. Spark.readStream.format() 명령을 사용하여 스트림으로 변경 피드 읽기를 시작합니다.

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. 콘솔에 쿼리 스트리밍을 시작합니다.

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Java SDK는 구성 매핑에 다음 값을 지원합니다.

|설정  |설명  |
|---------|---------|
|readchangefeed   |  컬렉션 콘텐츠가 CosmosDB 변경 피드에서 페치됨을 나타냅니다. 기본값은 False입니다.       |
|changefeedqueryname |   쿼리를 식별하는 사용자 지정 문자열입니다. 커넥터는 개별적으로 다양한 변경 피드 쿼리에 대한 컬렉션 연속 토큰을 추적합니다. readchangefeed가 true이면 빈 값을 사용할 수 없는 필수 구성입니다.      |
|changefeedcheckpointlocation  |   노드 오류가 발생할 경우, 연속 토큰을 지속할 로컬 파일 저장소의 경로입니다.      |
|changefeedstartfromthebeginning  |  변경 피드가 시작(true) 또는 현재(false) 지점에서 시작되어야 하는지 여부를 설정합니다. 기본적으로 현재(false)에서 시작됩니다.       |
|rollingchangefeed  |   변경 피드를 마지막 쿼리에서 가져와야 하는지 여부를 나타내는 부울 값입니다. 기본값은 false이며, 이는 변경 내용이 컬렉션의 첫 번째 읽기부터 계산됨을 의미합니다.      |
|changefeedusenexttoken  |   실패 시나리오 처리를 지원하는 부울 값입니다. 현재 변경 피드 일괄 처리가 정상적으로 처리되었음을 나타냅니다. 탄력적인 분산 데이터 세트는 변경 내용의 이후 일괄 처리를 가져오는 데 다음 연속 토큰을 사용해야 합니다.      |
| InferStreamSchema | 스트리밍 데이터의 스키마를 스트리밍 시작 시 샘플링해야 하는지 여부를 나타내는 부울 값입니다. 기본적으로 이 값은 true로 설정됩니다. 이 매개 변수가 true로 설정되고 스트리밍 데이터의 스키마가 데이터가 샘플링된 후 변경되면 새로 추가된 속성이 스트리밍 데이터 프레임에서 삭제됩니다. <br/><br/> 스트리밍 데이터 프레임을 스키마에 종속되지 않게 하려면 이 매개 변수를 false로 설정합니다. 이 모드에서 Azure Cosmos DB 변경 피드에서 읽은 문서의 본문은 본문 속성으로 래핑됩니다. 이 속성은 시스템 속성 값과 별개로 결과 스트리밍 데이터 프레임에 있습니다.
 |

### <a name="connection-settings"></a>연결 설정

Java SDK는 다음 연결 설정을 지원합니다.

|설정  |설명  |
|---------|---------|
|connectionmode   |  내부 DocumentClient가 Azure Cosmos DB와 통신하는 데 사용해야 하는 연결 모드를 설정합니다. 허용되는 값은 **DirectHttps**(기본값) 및 **Gateway**입니다. DirectHttps 연결 모드는 요청을 CosmosDB 파티션으로 직접 라우트하므로 대기 시간이 약간 감소합니다.       |
|connectionmaxpoolsize   |  내부 DocumentClient에서 사용하는 연결 풀 크기의 값을 설정합니다. 기본값은 100입니다.       |
|connectionidletimeout  |  유휴 연결의 제한 시간 값(초)을 설정합니다. 기본값은 60입니다.       |
|query_maxretryattemptsonthrottledrequests    |  최대 재시도 횟수를 설정합니다. 클라이언트의 요금 제한으로 인해 요청이 실패하는 경우에 이 값을 사용합니다. 이 값을 지정하지 않으면 기본값은 재시도 횟수 1000회입니다.       |
|query_maxretrywaittimeinseconds   |  최대 재시도 시간(초)을 설정합니다. 기본적으로는 1000초입니다.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Azure Cosmos DB에 Twitter 데이터 쓰기 

이 섹션에서는 Spark 쿼리를 실행하여 Twitter 데이터의 일괄 처리를 동일한 데이터베이스의 새 컬렉션에 씁니다. [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 리포지토리에서 [Write_Batch_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Write_Batch_Twitter_Data.ipynb) 노트를 호스트합니다. Azure Databricks 계정에 Notebook을 가져오고, 계정 URI, 마스터 키, 데이터베이스 및 컬렉션 이름을 업데이트할 수 있습니다. Notebook을 실행하거나 다음과 같이 만들 수 있습니다.

1. Azure Databricks 계정으로 이동하고, **작업 영역** > **만들기** > **Notebook**을 선택합니다.  

2. **Notebook 만들기** 대화 상자에서 사용자에게 익숙한 이름을 입력하고, **Scala**를 언어로 선택합니다. 드롭다운 목록에서 이전에 만든 클러스터를 선택하고, **만들기**를 선택합니다.  

3. 엔드포인트, 마스터 키, 데이터베이스 및 컬렉션 값을 업데이트하여 Twitter 데이터를 읽고 쓸 데이터베이스 컬렉션에 연결합니다.

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. 캐시된 데이터에서 여러 가지 해시태그별로 트윗 수를 가져오는 쿼리를 실행합니다. 

   ```scala
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

5. 트윗 태그의 새 데이터 프레임을 만들고 데이터를 새 컬렉션에 저장합니다. 다음 코드를 실행한 후 포털로 돌아가서 컬렉션에 데이터가 기록되었는지 확인할 수 있습니다. 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

Java SDK는 구성 매핑에 다음 값을 지원합니다.

|설정  |설명  |
|---------|---------|
| BulkImport | BulkExecutor 라이브러리를 사용하여 데이터를 가져와야 할지 여부를 나타내는 부울 값입니다. 기본적으로 이 값은 true로 설정됩니다. |
|WritingBatchSize  |   Azure Cosmos DB 컬렉션에 데이터를 쓸 때 사용할 일괄 처리 크기를 나타냅니다. <br/><br/> BulkImport 매개 변수가 true로 설정된 경우, WritingBatchSize 매개 변수는 BulkExecutor 라이브러리의 importAll API에 입력으로 제공된 문서의 일괄 처리 크기를 나타냅니다. 기본적으로 이 값은 100K로 설정됩니다. <br/><br/> BulkImport 매개 변수를 false로 설정하면 WritingBatchSize 매개 변수는 Azure Cosmos DB 컬렉션에 쓸 때 사용할 일괄 처리 크기를 나타냅니다. 커넥터는 createDocument 및 upsertDocument 요청을 비동기적으로 일괄 전송합니다. 일괄 처리 크기가 클수록 클러스터 리소스를 사용할 수 있는 한 더 많은 처리량을 달성할 수 있습니다. 반면에 속도 및 RU 사용을 제한하려면 더 작은 수의 일괄 처리 크기를 지정합니다. 기본적으로 쓰기 일괄 처리 크기는 500으로 설정됩니다.  |
|Upsert   |  Azure CosmosDB 컬렉션에 쓸 때 CreateDocument 대신 upsertDocument를 사용할지 여부를 나타내는 부울 값 문자열입니다.   |
| WriteThroughputBudget |  컬렉션에 할당된 총 처리량 중에서 대량 삽입 Spark 작업에 할당할 RU 수를 나타내는 정수 문자열입니다. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Azure Cosmos DB에 스트리밍되는 Twitter 데이터 쓰기 

이 섹션에서는 Spark 쿼리를 실행하여 스트리밍 Twitter 데이터의 변경 피드를 동일한 데이터베이스의 새 컬렉션에 씁니다. [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 리포지토리에서 [Write_Stream_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Write_Stream_Data.scala) 노트를 호스트합니다. Azure Databricks 계정에 Notebook을 가져오고, 계정 URI, 마스터 키, 데이터베이스 및 컬렉션 이름을 업데이트할 수 있습니다. Notebook을 실행하거나 다음과 같이 만들 수 있습니다.

1. Azure Databricks 계정으로 이동하고, **작업 영역** > **만들기** > **Notebook**을 선택합니다.  

2. **Notebook 만들기** 대화 상자에서 사용자에게 익숙한 이름을 입력하고, **Scala**를 언어로 선택합니다. 드롭다운 목록에서 이전에 만든 클러스터를 선택하고, **만들기**를 선택합니다.  

3. 엔드포인트, 마스터 키, 데이터베이스 및 컬렉션 값을 업데이트하여 Twitter 데이터를 읽고 쓸 데이터베이스 컬렉션에 연결합니다.

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. Spark.readStream.format() 명령을 사용하여 스트림으로 변경 피드 읽기를 시작합니다.
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. 대상 컬렉션의 구성을 정의하고 writeStream.format() 메서드를 사용하여 스트리밍 작업을 시작합니다.

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
    ```

Java SDK는 구성 매핑에 다음 값을 지원합니다.

|설정  |설명  |
|---------|---------|
|Upsert   |  Azure CosmosDB 컬렉션에 쓸 때 CreateDocument 대신 upsertDocument를 사용할지 여부를 나타내는 부울 값 문자열입니다.   |
|checkpointlocation  |   노드 오류가 발생할 경우, 연속 토큰을 지속할 로컬 파일 저장소의 경로입니다.   |
|WritingBatchSize  |  Azure Cosmos DB 컬렉션에 데이터를 쓸 때 사용할 일괄 처리 크기를 나타냅니다. 커넥터는 createDocument 및 upsertDocument 요청을 비동기적으로 일괄 전송합니다. 일괄 처리 크기가 클수록 클러스터 리소스를 사용할 수 있는 한 더 많은 처리량을 달성할 수 있습니다. 반면에 속도 및 RU 사용을 제한하려면 더 작은 수의 일괄 처리 크기를 지정합니다. 기본적으로 쓰기 일괄 처리 크기는 500으로 설정됩니다.  |


### <a name="considerations-when-using-java-sdk"></a>Java SDK 사용 시 고려 사항

다음 시나리오에서는 Java SDK를 사용하여 Spark를 Azure Cosmos DB에 연결하는 것이 좋습니다.

* Python 또는 Scala를 사용하려고 합니다.  

* Apache Spark와 Azure Cosmos DB 간에 많은 양의 데이터를 전송해야 합니다. Java SDK는 pyDocumentDB보다 효율적으로 수행합니다. 쿼리 성능 차이에 대한 자세한 내용은 [쿼리 테스트 실행 wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs)를 참조하세요.

## <a name="next-steps"></a>다음 단계

아직 없는 경우 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 리포지토리에서 Azure Cosmos DB 커넥터로 Spark를 다운로드합니다. 리포지토리에서 다음 추가 리소스를 살펴보세요.

* [집계 예제](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [샘플 스크립트 및 노트북](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

또한 [Apache Spark SQL, DataFrame 및 데이터 세트 가이드](https://spark.apache.org/docs/latest/sql-programming-guide.html) 및 [Azure HDInsight의 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 문서를 검토할 수도 있습니다.
