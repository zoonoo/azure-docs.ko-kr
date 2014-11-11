<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Build a web application with ASP.NET MVC using DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="hawong"></tags>

# <a name="_Toc395809351">DocumentDB를 사용하여 ASP.NET MVC로 웹 응용 프로그램 작성</a>

# <a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">개요</a>

## <a name="_Toc395637759">시나리오</a>

고객이 Azure DocumentDB를 효율적으로 활용하여
JSON 문서를 저장 및 쿼리할 수 있는 방법을 강조하기 위해 이 문서에서는
Azure DocumentDB를 사용하여 ToDo List 웹 응용 프로그램을 작성하는
종단 간 연습을 제공합니다.

이 연습에서는 Azure가 제공하는 DocumentDB 서비스를 사용하여
Azure에 호스트된 ASP.NET MVC 웹 응용 프로그램에서 데이터를 저장하고 액세스하는
방법을 보여 주며 이전에 ASP.NET MVC 및 Azure 웹 사이트를 사용한 경험이
있다고 가정합니다.

다음 내용을 배웁니다.

1. DocumentDB 계정 만들기 및 프로비전

2. ASP.NET MVC 응용 프로그램 만들기

3. 웹 응용 프로그램에서 Azure DocumentDB에 연결 및 사용

4. Azure 웹 사이트에 웹 응용 프로그램 배포

이 연습을 따라 작업을 만들고, 검색하고,
완료할 수 있는 간단한 웹 기반 작업 관리 응용 프로그램을
작성합니다. 작업은 Azure DocumentDB에 JSON 문서로
저장됩니다.

![대체 텍스트][]

# <a name="_Toc395637760">필수 조건</a>

이 문서의 지침을 따르기 전에 다음이 설치되어 있는지
확인해야 합니다.

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013(또는 [Visual Studio Express][]
(무료 버전))

Azure SDK for .NET 버전 2.3 이상
([Microsoft 웹 플랫폼 설치 관리자][]를 통해 사용 가능)

이 문서의 모든 스크린샷은 Visual Studio 2013 업데이트 3
및 Azure SDK for .NET 버전 2.4를 사용하여
생성되었습니다. 시스템이 다른 버전으로 구성된 경우
화면과 옵션이 완전히 일치하지 않을 수 있지만 위의 필수 구성 요소를
충족하면 이 솔루션이 작동합니다.

# <a name="_Toc395637761">DocumentDB 데이터베이스 계정 만들기</a>

Azure에서 DocumentDB 데이터베이스 계정을 프로비전하려면 Azure
관리 포털을 열고 홈페이지에서 Azure 갤러리 타일을 클릭하거나
화면 왼쪽 아래에 있는 "+"를 클릭합니다.

![대체 텍스트][1]

Azure 갤러리가 열리며, 여기서 사용 가능한 많은 Azure 서비스를
선택할 수 있습니다. 갤러리의 범주 목록에서 "데이터, 저장소 및
백업"을 선택합니다.

![대체 텍스트][2]

여기서 Azure DocumentDB에 대한 옵션을 선택합니다.

![대체 텍스트][3]

그런 다음 화면 맨 아래에서 "만들기"를 선택합니다.

![대체 텍스트][4]

"새 DocumentDB" 블레이드가 열립니다. 여기서 새 계정의
이름, 지역, 배율, 리소스 그룹 및 기타 설정을
지정할 수 있습니다.

![대체 텍스트][5]

계정의 값을 제공한 후 "만들기"를 클릭하면 프로비저닝 프로세스에서 데이터베이스 계정을 만들기 시작합니다.
프로비저닝 프로세스가 완료되면 포털의 알림 영역에
알림이 표시되며 시작 화면의 타일(만들도록
선택한 경우)이 완료된 작업을 표시하도록
변경됩니다.

![대체 텍스트][6]

프로비저닝이 완료된 후 시작 화면에서 DocumentDB 타일을
클릭하면 새로 만든 이 DocumentDB 계정의 기본 블레이드가
표시됩니다.

![대체 텍스트][7]
![대체 텍스트][8]

"키" 단추를 사용하여 끝점 URL과 기본 키에 액세스한 후
다음에 만들 웹 응용 프로그램에서 해당 값을 사용하므로
클립보드로 복사하여 유용하게 유지합니다.

이제 새 ASP.NET MVC 응용 프로그램을 처음부터 만드는
방법을 살펴보겠습니다. 참조를 위해 [여기]서 전체 솔루션을
다운로드할 수 있습니다.

# <a name="_Toc395637762">새 ASP.NET MVC 응용 프로그램 만들기</a>

Visual Studio에서 파일 – 새 프로젝트를 클릭하고 새 ASP.NET MVC
웹 응용 프로그램을 만드는 옵션을 선택합니다.

![대체 텍스트][9]

프로젝트를 만들려는 위치를 선택하고 확인을 클릭합니다.

![대체 텍스트][10]

Azure에서 응용 프로그램을 호스트하려는 경우 오른쪽 아래에 있는 "클라우드에 호스트" 확인란을 선택합니다. 클라우드에 호스트하도록 선택했으며 Azure 웹 사이트에 호스트된 응용 프로그램을 실행했습니다.
이 옵션을 선택하면 Azure 웹 사이트가 미리 프로비전되며 최종 응용 프로그램을
훨씬 쉽게 배포할 수 있습니다.

확인을 선택하면 Visual Studio에서 빈
ASP.NET MVC 템플릿을 스캐폴딩합니다. Azure 계정에 로그인한 다음
새 웹 사이트에 대한 일부 값을 제공하도록 요청하는 추가 화면이
표시될 수도 있습니다. 이러한 모든 Azure 값을 제공하고 계속합니다.

Visual Studio에서 상용구 MVC 응용 프로그램을 만들면
로컬에서 실행할 수 있는 빈 ASP.NET 응용 프로그램을 갖게 됩니다.

모두 ASP.NET "Hello World" 응용 프로그램을 본 적이 있다고 확신하므로 로컬 실행은 건너뛰겠습니다. 바로 이 프로젝트에 DocumentDB를 추가하고 응용 프로그램을 작성하겠습니다.

# <a name="_Toc395637763">ASP.NET MVC 응용 프로그램 설정</a>

### 

### <a name="_Toc395637764">모델 추가</a>

먼저 MVC의 **M**인 모델을 만들겠습니다. 솔루션
탐색기에서 *Models* 폴더를 마우스 오른쪽 단추로 클릭한 다음 **추가**,
**클래스**를 차례로 클릭합니다.

![대체 텍스트][11]

새 클래스의 이름을 **Item**으로 지정하고 이 새 클래스에
다음 코드를 추가합니다.

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public int ID { get; set; }
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

DocumentDB의 모든 데이터가 네트워크를 통해 전달되고 JSON으로 저장됩니다.
JSON.NET에서 개체를 직렬화/역직렬화하는 방식을 제어하기 위해
속성 이름이 소문자 속성 이름의 JSON 규칙을 따르도록 하는 JsonProperty 특성을
위와 같이 사용할 수 있습니다.
이 작업은 **필수가 아닙니다**.

### <a name="_Toc395637765">컨트롤러 추가</a>

M 작업을 마쳤으므로 이제 MVC의 **C**인 컨트롤러
클래스를 만들겠습니다.
 **솔루션 탐색기**에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭한 다음
**추가**,**컨트롤러**를 차례로 클릭합니다.

![대체 텍스트][12]

![대체 텍스트][13]

**스캐폴드 추가** 대화 상자에서 **MVC 5 컨트롤러 - 비어 있음**을 클릭합니다.
**추가**를 클릭합니다.

![대체 텍스트][14]

새 컨트롤러의 이름을 **ItemController**로 지정합니다.

이제 Visual Studio에서 ItemController를 추가하며 솔루션
탐색기가 아래와 같이 표시됩니다.

![대체 텍스트][15]

### <a name="_Toc395637766">뷰 추가</a>

마지막으로 MVC의 **V**인 뷰를 만들겠습니다.

#### 항목 인덱스 뷰 추가

솔루션 탐색기에서 ***Views*** 폴더를 확장하고
앞에서 *ItemController*를 추가할 때 Visual Studio가 만든
(빈) Item 폴더를 찾습니다. ***Item***을 마우스 오른쪽 단추로 클릭하고
새 뷰를 추가하도록 선택합니다.

![대체 텍스트][16]

"뷰 추가" 대화 상자에서 뷰의 이름을 "***Index***"로 지정하고
***List*** 템플릿을 사용한 다음 앞에서 만든 ***Item (Todo.Models)***을
클래스로 선택하고 솔루션의 ***\_Layout.cshtml***을
레이아웃 페이지로 사용합니다.

![대체 텍스트][17]

이러한 값이 모두 설정된 후 추가를 클릭하면 Visual Studio에서
뷰를 만듭니다. Visual Studio는 템플릿 뷰를 만듭니다. 완료되면
Visual Studio가 만들어진 cshtml 파일을 엽니다. 나중에 돌아올 것이므로 Visual Studio에서
이 문서를 닫아도 됩니다.

#### 새 항목 뷰 추가

위와 유사한 방식으로, 아래에 표시된 예제에 따라
새 항목을 만들기 위한 새 뷰를 만듭니다.

![대체 텍스트][18]

#### 항목 편집 뷰 추가

# <a name="_Toc395888515"></a>

마지막으로, 이전과 동일한 방식으로 항목을 편집하기 위한 최종 뷰를
추가합니다.

![대체 텍스트][19]

이 작업이 완료되면 나중에 이러한 뷰로 돌아올 것이므로 Visual Studio에서
cshtml 문서를 닫습니다.

</h1>
# <a name="_Toc395637767">프로젝트에 DocumentDB 추가</a>

이 솔루션에 필요한 ASP.NET MVC 배관이 대부분
처리되었습니다. 이제 이 자습서의 실제 목적으로, 웹 응용 프로그램에 Azure DocumentDB를 추가하겠습니다.

</h1>
## <a name="_Toc395637768">DocumentDB NuGet 패키지 설치</a>

DocumentDB .NET SDK는 NuGet 패키지로 패키징되어 배포됩니다.
Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리"를
선택하여 액세스할 수 있는 NuGet 패키지 관리자를 사용합니다.

![대체 텍스트][20]

온라인에서 "Azure DocumentDB"를 검색하고 패키지를 설치합니다. 그러면
DocumentDB 패키지 및 모든 종속성(예: Newtonsoft.Json)이
다운로드되어 설치됩니다.

설치된 Visual Studio 솔루션은 새 참조 두 개가 추가되어 다음과
유사하게 표시됩니다.

![대체 텍스트][21]

</h1>
## <a name="_Toc395637769">DocumentDB 연결</a>

### <a name="_Toc395637770">완료되지 않은 항목 나열</a>

**ItemController**를 열고 Visual Studio가 추가한 클래스 내의 모든 코드를
제거합니다(클래스는 유지). DocumentDB를 사용하여 하나씩 다시 작성하겠습니다.

이제 비어 있는 ItemController 클래스 내에 다음 코드 조각을
추가합니다.

    public async Task<ActionResult> Index() 
    {
        var items = await repo.GetIncompleteItems();
        return View(items);
    }

DocumentDB에 대한 "의사 리포지토리" 클래스가 사용됩니다. 이 클래스는
실제로 모든 DocumentDB 특정 코드가 포함된 도우미 클래스일 뿐입니다. 이 연습의
목적을 위해 실제 응용 프로그램을 작성하는 경우처럼 리포지토리
패턴을 사용한 종속성 주입을 통해 전체 데이터 액세스 계층을
구현하지 않습니다. 이 연습의
목적을 위해 편의상 모든 데이터 액세스 논리를 하나의 프로젝트에
배치하겠습니다.

프로젝트에 새 클래스를 추가하고 이름을 **DocumentDBRepository**로 지정합니다.
클래스 파일의 코드를 다음과 같이 바꿉니다.

    public static class DocumentDBRepository
    {
        public static async Task<List<Item>> GetIncompleteItems()
        {
            return await Task<List<Item>>.Run( () => 
                Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => !d.Completed)
                    .AsEnumerable()
                    .ToList<Item>());
        }
    } 

#### 

적절한 using 문을 추가하여 참조를
확인합니다. NuGet 패키지가 성공적으로 설치된 경우
Visual Studio에서 모든 참조를 쉽게 확인할 수 있습니다. *CreateDocumentQuery*에
대한 참조는 다음 using 지시문을 수동으로 추가해야 하는 경우에
확인됩니다.

    using Microsoft.Azure.Documents.Linq; 

모든 항목이 예상대로 작동하기 위해 의사 리포지토리
클래스에 수동 배관을 좀 더 추가해야 합니다. 리포지토리 클래스에 아래
코드를 추가합니다.

</h4>
        private static Database database;
        private static Database Database
        {
            get
            {
                if (database == null)
                {
                    ReadOrCreateDatabase().Wait();
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
                    ReadOrCreateCollection(Database.SelfLink).Wait();
                }
                
                return collection;
            }
        }

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

        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
                    String endpoint = ConfigurationManager.GetSetting("endpoint")
                    string authKey = ConfigurationManager.GetSetting("authKey");
                    Uri endpointUri = new Uri(endpoint);
                    client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }

        private static async Task ReadOrCreateCollection(string databaseLink)
        {
            var collections = Client.CreateDocumentCollectionQuery(databaseLink)
                              .Where(col => col.Id == CollectionId).ToArray();
            
            if (collections.Any())
            {
                collection = collections.First();
            }
            else
            {
                collection = await Client.CreateDocumentCollectionAsync(databaseLink, 
                    new DocumentCollection { Id = CollectionId });
            }
        }
     
        private static async Task ReadOrCreateDatabase()
        {
            var databases = Client.CreateDatabaseQuery()
                            .Where(db => db.Id == DatabaseId).ToArray();
            
            if (databases.Any())
            {
                database = databases.First();
            }
            else
            {
                Database database = new Database { Id = DatabaseId };   
                database = await Client.CreateDatabaseAsync(database);
            }
        }

</h1>
#### 

GetIncompleteItems() 등의 메서드에 처음 액세스하면 메서드가 클라이언트를 호출하며,
클라이언트가 Azure DocumentDB에 대한 연결을 설정합니다. GetIncompleteItems()를
처음 호출할 때 이 응용 프로그램에 대한 Database 및
DocumentCollection도 읽거나 만들려고 합니다. 이 작업은 한 번만
수행하면 되고, 그 후에는 두 리소스의 SelfLink 속성을 단순히 캐시하여
응용 프로그램에서 다시 사용할 수 있습니다.

나중에 동일한 응용 프로그램 내에서 DocumentDB에 연결하려는 경우
매번 새 인스턴스를 만드는 대신 클라이언트, 데이터베이스 및 컬렉션의 동일한
인스턴스가 다시 사용됩니다.

**web.config**를 열고 <appsettings\> 섹션 아래에
다음 줄을 추가합니다.

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>

</h4>
이때 오류 없이 솔루션을 작성할 수 있어야 합니다.

지금 응용 프로그램을 실행하는 경우 Home 컨트롤러 및 해당 컨트롤러의
인덱스 뷰로 이동합니다. 이것은 시작할 때 선택한 MVC 템플릿
프로젝트에 대한 기본 동작이지만 여기서는 사용하지 않습니다. 이 동작을 변경하기 위해
이 MVC 응용 프로그램의 라우팅을 변경하겠습니다.

App\_Start 폴더 아래의 ***RouteConfig.cs***를 열고
"defaults:"로 시작하는 줄을 찾은 후 다음과 같이 변경합니다.

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

이제 라우팅 동작을 제어하기 위해 URL의 값을 지정하지 않은 경우 "Home" 대신 "Item"을 컨트롤러로 사용하고 사용자 Index를 뷰로 사용하도록 ASP.NET MVC에 지정됩니다.
응용 프로그램을 실행하면 **ItemController**가 호출되고 **GetIncompleteItems** 메서드의 결과가 Views\\Item\\Index 뷰에 반환됩니다.
지금 이 프로젝트를 실행하면 다음과 같이 표시되어야 합니다.

![대체 텍스트][22]

### <a name="_Toc395637771">항목 추가</a>

빈 그리드 외에 확인할 항목이 있도록 이제 데이터베이스에
일부 항목을 추가하겠습니다.

이미 만들기용 뷰와 사용자를 만들기 뷰로 이동하는
인덱스 뷰의 단추가 있습니다. DocumentDB에 레코드를 저장하기 위해
컨트롤러 및 리포지토리에 일부 코드를 추가하겠습니다.

***ItemController.cs***를 열고 Create 작업에 대해 수행할 작업을
ASP.NET MVC에 알리는 다음 코드 조각을 추가합니다.
이 경우 앞에서 만든 관련 Create.cshtml 뷰를 렌더링합니다.

    public ActionResult Create()
    { 
          return View(); 
    }

이제 만들기 뷰의 제출을 수락하는 코드를
이 컨트롤러에 더 추가해야 합니다.

이 컨트롤러에 대한 폼 POST의 처리 방법을 ASP.NET MVC에 알리는
다음 코드 블록을 추가합니다.

    [HttpPost]  
    public async Task<ActionResult> Create(Item item)  
    {
        if (ModelState.IsValid)  
        {  
            await repo.CreateDocument(item);  
            return RedirectToAction("Index");  
        }   
        return View(item);   
    }

이제 Items Controller가 폼의 항목을
의사 리포지토리 클래스의 CreateDocument 메서드로 전달하므로
DocumentDBRepository 클래스에 다음 메서드를 추가합니다.

    public static async Task<Document> CreateDocument(dynamic item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item); 
    }

이 메서드는 단순히 전달된 개체를 받아서
DocumentDB에 저장합니다.

데이터베이스에 새 항목을 추가하는 데 필요한 코드가 완성되었습니다.

### <a name="_Toc395637772">항목 편집</a>

마지막으로 수행할 작업은 데이터베이스에서
항목을 편집하고 작업이 완료되면 완료로 표시하는
기능입니다. 추가와 마찬가지로 편집용 뷰는 이미 추가되었으므로
다시 컨트롤러와 DocumentDBRepository 클래스에 코드를 더
추가하기만 하면 됩니다.

ItemController 클래스에 다음을 추가합니다.

    public async Task<ActionResult> Edit(string id)    
    {  
        if (id == null)  
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);   
        }

        Item item = await repo.GetDocument(id);
        if (item == null)
        {       
            return HttpNotFound();
        }
        
        return View(item);
    }

    [HttpPost] 
    public async Task<ActionResult> Edit(Item item)  
    {
        if (ModelState.IsValid)
        {
             await repo.UpdateDocument(item);
             return RedirectToAction("Index");
        }
        
        return View(item); 
    }

첫 번째 메서드는 사용자가 인덱스 뷰에서 "편집" 링크를 클릭할 때
발생하는 Http Get을 처리합니다. 이 메서드는 DocumentDB에서 문서를
가져와 편집 뷰에 전달합니다.

### 

그러면 편집 뷰가 IndexController에 대해 다시 Http Post를 수행하며,
추가한 두 번째 메서드가 업데이트된 개체를 데이터베이스에 저장하기 위해
DocumentDB로 전달합니다.

DocumentDBRepository 클래스에 다음을 추가합니다.

    public static async Task<Item> GetDocument(string id)
    {
        return await Task<Item>.Run(() =>
            Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => d.ID == id)
                .AsEnumerable()
                .FirstOrDefault());
    }

    public static async Task<Document> UpdateDocument(Item item)
    {
        var doc = Client.CreateDocumentQuery<Document>(Collection.DocumentsLink)
                    .Where(d => d.Id == item.ID)
                    .AsEnumerable().FirstOrDefault();
        
        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

두 메서드 중 첫 번째 메서드가 DocumentDB에서 항목을 가져오며,
이 항목이 다시 ItemController 및 편집 뷰로 전달됩니다.

방금 추가한 두 메서드 중 두 번째 메서드는 DocumentDB의
문서를 ItemController에서 전달된 문서 버전으로
바꿉니다.

응용 프로그램을 실행하는 데 필요한 모든 작업(완료되지 않은 항목 나열,
새 항목 추가 및 항목 편집)이 완료되었습니다.

</h3>
# <a name="_Toc395637773">로컬에서 응용 프로그램 실행</a>

### 

로컬 컴퓨터에서 응용 프로그램을 테스트하려면 Visual Studio에서 F5 키를 누릅니다.
응용 프로그램이 작성되고 이전과 같이 빈 그리드 페이지가 표시된
브라우저가 시작됩니다.

![대체 텍스트][23]

1\. 항목, 항목 이름 및 범주에 대해 제공된 필드를 사용하여
정보를 입력하고 **"새로 만들기"** 링크를 클릭한 다음 일부 값을
제공합니다. "완료" 확인란을 선택 취소된 상태로 둡니다. 그렇지 않으면 새 항목이
완료 상태로 추가되며 초기 목록에 나타나지 않습니다.

![대체 텍스트][24]

만들기를 누르면 다시 인덱스 페이지로 리디렉션되며
해당 항목이 목록에 표시됩니다.

![대체 텍스트][25]

Todo 목록에 항목을 더 추가해도 됩니다.

2\. 목록에서 항목 옆의 "편집"을 클릭합니다. 편집 뷰로
이동되며, 여기서 "Completed" 플래그를 비롯한 개체 속성을
업데이트할 수 있습니다. 이 경우 항목이 완료로
표시되며 완료되지 않은 작업 목록에서 제거됩니다.

![대체 텍스트][26]

3\. 작업을 완료하려면 단순히 확인란을 선택하고
**저장**을 클릭합니다. 다시 목록 페이지로 리디렉션되며 해당 항목이
목록에 더 이상 표시되지 않습니다.

</h3>
# <a name="_Toc395637774">Azure 웹 사이트에 응용 프로그램 배포</a>

### 

이제 완료된 응용 프로그램이 DocumentDB에서 올바르게 작동하므로
Azure 웹 사이트에 배포하겠습니다.

빈 ASP.NET MVC 프로젝트를 만들 때 "클라우드에 호스트"를
선택한 경우 Visual Studio에서 대부분의 작업을 수행하므로
쉽게 배포할 수 있습니다. 이 응용 프로그램을 게시하려면
솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고(로컬에서 실행하고
있지 않은지 확인) 게시를 선택하기만 하면 됩니다.

![대체 텍스트][27]

사용자 자격 증명에 따라 모든 항목이 이미 구성되어 있어야 합니다.
실제로 Azure의 표시된 "대상 URL"에 웹 사이트가 이미 만들어져 있으므로
**게시**를 클릭하기만 하면 됩니다.

![대체 텍스트][28]

몇 초 후에 Visual Studio에서 웹 응용 프로그램 게시를 완료하고
브라우저를 시작하며, Azure에서 실행되는 작업 내용을
확인할 수 있습니다.

</h3>
# <a name="_Toc395637775">결론</a>

### 

축하합니다. 지금까지 Azure DocumentDB를 사용하여 첫 ASP.NET MVC 응용 프로그램을 작성하고 Azure 웹 사이트에 게시했습니다. 여기서 전체 참조 응용 프로그램의 소스 코드를 다운로드할 수 있습니다.

[여기](<http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409>)

  [대체 텍스트]: ./media/documentdb-dotnet-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [Microsoft 웹 플랫폼 설치 관리자]: http://www.microsoft.com/web/downloads/platform.aspx
  [1]: ./media/documentdb-dotnet-application/image2.png
  [2]: ./media/documentdb-dotnet-application/image3.png
  [3]: ./media/documentdb-dotnet-application/image4.png
  [4]: ./media/documentdb-dotnet-application/image5.png
  [5]: ./media/documentdb-dotnet-application/image6.png
  [6]: ./media/documentdb-dotnet-application/image7.png
  [7]: ./media/documentdb-dotnet-application/image8.png
  [8]: ./media/documentdb-dotnet-application/image9.png
  [9]: ./media/documentdb-dotnet-application/image10.png
  [10]: ./media/documentdb-dotnet-application/image11.png
  [11]: ./media/documentdb-dotnet-application/image12.png
  [12]: ./media/documentdb-dotnet-application/image13.png
  [13]: ./media/documentdb-dotnet-application/image14.png
  [14]: ./media/documentdb-dotnet-application/image15.png
  [15]: ./media/documentdb-dotnet-application/image16.png
  [16]: ./media/documentdb-dotnet-application/image17.png
  [17]: ./media/documentdb-dotnet-application/image18.png
  [18]: ./media/documentdb-dotnet-application/image19.png
  [19]: ./media/documentdb-dotnet-application/image20.png
  [20]: ./media/documentdb-dotnet-application/image21.png
  [21]: ./media/documentdb-dotnet-application/image22.png
  [22]: ./media/documentdb-dotnet-application/image23.png
  [23]: ./media/documentdb-dotnet-application/image24.png
  [24]: ./media/documentdb-dotnet-application/image25.png
  [25]: ./media/documentdb-dotnet-application/image26.png
  [26]: ./media/documentdb-dotnet-application/image27.png
  [27]: ./media/documentdb-dotnet-application/image28.png
  [28]: ./media/documentdb-dotnet-application/image29.png
