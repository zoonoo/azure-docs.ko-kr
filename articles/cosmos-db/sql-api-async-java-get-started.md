---
title: '자습서: 비동기 Java SDK를 사용하여 Azure Cosmos DB에서 SQL API 계정을 관리하는 Java 앱 빌드'
description: 이 자습서에서는 비동기 Java 애플리케이션을 사용하여 Azure Cosmos DB에서 SQL API 계정 내에 데이터를 저장하고 액세스하는 방법을 보여 줍니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/15/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: b46914728c684fe4b28cb1325afb1e0b662522ad
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475819"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>자습서: 비동기 Java SDK를 사용하여 SQL API 계정에 저장된 데이터를 관리하는 Java 앱 빌드

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET(미리 보기)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core(미리 보기)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [비동기 Java](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

개발자는 NoSQL 문서 데이터를 사용하는 애플리케이션을 가지고 있을 수도 있습니다. Azure Cosmos DB에서 SQL API 계정을 사용하여 이 문서 데이터를 저장하고 액세스할 수 있습니다. 이 자습서에서는 비동기 Java SDK를 사용하여 문서 데이터를 저장하고 관리하는 Java 애플리케이션을 빌드하는 방법을 보여 줍니다. 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Cosmos 계정 만들기 및 연결
> * 솔루션 구성
> * 컬렉션 만들기
> * JSON 문서 만들기
> * 컬렉션 쿼리

## <a name="prerequisites"></a>필수 조건

다음 리소스가 있는지 확인합니다.

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 

* [Git](https://git-scm.com/downloads)

* [JDK(Java Development Kit) 8 이상](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

다음 단계를 사용하여 Azure Cosmos 계정을 만듭니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>GitHub 리포지토리 복제

[Azure Cosmos DB 및 Java 시작](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)에 대한 GitHub 리포지토리를 복제합니다. 예를 들어 로컬 디렉터리에서 다음을 실행하여 프로젝트 샘플을 로컬로 검색합니다.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

디렉터리에는 `Main.java`를 포함하여 Java 소스 코드가 들어 있는 `pom.xml` 파일 및 `src/main/java/com/microsoft/azure/cosmosdb/sample` 폴더가 있습니다. 프로젝트에는 문서 만들기, 컬렉션 내 데이터 쿼리와 같은 Azure Cosmos DB 작업을 수행하는 데 필요한 코드가 포함되어 있습니다. `pom.xml` 파일에는 [Maven의 Azure Cosmos DB Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)에 대한 종속성이 포함되어 있습니다.

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Azure Cosmos 계정에 연결

다음으로, [Azure Portal](https://portal.azure.com)로 다시 이동하여 엔드포인트와 기본 마스터 키를 검색합니다. Azure Cosmos DB 엔드포인트와 기본 키는 응용 프로그램에서 연결할 위치를 식별하고 Azure Cosmos DB에서 응용 프로그램의 연결을 신뢰하는 데 필요합니다. `AccountSettings.java` 파일은 기본 키 및 URI 값을 포함하고 있습니다. 

Azure Portal에서 Azure Cosmos 계정으로 이동한 다음, **키**를 클릭합니다. 포털에서 URI 및 기본 키를 복사하여 `AccountSettings.java` 파일에 붙여넣습니다. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![포털에서 키 가져오기 스크린샷][keys]

## <a name="initialize-the-client-object"></a>클라이언트 개체 초기화

클라이언트 개체는 "AccountSettings.java" 파일에 정의된 호스트 URI와 기본 키 값을 사용하여 초기화합니다.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>데이터베이스 만들기

DB 데이터베이스는 DocumentClient 클래스의 `createDatabaseIfNotExists()` 메서드를 사용하여 Azure Cosmos 만듭니다. 데이터베이스는 여러 컬렉션으로 분할된 JSON 문서 저장소의 논리적 컨테이너입니다.

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

## <a id="CreateColl"></a>컬렉션 만들기

컬렉션은 DocumentClient 클래스의 `createDocumentCollectionIfNotExists()` 메서드를 사용하여 만들 수 있습니다. 컬렉션은 JSON 문서 및 관련 JavaScript 애플리케이션 논리의 컨테이너입니다.

> [!WARNING]
> **createCollection**은 가격 책정 의미가 포함된 예약된 처리량이 있는 새 컬렉션을 만듭니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.
> 
> 

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

## <a id="CreateDoc"></a>JSON 문서 만들기

문서는 DocumentClient 클래스의 createDocument 메서드를 사용하여 만듭니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 하나 이상의 문서를 삽입할 수 있습니다. "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" 파일은 패밀리 JSON 문서를 정의합니다. 

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

## <a id="Query"></a>Azure Cosmos DB 리소스 쿼리

Azure Cosmos DB는 각 컬렉션에 저장된 JSON 문서에 대해 다양한 쿼리를 지원합니다. 다음 샘플 코드에서는 `queryDocuments` 메서드와 함께 SQL 구문을 사용하여 Azure Cosmos DB에서 문서를 쿼리하는 방법을 보여 줍니다.

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

## <a id="Run"></a>Java 콘솔 애플리케이션 실행

콘솔에서 애플리케이션을 실행하려면 프로젝트 폴더로 이동하고 Maven을 사용하여 컴파일합니다.

```bash
mvn package
```

`mvn package`를 실행하면 Maven에서 최신 Azure Cosmos DB 라이브러리를 다운로드하고 `GetStarted-0.0.1-SNAPSHOT.jar`을 생성합니다. 그 후에 다음을 실행하여 앱을 실행합니다.

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

이제 이 NoSQL 자습서를 완료했으며, Java 콘솔 애플리케이션이 작동하고 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 경우 리소스 그룹, Azure Cosmos 계정 및 모든 관련 리소스를 삭제해도 됩니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고, **삭제**를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 비동기 Java SDK를 사용하여 Azure Cosmos DB에서 SQL API 데이터를 관리하는 Java 앱을 빌드하는 방법을 알아보았습니다. 이제 다음 문서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [JavaScript SDK 및 Azure Cosmos DB를 사용하여 Node.js 콘솔 앱 빌드](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
