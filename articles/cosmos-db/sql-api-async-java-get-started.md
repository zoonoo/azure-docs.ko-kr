---
title: Azure Cosmos DB Async Java SDK를 사용하여 Java 응용 프로그램 빌드 | Microsoft Docs
description: 이 자습서에서는 Azure Cosmos DB SQL API 계정을 사용하여 Async Java 응용 프로그램을 통해 데이터를 저장 및 액세스하는 방법을 보여줍니다.
keywords: NoSQL 자습서, 온라인 데이터베이스, Java 콘솔 응용 프로그램
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: aa2613f7cb73c2c338189aaaa48587c49a3093f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962210"
---
# <a name="build-a-java-application-by-using-azure-cosmos-db-async-java-sdk"></a>Azure Cosmos DB Async Java SDK를 사용하여 Java 응용 프로그램 빌드 

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [비동기 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Azure Cosmos DB는 전 세계에 배포된 다중 모델 데이터베이스입니다. 이 자습서에서는 Azure Cosmos DB SQL API 계정을 사용하여 Async Java 응용 프로그램을 통해 데이터를 저장 및 액세스하는 방법을 보여줍니다. 

다음 항목에 대해서 다룹니다.

* Azure Cosmos DB 계정 만들기 및 연결
* 솔루션 구성
* 컬렉션 만들기
* JSON 문서 만들기
* 컬렉션 쿼리

이제 시작하겠습니다.

## <a name="prerequisites"></a>필수 조건
다음 항목이 있는지 확인합니다.

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads)
* [JDK(Java Development Kit) 8 이상](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi)

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1단계: Azure Cosmos DB 계정 만들기
Azure Cosmos DB 계정을 만들어 보겠습니다. 사용하려는 계정이 이미 있는 경우 [GitHub 프로젝트 복제](#GitClone)로 건너뛸 수 있습니다. Azure Cosmos DB 에뮬레이터를 사용할 경우 [Azure Cosmos DB 에뮬레이터](local-emulator.md)의 단계에 따라 에뮬레이터를 설정하고 [GitHub 프로젝트 복제](#GitClone)로 건너뜁니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>2단계: GitHub 프로젝트 복제
[Azure Cosmos DB 및 Java 시작](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)의 경우 GitHub 리포지토리를 복제하여 시작할 수 있습니다. 예를 들어 로컬 디렉터리에서 다음을 실행하여 샘플 프로젝트를 로컬로 검색합니다.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started

```
디렉터리에는 프로젝트의 `pom.xml` 및 `Main.java`을 비롯한 Java 소스 코드를 포함하는 `src/main/java/com/microsoft/azure/cosmosdb/sample` 폴더를 포함합니다. 여기서는 Azure Cosmos DB를 사용하여 문서 만들기 및 컬렉션 내에서 데이터 쿼리와 같은 단순한 작업을 수행하는 방법을 보여 줍니다. `pom.xml`에는 [Maven의 Azure Cosmos DB Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)에 대한 종속성이 포함되어 있습니다.

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>3단계: Azure Cosmos DB 계정에 연결
다음으로 [Azure Portal](https://portal.azure.com)로 다시 이동하여 엔드포인트와 기본 마스터 키를 검색합니다. Azure Cosmos DB 엔드포인트와 기본 키는 응용 프로그램에서 연결할 위치를 식별하고 Azure Cosmos DB에서 응용 프로그램의 연결을 신뢰하는 데 필요합니다. `AccountSettings.java` 파일은 기본 키 및 URI 값을 포함하고 있습니다. 

Azure Portal에서 Azure Cosmos DB 계정으로 이동한 다음 **키**를 클릭합니다. 포털에서 URI 및 기본 키를 복사하여 `AccountSettings.java` 파일에 붙여넣습니다. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Java 콘솔 응용 프로그램을 만들기 위해 NoSQL 자습서에서 사용하는 Azure Portal의 스크린샷입니다. 액티브 허브, Azure Cosmos DB 계정 블레이드의 키 단추 및 키 블레이드의 URI, 기본 키 및 보조 키 값이 강조 표시된 Azure Cosmos DB 계정을 보여 줌][keys]

## <a name="step-4-initialize-the-client-object"></a>4단계: 클라이언트 개체 초기화
호스트 URI와 “AccountSettings.java” 파일에 정의된 기본 키 값을 사용하여 클라이언트 개체를 초기화합니다.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>5단계: 데이터베이스 만들기

DocumentClient 클래스의 createDatabaseIfNotExists() 메서드를 사용하여 Azure Cosmos DB [데이터베이스](sql-api-resources.md#databases)를 만들 수 있습니다. 데이터베이스는 여러 컬렉션으로 분할된 JSON 문서 저장소의 논리적 컨테이너입니다.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>6단계: 컬렉션 만들기

> [!WARNING]
> **createCollection**은 가격 책정 의미가 포함된 예약된 처리량이 있는 새 컬렉션을 만듭니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.
> 
> 
DocumentClient 클래스의 createDocumentCollectionIfNotExists() 메서드를 사용하여 컬렉션을 만들 수 있습니다. 컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다.

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>7단계: JSON 문서 만들기

DocumentClient 클래스의 createDocument 메서드를 사용하여 [문서](sql-api-resources.md#documents)를 만들 수 있습니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 하나 이상의 문서를 삽입할 수 있습니다. “src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java” 파일은 패밀리 JSON 문서를 정의합니다. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>8단계: Azure Cosmos DB 리소스 쿼리

Azure Cosmos DB는 각 컬렉션에 저장된 JSON 문서에 대해 [다양한 쿼리](sql-api-sql-query.md)를 지원합니다. 다음 샘플 코드에서는 [queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments) 메서드와 함께 SQL 구문을 사용하여 Azure Cosmos DB의 문서를 쿼리하는 방법을 보여 줍니다.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>9단계: Java 콘솔 응용 프로그램을 모두 함께 실행
콘솔에서 응용 프로그램을 실행하려면 프로젝트 폴더로 이동하고 Maven을 사용하여 컴파일합니다.

```bash
mvn package
```

`mvn package`를 실행하면 Maven에서 최신 Azure Cosmos DB 라이브러리를 다운로드하고 `GetStarted-0.0.1-SNAPSHOT.jar`을 생성합니다. 그 후에 다음을 실행하여 앱을 실행합니다.

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```
축하합니다! 이 NoSQL 자습서를 완료했으며 실행되는 Java 콘솔 응용 프로그램이 생겼습니다.

## <a name="next-steps"></a>다음 단계
* Java 웹앱 자습서가 필요한가요? [Azure Cosmos DB를 사용하여 Java로 웹 응용 프로그램 빌드](sql-api-java-application.md)를 참조하세요.
* [Azure Cosmos DB 계정 모니터링](monitor-accounts.md) 방법에 대해 알아보세요.
* [쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
