---
title: "Java로 Azure Cosmos DB 문서 데이터베이스 만들기 | Microsoft Docs | Microsoft Docs'"
description: "Azure Cosmos DB DocumentDB API에 연결 및 쿼리하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 08/02/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: f22e886ab3e59e69607e572d05a9f01d3d57f181
ms.contentlocale: ko-kr
ms.lasthandoff: 08/08/2017

---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Java 및 Azure Portal을 사용하여 문서 데이터베이스 만들기

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작은 Azure Cosmos DB용 Azure Portal 도구를 사용하여 문서 데이터베이스를 만듭니다. 또한 이 빠른 시작은 [DocumentDB Java API](documentdb-sdk-java.md)를 사용하는 Java 콘솔 앱을 빠르게 만드는 방법도 보여줍니다. 이 빠른 시작의 지침은 Java를 실행할 수 있는 모든 운영 체제에 적용될 수 있습니다. 이 빠른 시작을 완료하면 원하는 것이 무엇이든지 UI 또는 프로그래밍 방식으로 문서 데이터베이스 리소스를 만들고 수정하는 작업을 익히게 될 것입니다.

## <a name="prerequisites"></a>필수 조건

* [JDK(Java Development Kit) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Ubuntu에서 `apt-get install default-jdk`를 실행하여 JDK를 설치합니다.
    * JAVA_HOME 환경 변수가 반드시 JDK가 설치된 폴더를 지정하도록 설정합니다.
* [Maven](http://maven.apache.org/) 이진 아카이브 [다운로드](http://maven.apache.org/download.cgi) 및 [설치](http://maven.apache.org/install.html)
    * Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다.
* [Git](https://www.git-scm.com/)
    * Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 실행할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

그래프 데이터베이스를 만들려면 Azure Cosmos DB로 SQL (DocumentDB) 데이터베이스 계정을 만들어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>컬렉션 추가

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>샘플 데이터 추가

이제 데이터 탐색기를 사용하여 새 컬렉션에 데이터를 추가할 수 있습니다.

1. 데이터 탐색기에서 새 데이터베이스가 컬렉션 창에 나타납니다. **작업** 데이터베이스를 확장하고 **Items** 컬렉션을 확장하고 **문서**를 클릭한 후 **새 문서**를 클릭합니다. 

   ![Azure Portal의 데이터 탐색기에서 새 문서 만들기](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. 이제 다음과 같은 구조를 사용하여 컬렉션에 문서를 추가 합니다.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. **문서** 탭에 Json을 추가했으면 **저장**을 클릭합니다.

    ![Azure Portal의 데이터 탐색기에서 Json 데이터를 복사하고 저장을 클릭합니다.](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4.  `id` 속성에 대한 고유한 값을 삽입하는 경우 둘 이상의 문서를 만들고 저장하며 다른 속성을 적합하게 변경합니다. Azure Cosmos DB가 데이터에 어떠한 스키마도 적용하지 않으므로 새 문서는 사용자가 원하는 어떠한 구조든 가질 수 있습니다.

     이제 **필터 편집**과 **필터 적용** 단추를 클릭하여 데이터 탐색기에서 쿼리를 사용하여 데이터를 검색할 수 있습니다. 기본적으로 데이터 탐색기에서는 `SELECT * FROM c`를 사용하여 컬렉션의 모든 문서를 검색하지만, `SELECT * FROM c ORDER BY c._ts DESC` 등의 다른 [SQL 쿼리](documentdb-sql-query.md)로 변경하면 타임스탬프 기준으로 내림차순으로 모든 문서를 반환할 수 있습니다. 
 
     또한 서버 쪽 비즈니스 논리를 수행하고 처리량을 확장할 수 있도록 데이터 탐색기를 사용하여 저장 프로시저, UDF 및 트리거를 만들 수도 있습니다. 데이터 탐색기는 API에서 사용할 수 있는 모든 기본 제공 프로그래밍 방식 데이터 액세스를 표시하지만 Azure Portal의 데이터에도 쉽게 액세스할 수 있습니다.

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 코드 사용으로 전환해 보겠습니다. GitHub에서 DocumentDB API 앱을 복제하고 연결 문자열을 설정하며 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. git bash와 같은 git 터미널 창을 열고 `CD`를 수행하여 작업 디렉터리로 이동합니다.  

2. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>코드 검토

앱에서 어떤 상황이 발생하고 있는지 빠르게 살펴보겠습니다. \src\GetStarted 폴더에서 `Program.java` 파일을 열어 보면 이들 코드 줄에서 Azure Cosmos DB 리소스를 만드는 것을 알 수 있습니다. 

* `DocumentClient`가 초기화됩니다.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* 새 데이터베이스가 만들어집니다.

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* 새 컬렉션이 만들어집니다.

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* 일부 문서가 만들어집니다.

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* JSON을 통해 SQL 쿼리가 수행됩니다.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다.

1. [Azure Portal](http://portal.azure.com/)의 Azure Cosmos DB 계정에서 왼쪽 탐색 영역의 **키**를 클릭한 다음 **읽기-쓰기 키**를 클릭합니다. 다음 단계에서 화면 오른쪽의 복사 단추를 사용하여 URI 및 PRIMARY KEY를 `Program.java` 파일에 복사하게 됩니다.

    ![Azure Portal에서 선택 키 보기 및 복사, 키 블레이드](./media/create-documentdb-dotnet/keys.png)

2. 열린 `Program.java` 파일에서 포털에서 URI 값을 복사(복사 단추 사용)한 후 이 값을 `Program.java`의 DocumentClient 생성자에 대한 끝점 값으로 만듭니다. 

    `"https://FILLME.documents.azure.com"`

4. 그런 다음, 포털에서 PRIMARY KEY 값을 복사한 후 “FILLME”에 붙여 넣어 DocumentClient 생성자의 두 번째 매개 변수로 만듭니다. 이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 
    
## <a name="run-the-app"></a>앱 실행

1. git 터미널 창에서 azure-cosmos-db-documentdb-java-getting-started 폴더에 `cd`합니다.

2. git 터미널 창에서 `mvn package`를 입력하여 필요한 Java 패키지를 설치합니다.

3. git 터미널 창에서 터미널 창의 `mvn exec:java -D exec.mainClass=GetStarted.Program`을 실행하여 Java 응용 프로그램을 시작합니다.

    터미널 창에서 FamilyDB 데이터베이스가 생성된 알림을 받고 계속하려면 키를 누릅니다. 키를 눌러 데이터베이스를 생성한 다음 데이터 탐색기로 전환하면 FamilyDB 데이터베이스가 포함되어 있음을 확인할 수 있습니다. 키를 눌러 컬렉션과 문서를 만든 다음 쿼리를 수행합니다. 프로젝트가 완료되면 계정에서 리소스가 삭제됩니다. 

    ![Azure Portal에서 선택 키 보기 및 복사, 키 블레이드](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 빠른 시작에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 데이터 탐색기를 사용하여 Azure Cosmos DB 계정, 문서 데이터베이스, 컬렉션을 만들고, 프로그래밍 방법으로 같은 작업을 수행하도록 앱을 실행하는 방법을 알게 되었습니다. 이제 사용자의 Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](import-data.md)



