<properties 
	pageTitle="DocumentDB를 사용하여 ASP.NET MVC로 웹 응용 프로그램 빌드 | Azure" 
	description="DocumentDB와 .NET을 사용하여 할 일 모음 웹 응용 프로그램을 빌드하는 방법을 알아봅니다. Azure 웹 사이트에서 호스트되는 ASP.NET MVC 웹 응용 프로그램의 데이터를 저장하고 액세스합니다." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/23/2015" 
	ms.author="ryancraw"/>

#<a name="_Toc395809351"></a>DocumentDB를 사용하여 ASP.NET MVC로 웹 응용 프로그램 빌드

Azure DocumentDB를 효율적으로 활용하여 JSON 문서를 저장 및 쿼리할 수 있는 방법을 강조하기 위해 이 문서에서는 Azure DocumentDB를 사용하여 todo 모음 웹 응용 프로그램을 빌드하는 방법을 보여 주는 종합적인 연습을 제공합니다. 작업은 Azure DocumentDB에 JSON 문서로 저장됩니다.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image1.png)

이 연습에서는 Azure에서 제공하는 DocumentDB 서비스를 사용하여 Azure에서 호스트되는 ASP.NET MVC 웹 응용 프로그램의 데이터를 저장하고 액세스하는 방법을 보여 줍니다.

> [AZURE.TIP] 이 자습서에서는 이전에 ASP.NET MVC 및 Azure 웹 사이트를 사용해 본 경험이 있다고 가정합니다. ASP.NET 또는 [필수 도구](#_Toc395637760)를 처음 사용하는 경우 [GitHub](https://github.com/Azure/azure-documentdb-net)(영문)에서 전체 [todo](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/todo)(영문) 자습서 프로젝트를 다운로드하고 [이 문서의 끝에 있는 지침](#GetProject)을 사용하여 이 프로젝트를 빌드하는 것이 좋습니다. 프로젝트를 빌드하고 나면 이 문서를 검토하여 프로젝트의 컨텍스트에서 코드를 이해할 수 있습니다.

## <a name="_Toc395637760"></a>필수 조건

이 문서의 지침을 따르기 전에 다음이 있는지 확인해야 합니다.

- 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](../../pricing/free-trial/)을 참조하세요.
- [Visual Studio 2013](http://www.visualstudio.com/) 이상 또는 [Visual Studio Express](무료 버전)
- Azure SDK for .NET 버전 2.3 이상([Microsoft 웹 플랫폼 설치 관리자][]를 통해 사용 가능)

이 문서의 모든 스크린샷은 Visual Studio 2013 업데이트 3 및 Azure SDK for .NET 버전 2.4를 사용하여 생성되었습니다. 시스템이 다른 버전으로 구성된 경우 화면과 옵션이 일부 달라질 수 있지만 위의 필수 구성 요소를 충족하면 솔루션을 사용할 수 있습니다.

## <a name="_Toc395637761"></a>1단계: DocumentDB 데이터베이스 계정 만들기

먼저 DocumentDB 계정을 만듭니다. 계정이 이미 있는 경우를 [새 ASP.NET MVC 응용 프로그램 만들기](#_Toc395637762)로 건너뛸 수 있습니다.

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

<br/>
이제 새 ASP.NET MVC 응용 프로그램을 처음부터 만드는 방법을 살펴보겠습니다. 

## <a name="_Toc395637762"></a>2단계: 새 ASP.NET MVC 응용 프로그램 만들기

이제 새 ASP.NET 프로젝트를 만듭니다.

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기**를 가리킨 후 **프로젝트**를 클릭합니다.

   	**새 프로젝트** 대화 상자가 나타납니다.
2. **프로젝트 형식** 창에서 **템플릿**, **Visual C#**, **웹**을 확장한 후 **ASP.NET 웹 응용 프로그램**을 선택합니다.

  	![Screen shot of the New Project dialog box with the ASP.NET Web Application project type highlighted](./media/documentdb-dotnet-application/image10.png)

3. **이름** 상자에 프로젝트의 이름을 입력합니다. 이 자습서에서는 "todo"라는 이름을 사용합니다. 
4. **찾아보기**를 클릭하여 프로젝트를 만들 폴더로 이동한 후 **확인**을 클릭합니다.

  	**새 ASP.NET 프로젝트** 대화 상자가 나타납니다.

  	![Screen shot of the New ASP.NET Project dialog box with the MVC template highlighted and the Host in the cloud box checked](./media/documentdb-dotnet-application/image11.png)

5. 템플릿 창에서 **MVC**를 선택합니다.
6. Azure에서 응용 프로그램을 호스팅하려는 경우 오른쪽 아래에서 **클라우드에 호스트**를 선택하여 Azure에서 응용 프로그램이 호스트되도록 합니다. 클라우드에 호스트하도록 선택하고 Azure 웹 사이트에서 호스트되는 응용 프로그램을 실행하도록 선택했습니다. 이 옵션을 선택하면 Azure 웹 사이트가 미리 프로비전되며 최종 응용 프로그램을 훨씬 쉽게 배포할 수 있습니다. 다른 곳에 호스트하거나 Azure에서 구성하지 않으려면 **클라우드에 호스트** 옵션을 선택 취소합니다.
7. **확인**을 클릭하면 Visual Studio에서 빈 ASP.NET MVC 템플릿을 스캐폴딩합니다. 
8. 클라우드에 호스트를 선택하면 Azure 계정에 로그인하고 새 웹 사이트에 대한 값을 입력하라는 추가 화면이 하나 이상 표시됩니다. 모든 추가 값을 지정하고 계속합니다. 

  	여기서 Azure SQL 데이터베이스 서버를 사용하지 않을 것이기 때문에 "데이터베이스 서버"를 선택하지 않았으며, 나중에 Azure Preview 포털에서 새 Azure DocumentDB 계정을 만들 것입니다. 

  	![Screen shot of the Configure Microsoft Azure Website dialog box](./media/documentdb-dotnet-application/image11_1.png)

9. Visual Studio에서 상용구 MVC 응용 프로그램을 만들면 로컬에서 실행할 수 있는 빈 ASP.NET 응용 프로그램을 갖게 됩니다.

모두 ASP.NET "Hello World" 응용 프로그램을 본 적이 있다고 확신하므로 프로젝트 로컬 실행은 건너뛰겠습니다. 바로 이 프로젝트에 DocumentDB를 추가하고 응용 프로그램을 작성하겠습니다.

## <a name="_Toc395637767"></a>3단계: 프로젝트에 DocumentDB 추가

이 솔루션에 필요한 ASP.NET MVC 배관이 대부분
처리되었습니다. 이제 이 자습서의 실제 목적으로, 웹 응용 프로그램에 Azure DocumentDB를 추가하겠습니다.

1. DocumentDB .NET SDK는 패키지되어 NuGet 패키지로 배포되며, Visual Studio에서 NuGet 패키지를 다운로드하려면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭하여 Visual Studio에서 NuGet 패키지 관리자를 사용합니다.

  	**NuGet 패키지 관리** 대화 상자가 나타납니다.

  	![Sreen shot of the right-click options for the project in Solution Explorer, with Manage NuGet Packages highlighted.](./media/documentdb-dotnet-application/image21.png)

2. **온라인 검색** 상자에 "Azure DocumentDB"를 입력합니다. 결과에서 **Microsoft Azure DocumentDB Client Libraries** 패키지를 설치합니다. 그러면 DocumentDB 패키지 및 모든 종속성(예: Newtonsoft.Json)이 다운로드되어 설치됩니다.

  	> [AZURE.NOTE] 서비스가 아직 미리 보기로 제공되므로 NuGet 패키지는 "시험판"으로 표시됩니다. "시험판 포함" 옵션을 포함해야 합니다. 그렇지 않으면 패키지를 찾을 수 없습니다. 

  	또는 Package Command Console을 사용하여 다음을 입력하면 패키지를 설치할 수 있습니다.

    	Install-Package Microsoft.Azure.Documents.Client -Pre

3. 패키지가 설치되고 나면 Visual Studio 솔루션은 Microsoft.Azure.Documents.Client 및 Newtonsoft.Json이라는 두 개의 새 참조가 추가된 상태로 다음과 유사합니다.

  	![Sreen shot of the two references added to the project in Solution Explorer.](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>4단계: ASP.NET MVC 응용 프로그램 설정
 
이제 이 MVC 응용 프로그램에 모델, 뷰 및 컨트롤러를 추가합니다.

- [모델 추가](#_Toc395637764).
- [컨트롤러 추가](#_Toc395637765).
- [뷰 추가](#_Toc395637766).


### <a name="_Toc395637764"></a>모델 추가

먼저 MVC의 **M**인 모델을 만들겠습니다. 

1. 솔루션 탐색기에서 **Models** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **클래스**를 차례로 클릭합니다.

  	**새 항목 추가** 대화 상자가 나타납니다.

2. 새 클래스 이름을 **Item**으로 지정한 후 다음 코드를 새 Item.cs 파일에 추가합니다.

        public class Item
        {
        	[JsonProperty(PropertyName="id")]
        	public string Id { get; set; }
		
        	[JsonProperty(PropertyName="name")]
        	public string Name { get; set; }
		
        	[JsonProperty(PropertyName = "desc")]
        	public string Description { get; set; }
		
       		[JsonProperty(PropertyName="isComplete")]
        	public bool Completed { get; set; }    
		}

	DocumentDB의 모든 데이터가 네트워크를 통해 전달되고 JSON으로 저장됩니다. JSON.NET에서 개체를 직렬화/역직렬화하는 방식을 제어하기 위해 방금 만든 **Item** 클래스에서 본 것처럼 **JsonProperty** 특성을 사용할 수 있습니다. 꼭 **필요한 과정은 아니지만** 저는 제가 만든 속성이 JSON camelCase 명명 규칙을 따르는지 확인하고 싶습니다. 

	JSON의 경우 속성 이름 형식을 제어할 수 있을 뿐만 아니라, **Description** 속성에 대해 했던 것처럼 .NET 속성의 이름을 완전히 다시 지정할 수 있습니다. 

	원하는 경우 여기서도 **JsonConverter** 개체를 사용하여 직렬화를 처리하는 방식을 완전히 제어할 수 있습니다.  

3. Visual Studio에서 여기서 사용한 **JsonProperty** 특성을 확인하도록 하기 위해 클래스 파일의 using 섹션에 다음 using문을 추가해야 합니다.

    	using Newtonsoft.Json;

### <a name="_Toc395637765"></a>컨트롤러 추가

**M** 작업을 마쳤으므로 이제 MVC의 **C**인 컨트롤러 클래스를 만듭니다.

1. **솔루션 탐색기**에서 **Controllers** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **컨트롤러**를 차례로 클릭합니다.

    **스캐폴드 추가** 대화 상자가 나타납니다.

2. **MVC 5 컨트롤러 - 비어 있음**을 선택한 후 **추가**를 클릭합니다.

	![Screen shot of the Add Scaffold dialog box with the MVC 5 Controller - Empty option highlighted](./media/documentdb-dotnet-application/image14.png)

3. 새 컨트롤러의 이름을 **ItemController**로 지정합니다.

	![Screen shot of the Add Controller dialog box](./media/documentdb-dotnet-application/image15.png)

파일을 만들고 나면 Visual Studio 솔루션은 **솔루션 탐색기**에 새 ItemController.cs 파일이 있는 상태로 다음과 유사합니다.

![Screen shot of Solution Explorer with the new ItemController.cs file and Item.cs file highlighted](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766"></a>뷰 추가

끝으로, MVC의 **V**인 뷰를 만듭니다.

- [항목 인덱스 뷰 추가](#AddItemIndexView).
- [새 항목 뷰 추가](#AddNewIndexView).
- [항목 편집 뷰 추가](#_Toc395888515).


#### <a name="AddItemIndexView"></a>항목 인덱스 뷰 추가

1. **솔루션 탐색기**에서 ***Views*** 폴더를 확장하고 앞에서 **ItemController**를 추가할 때 Visual Studio에서 만들어진 빈 **Item** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**, **뷰**를 차례로 클릭합니다.

![Screen shot of Solution Explorer showing the Item folder with the Add View commands highlighted](./media/documentdb-dotnet-application/image17.png)

2. **뷰 추가** 대화 상자에서 다음을 수행합니다.
	- **뷰 이름** 상자에 ***Index***를 입력합니다.
	- **템플릿** 상자에서 ***List***를 선택합니다.
	- **모델 클래스** 상자에서 ***Item (todo.Models)***을 선택합니다.
	- 레이아웃 페이지 상자에 ***~/Views/Shared/_Layout.cshtml***을 입력합니다.
	- **추가**를 클릭합니다.
	
![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image18.png)

3. 이러한 값이 모두 설정된 후 **추가**를 클릭하면 Visual Studio에서 뷰를 만듭니다. Visual Studio는 템플릿 뷰를 만듭니다. 완료되면 Visual Studio가 만들어진 cshtml 파일을 엽니다. 나중에 돌아올 것이므로 Visual Studio에서 해당 파일을 닫아도 됩니다.

#### <a name="AddNewIndexView"></a>새 항목 뷰 추가

**항목 인덱스** 뷰를 만든 방법과 유사하게 이제 새 **항목**을 만들기 위한 새 뷰를 만듭니다.

**뷰 추가** 대화 상자에서 다음을 수행합니다.

- **뷰 이름** 상자에 ***Create***를 입력합니다.
- **템플릿** 상자에서 ***Create***를 선택합니다.
- **모델 클래스** 상자에서 ***Item (todo.Models)***을 선택합니다.
- 레이아웃 페이지 상자에 ***~/Views/Shared/_Layout.cshtml***을 입력합니다.
- **추가**를 클릭합니다.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image19.png)

#### <a name="_Toc395888515"></a>항목 편집 뷰 추가

마지막으로, 이전과 동일한 방식으로 **항목**을 편집하기 위한 최종 뷰를 추가합니다.


**뷰 추가** 대화 상자에서 다음을 수행합니다.

- **뷰 이름** 상자에 ***Edit***를 입력합니다.
- **템플릿** 상자에서 ***Edit***를 선택합니다.
- **모델 클래스** 상자에서 ***Item (todo.Models)***을 선택합니다.
- **부분 뷰로 만들기**를 선택합니다.
- **추가**를 클릭합니다.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image20.png)

이 작업이 완료되면 나중에 이러한 뷰로 돌아올 것이므로 Visual Studio에서 cshtml 문서를 닫습니다.

## <a name="_Toc395637769"></a>5단계: DocumentDB 연결

이 섹션에서는 다음을 처리하기 위한 코드를 추가합니다.

- [완료되지 않은 항목 나열](#_Toc395637770).
- [항목 추가](#_Toc395637771).
- [항목 편집](#_Toc395637772).

### <a name="_Toc395637770"></a>완료되지 않은 항목 나열

1. **ItemController**를 열고 Visual Studio가 추가한 클래스 내의 모든 코드를 제거합니다(클래스는 유지). DocumentDB를 사용하여 하나씩 다시 작성하겠습니다.

2. 이제 비어 있는 **ItemController** 클래스 내에 다음 코드 조각을 추가합니다.

    	public ActionResult Index()
    	{
			var items = DocumentDBRepository<Item>.GetItems(d => !d.Completed);
			return View(items);
    	}

	이 코드는 아직 만들지 않은 **DocumentDBRepository**라는 "pseudo repository" 클래스도 사용합니다. "pseudo repository" 클래스는 모든 DocumentDB 특정 코드가 들어 있는 도우미 클래스입니다. 이 연습의 목적을 위해 실제 응용 프로그램을 작성하는 경우처럼 팩터리 및 리포지토리 패턴을 사용한 종속성 주입을 통해 전체 데이터 액세스 계층을 구현하지 않습니다. 
	이 연습의 목적을 위해 편의상 모든 데이터 액세스 논리를 하나의 프로젝트에 바로 배치하여 작업을 간단히 만들고 DocumentDB 특정 비트에 초점을 맞추겠습니다.

3. 프로젝트에 새 클래스를 추가하고 이름을 **DocumentDBRepository**라고 지정합니다. 
4. **DocumentDBRepository** 클래스의 코드를 다음과 같이 바꿉니다.

    	public static class DocumentDBRepository<T>
    	{
	       using Microsoft.Azure.Documents; 
	       using Microsoft.Azure.Documents.Client; 
	       using Microsoft.Azure.Documents.Linq; 
		
     	   private static string databaseId;
     	   private static String DatabaseId
     	   {
				get
				{
					if (string.IsNullOrEmpty(databaseId))
					{
						databaseId = ConfigurationManager.AppSettings["database"];
					}
				
					return databaseId;
				}
       	 	}
			  
    	    private static string collectionId;
    	    private static String CollectionId
    	    {
				get
				{
					if (string.IsNullOrEmpty(collectionId))
					{
						collectionId = ConfigurationManager.AppSettings["collection"];
					}
				
					return collectionId;
				}
    	    }
					
    	    private static Database database;
    	    private static Database Database
    	    {
				get
				{
					if (database == null)
					{
						database = ReadOrCreateDatabase();
					}
					
					return database;
				}
    	    }
			
    	    private static DocumentCollection collection;
    	    private static DocumentCollection Collection
    	    {
				get
				{
					if (collection == null)
					{
						collection = ReadOrCreateCollection(Database.SelfLink);
					}
					
					return collection;
				}
    	    }
			
    	    private static DocumentClient client;
    	    private static DocumentClient Client
    	    {
    	        get
    	        {
    	            if (client == null)
    	            {
						string endpoint = ConfigurationManager.AppSettings["endpoint"];
						string authKey = ConfigurationManager.AppSettings["authKey"];
						Uri endpointUri = new Uri(endpoint);
						client = new DocumentClient(endpointUri, authKey);
    	            }
    	            
    	            return client;
    	        }
    	    }
    	}

5. Visual Studio에서 모든 네임스페이스를 확인하는 데 시간을 소요합니다. 여기에는 다음 using 지시문을 DocumentDBRepository.cs 파일에 추가하는 작업이 포함되어야 합니다.

		using Microsoft.Azure.Documents;
		using Microsoft.Azure.Documents.Client;
		using Microsoft.Azure.Documents.Linq;
		using System.Configuration;

 	NuGet 패키지가 성공적으로 설치된 경우 Visual Studio에서 모든 네임스페이스를 쉽게 확인할 수 있습니다. **ReadOrCreateDatabase** 및 **ReadOrCreateCollection** 메서드에 대한 참조는 이 메서드를 추가할 때까지 확인되지 않습니다.
 
6. DocumentDB 데이터베이스 및 문서 컬렉션을 읽거나 만들기 위해 여기서 두 메서드 호출이 사용됩니다. 따라서 다음 두 메서드를 **DocumentDBRepository** 클래스에 추가합니다.

    	private static DocumentCollection ReadOrCreateCollection(string databaseLink)
   		{
    	    var col = Client.CreateDocumentCollectionQuery(databaseLink)
        	                  .Where(c => c.Id == CollectionId)
        	                  .AsEnumerable()
        	                  .FirstOrDefault();
	
        	if (col == null)
        	{
        	    col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        	}
			
        	return col;
    	}
	
    	private static Database ReadOrCreateDatabase()
    	{
        	var db = Client.CreateDatabaseQuery()
        	                .Where(d => d.Id == DatabaseId)
        	                .AsEnumerable()
        	                .FirstOrDefault();
			
        	if (db == null)
        	{
        	    db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
        	}
			
        	return db;
    	}

	그러면 [**DocumentCollection**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.documentcollection.aspx)(영문)이라는 데이터베이스를 설정하고 [**DocumentClient**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx)(영문)를 통해 DocumentDB에 연결하는 일부 코드를 만듭니다. 

7. 구성에서 일부 값을 읽어올 것이므로 **Web.config** 파일을 열고 `<AppSettings>` 섹션 아래에 다음 줄을 추가합니다.
	
    	<add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    	<add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    	<add key="database" value="ToDoList"/>
    	<add key="collection" value="Items"/>
	
8. 이제 Azure 관리 포털의 키 블레이드를 사용하여 해당 값을 업데이트합니다. 키 블레이드의 **URI** 값을 끝점 키의 값으로 사용하고 키 블레이드의 **기본 키** 값을 authKey 키의 값으로 사용합니다.
	
	이제 작업을 수행하기 위한 코드를 추가하겠습니다. 	

9. todo 모음 응용 프로그램으로 가장 먼저 할 일은 완료되지 않은 항목을 표시하는 것입니다.  아래 메서드가 이러한 작업을 수행하므로 이 메서드를 복사해서 **DocumentDBRepository** 클래스 내의 아무 곳에나 붙여넣으면 됩니다.

	    public static IEnumerable<T> GetItems(Expression<Func<T, bool>> predicate) 
	    {
			return Client.CreateDocumentQuery<T>(Collection.DocumentsLink) 
				.Where(predicate) 
				.AsEnumerable(); 
		} 
	    
	이때 오류 없이 솔루션을 작성할 수 있어야 합니다.

	지금 응용 프로그램을 실행하면 **HomeController** 및 해당 컨트롤러의 **인덱스** 뷰로 이동합니다. 이것은 시작할 때 선택한 MVC 템플릿 프로젝트에 대한 기본 동작이지만 여기서는 사용하지 않습니다. 이 동작을 변경하기 위해 이 MVC 응용 프로그램의 라우팅을 변경하겠습니다.

11. ***App\_Start\RouteConfig.cs***를 열고 "defaults:"로 시작하는 줄을 찾은 후 다음과 같이 변경합니다.

    	defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

	이 구문은 이제 ASP.NET MVC에 라우팅 동작을 제어하기 위한 URL에 값이 지정되지 않은 경우 **Home** 대신 **Item**을 컨트롤러로 사용하고 사용자 **인덱스**를 뷰로 사용하라고 지시합니다.
	이제 응용 프로그램을 실행하면 **ItemController**를 호출하고 **GetIncompleteItems** 메서드의 결과를 **뷰**\\**항목**\\**인덱스** 뷰에 반환합니다. 

12. 이 프로젝트를 지금 빌드하여 실행하면 이제 다음과 같이 표시됩니다.    

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>항목 추가

빈 그리드 외에 확인할 항목이 있도록 데이터베이스에 일부 항목을 추가하겠습니다.

이미 **만들기**용 뷰와 사용자를 **만들기** 뷰로 이동하는 **인덱스** 뷰의 단추가 있습니다. DocumentDB에 레코드를 저장하기 위해 컨트롤러 및 리포지토리에 일부 코드를 추가하겠습니다.

1. ItemController.cs 파일을 열고 **Create** 작업에 대해 수행할 작업을 ASP.NET MVC에 알리는 다음 코드 조각을 추가합니다. 이 경우 앞에서 만든 관련 Create.cshtml 뷰를 렌더링합니다.

    	public ActionResult Create()
    	{ 
			return View(); 
   		}

	이제 **만들기** 뷰의 제출을 수락하는 코드를 이 컨트롤러에 더 추가해야 합니다.

2. 이 컨트롤러에 대한 폼 POST의 처리 방법을 ASP.NET MVC에 알리는 다음 코드 블록을 추가합니다.
	
    	[HttpPost]
    	[ValidateAntiForgeryToken]
   	 	public async Task<ActionResult> Create([Bind(Include = 	"Id,Name,Description,Completed")] Item item)  
  	  	{
			if (ModelState.IsValid)  
			{  
			    await DocumentDBRepository<Item>.CreateItemAsync(item);
			    return RedirectToAction("Index");  
			}   
			return View(item);   
		}

	**보안 정보**: **ValidateAntiForgeryToken** 특성은 여기서 교차 사이트 요청 위조 공격으로부터 이 응용 프로그램을 보호하는 데 사용됩니다. 이 특성을 추가하는 것 외에 뷰가 이 위조 방지 토큰과 작동하도록 해야 합니다. 이 주제에 대한 자세한 내용과 이를 올바르게 구현하는 방법의 예는 [교차 사이트 요청 위조 방지][](영문)를 참조하세요. [GitHub][]에서 제공하는 소스 코드에는 완벽하게 구현되어 있습니다.

	**보안 정보**: 또한 메서드 매개 변수에 **Bind** 특성을 사용하여 과도한 게시 공격으로부터 보호할 수 있습니다. 자세한 내용은 [ASP.NET MVC의 기본 CRUD 작업][](영문)을 참조하세요.

3. 이제 **Create** 메서드가 구현되었으므로 **ItemController**가 폼에서 **CreateDocument** 메서드로 **항목** 개체를 전달합니다. 따라서 이제 다음 메서드를 **DocumentDBRepository** 클래스에 추가합니다.

    	public static async Task<Document> CreateItemAsync(T item)
   	 	{
   	   		return await Client.CreateDocumentAsync(Collection.SelfLink, item);
   		}

	이 메서드는 단순히 전달된 개체를 받아서 DocumentDB에 저장합니다.

데이터베이스에 새 항목을 추가하는 데 필요한 코드가 완성되었습니다.


### <a name="_Toc395637772"></a>항목 편집

마지막으로 수행할 작업은 데이터베이스에서 **항목**을 편집하고 완료로 표시하는 기능입니다. 편집용 뷰는 이미 프로젝트에 추가되었으므로 다시 컨트롤러와 **DocumentDBRepository** 클래스에 일부 코드를 추가하기만 하면 됩니다.

1. **ItemController** 클래스에 다음을 추가합니다.

    	[HttpPost]
   		[ValidateAntiForgeryToken]
    	public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    	{
     	   if (ModelState.IsValid)
    	    {
    	        await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
    	        return RedirectToAction("Index");
    	    }

  	      return View(item);
 	   	}
		
		public ActionResult Edit(string id)
		{
		    if (string.IsNullOrEmpty(id))
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
					 
		    Item item = (Item)DocumentDBRepository<Item>.GetItem(d => d.Id == id);
		 	
		    if (item == null)
		    {
		        return HttpNotFound();
		    }
		 	
		    return View(item);
		}
		
	
	첫 번째 메서드는 사용자가 **인덱스** 뷰에서 **편집** 링크를 클릭할 때 발생하는 Http Get을 처리합니다. 이 메서드는 DocumentDB에서 [**문서**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx)(영문)를 가져와 **편집** 뷰에 전달합니다.

	그런 다음 **편집** 뷰는 **IndexController**에 Http Post를 수행합니다. 
	
	추가한 두 번째 메서드는 데이터베이스에 저장되도록 업데이트된 개체를 DocumentDB에 전달하는 작업을 처리합니다.

2. 다음 using 지시문을 ItemController.cs 파일에 추가합니다.

		using todo.Models;
		using System.Threading.Tasks;
		using System.Net;

3. **DocumentDBRepository** 클래스에 다음을 추가합니다.

    	public static T GetItem(Expression<Func<T, bool>> predicate)
    	{
        	return Client.CreateDocumentQuery<T>(Collection.DocumentsLink)
                    	.Where(predicate)
                    	.AsEnumerable()
                    	.FirstOrDefault();
    	}	
		
		public static Document GetDocument(string id) 
		{ 
			return Client.CreateDocumentQuery(Collection.DocumentsLink) 
				.Where(d => d.Id == id) 
				.AsEnumerable() 
				.FirstOrDefault(); 
		} 

    	public static async Task<Document> UpdateItemAsync(string id, T item)
    	{
        	Document doc = GetDocument(id);	
        	return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    	}

	이러한 메서드 중 첫 번째 메서드가 DocumentDB에서 **항목**을 가져오며, 이 항목이 다시 **ItemController** 및 **편집** 뷰로 전달됩니다.
	
	방금 추가한 두 메서드 중 두 번째 메서드는 DocumentDB의 **문서**를 **ItemController**에서 전달된 **문서** 버전으로 바꿉니다.

4. 다음 using 지시문을 DocumentDBRepository.cs 파일에 추가합니다.

		using System.Threading.Tasks;

	응용 프로그램을 실행하는 데 필요한 모든 작업(완료되지 않은 **항목** 나열, 새 **항목** 추가 및 **항목** 편집)이 완료되었습니다.

## <a name="_Toc395637773"></a>6단계: 로컬에서 응용 프로그램 실행

로컬 컴퓨터에서 응용 프로그램을 테스트하려면 다음을 수행합니다.

1. 디버그 모드에서 응용 프로그램을 빌드하려면 Visual Studio에서 F5 키를 누릅니다. 응용 프로그램이 빌드되고 앞에서 본 것처럼 빈 그리드 페이지가 포함된 상태로 브라우저가 시작되어야 합니다.

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image24.png)

	이때 오류가 발생하면 코드를 [GitHub][](영문)의 todo 자습서와 비교할 수 있습니다.

2. **새로 만들기** 링크를 클릭하고 **이름** 및 **설명** 필드에 값을 추가합니다. **완료** 확인란을 선택 취소된 상태로 둡니다. 그렇지 않으면 새 **항목**이 완료 상태로 추가되며 초기 목록에 나타나지 않습니다.

	![Screen shot of the Create view](./media/documentdb-dotnet-application/image25.png)

3. **만들기**를 클릭하면 **인덱스** 뷰로 다시 리디렉션되고 **항목**이 목록에 나타납니다.

	![Screen shot of the Index view](./media/documentdb-dotnet-application/image26.png)

	todo 모음에 **항목**을 더 추가해도 됩니다.

3. 목록에서 **항목** 옆의 **편집**을 클릭합니다. **편집** 뷰로 이동되며, 여기서 **Completed** 플래그를 비롯한 개체 속성을 업데이트할 수 있습니다. **Complete** 플래그를 표시하고 **저장**을 클릭하면 **항목**이 완료되지 않은 작업 목록에서 제거됩니다.

	![Screen shot of the Index view with the Completed box checked](./media/documentdb-dotnet-application/image27.png)

4. 앱을 테스트하고 나면 Ctrl+F5를 눌러 앱 디버깅을 중지합니다. 배포할 준비가 되었습니다!

##<a name="_Toc395637774"></a>7단계: Azure 웹 사이트에 응용 프로그램 배포

이제 전체 응용 프로그램이 DocumentDB와 올바르게 작동하므로 Azure 웹 사이트에 이 웹앱을 배포하겠습니다. 빈 ASP.NET MVC 프로젝트를 만들 때 **클라우드에 호스트**를 선택한 경우 Visual Studio에서 대부분의 작업을 수행하므로 쉽게 배포할 수 있습니다. 

이 응용 프로그램을 게시하기 위해 할 일은 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭하는 것뿐입니다.

![Screen shot of the Publish option in Solution Explorer](./media/documentdb-dotnet-application/image28.png)

사용자 자격 증명에 따라 모든 항목이 이미 구성되어 있어야 합니다. 실제로 Azure의 표시된 **대상 URL**에 웹 사이트가 이미 만들어져 있으므로 **게시**를 클릭하기만 하면 됩니다.

![Screen shot of the Publish Web dialog box](./media/documentdb-dotnet-application/image29.png)

몇 초 후에 Visual Studio에서 웹 응용 프로그램 게시를 완료하고 브라우저를 시작하며, Azure에서 실행되는 작업 내용을 확인할 수 있습니다.

##<a name="_Toc395637775"></a>다음 단계

축하합니다. 지금까지 Azure DocumentDB를 사용하여 첫 ASP.NET MVC 응용 프로그램을 빌드하고 Azure 웹 사이트에 게시했습니다. 이 자습서에 포함되지 않은 세부 정보 및 삭제 기능을 비롯한 전체 응용 프로그램 소스 코드는 [GitHub][](영문)에서 다운로드하거나 복제할 수 있습니다. 따라서 이 내용을 앱에 추가하려는 경우 코드를 끌어와서 이 앱에 추가하면 됩니다.

응용 프로그램에 기능을 더 추가하려면 [DocumentDB .NET 라이브러리](http://msdn.microsoft.com/library/azure/dn783362.aspx)(영문)에서 사용 가능한 API를 검토하고 [GitHub][](영문)의 DocumentDB .NET 라이브러리에 자유롭게 기여하세요. 

##<a id="GetProject"></a>GitHub에서 솔루션 다운로드

시간을 절약하면서 전체 todo 솔루션을 빌드하고 코드를 스스로 추가하지 않으려는 경우 간단한 방법이 있습니다. GitHub에서 전체 솔루션을 제공하므로 다음 지침에 따라 몇 분 만에 솔루션을 빌드하여 배포할 수 있습니다.

1. Visual Studio 및 Azure SDK for .NET 버전 2.3 이상을 포함하는 [필수 소프트웨어](#_Toc395637760)가 설치되어 있는지 확인합니다.

2. Git for Windows([http://www.git-scm.com/](http://www.git-scm.com/)(영문))를 사용하여 azure-documentdb-net 리포지토리를 복제하거나 [GitHub](https://github.com/Azure/azure-documentdb-net/)(영문)에서 zip 파일을 다운로드합니다.

2. Visual Studio의 azure-documentdb-net/tutorials/todo 디렉터리에서 todo.sln 파일을 엽니다.

3. Visual Studio 2013에서 DocumentDB .NET SDK에 대한 참조를 복원하려면 **솔루션 탐색기**에서 todo 솔루션을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 복원 사용**을 클릭합니다. 그러면 참조가 복원됩니다. 

4. [Azure Preview 포털](https://portal.azure.com/)에서 DocumentDB 계정의 **키** 블레이드에서 **URI** 및 **기본 키** 또는 **보조 키** 값을 검색합니다. 

	
	계정이 없는 경우 [데이터베이스 계정 만들기](documentdb-create-account.md)를 참조하여 계정을 설정합니다.

	![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the Keys button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade](../includes/media/documentdb-keys/keys.png)

5. Web.config 파일에서 **endpoint** 및 **authKey** 키의 기본값을 업데이트합니다.

    	<add key="endpoint" value="~enter URI for your DocumentDB Account, from Azure Management Portal~" /> 
		<add key="authKey" value="~enter either Primary or Secondary key for your DocumentDB Account, from Azure Management Portal~" /> 

	- 키 블레이드에서 **URI** 값을 복사하여 **endpoint** 속성 값에 붙여넣습니다. 
	- **키** 블레이드에서 **기본 키** 또는 **보조 키** 값을 복사하여 **authKey** 속성 값에 붙여넣습니다.
	


7. 이제 [로컬에서 응용 프로그램을 실행](#_Toc395637773)한 다음 [Azure 웹 사이트에 배포](#_Toc395637774)할 수 있습니다.


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft 웹 플랫폼 설치 관리자]: http://www.microsoft.com/web/downloads/platform.aspx
[GitHub]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[교차 사이트 요청 위조 방지]: http://go.microsoft.com/fwlink/?LinkID=517254
[ASP.NET MVC의 기본 CRUD 작업]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=49-->