---
title: .NET 콘솔 앱을 빌드하여 Azure Cosmos DB SQL API 계정에서 데이터 관리
description: SQL API를 사용하여 온라인 데이터베이스 및 C# 콘솔 앱을 만드는 자습서입니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: a8d144b2cb8ee18c69dc4c4768b09422d44bade2
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617323"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>.NET 콘솔 앱을 빌드하여 Azure Cosmos DB SQL API 계정에서 데이터 관리

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET(미리 보기)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core(미리 보기)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [비동기 Java](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

Azure Cosmos DB SQL API 시작 자습서를 시작합니다. 이 자습서가 완료되면 Azure Cosmos DB 리소스를 만들고 쿼리하는 콘솔 앱을 갖게 됩니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
>
> - Azure Cosmos DB 계정 만들기 및 연결
> - Visual Studio 솔루션 구성
> - 데이터베이스 만들기
> - 컬렉션 만들기
> - JSON 문서 만들기
> - 컬렉션 쿼리
> - JSON 문서 업데이트
> - 문서 삭제
> - 데이터베이스 삭제

## <a name="prerequisites"></a>필수 조건

Azure 개발 워크플로가 설치된 Visual Studio 2017:
- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **평가판**을 다운로드하여 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다. 

Azure 구독 또는 Cosmos DB 체험 계정:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Azure Cosmos DB 에뮬레이터를 사용하는 경우 [Azure Cosmos DB 에뮬레이터](local-emulator.md)의 단계에 따라 에뮬레이터를 설치합니다. 그런 다음, [Visual Studio 솔루션 설정](#SetupVS)에서 자습서를 시작합니다.
  
## <a name="get-the-completed-solution"></a>전체 솔루션 가져오기

자습서를 완료할 시간이 없거나 코드 샘플만 원하는 경우 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started)에서 전체 솔루션을 다운로드할 수 있습니다. 

다운로드한 전체 솔루션을 실행하려면 다음을 수행합니다. 

1. [필수 조건](#prerequisites)에 설명된 구성 요소가 설치되어 있는지 확인합니다. 
1. Visual Studio에서 다운로드한 *GetStarted.sln* 솔루션 파일을 엽니다.
1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 **GetStarted** 프로젝트를 클릭하고, **NuGet 패키지 관리**를 선택합니다.
1. **NuGet** 탭에서 **복원**을 선택하여 Azure Cosmos DB .NET SDK에 대한 참조를 복원합니다.
1. [Azure Cosmos DB 계정에 연결](#Connect) 섹션에서 설명한 대로 *App.config* 파일에서 `EndpointUrl` 및 `PrimaryKey` 값을 업데이트합니다.
1. **디버그** > **디버깅하지 않고 시작**을 차례로 선택하거나 **Ctrl**+**F5**를 눌러 앱을 빌드하고 실행합니다.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

다음 지침에 따라 Azure Portal에서 새 Azure Cosmos DB 계정을 만듭니다. 사용할 Azure Cosmos DB 계정이 이미 있는 경우 [Visual Studio 솔루션 설정](#SetupVS)을 건너뜁니다. 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Visual Studio 솔루션 설정

1. Visual Studio 2017에서 **파일** > **새로 만들기** > **프로젝트**를 차례로 선택합니다.
   
1. **새 프로젝트** 대화 상자에서 **Visual C#** > **콘솔 앱(.NET Framework)** 을 차례로 선택하고, 프로젝트 이름을 *AzureCosmosDBApp*으로 지정한 다음, **확인**을 선택합니다.
   
   ![새 프로젝트 창의 스크린샷](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. **솔루션 탐색기**에서 마우스 오른쪽 단추로 **AzureCosmosDBApp** 프로젝트를 클릭하고, **NuGet 패키지 관리**를 선택합니다.
   
   ![프로젝트 상황에 맞는 메뉴](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. **NuGet** 탭에서 **찾아보기**를 선택하고, 검색 상자에서 *azure documentdb*를 입력합니다.
   
1. **Microsoft.Azure.DocumentDB**를 찾아서 선택하고, 아직 설치되지 않은 경우 **설치**를 선택합니다.
   
   Azure Cosmos DB SQL API 클라이언트 라이브러리의 패키지 ID는 [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)입니다.
   
   ![Azure Cosmos DB 클라이언트 SDK를 찾기 위한 NuGet 메뉴의 스크린샷](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   솔루션 변경 내용의 미리 보기에 대한 메시지가 표시되면 **확인**을 선택합니다. 라이선스 승인에 대한 메시지가 표시되면 **동의함**을 선택합니다.

## <a id="Connect"></a>Azure Cosmos DB 계정에 연결

이제 코드를 작성해 보겠습니다. 이 자습서에 대한 전체 *Project.cs* 파일은 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs)에 있습니다.

1. **솔루션 탐색기**에서 *Program.cs*를 선택하고, 코드 편집기에서 다음 참조를 파일의 시작 부분에 추가합니다.
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. 다음으로, `public class Program`에 다음과 같이 두 개의 상수와 `client` 변수를 추가합니다.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. 엔드포인트 URL 및 기본 키를 사용하면 앱에서 Azure Cosmos DB 계정과 Azure Cosmos DB 계정에 연결하여 해당 연결을 신뢰할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 키를 복사하여 코드에 붙여넣습니다. 

   
   1. Azure Cosmos DB 계정의 왼쪽 탐색 모음에서 **키**를 선택합니다.
      
      ![Azure Portal에서 액세스 키 보기 및 복사](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. **읽기-쓰기 키** 아래에서 오른쪽의 복사 단추를 사용하여 **URI**를 복사하여 *Program.cs*의 `<your endpoint URL>`에 붙여넣습니다. 예:  
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. **기본 키** 값을 복사하여 *Program.cs*의 `<your primary key>`에 붙여넣습니다. 예:  
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. `client`이라는 새 `DocumentClient`가 인스턴스화되는 `GetStartedDemo`라는 새 비동기 작업을 `Main` 메서드 뒤에 추가합니다.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```
   
1. 다음 코드를 `Main` 메서드에 추가하여 `GetStartedDemo` 작업을 실행합니다. `Main` 메서드에서 예외를 catch하여 콘솔에 씁니다.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. **F5** 키를 눌러 앱을 실행합니다. 
   
1. 콘솔 창에 **데모의 끝입니다. 종료하려면 아무 키나 누르세요**라는 메시지가 표시되면 연결이 성공했음을 의미합니다. 아무 키를 눌러 콘솔 창을 닫습니다. 

Azure Cosmos DB 계정에 성공적으로 연결되었습니다. 이제 몇 가지 Azure Cosmos DB 리소스를 사용해 보겠습니다.  

## <a name="create-a-database"></a>데이터베이스 만들기

Azure Cosmos DB [데이터베이스](databases-containers-items.md#azure-cosmos-databases)는 컬렉션 전체에 걸쳐 분할된 JSON 문서 스토리지의 논리적 컨테이너입니다. `DocumentClient` 클래스의 [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) 메서드를 사용하여 데이터베이스를 만듭니다. 

1. 데이터베이스 생성을 위한 코드를 추가하기 전에, 콘솔에 쓰기 위한 도우미 메서드를 추가합니다. 다음 `WriteToConsoleAndPromptToContinue` 메서드를 복사하여 코드의 `GetStartedDemo` 메서드 뒤에 붙여넣습니다.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. 다음 줄을 복사하여 `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` 줄 뒤의 `GetStartedDemo` 메서드에 붙여넣습니다. 이 코드는 `FamilyDB`라는 데이터베이스를 만듭니다.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. **F5** 키를 눌러 앱을 실행합니다.

Azure Cosmos DB 데이터베이스가 성공적으로 만들어졌습니다. Azure Cosmos DB 계정의 왼쪽 탐색 영역에서 **데이터 탐색기**를 선택하여 [Azure Portal](https://portal.azure.com)에서 해당 데이터베이스를 볼 수 있습니다. 

## <a id="CreateColl"></a>컬렉션 만들기

컬렉션은 JSON 문서 및 관련 JavaScript 애플리케이션 논리의 컨테이너입니다. `DocumentClient` 클래스의 [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) 메서드를 사용하여 컬렉션을 만듭니다. 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync**는 가격 책정에 영향을 주는 예약된 처리량을 사용하여 새 컬렉션을 만듭니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.
> 

1. 다음 코드를 복사하여 `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` 줄 뒤의 `GetStartedDemo` 메서드에 붙여넣습니다. 이 코드는 `FamilyCollection`이라는 문서 컬렉션을 만듭니다.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. **F5** 키를 눌러 앱을 실행합니다.

Azure Cosmos DB 데이터베이스 컬렉션이 성공적으로 만들어졌습니다. Azure Portal에 있는 **데이터 탐색기**의 **FamilyDB** 데이터베이스 아래에서 해당 컬렉션을 볼 수 있습니다.  

## <a id="CreateDoc"></a>JSON 문서 만들기

문서는 사용자 정의 임의 JSON 콘텐츠입니다. 문서에는 `id`로 직렬화된 JSON 형식의 ID 속성이 있어야 합니다. `DocumentClient` 클래스의 [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) 메서드를 사용하여 문서를 만듭니다. 

> [!TIP]
> 데이터베이스에 저장하려는 데이터가 이미 있는 경우 Azure Cosmos DB의 [데이터 마이그레이션 도구](import-data.md)를 사용하여 해당 데이터를 가져올 수 있습니다.
>

다음 코드는 두 개의 문서를 만들어 데이터베이스 컬렉션에 삽입합니다. 먼저, `Family` 내에서 사용할 `Family` 클래스와 `Parent`, `Child`, `Pet` 및 `Address` 하위 클래스를 만듭니다. 그런 다음, `CreateFamilyDocumentIfNotExists` 메서드를 만든 다음, 두 개의 문서를 만들어 삽입합니다. 

1. 다음 `Family`, `Parent`, `Child`, `Pet` 및 `Address` 클래스를 복사하여 코드의 `WriteToConsoleAndPromptToContinue` 메서드 뒤에 붙여넣습니다.
   
   ```csharp
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
   ```
   
1. 다음 `CreateFamilyDocumentIfNotExists` 메서드를 복사하여 방금 추가한 `Address` 클래스 뒤에 붙여넣습니다.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. 다음 코드를 복사하여 `await client.CreateDocumentCollectionIfNotExistsAsync` 줄 뒤에 나오는 `GetStartedDemo` 메서드의 끝에 붙여넣습니다. 이 코드는 Andersen 및 Wakefield 패밀리에 각각 하나씩 두 개의 문서를 만들고 삽입합니다.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents = new Parent[]
        {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
            new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
            new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
            new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
            new Child
            {
                FamilyName = "Merriam",
                FirstName = "Jesse",
                Gender = "female",
                Grade = 8,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Goofy" },
                    new Pet { GivenName = "Shadow" }
                }
            },
            new Child
            {
                FamilyName = "Miller",
                FirstName = "Lisa",
                Gender = "female",
                Grade = 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. **F5** 키를 눌러 앱을 실행합니다.

두 개의 Azure Cosmos DB 문서가 성공적으로 만들어졌습니다. Azure Portal에 있는 **데이터 탐색기**의 **FamilyDB** 데이터베이스 및 **FamilyCollection** 컬렉션 아래에서 해당 문서를 볼 수 있습니다.   

![계정, 온라인 데이터베이스, 컬렉션 및 문서 간의 계층적 관계를 보여 주는 다이어그램](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Azure Cosmos DB 리소스 쿼리

Azure Cosmos DB는 컬렉션에 저장된 JSON 문서에 대해 다양한 [쿼리](how-to-sql-query.md)를 지원합니다. 다음 샘플 코드에서는 LINQ 및 Azure Cosmos DB SQL 구문을 사용하여 샘플 문서에 대해 쿼리를 실행합니다.

1. 다음 `ExecuteSimpleQuery` 메서드를 복사하여 코드의 `CreateFamilyDocumentIfNotExists` 메서드 뒤에 붙여넣습니다.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. 다음 코드를 복사하여 `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` 줄 뒤에 나오는 `GetStartedDemo` 메서드의 끝에 붙여넣습니다.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. **F5** 키를 눌러 앱을 실행합니다.

앞의 쿼리는 Andersen 패밀리에 대한 전체 항목을 반환합니다. Azure Cosmos DB 컬렉션에 대한 쿼리가 성공적으로 수행되었습니다.

다음 다이어그램에서는 컬렉션에 대한 Azure Cosmos DB SQL 쿼리 구문을 호출하는 방법을 보여 줍니다. 동일한 논리가 LINQ 쿼리에 적용됩니다.

![NoSQL에서 C# 콘솔 애플리케이션을 만들기 위해 사용한 쿼리의 의미와 범위를 보여 주는 다이어그램](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Azure Cosmos DB 쿼리의 범위가 이미 단일 컬렉션으로 지정되었으므로 SQL 쿼리의 [FROM](how-to-sql-query.md#FromClause) 키워드는 선택 사항입니다. `FROM Families f`를 `FROM root r` 또는 선택한 다른 변수 이름과 바꿀 수 있습니다. Azure Cosmos DB는 선택한 `Families`, `root` 또는 변수 이름에서 현재 컬렉션을 참조한다고 유추합니다.

## <a id="ReplaceDocument"></a>JSON 문서 업데이트

Azure Cosmos DB SQL API는 JSON 문서 업데이트 및 대체를 지원합니다.  

1. 다음 `ReplaceFamilyDocument` 메서드를 복사하여 코드의 `ExecuteSimpleQuery` 메서드 뒤에 붙여넣습니다.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. 다음 코드를 복사하여 `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` 줄 뒤에 나오는 `GetStartedDemo` 메서드의 끝에 붙여넣습니다. 이 코드는 문서 중 하나의 데이터를 업데이트한 다음, 쿼리를 다시 실행하여 변경된 문서를 표시합니다.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. **F5** 키를 눌러 앱을 실행합니다.

쿼리 출력에서 Andersen 패밀리의 자식 항목에 대한 `Grade`가 `5`에서 `6`으로 업데이트되었음을 보여 줍니다. Azure Cosmos DB 문서가 성공적으로 업데이트되고 대체되었습니다. 

## <a id="DeleteDocument"></a>JSON 문서 삭제

Azure Cosmos DB SQL API는 JSON 문서 삭제를 지원합니다.  

1. 다음 `DeleteFamilyDocument` 메서드를 복사하여 `ReplaceFamilyDocument` 메서드 뒤에 붙여넣습니다.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. 다음 코드를 복사하여 두 번째 `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` 줄 뒤에 나오는 `GetStartedDemo` 메서드의 끝에 붙여넣습니다.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. **F5** 키를 눌러 앱을 실행합니다.

Azure Cosmos DB 문서가 성공적으로 삭제되었습니다. 

## <a id="DeleteDatabase"></a>데이터베이스 삭제

만든 데이터베이스를 삭제하여 데이터베이스와 컬렉션 및 문서를 포함한 해당 자식 리소스를 모두 제거합니다. 

1. 다음 코드를 복사하여 `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` 줄 뒤에 나오는 `GetStartedDemo` 메서드의 끝에 붙여넣습니다. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. **F5** 키를 눌러 앱을 실행합니다.

Azure Cosmos DB 데이터베이스가 성공적으로 삭제되었습니다. Azure Cosmos DB 계정에 대한 **데이터 탐색기**에서 FamilyDB 데이터베이스가 삭제되었음을 확인할 수 있습니다. 

## <a id="Run"></a>전체 C# 콘솔 앱 실행

Visual Studio에서 **F5** 키를 눌러 전체 C# 콘솔 앱을 빌드하고 디버그 모드에서 실행합니다. 콘솔 창에서 다음과 같은 출력이 표시됩니다.

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

축하합니다! 자습서가 완료되었으며, Azure Cosmos DB 리소스를 생성, 쿼리, 업데이트 및 삭제하는 작업용 C# 콘솔 앱이 있습니다.  

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 시작](introduction.md)을 참조하세요.
* 더 복잡한 ASP.NET MVC 자습서는 [ASP.NET MVC 자습서: Azure Cosmos DB를 사용한 웹 애플리케이션 개발](sql-api-dotnet-application.md)을 참조하세요.
* Azure Cosmos DB를 사용하여 크기 조정 및 성능 테스트를 수행하려면 [Azure Cosmos DB를 사용한 성능 및 크기 조정 테스트](performance-testing.md)를 참조하세요.
* Azure Cosmos DB 요청, 사용량 및 스토리지를 모니터링하는 방법을 알아보려면 [계정 모니터링](monitor-accounts.md)을 참조하세요.
* [쿼리 실습](https://www.documentdb.com/sql/demo)에서 샘플 데이터 세트에 대해 쿼리를 실행합니다.

