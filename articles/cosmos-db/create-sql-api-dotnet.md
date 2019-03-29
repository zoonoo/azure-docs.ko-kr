---
title: SQL API를 사용하여 Azure Cosmos DB가 있는 .NET 웹앱 빌드
description: 이 빠른 시작에서 Azure Cosmos DB SQL API 및 Azure Portal을 사용하여 .NET 웹앱 만들기
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/15/2019
ms.openlocfilehash: 1ef414b2de2acbf5b92661c8b5f1e249549b14df
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259145"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-db-sql-api-account"></a>빠른 시작: Azure Cosmos DB SQL API 계정을 사용하여 .NET 웹앱 빌드

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET(미리 보기)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Cosmos DB [SQL API](sql-api-introduction.md) 계정, 문서 데이터베이스 및 컬렉션을 만들고 컬렉션에 샘플 데이터를 추가하는 방법을 보여줍니다. 그런 다음, [SQL .NET SDK](sql-api-sdk-dotnet.md)를 사용하여 할 일 목록 웹앱을 빌드하여 배포하고, 컬렉션 내의 관리 데이터를 더 많이 추가하겠습니다. 

## <a name="prerequisites"></a>필수 조건

Visual Studio 2017이 아직 설치되지 않은 경우 **체험판** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-account"></a>계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>데이터베이스 및 컬렉션 추가

이제 Azure Portal에서 데이터 탐색기 도구를 사용하여 데이터베이스 및 컬렉션을 만들 수 있습니다. 

1. **데이터 탐색기** > **새 컬렉션**을 클릭합니다. 
    
    **컬렉션 추가** 영역이 맨 오른쪽에 표시되면 확인하기 위해 오른쪽으로 스크롤해야 합니다.

    ![Azure Portal Data Explorer, 컬렉션 추가 창](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)

2. **컬렉션 추가** 페이지에서 새 컬렉션에 대한 설정을 입력합니다.

    설정|제안 값|설명
    ---|---|---
    **데이터베이스 ID**|ToDoList|새 데이터베이스의 이름으로 *ToDoList*를 입력합니다. 데이터베이스 이름은 1~255자여야 하며, `/, \\, #, ?` 또는 후행 공백은 포함할 수 없습니다.
    **컬렉션 ID**|항목|새 컬렉션의 이름으로 *항목*을 입력입니다. 컬렉션 ID에는 데이터베이스 이름과 동일한 문자 요구 사항이 적용됩니다.
    **파티션 키**| `<your_partition_key>`| 파티션 키를 입력합니다. 이 문서에 설명된 샘플은 파티션 키로 */category*를 사용합니다.
    **처리량**|400RU|처리량을 400RU/s(초당 요청 단위)로 변경합니다. 대기 시간을 줄이면 나중에 처리량을 늘릴 수 있습니다. 
    
    앞의 설정 외에도 선택적으로 컬렉션에 대한 **고유 키**를 추가할 수 있습니다. 이 예에서 필드는 비워 두겠습니다. 고유 키는 데이터베이스에 데이터 무결성 계층을 추가할 수 있는 기능을 개발자에게 제공합니다. 컬렉션을 만드는 동안 고유 키 정책을 만들면 파티션 키당 하나 이상의 값에 대한 고유성이 보장됩니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](unique-keys.md) 문서를 참조하세요.
    
    **확인**을 클릭합니다.

    데이터 탐색기는 새 데이터베이스 및 컬렉션을 표시합니다.

    ![Azure Portal 데이터 탐색기, 새 데이터베이스 및 컬렉션 표시](./media/create-sql-api-dotnet/azure-cosmos-db-new-collection.png)

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>샘플 데이터 추가

이제 데이터 탐색기를 사용하여 새 컬렉션에 데이터를 추가할 수 있습니다.

1. 데이터 탐색기에서 새 데이터베이스가 컬렉션 창에 나타납니다. **작업** 데이터베이스를 확장하고 **Items** 컬렉션을 확장하고 **문서**를 클릭한 후 **새 문서**를 클릭합니다. 

   ![Azure Portal의 데이터 탐색기에서 새 문서 만들기](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
  
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

    ![Azure Portal의 데이터 탐색기에서 Json 데이터를 복사하고 저장을 클릭합니다.](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)

4. `id` 속성에 대한 고유한 값을 삽입하는 경우 둘 이상의 문서를 만들고 저장하며 다른 속성을 적합하게 변경합니다. Azure Cosmos DB가 데이터에 어떠한 스키마도 적용하지 않으므로 새 문서는 사용자가 원하는 어떠한 구조든 가질 수 있습니다.

## <a name="query-your-data"></a>데이터 쿼리

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 코드 사용으로 전환해 보겠습니다. [GitHub에서 SQL API 앱](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)을 복제하고 연결 문자열을 설정하고 실행해보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

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
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

4. 그런 다음 Visual Studio에서 todo 솔루션 파일을 엽니다. 

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [연결 문자열 업데이트](#update-your-connection-string)로 건너뛸 수 있습니다. 이 빠른 시작에서는 Azure Portal을 사용하여 데이터베이스 및 컬렉션을 만들고, .NET 샘플을 사용하여 샘플 데이터를 추가합니다. .NET 샘플을 사용하여 데이터베이스 및 컬렉션을 만들 수도 있습니다. 

다음 코드 조각은 모두 DocumentDBRepository.cs 파일에서 가져옵니다.

* DocumentClient는 다음 코드처럼 초기화됩니다.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* 다음 코드처럼 `CreateDatabaseAsync` 메서드를 사용하여 새 데이터베이스가 만들어집니다.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* 다음 코드처럼 `CreateDocumentCollectionAsync`를 사용하여 새 컬렉션이 만들어집니다.

    ```csharp
    private static async Task CreateCollectionIfNotExistsAsync()
    {
        try
        {
           await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
        }
        catch (DocumentClientException e)
        {
           if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
           {
              await client.CreateDocumentCollectionAsync(
              UriFactory.CreateDatabaseUri(DatabaseId),
              new DocumentCollection
              {
                  Id = CollectionId
              },
              new RequestOptions { OfferThroughput = 400 });
           }
           else
           {
             throw;
           }
        }
    }
    ```

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 왼쪽 탐색 영역의 **키**를 선택한 다음, **읽기-쓰기 키**를 선택합니다. 다음 단계에서 화면 오른쪽의 복사 단추를 사용하여 URI 및 기본 키를 web.config 파일에 복사하게 됩니다.

    ![Azure Portal에서 선택 키 보기 및 복사, 키 블레이드](./media/create-sql-api-dotnet/keys.png)

2. Visual Studio 2017에서 web.config 파일을 엽니다. 

3. 포털에서 URI 값을 복사(복사 단추 사용)한 후 이 값을 web.config에서 엔드포인트 키 값으로 만듭니다. 

    `<add key="endpoint" value="FILLME" />`

4. 그 다음, 포털에서 사용자의 기본 키 값을 복사한 후 web.config에서 authKey 값으로 만듭니다. 

    `<add key="authKey" value="FILLME" />`
    
5. 그런 다음, 이전에 만든 데이터베이스의 이름과 일치하도록 데이터베이스 및 컬렉션 값을 업데이트합니다. 이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 

   ```csharp
   <add key="database" value="ToDoList"/>
   <add key="collection" value="Items"/>
   ```
 
## <a name="run-the-web-app"></a>웹앱 실행
1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 선택합니다. 

2. NuGet **찾아보기** 상자에 *DocumentDB*를 입력합니다.

3. 결과에서 **Microsoft.Azure.DocumentDB** 라이브러리를 설치합니다. 그러면 Microsoft.Azure.DocumentDB 패키지 뿐만 아니라 모든 종속성도 설치됩니다.

4. Ctrl + F5를 선택하여 응용 프로그램을 실행합니다. 앱이 브라우저에 표시됩니다. 

5. 브라우저에서 **새로 만들기**를 선택하고 Todo 앱에서 몇 가지 새 작업을 만듭니다.

   ![샘플 데이터를 사용한 Todo 앱](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

이제 데이터 탐색기로 돌아가서 이 새 데이터를 쿼리 및 수정하고 작업에 사용할 수 있습니다. 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 컬렉션을 만들고, 웹앱을 실행하는 방법을 알아보았습니다. 이제 사용자의 Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 데이터 가져오기](import-data.md)


