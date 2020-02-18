---
title: 빠른 시작 - Java를 사용하여 Azure Cosmos DB를 통해 문서 데이터베이스 만들기
description: 이 빠른 시작에서는 Azure Cosmos DB SQL API에 연결하고 쿼리하는 데 사용할 수 있는 Java 코드 샘플을 제공합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8c2ae82bae8457a1c715f160994c7a0da94193ff
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134503"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>빠른 시작: Azure Cosmos DB SQL API 데이터를 관리하는 Java 앱 빌드


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

이 빠른 시작에서는 Azure Portal 및 GitHub에서 복제된 Java 앱을 사용하여 Azure Cosmos DB SQL API 계정을 만들고 관리합니다. 먼저 Azure Portal을 사용하여 Azure Cosmos DB SQL API 계정을 만들고, SQL Java SDK를 사용하여 Java 앱을 만든 다음, Java 애플리케이션을 사용하여 Cosmos DB 계정에 리소스를 추가합니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다. URI가 `https://localhost:8081`이고 키가 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`인 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator)를 사용할 수도 있습니다.
- [JDK(Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). `JAVA_HOME` 환경 변수를 JDK가 설치된 폴더로 지정합니다.
- [Maven 이진 보관 파일](https://maven.apache.org/download.cgi). Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 설치합니다.
- [Git](https://www.git-scm.com/downloads) Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 설치합니다.

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

문서 데이터베이스를 만들기 전에 Azure Cosmos DB를 사용하여 SQL API 계정을 만들어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>컨테이너 추가

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>샘플 데이터 추가

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>데이터 쿼리

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 코드 사용으로 전환해 보겠습니다. GitHub에서 SQL API 앱을 복제하고 연결 문자열을 설정한 다음 실행해보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [앱 실행](#run-the-app)으로 넘어갈 수 있습니다. 

* `CosmosClient` 초기화 `CosmosClient`는 Azure Cosmos 데이터베이스 서비스에 대한 클라이언트 쪽 논리적 표현을 제공합니다. 이 클라이언트는 서비스에 대한 요청을 구성하고 실행하는 데 사용됩니다.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* CosmosDatabase 생성

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* CosmosContainer 생성

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* `createItem` 메서드를 사용하여 항목 생성

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* `getItem` 및 `read` 메서드를 사용하여 지점 읽기가 수행됩니다.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* JSON에 대한 SQL 쿼리는 `queryItems` 메서드를 사용하여 수행됩니다.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>앱 실행

이제 Azure Portal로 돌아가서 연결 문자열 정보를 가져오고 엔드포인트 정보를 사용하여 앱을 시작합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다.

1. git 터미널 창에서 샘플 코드 폴더로 `cd`합니다.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. git 터미널 창에서 다음 명령을 사용하여 필요한 Java 패키지를 설치합니다.

    ```bash
    mvn package
    ```

3. git 터미널 창에서 다음 명령을 사용하여 Java 애플리케이션을 시작합니다. (YOUR_COSMOS_DB_HOSTNAME을 포털의 따옴표가 붙은 URI 값으로 바꾸고, YOUR_COSMOS_DB_MASTER_KEY를 포털의 따옴표가 붙은 기본 키로 바꿉니다.)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    터미널 창은 FamilyDB 데이터베이스가 만들어졌다는 알림을 표시합니다. 
    
4. 앱에서 이름이 `AzureSampleFamilyDB`인 데이터베이스 만들기
5. 앱에서 이름이 `FamilyContainer`인 컨테이너 만들기
6. 앱은 개체 ID와 파티션 키 값(샘플의 lastName)을 사용하여 지점 읽기를 수행합니다. 
7. 이 앱은 ('Andersen', 'Wakefield', 'Johnson')의 성이 있는 모든 패밀리를 검색하기 위해 항목을 쿼리합니다.

7. 앱은 생성된 리소스를 삭제하지 않습니다. 포털로 전환하여 [리소스를 정리](#clean-up-resources)합니다.  그러면 계정에서 요금이 발생하지 않습니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos DB SQL API 계정을 만들고, Data Explorer를 사용하여 문서 데이터베이스 및 컨테이너를 만들고, Java 앱을 실행하여 프로그래밍 방식으로 동일한 작업을 수행하는 방법을 알아보았습니다. 이제 Azure Cosmos DB 계정으로 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](import-data.md)
