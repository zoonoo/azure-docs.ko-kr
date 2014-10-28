<properties title="Get started with a DocumentDB account" pageTitle="Get started with a DocumentDB account | Azure" description="Learn how to create and configure an Azure DocumentDB account, create databases, create collections, and store JSON documents within the account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="paulettm" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# DocumentDB 계정 시작

이 가이드에서는 **Azure DocumentDB(미리 보기)**를 사용하는 방법에 대해 설명합니다. 샘플은 C# 코드로 작성되었고 DocumentDB .NET SDK를 사용합니다. 여기서 다룬 시나리오에는 DocumentDB 계정 생성 및 구성, 데이터베이스 생성, 컬렉션 생성 및 계정에 JSON 문서 저장이 포함됩니다. Azure DocumentDB 사용에 대한 자세한 내용은 다음 단계 섹션을 참조하세요.

이 시작 가이드를 사용하려면 DocumentDB 계정 및 계정의 액세스 키(기본 또는 보조)가 있어야 합니다. 자세한 내용은 다음을 참조하세요.

-   [DocumentDB 계정 만들기][DocumentDB 계정 만들기]

## 목차

-   [DocumentDB 계정에 연결][DocumentDB 계정에 연결]
-   [데이터베이스 만들기][데이터베이스 만들기]
-   [컬렉션 만들기][컬렉션 만들기]
-   [문서 만들기][문서 만들기]
-   [DocumentDB 리소스 쿼리][DocumentDB 리소스 쿼리]
-   [다음 단계][다음 단계]

## <span id="Connect"></span></a>DocumentDB 계정에 연결

DocumentDB에 대해 프로그래밍상으로 작업할 수 있는 다양한 SDK 및 API가 있습니다. 아래의 샘플은 C# 코드로 작성된 것이며 DocumentDB .NET SDK를 사용합니다.

DocumentDB 계정에 대한 연결을 설정하기 위해 먼저 DocumentClient를 만듭니다. C# 응용 프로그램에는 다음과 같은 참조가 필요합니다.

    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Microsoft.Azure.Documents;  

DocumentDB 계정 끝점 및 계정과 연결된 기본 또는 보조 액세스 키를 사용하여 DocumentClient를 인스턴스화할 수 있습니다.

DocumentDB 계정 끝점 및 키는 Azure 관리 미리 보기 포털에서 사용 중인 DocumentDB 계정에 대한 블레이드에서 가져올 수 있습니다.

![][]

> 키 블레이드에서 사용 가능한 DocumentDB 액세스 키는 DocumentDB 계정 및 해당 계정의 리소스에 대한 관리 액세스 권한을 부여합니다. 또한 DocumentDB는 클라이언트가 계정 키 없이 부여된 사용 권한에 따라 DocumentDB 계정의 리소스를 읽고, 쓰고, 삭제할 수 있는 리소스 키 사용을 지원합니다.

다음 예제와 같은 코드를 사용하여 클라이언트를 만듭니다.

    private static string endpointUrl = "<your endpoint URI>";
    private static string authorizationKey = "<your key>";

    //Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(endpointUrl), authorizationKey);  

**Warning:** 소스 코드에 자격 증명을 저장해서는 안 됩니다. 이 샘플을 단순하게 유지하기 위해 소스 코드로 표시되었습니다. [Microsoft Azure 웹 사이트: 응용 프로그램 설정 및 연결 문자열 작동 방식][Microsoft Azure 웹 사이트: 응용 프로그램 설정 및 연결 문자열 작동 방식](영문)에서 자격 증명 저장 방법에 대해 자세히 알아보세요.

DocumentDB 계정에 연결하여 DocumentClient의 인스턴스를 만드는 방법을 알게 되었으므로 이제 DocumentDB 리소스에 대한 작업을 살펴보겠습니다.

## <span id="CreateDB"></span></a>데이터베이스 만들기

.NET SDK를 사용하여 DocumentClient의 CreateDatabaseAsync 메서드를 통해 DocumentDB 데이터베이스를 만들 수 있습니다.

    //Create a Database
     Database database = await client.CreateDatabaseAsync(
        new Database
        {
        Id = "FamilyRegistry"
        });

## <span id="CreateColl"></span></a>컬렉션 만들기

.NET SDK를 사용하여 DocumentClient의 CreateDocumentCollectionAsync 메서드를 통해 DocumentDB 컬렉션을 만들 수 있습니다. 위의 단계에서 생성된 데이터베이스는 몇 가지 속성을 포함하며 그중 한 가지가 SelfLink 속성입니다. 이러한 정보를 사용하여 이제 컬렉션을 만들 수 있습니다.

        //Create a document collection 
    documentCollection = new DocumentCollection
        {
            Id = "FamilyCollection"
        };

        documentCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,documentCollection); 

## <span id="CreateDoc"></span></a>문서 만들기

.NET SDK를 사용하여 DocumentClient의 CreateDocumentAsync 메서드를 통해 DocumentDB 문서를 만들 수 있습니다. 위의 단계에서 생성된 컬렉션은 몇 가지 속성을 포함하며 그중 한 가지가 DocumentsLink 속성입니다. 이러한 정보를 사용하여 이제 1개 이상의 문서를 삽입할 수 있습니다. 이 예제에서는 이름, 성별 및 나이와 같은 가족의 특성을 설명하는 Family 클래스를 사용한다고 가정합니다.

    private static async Task CreateDocuments(string    colSelfLink)
    {
        Family AndersonFamily = new Family
        {
            Id = "AndersenFamily",
            LastName = "Andersen",
            Parents =  new Parent[] {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay"}
            },
            Children = new Child[] {
                new Child
                { 
                    FirstName = "Henriette Thaulow", 
                    Gender = "female", 
                    Grade = 5, 
                    Pets = new [] {
                        new Pet { GivenName = "Fluffy" } 
                    }
                } 
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
        };

        await client.CreateDocumentAsync(colSelfLink, AndersonFamily);

        Family WakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new [] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child
                {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child
                {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        await client.CreateDocumentAsync(colSelfLink, WakefieldFamily);


## <span id="Query"></span></a>DocumentDB 리소스 쿼리

DocumentDB는 각 컬렉션에 저장된 JSON 문서에 대해 다양한 쿼리를 지원합니다. 아래 샘플 코드는 DocumentDB SQL 구문뿐 아니라 LINQ를 사용하는 다양한 쿼리를 보여 줍니다. 이러한 쿼리는 위의 단계에서 삽입한 문서에 대해 실행할 수 있습니다.

    //
    //Querying the documents using DocumentDB SQL for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink, 
    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
    Console.WriteLine("\tRead {0} from SQL", family);
    }

    //
    //Querying the documents using LINQ for the Andersen family
    //
    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
     Console.WriteLine("\tRead {0} from LINQ", family);
    }

    //
    //Querying the documents using LINQ lambdas for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink)
    .Where(f => f.Id == "AndersenFamily")
    .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    //
    //DocumentDB SQL -  using <> interchangably with != for "not equals"
    //
    families = client.CreateDocumentQuery<Family>(colSelfLink, "SELECT * FROM Families f WHERE f.id <> 'AndersenFamily'");

    //   
    // LINQ - combine equality and inequality
    //
    families = from f in client.CreateDocumentQuery<Family>(colSelfLink)
           where f.Id == "Wakefield" && f.Address.City != "NY"
           select f; 

## <span id="NextSteps"></span></a>다음 단계

-   [DocumentDB 계정 모니터링][DocumentDB 계정 모니터링] 방법에 대해 자세히 알아봅니다.
-   프로그래밍 모델에 대한 자세한 내용은 [DocumentDB 설명서 페이지][DocumentDB 설명서 페이지]에 대한 개발 섹션을 참조하세요.

  [DocumentDB 계정 만들기]: ../documentdb-create-account/
  [DocumentDB 계정에 연결]: #Connect
  [데이터베이스 만들기]: #CreateDB
  [컬렉션 만들기]: #CreateColl
  [문서 만들기]: #CreateDoc
  [DocumentDB 리소스 쿼리]: #Query
  [다음 단계]: #NextSteps
  []: ./media/documentdb-get-started/gs1.png
  [Microsoft Azure 웹 사이트: 응용 프로그램 설정 및 연결 문자열 작동 방식]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [DocumentDB 계정 모니터링]: http://go.microsoft.com/fwlink/p/?LinkId=402378
  [DocumentDB 설명서 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=402319
