---
title: 소파베이스에서 Azure 코스모스 DB SQL API로 마이그레이션
description: CouchBase에서 Azure 코스모스 DB SQL API로 마이그레이션하기 위한 단계별 지침
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210945"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>소파베이스에서 Azure 코스모스 DB SQL API로 마이그레이션

Azure Cosmos DB는 확장 가능하고 전역적으로 분산된 완전히 관리되는 데이터베이스입니다. 데이터에 대한 짧은 대기 시간 액세스를 보장합니다. Azure Cosmos DB에 대한 자세한 내용은 [개요](introduction.md) 문서를 참조하세요. 이 문서에서는 Couchbase에 연결된 Java 응용 프로그램을 Azure Cosmos DB의 SQL API 계정으로 마이그레이션하는 지침을 제공합니다.

## <a name="differences-in-nomenclature"></a>명명성의 차이

다음은 Couchbase와 비교할 때 Azure Cosmos DB에서 다르게 작동하는 주요 기능입니다.

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|카우치베이스 서버| 계정       |
|양동이           | 데이터베이스      |
|양동이           | 컨테이너/수거 |
|JSON 문서    | 항목 / 문서 |

## <a name="key-differences"></a>주요 차이점

* Azure Cosmos DB에는 문서 내에 "ID" 필드가 있는 반면 Couchbase에는 버킷의 일부로 ID가 있습니다. "ID" 필드는 파티션 전체에서 고유합니다.

* Azure Cosmos DB는 분할 또는 샤딩 기술을 사용하여 확장합니다. 즉, 데이터를 여러 샤드/파티션으로 분할합니다. 이러한 파티션/샤드는 제공한 파티션 키 속성을 기반으로 만들어집니다. 파티션 키를 선택하여 읽기뿐만 아니라 쓰기 작업또는 읽기/쓰기도 최적화할 수 있습니다. 자세한 내용은 [분할](./partition-data.md) 문서를 참조하세요.

* Azure Cosmos DB에서는 컬렉션 이름이 이미 있으므로 최상위 계층 구조에서 컬렉션을 나타내는 데 필요하지 않습니다. 이 기능을 사용하면 JSON 구조가 훨씬 간단해집니다. 다음은 Couchbase와 Azure Cosmos DB 간의 데이터 모델 차이를 보여 주는 예제입니다.

   **소파베이스**: 문서 ID = "99FF444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure 코스모스 DB**: 아래와 같이 문서 내의 "ID"를 참조하십시오.

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>자바 SDK 지원

Azure Cosmos DB는 다음과 같은 SDK를 사용하여 다양한 Java 프레임워크를 지원합니다.

* 비동기 SDK
* 스프링 부츠 SDK

다음 섹션에서는 이러한 각 SDK를 사용하는 시기를 설명합니다. 세 가지 유형의 워크로드가 있는 예를 생각해 보십시오.

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>문서 저장소로 Couchbase & 스프링 데이터 기반 사용자 지정 쿼리

마이그레이션하는 워크로드가 Spring Boot 기반 SDK를 기반으로 하는 경우 다음 단계를 사용할 수 있습니다.

1. POM.xml 파일에 부모 추가:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. POM.xml 파일에 속성을 추가합니다.

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. POM.xml 파일에 종속성을 추가합니다.

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. 리소스 아래에 응용 프로그램 속성을 추가하고 다음을 지정합니다. URL, 키 및 데이터베이스 이름 매개 변수를 바꿔야 합니다.

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 모델에서 컬렉션의 이름을 정의합니다. 추가 주석을 지정할 수도 있습니다. 예를 들어, ID, 파티션 키를 명시적으로 나타냅니다.

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

다음은 CRUD 작업에 대한 코드 스니펫입니다.

### <a name="insert-and-update-operations"></a>작업 삽입 및 업데이트

*_repo* 저장소및 *문서의* 개체는 POJO 클래스의 개체입니다. 삽입하거나 `.save` upsert하는 데 사용할 수 있습니다 (지정된 ID가있는 문서가 있는 경우). 다음 코드 조각에서는 doc 개체를 삽입하거나 업데이트하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 줄 수 있습니다.

```_repo.save(doc);```

### <a name="delete-operation"></a>삭제 작업

doc 개체에 개체를 찾아 삭제하려면 반드시 ID와 파티션 키가 있어야 하는 다음 코드 조각을 고려하십시오.

```_repo.delete(doc);```

### <a name="read-operation"></a>읽기 작업

파티션 키를 지정하거나 지정하지 않고 문서를 읽을 수 있습니다. 파티션 키를 지정하지 않으면 파티션 간 쿼리로 처리됩니다. 다음 코드 샘플을 고려하면 먼저 ID 및 파티션 키 필드를 사용하여 작업을 수행합니다. 두 번째 예제에서는 파티션 키 필드를 지정하지 않고 & 일반 필드를 사용합니다.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

이제 Azure Cosmos DB에서 응용 프로그램을 사용할 수 있습니다. 이 문서에 설명된 예제에 대한 전체 코드 샘플은 [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>N1QL 쿼리를 사용하여 문서 리포지토리 & 소파베이스

N1QL 쿼리는 Couchbase에서 쿼리를 정의하는 방법입니다.

|N1QL 쿼리 | Azure 코스모스DB 쿼리|
|-------------------|-------------------|
|SELECT`TravelDocument`META( ).id `TravelDocument`AS ID, .* 어디에서 `TravelDocument` `_type` = "com.xx.xx.xxx.xxx.xxx.xxx" 및 국가 = '인도' 및 비자 SATISFIES m.type == '다중 엔트리'와 m.Country에서 ['인도', 부탄'] 주문 ` Validity` 별 DESC LIMIT 25 OFFSET 0   | select c.id, c.country='India'c._type = "com.xx.xx.xx.xxx.xxx.xxx.xxxx" 및 c.country = '인도' 및 m.type = '다중 엔트리' 및 m.Country IN('인도', '부탄') 주문 BY c.Validity DESC 오프셋 0 한계 25 |

N1QL 쿼리에서 다음과 같은 변경 사항을 확인할 수 있습니다.

* META 키워드를 사용하거나 첫 번째 수준 문서를 참조할 필요가 없습니다. 대신 컨테이너에 대한 사용자 고유의 참조를 만들 수 있습니다. 이 예제에서는 "c"로 간주했습니다(무엇이든 될 수 있음). 이 참조는 모든 첫 번째 수준 필드의 접두사로 사용됩니다. Fr 예, c.id, c.country 등

* 이제 "ANY" 대신 하위 문서에 조인을 수행하여 "m"과 같은 전용 별칭으로 참조할 수 있습니다. 하위 문서에 대한 별칭을 만든 후에는 별칭을 사용해야 합니다. 예를 들어, m.Country.

* 오프셋 시퀀스는 Azure Cosmos DB 쿼리에서 다르며 먼저 OFFSET다음 LIMIT를 지정해야 합니다. Azure Cosmos DB에 쿼리를 전달하는 동안 클라이언트 측에서 불필요한 오버헤드가 있을 수 있으므로 최대 사용자 정의 쿼리를 사용하는 경우 Spring Data SDK를 사용하지 않는 것이 좋습니다. 대신 우리는이 경우에 훨씬 효율적으로 활용 될 수있는 직접 비동기 자바 SDK를 가지고 있습니다.

### <a name="read-operation"></a>읽기 작업

다음 단계와 함께 비동기 Java SDK를 사용합니다.

1. POM.xml 파일에 다음 종속성을 구성합니다.

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 다음 예제와 같이 메서드를 사용하여 `ConnectionBuilder` Azure Cosmos DB에 대한 연결 개체를 만듭니다. 다음 코드가 한 번만 실행되도록 이 선언을 bean에 넣어야 합니다.

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. 쿼리를 실행하려면 다음 코드 조각을 실행해야 합니다.

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

이제 위의 방법의 도움으로 여러 쿼리를 전달하고 번거 로움없이 실행할 수 있습니다. 여러 쿼리로 분할할 수 있는 하나의 큰 쿼리를 실행해야 하는 경우 이전 쿼리 대신 다음 코드 조각을 시도하십시오.

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

이전 코드를 사용하면 쿼리를 병렬로 실행하고 분산 실행을 늘려 최적화할 수 있습니다. 또한 삽입 및 업데이트 작업도 실행할 수 있습니다.

### <a name="insert-operation"></a>삽입 작업

문서를 삽입하려면 다음 코드를 실행합니다.

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

그런 다음 다음과 같이 모노를 구독하십시오.

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>업서트 작동

Upsert 작업을 수행하려면 업데이트해야 하는 문서를 지정해야 합니다. 전체 문서를 가져오려면 제목 읽기 작업아래에 언급된 스니펫을 사용한 다음 필요한 필드를 수정할 수 있습니다. 다음 코드 스니펫은 문서를 업서트합니다.

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
그런 다음 모노를 구독합니다. 삽입 작업에서 모노 구독 스니펫을 참조하십시오.

### <a name="delete-operation"></a>삭제 작업

다음 스니펫은 삭제 작업을 수행합니다.

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

그런 다음 모노구독을 구독하고 삽입 작업에서 모노 구독 스니펫을 참조하십시오. 전체 코드 샘플은 [CouchbaseToCosmosDB-비동기인 봄](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="couchbase-as-a-keyvalue-pair"></a>키/값 쌍으로 소파베이스

쿼리 대신 조회를 수행할 수 있는 간단한 작업 유형입니다. 키/값 쌍에 다음 단계를 사용합니다.

1. "/ID"를 기본 키로 두면 특정 파티션에서 직접 조회 작업을 수행할 수 있습니다. 컬렉션을 만들고 "/ID"를 파티션 키로 지정합니다.

1. 인덱싱을 완전히 끕지. 조회 작업을 실행하므로 인덱싱 오버헤드를 수행할 수 있는 지점은 없습니다. 인덱싱을 해제하려면 Azure 포털에 로그인하여 Azure Cosmos DB 계정으로 이동합니다. 데이터 **탐색기를**열고 **데이터베이스** 및 **컨테이너를**선택합니다. 조정 **& 설정** 탭을 열고 **인덱싱 정책을 선택합니다.** 현재 인덱싱 정책은 다음과 같습니다.
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   위의 인덱싱 정책을 다음 정책으로 바꿉꿉입니다.

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. 다음 코드 조각을 사용하여 연결 개체를 만듭니다. 연결 객체(에 @Bean 배치하거나 정으로 만들):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

이제 다음과 같이 CRUD 작업을 실행할 수 있습니다.

### <a name="read-operation"></a>읽기 작업

항목을 읽으려면 다음 스니펫을 사용합니다.

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>삽입 작업

항목을 삽입하려면 다음 코드를 수행할 수 있습니다.

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

그런 다음 다음과 같이 모노를 구독하십시오.

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>업서트 작동

항목값을 업데이트하려면 아래 코드 조각을 참조하십시오.

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
그런 다음 모노구독을 구독하고 삽입 작업에서 모노 구독 스니펫을 참조하십시오.

### <a name="delete-operation"></a>삭제 작업

다음 코드 조각을 사용하여 delete 작업을 실행합니다.

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

그런 다음 모노구독을 구독하고 삽입 작업에서 모노 구독 스니펫을 참조하십시오. 전체 코드 샘플은 [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="data-migration"></a>데이터 마이그레이션

데이터를 마이그레이션하는 방법에는 두 가지가 있습니다.

* **Azure 데이터 팩터리 사용:** 이 방법은 데이터를 마이그레이션하는 데 가장 권장되는 방법입니다. 소스를 Couchbase로 구성하고 Azure Cosmos DB SQL API로 싱크하면 Azure [Cosmos DB 데이터 팩토리 커넥터](../data-factory/connector-azure-cosmos-db.md) 문서를 참조하여 자세한 단계를 확인하십시오.

* **Azure Cosmos DB 데이터 가져오기 도구를 사용합니다.** 이 옵션은 적은 양의 데이터로 VM을 사용하여 마이그레이션하는 것이 좋습니다. 자세한 단계는 [데이터 가져오기](./import-data.md) 문서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

* 성능 테스트를 수행하려면 Azure Cosmos DB 문서를 [사용하여 성능 및 확장 테스트를 참조하세요.](./performance-testing.md)
* 코드를 최적화하려면 Azure Cosmos DB 문서에 [대한 성능 팁을 참조하세요.](./performance-tips-async-java.md)
* 자바 비동기 V3 SDK, [SDK 참조](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub 리포지토리를 탐색합니다.
