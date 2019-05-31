---
title: Java로 Azure Cosmos DB 문서 데이터베이스 만들기
description: Azure Cosmos DB SQL API에 연결하고 쿼리하는 데 사용할 수 있는 Java 코드 샘플을 제공합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: cb4ad20067783d8d6bd649710950fea51a22a454
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979133"
---
# <a name="quickstart-build-a-java-application-using-azure-cosmos-db-sql-api-account"></a>빠른 시작: Azure Cosmos DB SQL API 계정을 사용하여 Java 애플리케이션 빌드


> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET(미리 보기)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

이 빠른 시작에서는 Java 응용 프로그램을 사용하여 Azure Cosmos DB [SQL API](sql-api-introduction.md) 계정 리소스를 만들고 관리하는 방법을 보여줍니다. 먼저 Azure Portal을 사용하여 Azure Cosmos DB SQL API 계정을 만들고, [SQL Java SDK](sql-api-sdk-async-java.md)를 사용하여 Java 앱을 만들고, Java 응용 프로그램을 사용하여 Cosmos DB 계정에 리소스를 추가합니다. 이 빠른 시작의 지침은 Java를 실행할 수 있는 모든 운영 체제에 적용될 수 있습니다. 이 빠른 시작을 완료하면 UI를 사용하여 또는 프로그래밍 방식으로 Cosmos DB 데이터베이스 컬렉션을 만들고 수정하는 작업을 익힐 수 있습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

또한, 

* [JDK(Java Development Kit) 버전 8](https://aka.ms/azure-jdks)
    * JAVA_HOME 환경 변수가 반드시 JDK가 설치된 폴더를 지정하도록 설정합니다.
* [Maven](https://maven.apache.org/) 이진 아카이브 [다운로드](https://maven.apache.org/download.cgi) 및 [설치](https://maven.apache.org/install.html)
    * Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다.
* [Git](https://www.git-scm.com/)
    * Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 실행할 수 있습니다.

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

문서 데이터베이스를 만들기 전에 Azure Cosmos DB를 사용하여 SQL API 계정을 만들어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>컬렉션 추가

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>샘플 데이터 추가

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>데이터 쿼리

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 코드 사용으로 전환해 보겠습니다. GitHub에서 SQL API 앱을 복제하고 연결 문자열을 설정한 다음 실행해보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [앱 실행](#run-the-app)으로 넘어갈 수 있습니다. 

* `AsyncDocumentClient` 초기화 [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient)에서는 Azure Cosmos DB 데이터베이스 서비스에 대한 클라이언트 쪽 논리적 표현을 제공합니다. 이 클라이언트는 서비스에 대한 요청을 구성하고 실행하는 데 사용됩니다.

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* [데이터베이스](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.database) 생성

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection) 생성

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document) 메서드를 사용하여 문서 생성

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* JSON에 대한 SQL 쿼리는 [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments?view=azure-java-stable) 메서드를 사용하여 수행됩니다.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>앱 실행

이제 Azure Portal로 돌아가서 연결 문자열 정보를 가져오고 엔드포인트 정보를 사용하여 앱을 시작합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다.


1. git 터미널 창에서 샘플 코드 폴더로 `cd`합니다.

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. git 터미널 창에서 다음 명령을 사용하여 필요한 Java 패키지를 설치합니다.

    ```bash
    mvn package
    ```

3. git 터미널 창에서 다음 명령을 사용하여 Java 응용 프로그램을 시작합니다. (YOUR_COSMOS_DB_HOSTNAME을 포털의 따옴표가 붙은 URI 값으로 바꾸고, YOUR_COSMOS_DB_MASTER_KEY를 포털의 따옴표가 붙은 기본 키로 바꿉니다.)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    터미널 창은 FamilyDB 데이터베이스가 만들어졌다는 알림을 표시합니다. 
    
4. 데이터베이스를 만들려면 아무 키를 누른 다음, 또 다른 키를 눌러 컬렉션을 만듭니다. 

    브라우저에서 데이터 탐색기로 전환하여 FamilyDB 데이터베이스 및 FamilyCollection 컬렉션이 포함되어 있는지 확인합니다.

5. 콘솔 창으로 전환하고 아무 키를 눌러 첫 번째 문서를 만든 다음, 또 다른 키를 눌러 두 번째 문서를 만듭니다. 그리고 데이터 탐색기로 전환하여 문서를 살펴봅니다. 

6. 아무 키를 눌러 쿼리를 실행하고 콘솔 창에서 출력을 봅니다. 

7. 앱은 생성된 리소스를 삭제하지 않습니다. 포털로 전환하여 [리소스를 정리](#clean-up-resources)합니다.  그러면 계정에서 요금이 발생하지 않습니다.

    ![콘솔 출력](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 데이터 탐색기를 사용하여 Azure Cosmos DB 계정, 문서 데이터베이스, 컬렉션을 만들고, 프로그래밍 방법으로 같은 작업을 수행하도록 앱을 실행하는 방법을 알게 되었습니다. 이제 Azure Cosmos DB 컬렉션에 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](import-data.md)
