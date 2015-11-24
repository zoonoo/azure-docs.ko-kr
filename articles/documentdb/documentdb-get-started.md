<properties
	pageTitle="NoSQL 자습서: DocumentDB .NET SDK | Microsoft Azure"
	description="DocumentDB .NET SDK를 사용하여 온라인 데이터베이스 및 C# 콘솔 응용 프로그램을 만드는 NoSQL 자습서입니다. DocumentDB는 JSON에 대한 NoSQL 데이터베이스입니다."
	keywords="NoSQL 자습서, 온라인 데이터베이스, C# 콘솔 응용 프로그램"
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
	ms.date="11/18/2015"
	ms.author="anhoh"/>

# NoSQL 자습서: DocumentDB C# 콘솔 응용 프로그램 

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

DocumentDB .NET SDK용 NoSQL 자습서를 시작합니다. 이 자습서를 따라하면 DocumentDB 리소스를 만들고 쿼리하는 콘솔 응용 프로그램이 생깁니다.

다음에 대해 설명합니다.

- DocumentDB 계정 만들기 및 연결
- Visual Studio 솔루션 구성
- 온라인 데이터베이스 만들기
- 컬렉션 만들기
- JSON 문서 만들기
- 컬렉션 쿼리
- 데이터베이스 삭제

시간이 없으십니까? 염려하지 마십시오. [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started)에서 전체 솔루션을 사용할 수 있습니다. 빠른 지침은 [전체 솔루션 다운로드](#GetSolution)를 참조하세요.

나중에 이 페이지 위쪽 또는 아래쪽에 있는 응답 단추를 통해 의견을 보내주세요. 직접 연락을 받고 싶은 경우 설명에 메일 주소를 포함하세요.

이제 시작하겠습니다.

## 필수 조건

다음 항목이 있는지 확인합니다.

- 활성 Azure 계정. 아직 구독하지 않은 경우 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
- [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).

## 1단계: DocumentDB 계정 만들기

DocumentDB 계정을 만들어 보겠습니다. 계정이 이미 있는 경우 [Visual Studio 솔루션 설치](#SetupVS)로 건너뛸 수 있습니다.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> 2단계: Visual Studio 솔루션 설치

1. 컴퓨터에서 **Visual Studio**를 엽니다.
2. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.
3. **새 프로젝트** 대화 상자에서 **템플릿**/**Visual C#**/**콘솔 응용 프로그램**을 선택하고 프로젝트 이름을 지정한 후 **확인**을 클릭합니다.
4. **솔루션 탐색기**에서 Visual Studio 솔루션 아래에 있는 새 콘솔 응용 프로그램을 마우스 오른쪽 단추로 클릭합니다.
5. 그런 다음 메뉴를 종료하지 않고 **NuGet 패키지 관리...**를 클릭합니다.
6. **NuGet 패키지 관리** 창의 맨 왼쪽 패널에서 **온라인**/**nuget.org**를 클릭합니다.
7. **온라인 검색** 입력 상자에서 **DocumentDB Client Library**를 검색합니다.
8. 결과 내에서 **Microsoft Azure DocumentDB Client Library**를 찾아 **설치**를 클릭합니다. DocumentDB 클라이언트 라이브러리의 패키지 ID는 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)입니다.

잘하셨습니다. 설치를 완료했으므로 코드를 작성해 보겠습니다.

##<a id="Connect"></a>3단계: DocumentDB 계정에 연결

먼저 Program.cs에서 C# 응용 프로그램의 시작 부분에 다음 참조를 추가합니다.

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT]이 NoSQL 자습서를 완료하려면 위의 종속성을 추가했는지 확인합니다.

다음으로 DocumentDB 계정 끝점과 기본 또는 보조 선택키를 저장합니다. [Azure Preview 포털](https://portal.azure.com)에서 확인할 수 있습니다.

![활성 허브, DocumentDB 계정 블레이드의 키 단추 및 키 블레이드의 URI, 기본 키 및 보조키 값이 강조 표시된 DocumentDB 계정을 보여 주는 Azure Preview 포털의 스크린샷][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

**DocumentClient**의 새 인스턴스를 만드는 것으로 데모 응용 프로그램을 시작해 보겠습니다. 이름이 **GetStartedDemo**인 새 비동기 작업을 만들고 새 **DocumentClient**를 인스턴스화합니다.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

아래 코드와 유사하게 **Main** 메서드에서 비동기 작업을 호출합니다.

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

> [AZURE.WARNING]소스 코드에 자격 증명을 저장해서는 안 됩니다. 이 샘플을 단순하게 유지하기 위해 자격 증명이 소스 코드에 표시되어 있습니다. [Azure 웹 사이트: 응용 프로그램 문자열 및 연결 문자열 작동 방식](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)에서 프로덕션 환경에서 자격 증명을 저장하는 방법에 대한 자세한 내용을 참조하세요. 소스 코드 외부에 자격 증명을 저장하는 예제는 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs)의 샘플 응용 프로그램에서 확인할 수 있습니다.

DocumentDB 계정에 연결하여 **DocumentClient** 클래스의 인스턴스를 만드는 방법을 알게 되었으므로 이제 DocumentDB 리소스에 대한 작업을 살펴보겠습니다.

## 4단계: 온라인 데이터베이스 만들기
**DocumentClient** 클래스의 [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 메서드를 사용하여 DocumentDB [데이터베이스](documentdb-resources.md#databases)를 만들 수 있습니다. 데이터베이스는 여러 컬렉션으로 분할된 JSON 문서 저장소의 논리적 컨테이너입니다. **DocumentClient**를 만든 후 **GetStartedDemo** 메서드에서 새 데이터베이스를 만듭니다.

	// Check to verify a database with the id=FamilyRegistry does not exist
	Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

	// If the database does not exist, create a new database
    if (database == null)
    {
    	database = await client.CreateDatabaseAsync(
    		new Database
            {
            	Id = "FamilyRegistry"
            });

		// Write the new database's id to the console
		Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateColl"></a>5단계: 컬렉션 만들기  

> [AZURE.WARNING]**CreateDocumentCollectionAsync**는 가격의 의미가 포함된 새 S1 컬렉션을 만듭니다. 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하세요.

**DocumentClient** 클래스의 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 메서드를 사용하여 [컬렉션](documentdb-resources.md#collections)을 만들 수 있습니다. 컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다. 새로 만든 컬렉션은 [S1 성능 수준](documentdb-performance-levels.md)에 매핑됩니다. **GetStartedDemo** 메서드에서 데이터베이스를 만든 후 이름이 **FamilyCollection**인 새 컬렉션을 만듭니다.

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

	// If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
    	documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
        	new DocumentCollection
            {
            	Id = "FamilyCollection"
            });

		// Write the new collection's id to the console
		Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateDoc"></a>6단계: JSON 문서 만들기
**DocumentClient** 클래스의 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 메서드를 사용하여 [문서](documentdb-resources.md#documents)를 만들 수 있습니다. 문서는 사용자 정의(임의) JSON 콘텐츠입니다. 이제 하나 이상의 문서를 삽입할 수 있습니다. 데이터베이스에 저장하려는 데이터가 이미 있다면 DocumentDB의 [데이터 마이그레이션 도구](documentdb-import-data.md)를 사용할 수 있습니다.

먼저 **Parent**, **Child**, **Pet**, **Address** 및 **Family** 클래스를 만들어야 합니다. **GetStartedDemo** 메서드 다음에 다음 내부 하위 클래스를 추가하여 이러한 클래스를 만듭니다.

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

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

	// If the document does not exist, create a new document
	if (document == null)
	{
	    // Create the Andersen Family document
	    Family andersonFamily = new Family
	    {
	        Id = "AndersenFamily",
	        LastName = "Andersen",
	        Parents = new Parent[] {
	            new Parent { FirstName = "Thomas" },
	            new Parent { FirstName = "Mary Kay"}
	        },
	        Children = new Child[] {
	            new Child
	            { 
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
	
	    // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
	    await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
	}

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
        Family wakefieldFamily = new Family
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

        // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
	}

이제 DocumentDB 계정에서 다음 데이터베이스, 컬렉션 및 문서를 만들었습니다.

![계정, 데이터베이스, 컬렉션 및 문서 간의 계층 관계를 보여 주는 다이어그램](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>7단계: DocumentDB 리소스 쿼리

DocumentDB는 각 컬렉션에 저장된 JSON 문서에 대해 다양한 [쿼리](documentdb-sql-query.md)를 지원합니다. 다음 샘플 코드는 DocumentDB SQL 구문뿐 아니라 LINQ를 사용하는 다양한 쿼리를 보여 줍니다. 이러한 쿼리는 이전 단계에서 삽입한 문서에 대해 실행할 수 있습니다. 이러한 쿼리를 **GetStartedDemo** 비동기 메서드에 추가합니다.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

다음 다이어그램에서는 만든 컬렉션에 대해 DocumentDB SQL 쿼리 구문을 호출하는 방법을 보여 주며, 마찬가지로 동일한 논리가 LINQ 쿼리에 적용됩니다.

![쿼리의 의미와 범위를 보여 주는 다이어그램](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

DocumentDB 쿼리는 이미 단일 컬렉션으로 범위가 지정되었기 때문에 [FROM](documentdb-sql-query.md#from-clause) 키워드는 쿼리에서 선택 사항입니다. 따라서 "FROM Families f"를 "FROM root r" 또는 선택한 다른 변수 이름으로 교체할 수 있습니다. DocumentDB는 패밀리, 루트 또는 선택한 변수 이름이 기본적으로 현재 컬렉션을 참조하는 것으로 유추합니다.

##<a id="DeleteDatabase"></a>8단계: 데이터베이스 삭제

만든 데이터베이스를 삭제하면 데이터베이스와 모든 자식 리소스(컬렉션, 문서 등)가 제거됩니다. 다음 코드 조각을 **GetStartedDemo** 비동기 메서드 끝에 추가하여 데이터베이스 및 문서 클라이언트를 삭제할 수 있습니다.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
	client.Dispose();

##<a id="Run"></a>9단계: C# 콘솔 응용 프로그램 실행

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
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
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
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
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
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query

축하합니다. 이 NoSQL 자습서를 완료했습니다!

##<a id="GetSolution"></a> 전체 솔루션 다운로드
이 문서의 모든 샘플을 포함하는 GetStarted 솔루션을 빌드하려면 다음이 필요합니다.

-   [DocumentDB 계정][documentdb-create-account]
-   GitHub에서 제공하는 [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) 솔루션

Visual Studio에서 DocumentDB .NET SDK에 대한 참조를 복원하려면 솔루션 탐색기에서 **GetStarted** 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 복원 사용**을 클릭합니다. 다음으로, App.config 파일에서 EndpointUrl 및 AuthorizationKey 값을 [DocumentDB 계정에 연결](#Connect)에 설명된 대로 업데이트합니다.

## 다음 단계

-   보다 복잡한 ASP.NET MVC NoSQL 자습서가 필요하신가요? [DocumentDB를 사용하여 ASP.NET MVC로 웹 응용 프로그램 빌드](documentdb-dotnet-application.md)를 참조하세요.
-	[DocumentDB 계정 모니터링](documentdb-monitor-accounts.md) 방법에 대해 자세히 알아봅니다.
-	[쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
-	[DocumentDB 설명서](../../services/documentdb/) 페이지의 개발 섹션에서 프로그래밍 모델에 대해 자세히 알아봅니다.

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/nosql-tutorial-keys.png
 

<!---HONumber=Nov15_HO4-->