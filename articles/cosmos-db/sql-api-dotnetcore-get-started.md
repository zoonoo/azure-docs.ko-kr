---
title: '자습서: .NET Core 앱을 빌드하여 Azure Cosmos DB의 SQL API 계정에 저장된 데이터 관리'
description: 이 자습서에서는 Azure Cosmos DB에 SQL API .NET Core SDK를 사용하여 온라인 데이터베이스 및 C# 콘솔 애플리케이션을 만듭니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 1aad68a3248561c86e195b55c1d509ab7a15964e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035681"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>자습서: .NET Core 앱을 빌드하여 SQL API 계정에 저장된 데이터 관리

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core(미리 보기)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET(미리 보기)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [비동기 Java](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

개발자에게 NoSQL 문서 데이터를 사용하는 애플리케이션이 있을 수 있습니다. Azure Cosmos DB에서 SQL API 계정을 사용하여 이 문서 데이터를 저장하고 액세스할 수 있습니다. 이 자습서에서는 Azure Cosmos DB의 SQL API 계정에 저장된 데이터를 만들고 쿼리하는 .NET Core 앱을 빌드하는 방법을 보여 줍니다. 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Cosmos 계정 만들기 및 연결
> * Visual Studio 솔루션 구성
> * 온라인 데이터베이스 만들기
> * 컬렉션 만들기
> * JSON 문서 만들기
> * 항목, 컨테이너 및 데이터베이스에서 CRUD 작업 수행

애플리케이션을 만든 시간이 없나요? [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)에서 전체 솔루션을 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 등록할 수 있습니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* 무료 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하여 사용할 수 있습니다. UWP(유니버설 Windows 플랫폼) 앱을 개발하는 경우에는 **Visual Studio 2017 버전 15.4** 이상을 사용해야 합니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.

    * MacOS 또는 Linux의 경우 원하는 플랫폼에 대한 [.NET Core SDK](https://www.microsoft.com/net/core#macos)를 설치하여 명령줄에서 .NET Core 앱을 개발할 수 있습니다. 

    * Windows의 경우 [.NET Core SDK](https://www.microsoft.com/net/core#windows)를 설치하여 명령줄에서 .NET Core 앱을 개발할 수 있습니다. 

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos 계정 만들기

다음 단계를 사용하여 Azure Cosmos 계정을 만듭니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Visual Studio 솔루션 설치

1. 컴퓨터에서 **Visual Studio 2017**을 엽니다.

2. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.

3. **새 프로젝트** 대화 상자에서 **템플릿** > **Visual C#** > **.NET Core** > **콘솔 응용 프로그램(.NET Core)** 을 선택하고 프로젝트 이름을 **DocumentDBGettingStarted**라고 지정한 후 **확인**을 선택합니다.

   ![새 프로젝트 창의 스크린샷](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. **솔루션 탐색기**에서 **DocumentDBGettingStarted**를 마우스 오른쪽 단추로 클릭합니다.

5. 같은 메뉴에서 **NuGet 패키지 관리**를 선택합니다.

   ![프로젝트의 마우스 오른쪽 단추 클릭 메뉴 스크린샷](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. **NuGet** 탭에서 창의 맨 위에 있는 **찾아보기**를 선택하고 검색 상자에 **azure documentdb**를 입력합니다.

7. 결과에서 **Microsoft.Azure.DocumentDB.Core**를 찾아 **설치**를 선택합니다.

   라이브러리의 패키지 ID는 [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)입니다. 이 .NET Core NuGet 패키지에서 지원되지 않는 .NET Framework 버전(예: net461)을 대상으로 하는 경우 .NET Framework 4.5 이후의 모든 .NET Framework 버전을 지원하는 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)를 사용합니다.

8. 프롬프트가 표시되면 NuGet 패키지 설치를 수락하고 사용권 계약에 동의합니다.

이제 설치가 완료되었으므로 코드를 작성해 보겠습니다. [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)에서 이 자습서의 완료된 코드 프로젝트를 찾을 수 있습니다.

## <a id="Connect"></a>Azure Cosmos 계정에 연결

필요한 종속성을 가져와서 Azure Cosmos 계정에 연결합니다. 종속성을 가져오려면 Program.cs 파일의 시작 부분에 다음 코드를 추가합니다.

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

다음으로 두 가지 상수와 *클라이언트* 변수를 공용 클래스 *프로그램* 아래에 추가합니다.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

다음으로, [Azure Portal](https://portal.azure.com)로 이동하여 URI 및 기본 키를 검색합니다. Azure Cosmos DB URI와 기본 키는 애플리케이션에서 연결할 위치를 식별하고 Azure Cosmos DB에서 애플리케이션의 연결을 신뢰하는 데 필요합니다.

Azure Portal에서 Azure Cosmos 계정으로 이동한 다음, **키**를 선택합니다.

포털에서 URI를 복사하고 program.cs 파일의 `<your endpoint URI>` 에 붙여 넣습니다. 그런 다음 포털에서 기본 키를 복사하고 `<your key>`에 붙여 넣습니다. &lt; 및 &gt;를 반드시 제거하고 엔드포인트 및 키를 묶은 큰따옴표는 유지합니다.

![Azure Portal에서 키 가져오기][keys]

**DocumentClient**의 새 인스턴스를 만드는 것으로 시작 애플리케이션을 시작해 보겠습니다.

**Main** 메서드 아래에 **GetStartedDemo**라는 이름의 새로운 비동기 작업을 추가하면 새 **DocumentClient**가 인스턴스화됩니다.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

다음 코드를 추가하여 **Main** 메서드에서 비동기 작업을 실행합니다. **Main** 메서드가 예외를 catch하고 콘솔에 씁니다.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

**DocumentDBGettingStarted** 단추를 선택하여 애플리케이션을 빌드하고 실행합니다.

## <a id="CreateDatabase"></a>데이터베이스 만들기

데이터베이스 생성을 위한 코드를 추가하기 전에, 콘솔에 쓰기 위한 도우미 메서드를 추가합니다. **GetStartedDemo** 메서드에 **WriteToConsoleAndPromptToContinue** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

**DocumentClient** 클래스의 `CreateDatabaseAsync` 메서드를 사용하여 Azure Cosmos DB 데이터베이스를 만듭니다. 데이터베이스는 여러 컬렉션으로 분할된 JSON 문서 저장소의 논리적 컨테이너입니다. 클라이언트 생성의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다. *FamilyDB*라는 데이터베이스가 만들어집니다.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

**DocumentDBGettingStarted** 단추를 선택하여 애플리케이션을 실행합니다.

## <a id="CreateColl"></a>컬렉션 만들기

**DocumentClient** 클래스의 `CreateDocumentCollectionAsync` 메서드를 사용하여 컬렉션을 만듭니다. 컬렉션은 JSON 문서 및 관련 JavaScript 애플리케이션 논리의 컨테이너입니다.

> [!WARNING]
> **CreateDocumentCollectionAsync**는 가격 책정 의미가 포함된 예약된 처리량이 있는 새 컬렉션을 만듭니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

데이터베이스 생성의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다. 이 코드는 *FamilyCollection_oa*라는 문서 컬렉션을 만듭니다.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

**DocumentDBGettingStarted** 단추를 선택하여 애플리케이션을 실행합니다.

## <a id="CreateDoc"></a>JSON 문서 만들기

**DocumentClient** 클래스의 `CreateDocumentAsync` 메서드를 사용하여 문서를 만듭니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 하나 이상의 문서를 삽입할 수 있습니다. 

먼저 Azure Cosmos DB 내에 저장된 개체를 나타내는 **Family** 클래스를 만듭니다. 또한 **Family** 내에서 사용되는 **Parent**, **Child**, **Pet**, **Address** 하위 클래스를 만듭니다. 문서에는 JSON에서 **Id**로 직렬화된 **id** 속성이 있어야 합니다. **GetStartedDemo** 메서드 다음에 다음 내부 서브클래스를 추가하여 이러한 클래스를 만듭니다. **WriteToConsoleAndPromptToContinue** 메서드에 **가족**, **부모**, **자식**, **애완 동물** 및 **주소** 클래스를 복사하여 붙여 넣습니다.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

**CreateDocumentCollectionIfNotExists** 메서드에 **CreateFamilyDocumentIfNotExists** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

그리고 Andersen Family와 Wakefield Family의 문서 하나씩 두 개의 문서를 삽입합니다. 문서 컬렉션 생성 아래에서 **GetStartedDemo** 메서드에 `// ADD THIS PART TO YOUR CODE` 다음에 코드를 복사하고 붙여 넣습니다.

```csharp
await this.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

**DocumentDBGettingStarted** 단추를 선택하여 애플리케이션을 실행합니다.

![계정, 온라인 데이터베이스, 컬렉션 간의 계층 관계](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Azure Cosmos DB 리소스 쿼리

Azure Cosmos DB는 각 컬렉션에 저장된 JSON 문서에 대해 다양한 쿼리를 지원합니다. 다음 샘플 코드는 Azure Cosmos DB SQL 구문 및 LINQ를 둘 다 사용하는 다양한 쿼리를 보여 줍니다. 이러한 쿼리는 이전 단계에서 삽입한 문서에 대해 실행할 수 있습니다.

**CreateFamilyDocumentIfNotExists** 메서드에 **ExecuteSimpleQuery** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

두 번째 문서 생성의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

**DocumentDBGettingStarted** 단추를 선택하여 애플리케이션을 실행합니다.

다음 다이어그램에서는 만든 컬렉션에 대해 Azure Cosmos DB SQL 쿼리 구문을 호출하는 방법을 보여 줍니다. 동일한 논리가 LINQ 쿼리에 적용됩니다.

![NoSQL에서 C# 콘솔 애플리케이션을 만들기 위해 사용한 쿼리의 의미와 범위를 보여 주는 다이어그램](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

Azure Cosmos DB 쿼리는 이미 단일 컬렉션으로 범위가 지정되었기 때문에 `FROM` 키워드는 쿼리에서 선택 사항입니다. 따라서 "FROM Families f"를 "FROM root r" 또는 선택한 다른 변수 이름으로 교체할 수 있습니다. Azure Cosmos DB는 Families, root 또는 선택한 변수 이름이 기본적으로 현재 컬렉션을 참조하는 것으로 유추합니다.

## <a id="ReplaceDocument"></a>JSON 문서 바꾸기

Azure Cosmos DB는 JSON 문서 바꾸기를 지원합니다.  

**ExecuteSimpleQuery** 메서드에 **ReplaceFamilyDocument** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

쿼리 실행의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다. 문서를 바꾼 후에, 변경된 문서를 표시하도록 같은 쿼리가 다시 실행됩니다.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

**DocumentDBGettingStarted** 단추를 선택하여 애플리케이션을 실행합니다.

## <a id="DeleteDocument"></a>JSON 문서 삭제

Azure Cosmos DB는 JSON 문서 삭제를 지원합니다.  

**ReplaceFamilyDocument** 메서드에 **DeleteFamilyDocument** 메서드를 복사하여 붙여넣습니다.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

두 번째 쿼리 실행의 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

**DocumentDBGettingStarted** 단추를 선택하여 애플리케이션을 실행합니다.

## <a id="DeleteDatabase"></a>데이터베이스 삭제

만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컬렉션, 문서 등)가 제거됩니다. 전체 데이터베이스와 모든 자식 리소스를 삭제하기 위해 문서 바로 아래 **GetStartedDemo** 메서드에 다음 코드를 복사하여 붙여넣습니다.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

**DocumentDBGettingStarted** 단추를 선택하여 애플리케이션을 실행합니다.

## <a id="Run"></a>C# 콘솔 애플리케이션 실행

Visual Studio에서 **DocumentDBGettingStarted** 단추를 선택하여 디버그 모드에서 애플리케이션을 빌드합니다. 시작한 앱의 출력이 콘솔 창에 표시됩니다. 출력은 추가한 쿼리 결과를 보여 주며, 아래 예제 텍스트와 일치해야 합니다.

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

이제 이 자습서를 완료했으며 실행되는 C# 콘솔 애플리케이션이 생겼습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 경우 리소스 그룹, Azure Cosmos 계정 및 모든 관련 리소스를 삭제해도 됩니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고, **삭제**를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB의 SQL API 계정에 저장된 데이터를 관리하는 .NET Core 앱을 빌드하는 방법을 알아보았습니다. 이제 다음 문서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB의 SQL API 계정을 사용하여 Java 콘솔 앱 빌드](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
