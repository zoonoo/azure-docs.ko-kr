<properties 
	pageTitle="DocumentDB .NET SDK 시작 | Azure" 
	description="Azure DocumentDB 계정을 작성 및 구성하고, 데이터베이스와 컬렉션을 만들고, NoSQL 문서 데이터베이스 계정 내에 JSON 문서를 저장하는 방법을 알아봅니다." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="04/29/2015" 
	ms.author="anhoh"/>

# DocumentDB .NET SDK 시작  

이 자습서에서는 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 및 [DocumentDB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)를 사용하여 시작하는 방법을 보여 줍니다. DocumentDB 리소스를 만들고 쿼리하는 콘솔 응용 프로그램을 빌드한 후 콘솔 창에 출력을 기록합니다.

DocumentDB는 NoSQL 문서 데이터베이스 서비스로서, [사용할 수 있는 여러 API 및 SDK](https://msdn.microsoft.com/library/dn781482.aspx)를 포함하고 있습니다. 이 문서의 코드는 C\#으로 작성되었으며, NuGet 패키지로 패키지되고 배포되는 DocumentDB .NET SDK를 사용합니다.

이 문서에서 다루는 시나리오는 다음과 같습니다.

- DocumentDB 계정 만들기 및 연결
- Visual Studio 솔루션에 DocumentDB 추가
- 데이터베이스 만들기
- 컬렉션 만들기
- JSON 문서 만들기
- 리소스 쿼리 
- 데이터베이스 삭제 

자습서를 완료할 시간이 없고 작동하는 솔루션을 가져오고 싶으신가요? 걱정하지 마세요. [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침은 [전체 솔루션 다운로드](#GetSolution)를 참조하세요.

## 필수 조건

이 문서의 지침을 따르기 전에 다음이 있는지 확인해야 합니다.

- 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하십시오.
- [Visual Studio 2013](http://www.visualstudio.com/) 업데이트 4 이상

## 1단계: DocumentDB 계정 만들기

먼저 DocumentDB 계정을 만듭니다. 계정이 이미 있는 경우 [Visual Studio 솔루션 설치](#SetupVS)로 건너뛸 수 있습니다.

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a> 2단계: Visual Studio 솔루션 설치

1. 컴퓨터에서 **Visual Studio**를 엽니다.
2. **파일** 메뉴에서 **새로 만들기**를 선택하고 **프로젝트**를 선택합니다.
3. **새 프로젝트 대화 상자**에서 **템플릿**/**Visual C\#**/**콘솔 응용 프로그램**을 선택하고 프로젝트 이름을 지정한 후 **추가**를 클릭합니다.
4. **솔루션 탐색기**에서 Visual Studio 솔루션 아래에 있는 새 콘솔 응용 프로그램을 마우스 오른쪽 단추로 클릭합니다.
5. 그런 다음 메뉴를 종료하지 않고 **NuGet 패키지 관리...**를 클릭합니다.
6. **NuGet 패키지 관리** 창의 맨 왼쪽 패널에서 **온라인**/**nuget.org**를 클릭합니다.
7. **온라인 검색** 입력 상자에서 **DocumentDB Client Library**를 검색합니다.
8. 결과 내에서 **Microsoft Azure DocumentDB Client Library**를 찾아 **설치**를 클릭합니다.

잘하셨습니다. 이제 DocumentDB 작업을 시작할 준비가 되었습니다.

## <a id="Connect"></a> 3단계: DocumentDB 계정에 연결

DocumentDB 계정에 대한 연결을 설정하기 위해 먼저 [DocumentClient](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.aspx) 클래스의 새 인스턴스를 만듭니다. C\# 응용 프로그램의 시작 부분에 다음 참조가 필요합니다.

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;
 
다음으로, DocumentDB 계정 끝점 및 계정과 연결된 기본 또는 보조 액세스 키를 사용하여 **DocumentClient**를 인스턴스화할 수 있습니다. 클래스에 다음 속성을 추가합니다.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

이제 클래스에 **GetStartedDemo**라는 새 비동기 작업을 만듭니다. 이 새 작업 내에서 **DocumentClient**를 만들고 설정합니다.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient.
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey); 
	}

아래 코드처럼 Main 메서드에서 비동기 작업을 호출합니다.

	public static void Main(string[] args)
    {
		try
    	{
        	GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

> [AZURE.WARNING]소스 코드에 자격 증명을 저장해서는 안 됩니다. 이 샘플을 단순하게 유지하기 위해 자격 증명이 소스 코드에 표시되어 있습니다. [Azure 웹 사이트: 응용 프로그램 문자열 및 연결 문자열 작동 방식](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)에서 프로덕션 환경에서 자격 증명을 저장하는 방법에 대한 자세한 내용을 참조하세요. 소스 코드 외부에 자격 증명을 저장하는 예제는 [GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs)의 샘플 응용 프로그램에서 확인할 수 있습니다.

EndpointUrl 및 AuthorizationKey의 값은 DocumentDB 계정의 URI 및 기본 키로서, DocumentDB 계정의 [키](https://portal.azure.com) 블레이드에서 얻을 수 있습니다.

![활성 허브, DocumentDB 계정 블레이드의 키 단추 및 키 블레이드의 URI, 기본 키 및 보조키 값이 강조 표시된 DocumentDB 계정을 보여 주는 Azure 포털의 스크린샷][keys]
 
이러한 키는 DocumentDB 계정 및 그 안에 포함된 리소스에 대한 관리 권한을 부여합니다. 또한 DocumentDB는 클라이언트가 계정 키 없이 부여된 사용 권한에 따라 DocumentDB 계정의 리소스를 읽고, 쓰고, 삭제할 수 있는 리소스 키 사용을 지원합니다. 리소스 키에 대한 자세한 내용은 [사용 권한](documentdb-resources.md#permissions) 및 [액세스 키 보기, 복사 및 다시 생성](documentdb-manage-account.md#keys)을 참조하세요.

DocumentDB 계정에 연결하여 **DocumentClient** 클래스의 인스턴스를 만드는 방법을 알게 되었으므로 이제 DocumentDB 리소스에 대한 작업을 살펴보겠습니다.

## 4단계: 데이터베이스 만들기
**DocumentClient** 클래스의 [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 메서드를 사용하여 [데이터베이스](documentdb-resources.md#databases)를 만들 수 있습니다. 데이터베이스는 여러 컬렉션으로 분할된 문서 저장소의 논리적 컨테이너입니다. **DocumentClient**를 만든 후 **GetStartedDemo** 메서드에서 새 데이터베이스를 만듭니다.

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

## <a id="CreateColl"></a>5단계: 컬렉션 만들기  

> [AZURE.WARNING]**CreateDocumentCollectionAsync**는 가격의 의미가 포함된 새 S1 컬렉션을 만듭니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하세요.

**DocumentClient** 클래스의 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 메서드를 사용하여 [컬렉션](documentdb-resources.md#collections)을 만들 수 있습니다. 컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다. 새로 만든 컬렉션은 [S1 성능 수준](documentdb-performance-levels.md)에 매핑됩니다. 위의 단계에서 생성된 데이터베이스는 몇 가지 속성을 포함하며 그중 한 가지가 [CollectionsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.database.collectionslink.aspx) 속성입니다. 이러한 정보를 사용하여 이제 데이터베이스를 만든 후 컬렉션을 만들 수 있습니다.

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
## <a id="CreateDoc"></a>6단계: 문서 만들기
**DocumentClient** 클래스의 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 메서드를 사용하여 [문서](documentdb-resources.md#documents)를 만들 수 있습니다. 문서는 사용자 정의\(임의\) JSON 콘텐츠입니다. 위의 단계에서 생성된 컬렉션은 몇 가지 속성을 포함하며 그중 한 가지가 [DocumentsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.documentcollection.documentslink.aspx) 속성입니다. 이러한 정보를 사용하여 이제 하나 이상의 문서를 삽입할 수 있습니다.

먼저 **Parent**, **Child**, **Pet**, **Address** 및 **Family** 클래스를 만들어야 합니다. 다음 내부 하위 클래스를 추가하여 이러한 클래스를 만듭니다.

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

다음으로, **GetStartedDemo** 비동기 메서드 내에 문서를 만듭니다.

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
 
이제 DocumentDB 계정에서 다음 데이터베이스, 컬렉션 및 문서를 만들었습니다.

![계정, 데이터베이스, 컬렉션 및 문서 간의 계층 관계를 보여 주는 다이어그램](./media/documentdb-get-started/account-database.png)

## <a id="Query"></a>7단계: DocumentDB 리소스 쿼리

DocumentDB는 각 컬렉션에 저장된 JSON 문서에 대해 다양한 [쿼리](documentdb-sql-query.md)를 지원합니다. 다음 샘플 코드는 DocumentDB SQL 구문뿐 아니라 LINQ를 사용하는 다양한 쿼리를 보여 줍니다. 이러한 쿼리는 이전 단계에서 삽입한 문서에 대해 실행할 수 있습니다. 이러한 쿼리를 **GetStartedDemo** 비동기 메서드에 추가합니다.

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

다음 다이어그램에서는 만든 컬렉션에 대해 DocumentDB SQL 쿼리 구문을 호출하는 방법을 보여 주며, 마찬가지로 동일한 논리가 LINQ 쿼리에 적용됩니다.

![쿼리의 의미와 범위를 보여 주는 다이어그램](./media/documentdb-get-started/collection-documents.png)

DocumentDB 쿼리는 이미 단일 컬렉션으로 범위가 지정되었기 때문에 [FROM](documentdb-sql-query.md/#from-clause) 키워드는 쿼리에서 선택 사항입니다. 따라서 "FROM Families f"를 "FROM root r" 또는 선택한 다른 변수 이름으로 교체할 수 있습니다. DocumentDB는 패밀리, 루트 또는 선택한 변수 이름이 기본적으로 현재 컬렉션을 참조하는 것으로 유추합니다.

## <a id="DeleteDatabase"></a>8단계: 데이터베이스 삭제

만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스\(컬렉션, 문서 등\)가 제거됩니다. 다음 코드 조각을 **GetStartedDemo** 비동기 메서드 끝에 추가하여 데이터베이스 및 문서 클라이언트를 삭제할 수 있습니다.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync(database.SelfLink);
	client.Dispose();

## <a id="Run"></a>9단계: 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다. **Main** 메서드 끝에 다음 코드 줄을 추가합니다. 그러면 응용 프로그램 실행이 완료되기 전에 콘솔 출력을 읽을 수 있습니다.

	Console.ReadLine();

이제 디버그 모드에서 응용 프로그램을 빌드하기 위해 Visual Studio에서 F5 키를 누릅니다.

시작한 앱의 출력이 표시됩니다. 출력은 추가한 쿼리 결과를 보여 주며, 아래 예제 텍스트와 일치해야 합니다.

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": "\"0000400c-0000-0000-0000-55233aed0000\"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": "\"0000400c-0000-0000-0000-55233aed0000\"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": "\"0000400c-0000-0000-0000-55233aed0000\"",
	  "_attachments": "attachments/"
	} from LINQ query
	{
	  "id": "AndersenFamily",
 	  "child": "Henriette Thaulow"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Jesse"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Lisa"
	}
	{ family = AndersenFamily, child = Henriette Thaulow }
	{ family = WakefieldFamily, child = Jesse }
	{ family = WakefieldFamily, child = Lisa }

> [AZURE.NOTE]데이터베이스를 제거하지 않고 응용 프로그램을 여러 번 실행하면 이미 사용 중인 ID를 사용하는 새 데이터베이스를 만드는 문제가 발생할 수 있습니다. 이를 방지하기 위해 동일한 ID를 사용하는 데이터베이스, 컬렉션 또는 문서가 있는지 확인할 수 있습니다. 이 작업을 수행하는 방법은 [GitHub 페이지](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started)를 참조하세요.
	
## <a id="GetSolution"></a> 전체 솔루션 다운로드
이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 빌드하려면 다음이 필요합니다.

-   [DocumentDB 계정][documentdb-create-account]
-   GitHub에서 제공하는 [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) 솔루션 

Visual Studio 2013에서 DocumentDB .NET SDK에 대한 참조를 복원하려면 솔루션 탐색기에서 **GetStarted** 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 복원 사용**을 클릭합니다. 다음으로, App.config 파일에서 EndpointUrl 및 AuthorizationKey 값을 [DocumentDB 계정에 연결](#Connect)에 설명된 대로 업데이트합니다.

## 다음 단계
-   보다 복잡한 ASP.NET MVC 샘플이 필요하신가요? [DocumentDB를 사용하여 ASP.NET MVC로 웹 응용 프로그램 빌드](documentdb-dotnet-application.md)를 참조하세요.
-	[DocumentDB 계정 모니터링](documentdb-monitor-accounts.md) 방법에 대해 자세히 알아봅니다.
-	[쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
-	[DocumentDB 설명서](../../services/documentdb/) 페이지의 개발 섹션에서 프로그래밍 모델에 대해 자세히 알아봅니다.

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: ../includes/media/documentdb-keys/keys.png

<!--HONumber=52-->
