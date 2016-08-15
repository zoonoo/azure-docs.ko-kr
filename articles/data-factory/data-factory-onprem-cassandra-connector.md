<properties 
	pageTitle="Data Factory를 사용하여 Cassandra에서 데이터 이동 | Microsoft Azure" 
	description="Azure Data Factory를 사용하여 온-프레미스 Cassandra 데이터베이스에서 데이터를 이동하는 방법을 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2016" 
	ms.author="spelluru"/>

# Azure Data Factory를 사용하여 온-프레미스 Cassandra 데이터베이스에서 데이터 이동
이 문서에서는 Azure Data Factory의 복사 활동을 사용하여 온-프레미스 Cassandra 데이터베이스의 데이터를 [지원되는 원본 및 싱크](data-factory-data-movement-activities.md#supported-data-stores) 섹션에 있는 싱크 열에 나열된 데이터 저장소에 복사하는 방법을 설명입니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 Data Factory는 Cassandra 데이터베이스에서 [지원되는 싱크 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores)로 이동하는 작업만 지원하고, 다른 데이터 저장소의 데이터를 Cassandra 데이터베이스로 이동하는 작업은 지원하지 않습니다.

## 필수 조건
Azure Data Factory 서비스에서 온-프레미스 Cassandra 데이터베이스에 연결할 수 있으려면 다음을 설치해야 합니다.

- 호스트하는 동일한 컴퓨터 또는 별도 컴퓨터(데이터베이스와의 리소스 경쟁을 방지하려는 경우)에 있는 데이터 관리 게이트웨이 2.0 이상. 데이터 관리 게이트웨이는 온-프레미스 데이터 원본을 클라우드 서비스에 안전하고 관리되는 방식으로 연결하는 소프트웨어입니다. 데이터 관리 게이트웨이에 대한 자세한 내용은 [온-프레미스 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.
  
	게이트웨이를 설치하면 Cassandra 데이터베이스에 연결하는 데 사용되는 Microsoft Cassandra ODBC 드라이버가 자동으로 설치됩니다.

> [AZURE.NOTE] 연결/게이트웨이 관련 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)을 참조하세요.

## 데이터 복사 마법사
Cassandra 데이터베이스의 데이터를 지원되는 싱크 데이터 저장소 중 하나에 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다.

## 샘플: Cassandra에서 Blob으로 데이터 복사
샘플은 1시간마다 Cassandra 데이터베이스의 데이터를 Azure Blob으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다. Azure Data Factory의 복사 작업을 사용하여 [데이터 이동 활동](data-factory-data-movement-activities.md#supported-data-stores) 문서에 설명한 싱크로 직접 데이터를 복사할 수 있습니다.

- [OnPremisesCassandra](#onpremisescassandra-linked-service-properties) 형식의 연결된 서비스
- [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
- [CassandraTable](#cassandratable-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
- [CassandraSource](#cassandrasource-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

**Cassandra 연결된 서비스**

이 예제에서는 **Cassandra** 연결된 서비스를 사용합니다. 이 연결된 서비스에서 지원하는 속성 목록은 [Cassandra 연결된 서비스](#onpremisescassandra-linked-service-properties) 섹션을 참조하세요.

	{
    	"name": "CassandraLinkedService",
    	"properties":
    	{
			"type": "OnPremisesCassandra",
			"typeProperties":
			{
				"authenticationType": "Basic",
				"host": "mycassandraserver",
				"port": 9042,
				"username": "user",
				"password": "password",
				"gatewayName": "mygateway"
        	}
    	}
	}


**Azure 저장소 연결된 서비스**

	{
		"name": "AzureStorageLinkedService",
		"properties": {
		"type": "AzureStorage",
			"typeProperties": {
				"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
			}
		}
	}

**Cassandra 입력 데이터 집합**

	{
		"name": "CassandraInput",
		"properties": {
			"linkedServiceName": "CassandraLinkedService",
			"type": "CassandraTable",
			"typeProperties": {
				"tableName": "mytable",
				"keySpace": "mykeyspace" 
			},
			"availability": {
				"frequency": "Hour",
				"interval": 1
			},
			"external": true,
			"policy": {
				"externalData": {
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}
		}
	}

**external**을 **true**로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

**Azure Blob 출력 데이터 집합**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회)

	{
		"name": "AzureBlobOutput",
		"properties":
		{
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties":
			{
				"folderPath": "adfgetstarted/fromcassandra"
			},
			"availability":
			{
				"frequency": "Hour",
				"interval": 1
			}
		}
	}


**복사 작업을 포함하는 파이프라인**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **CassandraSource**로 설정되고 **싱크** 형식은 **BlobSink**로 설정됩니다.

RelationalSource에서 지원하는 속성 목록은 [RelationalSource 형식 속성](#cassandrasource-type-properties)을 참조하세요.
	
	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "CassandraToAzureBlob",
				"description": "Copy from Cassandra to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "CassandraInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "CassandraSource",
						"query": "select id, firstname, lastname from mykeyspace.mytable"
		
					},
					"sink": {
						"type": "BlobSink"
					}
				},
				"scheduler": {
					"frequency": "Hour",
					"interval": 1
				},
				"policy": {
					"concurrency": 1,
					"executionPriorityOrder": "OldestFirst",
					"retry": 0,
					"timeout": "01:00:00"
				}
			}
			]	
		}
	}
## OnPremisesCassandra 연결된 서비스 속성

다음 표에서는 Cassandra 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- | 
| type | type 속성은 **OnPremisesCassandra**로 설정되어야 합니다. | 예 |
| host | Cassandra 서버에 대한 하나 이상의 IP 주소 또는 호스트 이름.<br/><br/>모든 서버에 동시에 연결하려면 쉼표로 구분된 IP 주소 또는 호스트 이름 목록을 지정합니다. | 예 | 
| 포트 | Cassandra 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. | 아니요. 기본값: 9042 |
| authenticationType | Basic 또는 Anonymous | 예 |
| username |사용자 계정의 사용자 이름을 지정합니다. | 예. authenticationType은 Basic으로 설정됩니다. |
| password | 사용자 계정으로 password를 지정합니다. | 예. authenticationType은 Basic으로 설정됩니다. |
| gatewayName | 온-프레미스 Cassandra 데이터베이스에 연결하는 데 사용할 게이트웨이 이름입니다. | 예 |
| encryptedCredential | 게이트웨이에 의해 암호화된 자격 증명입니다. | 아니요 | 

## CassandraTable 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **CassandraTable** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| keyspace | Cassandra 데이터베이스의 키스페이스 또는 스키마의 이름입니다. | 예(**CassandraSource**의 **query**가 정의되지 않은 경우) |
| tableName | Cassandra 데이터베이스에 있는 테이블의 이름입니다. | 예(**CassandraSource**의 **query**가 정의되지 않은 경우) |


## CassandraSource 형식 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다.

원본이 **CassandraSource** 형식인 복사 작업의 경우 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| 쿼리 | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL-92 쿼리 또는 CQL 쿼리입니다. [CQL 참조](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)를 참조하세요. <br/><br/>SQL 쿼리를 사용할 경우 **keyspace name.table name**을 지정하여 쿼리하려는 테이블을 나타냅니다. | 아니요(데이터 집합의 tableName 및 keyspace가 정의된 경우) |
| consistencyLevel | 일관성 수준은 클라이언트 응용 프로그램에 데이터를 반환하기 전에 읽기 요청에 응답해야 하는 복제본 수를 지정합니다. Cassandra는 데이터의 지정된 수의 복제본이 읽기 요청을 충족하는지 확인합니다. | ONE, TWO, THREE, QUORUM, ALL, LOCAL\_QUORUM, EACH\_QUORUM, LOCAL\_ONE. 자세한 내용은 [데이터 일관성 구성](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html)을 참조하세요. | 아니요. 기본값은 ONE입니다. |  


### Cassandra에 대한 형식 매핑
Cassandra 형식 | .NET 기반 형식
--------------- | ---------------
ASCII |	문자열 
BIGINT | Int64
BLOB | Byte
BOOLEAN | Boolean
DECIMAL | 10진수
DOUBLE | Double
FLOAT | 단일
INET | 문자열
INT | Int32
TEXT | 문자열
TIMESTAMP | DateTime
TIMEUUID | Guid
UUID | Guid
VARCHAR | 문자열
VARINT | 10진수

> [AZURE.NOTE]  
컬렉션 형식(맵, 집합, 목록 등)에 대해서는 [가상 테이블을 사용하여 Cassandra 컬렉션 형식으로 작업](#work-with-collections-using-virtual-table) 섹션을 참조하세요.
> 
> 사용자 정의 형식은 지원되지 않습니다.
> 
> 이진 열의 길이와 문자열 열 길이는 4000보다 클 수 없습니다.

## 가상 테이블을 사용하여 컬렉션으로 작업
Azure Data Factory는 기본 제공 ODBC 드라이버를 사용하여 Cassandra 데이터베이스에 연결하고 데이터를 복사합니다. 맵, 집합 및 목록을 포함하는 컬렉션 형식의 경우 드라이버는 데이터를 해당 가상 테이블로 다시 정규화합니다. 특히, 테이블에 컬렉션 열이 포함되어 있으면 드라이버는 다음 가상 테이블을 생성합니다.

-	**기본 테이블**: 컬렉션 열을 제외하고 실제 테이블과 동일한 데이터가 포함되어 있습니다. 기본 테이블은 나타내는 실제 테이블과 동일한 이름을 사용합니다.
-	**가상 테이블**: 컬렉션 열에 대해 생성되며, 중첩된 데이터를 확장합니다. 컬렉션을 나타내는 가상 테이블 이름은 실제 테이블의 이름, 구분 기호 "_vt_" 및 열 이름을 사용하여 지정합니다.

가상 테이블은 실제 테이블의 데이터를 나타내며, 드라이버가 정규화되지 않은 데이터에 액세스할 수 있도록 합니다. 자세한 내용은 아래의 예제 섹션을 참조하세요. 가상 테이블을 쿼리 및 조인하여 Cassandra 컬렉션의 콘텐츠에 액세스할 수 있습니다.

[복사 마법사](data-factory-data-movement-activities.md#data-factory-copy-wizard)를 사용하여 가상 테이블을 비롯한 Cassandra 데이터베이스의 테이블 목록을 표시하고 내부의 데이터를 미리 볼 수 있습니다. 또한 복사 마법사에서 쿼리를 생성하고 결과가 유효한지 확인할 수도 있습니다.

### 예
예를 들어 아래의 "ExampleTable"은 "pk\_int"라는 정수 기본 키 열, value라는 텍스트 열, 목록 열, 맵 열, 집합 열("StringSet")을 포함하는 Cassandra 데이터베이스 테이블입니다.

pk\_int | 값 | 나열 | Map |	StringSet
------ | ----- | ---- | --- | --------
1 | "sample value 1" | ["1", "2", "3"] | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "sample value 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

드라이버는 이 단일 테이블을 나타내는 여러 개의 가상 테이블을 생성합니다. 가상 테이블의 외래 키 열은 실제 테이블의 기본 키 열을 참조하고, 가상 테이블 행에 해당하는 실제 테이블 행을 나타냅니다.

첫 번째 가상 테이블은 아래에 표시된 "ExampleTable"이라는 기본 테이블입니다. 기본 테이블에는 컬렉션을 제외하고 원래 데이터베이스 테이블과 동일한 데이터가 포함됩니다. 컬렉션은 테이블에서 생략된 후 다른 가상 테이블에서 확장됩니다.

pk\_int | 값
------ | -----
1 | "sample value 1"
3 | "sample value 3"

다음 표에서는 List, Map 및 StringSet 열의 데이터를 다시 정규화하는 가상 테이블을 보여 줍니다. 이름이 “\_index” 또는 “\_key”로 끝나는 열은 원본 목록이나 맵 내의 데이터 위치를 나타냅니다. 이름이 "\_value"로 끝나는 열에는 컬렉션에서 확장된 데이터가 포함됩니다.

#### 테이블 "ExampleTable\_vt\_List":
pk\_int | List\_index | List\_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### 테이블 “ExampleTable\_vt\_Map”:
pk\_int | Map\_key | Map\_value
------ | ------- | ---------
1 | S1 | A
1 | S2 | b
3 | S1 | t

#### 테이블 “ExampleTable\_vt\_StringSet”:
pk\_int | StringSet\_value
------ | ---------------
1 | A
1 | B
1 | C
3 | A
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

<!---HONumber=AcomDC_0803_2016-->