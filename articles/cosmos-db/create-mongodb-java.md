---
title: Azure Cosmos DB의 API for MongoDB 및 Java SDK를 사용하여 콘솔 앱 빌드
description: Azure Cosmos DB의 API for MongoDB를 사용하여 연결 및 쿼리할 수 있는 Java 코드 샘플을 제공합니다.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: 2a5cfc417f75e5bb08e416b3243903badcf572e1
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480478"
---
# <a name="quickstart-build-a-web-app-using-azure-cosmos-dbs-api-for-mongodb-and-java-sdk"></a>빠른 시작: Azure Cosmos DB의 API for MongoDB 및 Java SDK를 사용하여 웹앱 빌드

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Cosmos DB의 핵심인 글로벌 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 [Azure Cosmos DB의 API for MongoDB](mongodb-introduction.md)를 사용하여 Cosmos 계정을 만드는 방법을 보여줍니다. 그런 다음, [MongoDB Java 드라이버](https://docs.mongodb.com/ecosystem/drivers/java/)를 사용하여 콘솔 앱을 빌드 및 배포합니다. 

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하기 전에 다음 필수 조건이 있어야 합니다.
* [Azure 및 Azure Stack JDK 8 버전용 JDK 설치](https://aka.ms/azure-jdks)
* Maven(Maven이 없는 경우 `apt-get install maven` 실행)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>컬렉션 추가

새 사용자 데이터베이스 이름을 **db**로 지정하고 새로운 컬렉션 이름을 **coll**로 지정합니다.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 앱을 복제하고, 연결 문자열을 설정하고, 앱을 실행하겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. 명령 프롬프트를 git-samples라는 새 폴더를 만든 다음 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 그런 다음, 선호하는 편집기에서 코드를 엽니다. 

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [연결 문자열 업데이트](#update-your-connection-string)로 건너뛸 수 있습니다. 

다음 코드 조각은 모두 Program.java 파일에서 가져옵니다.

* DocumentClient가 초기화됩니다.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 새 데이터베이스 및 컬렉션이 만들어집니다.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* `MongoCollection.insertOne`을 사용하여 일부 문서가 삽입됩니다.

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* `MongoCollection.find`을 사용하여 일부 쿼리가 수행됩니다.

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. 계정에서 **빠른 시작**을 선택하고 Java를 선택한 다음 연결 문자열을 클립보드에 복사합니다.

2. `Program.java` 파일을 열고 MongoClientURI 생성자에 대한 인수를 연결 문자열로 바꿉니다. 이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 
    
## <a name="run-the-console-app"></a>콘솔 앱 실행

1. 터미널에서 `mvn package`를 실행하여 필요한 npm 모듈을 설치합니다.

2. 터미널에서 `mvn exec:java -D exec.mainClass=GetStarted.Program`을 실행하여 Java 응용 프로그램을 시작합니다.

이제 [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md)를 사용하여 이 새 데이터를 쿼리, 수정 및 사용할 수 있습니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Cosmos 계정을 만들고, 컬렉션을 만들고, 콘솔 앱을 실행하는 방법을 알아보았습니다. 이제 Cosmos 데이터베이스에 추가 데이터를 가져올 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](mongodb-migrate.md)
