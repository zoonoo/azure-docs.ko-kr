<properties 
	pageTitle="DocumentDB .NET SDK 시작 | Azure" 
	description="Azure DocumentDB 계정을 작성 및 구성하고, 데이터베이스와 컬렉션을 만들고, NoSQL 문서 데이터베이스 계정 내에 JSON 문서를 저장하는 방법을 알아봅니다." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="anhoh"/>

#DocumentDB .NET SDK 시작  

이 가이드에서는 [Microsoft Azure DocumentDB(미리 보기)](https://portal.azure.com/#gallery/Microsoft.DocumentDB) 및 [DocumentDB .NET SDK](https://go.microsoft.com/fwlink/p/?linkid=402989)(영문)를 사용하여 시작하는 방법을 보여 줍니다. DocumentDB는 NoSQL 문서 데이터베이스 서비스로서, [사용할 수 있는 여러 API 및 SDK](https://go.microsoft.com/fwlink/p/?linkid=522476)(영문)를 포함하고 있습니다. 이 문서의 코드 샘플은 C#으로 작성되었으며, NuGet 패키지로 패키지되고 배포되는 DocumentDB .NET SDK를 사용합니다. 

이 문서에서 다루는 시나리오에는 DocumentDB 계정 만들기 및 구성, 데이터베이스 만들기, 컬렉션 만들기 및 계정에 JSON 문서 저장이 포함됩니다. 이러한 각 샘플은 [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started)(영문)에서 제공하는 전체 솔루션의 일부입니다. [솔루션을 다운로드](#GetSolution)하여 컨텍스트에서 샘플 코드를 보거나 이 문서의 샘플을 검토할 수 있습니다.

##<a id="CreateAccount"></a>DocumentDB 계정 만들기

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

##<a id="Connect"></a>DocumentDB 계정에 연결

DocumentDB 계정에 대한 연결을 설정하기 위해 먼저 [DocumentClient](https://go.microsoft.com/fwlink/p/?linkid=522477) 클래스의 새 인스턴스를 만듭니다. C# 응용 프로그램에는 다음과 같은 참조가 필요합니다.  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
다음으로, DocumentDB 계정 끝점 및 계정과 연결된 기본 또는 보조 액세스 키를 사용하여 **DocumentClient**를 인스턴스화할 수 있습니다. 다음과 유사한 코드를 사용하여 클라이언트를 만듭니다.   

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

> [AZURE.WARNING] 소스 코드에 자격 증명을 저장해서는 안 됩니다. 이 샘플을 단순하게 유지하기 위해 자격 증명이 소스 코드에 표시되어 있습니다. [Azure 웹 사이트: 응용 프로그램 문자열 및 연결 문자열 작동 방식](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)(영문)에서 프로덕션 환경에서 자격 증명을 저장하는 방법에 대한 자세한 내용을 참조하세요. 

EndpointUrl 및 AuthorizationKey의 값은 DocumentDB 계정의 URI 및 기본 키로서, DocumentDB 계정의 [Azure Preview 관리 포털](https://portal.azure.com) 블레이드에서 얻을 수 있습니다. 

![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the KEYS button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade][1]
 
이러한 키는 DocumentDB 계정 및 그 안에 포함된 리소스에 대한 관리 권한을 부여합니다. 또한 DocumentDB는 클라이언트가 계정 키 없이 부여된 사용 권한에 따라 DocumentDB 계정의 리소스를 읽고, 쓰고, 삭제할 수 있는 리소스 키 사용을 지원합니다. 리소스 키에 대한 자세한 내용은 [DocumentDB 리소스 모델 및 개념](documentdb-resources.md)의 권한 섹션을 참조하세요.

DocumentDB 계정에 연결하여 **DocumentClient** 클래스의 인스턴스를 만드는 방법을 알게 되었으므로 이제 DocumentDB 리소스에 대한 작업을 살펴보겠습니다.  

##<a id="CreateDB"></a>데이터베이스 만들기
**DocumentClient** 클래스의 [CreateDatabaseAsync](https://go.microsoft.com/fwlink/p/?linkid=522478) 메서드를 사용하여 데이터베이스를 만들 수 있습니다.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>컬렉션 만들기  

**DocumentClient** 클래스의 [CreateDocumentCollectionAsync](https://go.microsoft.com/fwlink/p/?linkid=522479) 메서드를 사용하여 컬렉션을 만들 수 있습니다.  이전 단계에서 만든 데이터베이스는 몇 가지 속성을 포함하며 그 중 한 가지가 [CollectionsLink](https://go.microsoft.com/fwlink/p/?linkid=522481) 속성입니다.  이러한 정보를 사용하여 이제 컬렉션을 만들 수 있습니다.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>문서 만들기	
**DocumentClient** 클래스의 [CreateDocumentAsync](https://go.microsoft.com/fwlink/p/?linkid=522482) 메서드를 사용하여 문서를 만들 수 있습니다.  이전 단계에서 만든 컬렉션은 몇 가지 속성을 포함하며 그 중 한 가지가 [DocumentsLink](https://go.microsoft.com/fwlink/p/?linkid=522483) 속성입니다.  이러한 정보를 사용하여 이제 하나 이상의 문서를 삽입할 수 있습니다.  이 예제에서는 이름, 성별 및 나이와 같은 가족의 특성을 설명하는 Family 클래스를 사용한다고 가정합니다. 샘플 Family 클래스를 보려면 [GitHub 리포지토리](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs)를 참조하세요. 

    // Create the Andersen family document.
	Family AndersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child { 
                FirstName = "Henriette Thaulow", 
                Gender = "female", 
                Grade = 5, 
                Pets = new Pet[] {
                    new Pet { GivenName = "Fluffy" } 
                }
            } 
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, AndersenFamily);
    
    // Create the WakeField family document.
    Family WakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        Parents = new Parent[] {
            new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
            new Parent { FamilyName= "Miller", FirstName= "Ben" }
        },
        Children = new Child[] {
            new Child {
                FamilyName= "Merriam", 
                FirstName= "Jesse", 
                Gender= "female", 
                Grade= 8,
                Pets= new Pet[] {
                    new Pet { GivenName= "Goofy" },
                    new Pet { GivenName= "Shadow" }
                }
            },
            new Child {
                FamilyName= "Miller", 
                FirstName= "Lisa", 
                Gender= "female", 
                Grade= 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, WakefieldFamily);
 

##<a id="Query"></a>DocumentDB 리소스 쿼리

DocumentDB는 각 컬렉션에 저장된 JSON 문서에 대해 다양한 쿼리를 지원합니다.  다음 샘플 코드는 DocumentDB SQL 구문뿐 아니라 LINQ를 사용하는 다양한 쿼리를 보여 줍니다. 이러한 쿼리는 이전 단계에서 삽입한 문서에 대해 실행할 수 있습니다. 

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQL with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }
	
##<a id="GetSolution"></a>전체 솔루션 다운로드
이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 빌드하려면 다음이 필요합니다.

-   [DocumentDB 계정][documentdb-create-account].
-   GitHub에서 제공하는 [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started)(영문) 솔루션 

Visual Studio 2013에서 DocumentDB .NET SDK에 대한 참조를 복원하려면 솔루션 탐색기에서 GetStarted 솔루션을 마우스 오른쪽 단추로 클릭한 다음 NuGet 패키지 복원 사용을 클릭합니다. 다음으로, App.config 파일에서 EndpointUrl 및 AuthorizationKey 값을 [DocumentDB 계정에 연결](#Connect)에 설명된 대로 업데이트합니다. 

##<a id="NextSteps"></a>다음 단계
-	[DocumentDB 계정 모니터링](https://go.microsoft.com/fwlink/p/?LinkId=402378) 방법을 알아봅니다.
-	[쿼리 실습](http://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
-	[DocumentDB 설명서 페이지](../documentation/services/documentdb/)의 개발 섹션에서 프로그래밍 모델에 대해 자세히 알아봅니다.


[DocumentDB 계정에 연결]: #Connect
[데이터베이스 만들기]: #CreateDB
[컬렉션 만들기]: #CreateColl
[문서 만들기]: #CreateDoc
[DocumentDB 리소스 쿼리]: #Query
[다음 단계]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[1]: ../includes/media/documentdb-keys/keys.png


<!--HONumber=49-->