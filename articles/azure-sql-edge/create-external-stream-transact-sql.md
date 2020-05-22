---
title: CREATE EXTERNAL STREAM(Transact-SQL) - Azure SQL Edge(미리 보기)
description: Azure SQL Edge(미리 보기)의 CREATE EXTERNAL STREAM 문에 대해 알아보기
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d4ad446d43c90eee1c48ee2ba94585242805fa7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595402"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM(Transact-SQL)

EXTERNAL STREAM 개체는 입력과 출력이라는 두 가지 용도를 모두 갖고 있습니다. Azure Event 또는 IoT 허브 같은 이벤트 수집 서비스에서 스트리밍 데이터를 쿼리하는 입력으로 사용할 수도 있고, 스트리밍 쿼리의 결과를 저장하는 위치와 방법을 지정하는 출력으로 사용할 수도 있습니다.

EXTERNAL STREAM을 지정하고 이벤트 허브 또는 Blob 스토리지 같은 서비스의 출력과 입력으로 만들 수도 있습니다. 스트리밍 쿼리가 결과를 외부 스트림에 출력으로 유지하고 또 다른 스트리밍 쿼리가 입력과 동일한 외부 스트림에서 읽는 시나리오를 연결하는 것이 그 목적입니다. 


## <a name="syntax"></a>구문

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>인수


- [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **위치**: 데이터 원본에서 실제 데이터 또는 위치의 이름을 지정합니다. Edge Hub 또는 Kafka 스트림 개체의 경우 위치는 데이터를 읽거나 쓸 Edge Hub 또는 Kafka 토픽의 이름을 지정합니다.
- **INPUT_OPTIONS**: 옵션을 스트리밍 쿼리의 입력인 Event 및 IOT Hubs 같은 서비스의 키-값 쌍으로 지정합니다.
    - CONSUMER_GROUP: Event 및 IoT Hubs는 한 소비자 그룹 내의 reader 수를 5로 제한합니다. 이 필드를 비워 두면 '$Default' 소비자 그룹이 사용됩니다.
      - 적용 대상: Event 및 IOT Hubs
    - TIME_POLICY: 지연 이벤트 또는 잘못된 이벤트가 허용 오차 값을 전달할 때 이벤트를 삭제할 것인지 아니면 이벤트 시간을 조정할 것인지 여부를 설명합니다.
      - 적용 대상: Event 및 IOT Hubs
    - LATE_EVENT_TOLERANCE: 허용되는 최대 시간 지연입니다. 지연은 이벤트의 타임스탬프와 시스템 시계의 차이를 나타냅니다.
      - 적용 대상: Event 및 IOT Hubs
    - OUT_OF_ORDER_EVENT_TOLERANCE: 이벤트가 입력에서 스트리밍 쿼리로 이동한 후 순서 없이 도착할 수 있습니다. 이러한 이벤트를 그대로 허용할 수도 있고, 일정 시간 동안 일시 중지하고 순서를 변경할 수도 있습니다.
      - 적용 대상: Event 및 IOT Hubs
- **OUTPUT_OPTIONS**: 옵션을 스트리밍 쿼리의 출력인 지원되는 서비스의 키-값 쌍으로 지정합니다. 
  - REJECT_POLICY:  DROP | RETRY 데이터 변환 오류가 발생할 때 데이터 오류 처리 정책을 지정합니다. 
    - 적용 대상: 지원되는 모든 출력 
  - MINIMUM_ROWS:  
    출력에 작성된 일괄 처리마다 필요한 최소 행 수입니다. Parquet의 경우 일괄 처리마다 새 파일을 만듭니다. 
    - 적용 대상: 지원되는 모든 출력 
  - MAXIMUM_TIME:  
    일괄 처리당 최대 대기 시간입니다. 이 시간이 지나면 최소 행 수 요구 사항이 충족되지 않아도 일괄 처리가 출력에 기록됩니다. 
    - 적용 대상: 지원되는 모든 출력 
  - PARTITION_KEY_COLUMN:  
    파티션 키에 사용되는 열입니다. 
    - 적용 대상: 이벤트 허브 및 Service Bus 토픽 
  - PROPERTY_COLUMNS:  
    쉼표로 구분된 출력 열 이름 목록이며, 메시지에 사용자 지정 속성으로 첨부되는 방식으로 제공됩니다.  
    - 적용 대상: 이벤트 허브 및 Service Bus 토픽과 큐 
  - SYSTEM_PROPERTY_COLUMNS:  
    Service Bus 메시지에서 채워질 시스템 속성 이름 및 출력 열의 JSON 형식 이름/값 쌍 컬렉션입니다. 예: { "MessageId":   "column1", "PartitionKey": "column2"} 
    - 적용 대상: Service Bus 토픽 및 큐 
  - PARTITION_KEY:  
    파티션 키를 포함하는 출력 열의 이름입니다. 파티션 키는 특정 테이블 내에서 분할의 고유 식별자 역할을 하며 엔터티 기본 키의 첫 번째 부분을 형성합니다. 크기가 최대 1KB인 문자열 값입니다. 
    - 적용 대상: Table Storage 및 Azure 함수 
  - ROW_KEY:  
    행 키를 포함하는 출력 열의 이름입니다. 행 키는 주어진 파티션 내 엔터티의 고유 식별자입니다. 엔터티 기본 키의 두 번째 부분을 형성합니다. 행 키는 크기가 최대 1KB인 문자열 값입니다. 
    - 적용 대상: Table Storage 및 Azure 함수 
  - BATCH_SIZE:  
    최대 레코드 수가 100개인 테이블 스토리지의 트랜잭션 수를 나타냅니다. Azure Functions의 경우 호출마다 함수로 전송되는 일괄 처리 크기(바이트)를 나타내며 기본값은 256kB입니다. 
    - 적용 대상: Table Storage 및 Azure 함수 
  - MAXIMUM_BATCH_COUNT:  
    Azure 함수 호출마다 함수에 전송되는 최대 이벤트 수이며 기본값은 100입니다. SQL Database의 경우 모든 대량 삽입 트랜잭션과 함께 전송되는 최대 레코드 수이며 기본값은 10,000입니다. 
    - 적용 대상: SQL Database 및 Azure 함수 
  - STAGING_AREA: EXTERNAL DATA SOURCE 개체에서Blob Storage로 적용. SQL Data Warehouse에 높은 처리량의 데이터를 수집하기 위한 준비 영역입니다. 
    - 적용 대상: SQL Data Warehouse


## <a name="examples"></a>예

### <a name="example-1---edgehub"></a>예제 1 - EdgeHub

유형: 입력 또는 출력<br>
매개 변수
- 입력 또는 출력
  - Alias 
  - 이벤트 직렬화 형식 
  - Encoding 
- 입력만 해당: 
  - 이벤트 압축 유형 

구문

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>예제 2 - Azure SQL Database, Azure SQL Edge, SQL Server

유형: 출력<br>
매개 변수
- 출력 별칭  
- 데이터베이스(SQL Database에 필요) 
- 서버(SQL Database에 필요) 
- 사용자 이름(SQL Database에 필요) 
- 암호(SQL Database에 필요) 
- 테이블 
- 모든 입력 파티션을 단일 쓰기에 병합하거나 이전 쿼리 단계 또는 입력의 파티션 구성표를 상속(SQL Database에 필요) 
- 최대 일괄 처리 수 

구문

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>예제 3 - Kafka

유형: 입력<br>
매개 변수

- Kafka 부트스트랩 서버 
- Kafka 토픽 이름 
- 원본 파티션 수 

구문

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>예제 4 - Blob 스토리지

유형: 입력 또는 출력<br>
매개 변수
- 입력 또는 출력:
  - Alias 
  - 스토리지 계정 
  - Storage 계정 키 
  - 컨테이너 
  - 경로 패턴 
  - 날짜 형식 
  - 시간 형식 
  - 이벤트 직렬화 형식 
  - Encoding 
- 입력만 해당: 
  - 파티션(입력) 
  - 이벤트 압축 유형(입력) 
- 출력만 해당: 
  - 최소 행 수(출력) 
  - 최대 시간(출력) 
  - 인증 모드(출력) 

구문

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>예제 5 - 이벤트 허브

유형: 입력 또는 출력<br>
매개 변수
- 입력 또는 출력:
  - Alias 
  - Service Bus 네임스페이스 
  - 이벤트 허브 이름 
  - 이벤트 허브 정책 이름 
  - 이벤트 허브 정책 키 
  - 이벤트 직렬화 형식 
  - Encoding 
- 입력만 해당: 
  - 이벤트 허브 소비자 그룹 
  - 이벤트 압축 유형 
- 출력만 해당: 
  - 파티션 키 열 
  - 속성 열 

구문

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>예제 6 - IOT Hub

유형: 입력<br>
매개 변수

- 입력 별칭 
- IoT Hub 
- 엔드포인트 
- 공유 액세스 정책 이름 
- 공유 액세스 정책 키 
- 소비자 그룹 
- 이벤트 직렬화 형식 
- Encoding 
- 이벤트 압축 유형 

구문

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>예제 7 - Azure Synapse Analytics(이전의 SQL Data Warehouse)

유형: 출력<br>
매개 변수
- 출력 별칭 
- 데이터베이스 
- 서버 
- 사용자 이름 
- 암호 
- 테이블 
- 준비 영역(COPY의 경우) 

구문

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>예제 8 - Table Storage

유형: 출력<br>
매개 변수
- 출력 별칭 
- 스토리지 계정 
- Storage 계정 키 
- 테이블 이름 
- 파티션 키 
- 행 키. 
- Batch 크기 

구문

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>예제 9 - Service Bus 토픽(큐와 동일한 속성)

유형: 출력<br>
매개 변수
- 출력 별칭 
- Service Bus 네임스페이스 
- 항목 이름 
- 토픽 정책 이름 
- 토픽 정책 키 
- 속성 열 
- 시스템 속성 열 
- 이벤트 직렬화 형식 
- Encoding 

구문

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>예제 10 - Cosmos DB

유형: 출력<br>
매개 변수
- 출력 별칭 
- 계정 ID 
- 계정 키 
- 데이터베이스 
- 컨테이너 이름 
- 문서 ID 


구문

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>예제 11 - Power BI

유형: 출력<br>
매개 변수
- 출력 별칭 
- 데이터 세트 이름 
- 테이블 이름 


구문

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>예제 12 - Azure 함수

유형: 출력<br>
매개 변수
- 함수 앱 
- 함수 
- 키 
- 최대 일괄 처리 크기 
- 최대 일괄 처리 수 

구문

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>참고 항목

- [ALTER EXTERNAL STREAM(Transact-SQL)](alter-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM(Transact-SQL)](drop-external-stream-transact-sql.md) 

