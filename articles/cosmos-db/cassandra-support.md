---
title: Azure Cosmos DB Cassandra API에서 지원하는 Apache Cassandra 기능 및 명령
description: Azure Cosmos DB Cassandra API에서 지원되는 Apache Cassandra 기능에 대해 알아봅니다.
services: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 5a12a65e03e7ac104eb7b09b116f7c463bbb5b98
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240718"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API에서 지원하는 Apache Cassandra 기능 

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. CQL(Cassandra Query Language) v4 [유선 프로토콜](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) 호환 오픈 소스 Cassandra 클라이언트 [드라이버](http://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)를 통해 Azure Cosmos DB Cassandra API와 통신할 수 있습니다. 

Azure Cosmos DB Cassandra API를 사용하면 Apache Cassandra API의 기능뿐 아니라 Azure Cosmos DB에서 제공되는 엔터프라이즈 기능도 활용할 수 있습니다. 엔터프라이즈 기능에는 [전역 배포](distribute-data-globally.md), [자동 규모 확장 분할](partition-data.md), 가용성 및 대기 시간 보장, 미사용 시 암호화, 백업 등 여러 가지가 있습니다.

## <a name="cassandra-protocol"></a>Cassandra 프로토콜 

Azure Cosmos DB Cassandra API는 CQL 버전 **v4**와 호환됩니다. 지원되는 CQL 명령, 도구, 제한 사항 및 예외는 아래에 나와 있습니다. 이러한 프로토콜을 인식하는 모든 클라이언트 드라이버는 Azure Cosmos DB Cassandra API에 연결할 수 있습니다.

## <a name="cassandra-driver"></a>Cassandra 드라이버

Azure Cosmos DB Cassandra API에서 지원하는 Cassandra 드라이버의 버전은 다음과 같습니다.

* [Java 3.5 이상](https://github.com/datastax/java-driver)  
* [C# 3.5 이상](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5 이상](https://github.com/datastax/nodejs-driver)  
* [Python 3.15 이상](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>CQL 데이터 형식 

Azure Cosmos DB Cassandra API는 다음 CQL 데이터 형식을 지원합니다.

* ascii  
* bigint  
* Blob  
* 부울  
* counter  
* date  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* text  
* 실시간  
* timestamp  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>CQL 함수

Azure Cosmos DB Cassandra API는 다음 CQL 함수를 지원합니다.

* 토큰  
* Blob 변환 함수 
  * typeAsBlob(value)  
  * blobAsType(value)
* UUID 및 timeuuid 함수 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date)  


## <a name="cassandra-query-language-limits"></a>Cassandra Query Language의 제한

Azure Cosmos DB Cassandra API에는 테이블에 저장할 수 있는 데이터 크기에 제한이 없습니다. 그러므로 파티션 키 제한 범위 내에서 수백 TB/PB에 달하는 데이터를 저장할 수 있습니다. 마찬가지로, 모든 엔터티 또는 그에 해당하는 행에도 열 수는 제한되지 않습니다. 그러나 엔터티의 총 크기는 2MB를 초과할 수 없습니다.

## <a name="tools"></a>도구 

Azure Cosmos DB Cassandra API는 관리되는 서비스 플랫폼입니다. 그러므로 클러스터 관리 과정의 오버헤드가 발생하지 않으며 가비지 수집기, JVM(Java Virtual Machine), nodetool 등의 유틸리티를 사용할 필요가 없습니다. 그리고 이진 CQLv4 호환성을 활용하는 cqlsh 등의 도구를 지원합니다. 

* 계정 관리를 위해 지원되는 다른 메커니즘으로는 Azure Portal의 데이터 탐색기, 메트릭, 로그 진단, PowerShell 및 CLI 등이 있습니다.

## <a name="cql-shell"></a>CQL Shell  

Apache Cassandra 3.1.1과 함께 제공되는 CQLSH 명령줄 유틸리티는 다음 환경 변수가 사용하도록 설정된 상태로 기본 작동합니다.

다음 명령을 실행하기 전에 [cacerts 저장소에 Baltimore 루트 인증서를 추가](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store)합니다. 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```

## <a name="cql-commands"></a>CQL 명령

Azure Cosmos DB는 Cassandra API 계정에서 다음 데이터베이스 명령을 지원합니다.

* CREATE KEYSPACE 
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - 로깅되지 않는 명령만 지원됨 
* 삭제

CQLV4 호환 SDK를 통해 실행하는 모든 CRUD 작업에서는 오류, 사용한 요청 단위 및 활동 ID와 관련된 추가 정보가 반환됩니다. 프로비전된 리소스를 과도하게 사용하지 않으려면 리소스 거버넌스를 고려하여 삭제 및 업데이트 명령을 처리해야 합니다. 
* 참고: gc_grace_seconds 값은 지정하는 경우 0이어야 합니다.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            string value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>일관성 매핑 

Azure Cosmos DB Cassandra API에서는 읽기 작업을 일관되게 수행할지 여부를 선택할 수 있습니다. 모든 쓰기 작업은 계정 일치 여부에 관계없이 항상 쓰기 성능 SLA에 따라 기록됩니다.

## <a name="permission-and-role-management"></a>권한 및 역할 관리

Azure Cosmos DB는 [Azure Portal](https://portal.azure.com)을 통해 가져올 수 있는 RBAC(역할 기반 액세스 제어)와 읽기-쓰기 및 읽기 전용 암호/키를 지원합니다. 데이터 평면 활동을 위한 사용자와 역할은 아직 지원되지 않습니다. 

## <a name="planned-support"></a>계획된 지원 
* 타임스탬프와 TTL을 모두 사용합니다.  
* create keyspace 명령의 지역 이름은 현재 무시됩니다. 데이터 배포는 기본 Cosmos DB 플랫폼에서 구현되고, 포털 또는 PowerShell을 통해 계정에 제공됩니다. 





## <a name="next-steps"></a>다음 단계

- Java 응용 프로그램을 사용하여 [Cassandra API 계정, 데이터베이스 및 테이블 만들기](create-cassandra-api-account-java.md)를 시작합니다.

