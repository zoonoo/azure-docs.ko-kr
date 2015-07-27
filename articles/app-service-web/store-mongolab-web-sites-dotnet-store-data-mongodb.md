<properties 
	pageTitle="Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 NET 웹 앱 만들기" 
	description="MongoLab에서 호스팅되는 MongoDB에 데이터를 저장하는 Azure 앱 서비스에 ASP.NET 웹 앱을 만드는 방법에 대해 알아봅니다." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="chrisckchang" 
	manager="partners@mongolab.com" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="chris@mongolab.com"/>



# Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 NET 웹 앱 만들기

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

<p><em>작성자: Eric Sedor, MongoLab</em></p>

모험심이 많은 사용자 여러분, 반갑습니다! MongoDB-as-a-Service에 오신 것을 환영합니다. 이 자습서에서는 다음을 수행합니다.

1. [데이터베이스 프로비전][provision] - Azure 마켓플레이스 [MongoLab](http://mongolab.com) 추가 기능은 Azure 클라우드에 호스트되고 MongoLab 클라우드 데이터베이스 플랫폼에서 관리되는 MongoDB 데이터베이스를 제공합니다.
1. [앱 만들기][create] - 참고를 위한 간단한 C# ASP.NET MVC 앱입니다.
1. [앱 배포][deploy] - 몇 가지 구성 후크를 연결하여 [Azure 앱 서비스 웹 앱](http://go.microsoft.com/fwlink/?LinkId=529714)에 원활하게 코드를 푸시합니다.
1. [데이터베이스 관리][manage] - 마지막으로 쉽게 데이터를 검색하고, 시각화하고, 수정할 수 있는 MongoLab의 웹 기반 데이터베이스 관리 포털을 보여 줍니다.

언제든 이 자습서를 보면서 질문할 사항이 있으면 [support@mongolab.com](mailto:support@mongolab.com)으로 전자 메일을 보내주십시오.

## 빠른 시작
사용할 Azure 앱 서비스에 웹 앱이 이미 있거나 Azure 마켓플레이스에 대해 잘 알고 있는 경우 이 섹션을 사용하여 빠르게 시작할 수 있습니다. 그렇지 않은 경우 아래의 [데이터베이스 프로비전][provision]을 계속 진행합니다.
 
1. **새로 만들기** > **마켓플레이스**를 클릭하여 Azure 마켓플레이스를 엽니다.  
	<!-- ![Store][button-store] -->

1. MongoLab 추가 기능을 구입합니다.![MongoLab][entry-mongolab]

1. 추가 기능 목록에서 MongoLab 추가 기능을 클릭하고 **연결 정보**를 클릭합니다. ![ConnectionInfoButton][button-connectioninfo]

1. MONGOLAB_URI를 클립보드로 복사합니다. ![ConnectionInfoScreen][screen-connectioninfo] **이 URI에는 데이터베이스 사용자 이름과 암호가 포함되어 있습니다. 중요한 정보로 처리하고 공유하지 마십시오.**

1. Azure 웹 응용 프로그램 구성 메뉴의 연결 문자열 목록에 값을 추가합니다. ![WebSiteConnectionStrings][focus-website-connectinfo]

1. **Name**에 MONGOLAB_URI를 입력합니다.

1. **값**에 이전 섹션에서 얻은 연결 문자열을 붙여 넣습니다.

1. 유형 드롭다운에서 기본값 **SQLAzure** 대신 **사용자 지정**을 선택합니다.

1. Visual Studio에서 **도구 > 라이브러리 패키지 관리자 > 패키지 관리자 콘솔**을 선택하여 Mongo C# 드라이버를 설치합니다. PM 콘솔에서 **Install-Package mongocsharpdriver**를 입력하고 **Enter** 키를 누릅니다.

1. 코드에서 후크를 설정하여 환경 변수의 MongoLab 연결 URI를 가져옵니다.

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

> **참고:** Azure는**CUSTOMCONNSTR_** 접두사를 처음에 선언된 연결 문자열에 추가합니다. 따라서 코드가 **MONGOLAB_URI** 대신 **CUSTOMCONNSTR_MONGOLAB_URI.**를 참조합니다.

이제, 전체 자습서에서...

<a name="provision"></a>
## 데이터베이스 프로비전

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## 앱 만들기

이 섹션에서는 C# ASP.NET Visual Studio 프로젝트를 만드는 방법을 설명하고 C# MongoDB 드라이버를 사용하여 간단한 메모 앱을 만드는 과정을 단계별로 안내합니다. 웹 앱을 방문하여, 메모를 작성하고, 남겨진 모든 메모를 확인할 수 있습니다.

이 개발 작업은 Visual Studio Express 2013 for Web에서 수행합니다.

### 프로젝트 만들기
샘플 앱에서는 Visual Studio 템플릿을 사용하여 시작합니다. .NET Framework 4.5를 사용해야 합니다.

1. **파일 > 새 프로젝트**를 선택합니다. 새 프로젝트 대화 상자가 표시됩니다. ![NewProject][dialog-mongolab-csharp-newproject]

1. **설치됨 > 템플릿 > Visual C# > 웹**을 선택합니다.

1. .NET 버전 드롭다운 메뉴에서 **.NET Framework 4.5**를 선택합니다.

1. **MVC 응용 프로그램**을 선택합니다.

1. _mongoNotes_를 **프로젝트 이름**으로 입력합니다. 다른 이름을 선택할 경우 자습서 전체에 제공된 코드를 수정해야 합니다.

1. **도구 > 라이브러리 패키지 관리자 > 패키지 관리자 콘솔**을 선택합니다. PM 콘솔에서 **Install-Package mongocsharpdriver**를 입력하고 **Enter** 키를 누릅니다. ![PMConsole][focus-mongolab-csharp-pmconsole] MongoDB C# 드라이버가 프로젝트에 통합되고, 다음 줄이 _packages.config_ 파일에 자동으로 추가됩니다.

        < package id="mongocsharpdriver" version="1.9.2" targetFramework="net45" / >

### 메모 모델 추가
먼저 날짜 및 텍스트 콘텐츠만 사용하여 메모용 모델을 설정합니다.

1. 솔루션 탐색기에서 **모델**을 마우스 오른쪽 단추로 클릭한 다음 **추가 > 클래스**를 선택합니다. 이 새 클래스의 이름을 *Note.cs*로 지정합니다.

1. 이 클래스에 대해 자동으로 생성된 코드를 다음과 같이 바꿉니다.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MongoDB.Bson.Serialization.Attributes;
        using MongoDB.Bson.Serialization.IdGenerators;
        using MongoDB.Bson;
                
        namespace mongoNotes.Models
        {
            public class Note
            {
                public Note()
                {
                    Date = DateTime.UtcNow;
                }
                
                private DateTime date;
        
                [BsonId(IdGenerator = typeof(CombGuidGenerator))]
                public Guid Id { get; set; }
        
                [BsonElement("Note")]
                public string Text { get; set; }
        
                [BsonElement("Date")]
                public DateTime Date {
                    get { return date.ToLocalTime(); }
                    set { date = value;}
                }
            }
        }

### 데이터 액세스 계층 추가
MongoDB에 액세스하여 메모를 검색하고 저장하는 방법을 구성해야 합니다. 데이터 액세스 계층에서는 나중에 메모 모델을 사용하고 HomeController에 연결할 것입니다.

1. 솔루션 탐색기에서 **mongoNotes**를 마우스 오른쪽 단추로 클릭하고 **추가 > 새 폴더**를 선택합니다. 폴더의 이름을 **DAL**로 지정합니다.

1. 솔루션 탐색기에서 **DAL**을 마우스 오른쪽 단추로 클릭하고 **추가 > 클래스**를 선택합니다. 이 새 클래스의 이름을 *Dal.cs*로 지정합니다.

1. 이 클래스에 대해 자동으로 생성된 코드를 다음과 같이 바꿉니다.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using mongoNotes.Models;
        using MongoDB.Driver;
        using System.Configuration;

        namespace mongoNotes
        {
            public class Dal : IDisposable
            {
                private MongoServer mongoServer = null;
                private bool disposed = false;
        
                private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
                MongoUrl url;
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                    url = new MongoUrl(connectionString);
                }
           
                public List<Note> GetAllNotes()
                {
                    try
                    {
                        MongoCollection<Note> collection = GetNotesCollection();
                        return collection.FindAll().ToList<Note>();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<Note>();
                    }
                }
        
                // Creates a Note and inserts it into the collection in MongoDB.
                public void CreateNote(Note note)
                {
                    MongoCollection<Note> collection = getNotesCollectionForEdit();
                    try
                    {
                        collection.Insert(note);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private MongoCollection<Note> GetNotesCollection()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
                    return noteCollection;
                }
        
                private MongoCollection<Note> getNotesCollectionForEdit()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
                    return notesCollection;
                }
        
                # region IDisposable
        
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            if (mongoServer != null)
                            {
                                this.mongoServer.Disconnect();
                            }
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
            }
        }

1. 위에서 다음 코드에 유의하십시오.
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
여기서 나중에 구성할 환경 변수에 액세스합니다. 개발을 위해 로컬 mongo 인스턴스를 실행하는 경우 일시적으로 이 값을 "localhost"로 설정할 수 있습니다.
  
  또한 데이터베이스 이름을 설정합니다. 특히, **dbName** 값을 MongoLab 추가 기능을 프로비전할 때 입력한 이름으로 설정합니다.

1. 마지막으로 **GetNotesCollection()**에서 다음 코드를 조사합니다.  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  여기서는 아무것도 변경하지 않습니다. 이 코드는 **GetAllNotes()**의 다음과 같이 삽입, 업데이트 및 쿼리를 수행하는 MongoCollection 개체를 가져오는 방법을 보여 줍니다.

        collection.FindAll().ToList<Note>();

C# MongoDB 드라이버를 활용하는 방법에 대한 자세한 내용은 mongodb.org에서 [CSharp 드라이버 퀵 스타트](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "CSharp 드라이버 퀵 스타트")를 참조하세요.

### create 뷰 추가
이제 새 메모 작성을 위한 뷰를 추가합니다.

1. 솔루션 탐색기에서 **뷰 > 홈** 항목을 마우스 오른쪽 단추로 클릭하고 **추가 > 뷰**를 선택합니다. 새 뷰의 이름을 **Create**로 지정하고 **추가**를 클릭합니다.

1. 이 뷰(**Create.cshtml**)에 대해 자동으로 생성된 코드를 다음과 같이 바꿉니다.

        @model mongoNotes.Models.Note
        
        <script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
        
        @using (Html.BeginForm("Create", "Home")) {
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>New Note</legend>
                <h3>New Note</h3>       
                <div class="editor-label">
                    @Html.LabelFor(model => model.Text)
                </div>
                <div class="editor-field">
                    @Html.EditorFor(model => model.Text)
                </div>
               <p>
                    <input type="submit" value="Create" />
               </p>
           </fieldset>
        }

### index.cshtml 수정
이제 웹 앱에서 메모를 보고 작성할 수 있는 간단한 레이아웃에 대해 살펴봅니다.

1. **뷰 > 홈**에서 **Index.cshtml**을 열고 내용을 다음과 같이 바꿉니다.  

        @model IEnumerable<mongoNotes.Models.Note>
        
        @{
            ViewBag.Title = "Notes";
        }
        
        <h2>My Notes</h2>

        <table border="1">
            <tr>
                <th>Date</th>
                <th>Note Text</th>      
            </tr>
        
        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Date)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Text)
                </td>       
            </tr>
        }
        
        </table>
        <div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### HomeController.cs 수정
마지막으로 HomeController는 데이터 액세스 계층을 인스턴스화하고 뷰에 적용해야 합니다.

1. 솔루션 탐색기의 **컨트롤러**에서 **HomeController.cs**를 열고 내용을 다음과 같이 바꿉니다.  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using System.Web.Mvc;
        using mongoNotes.Models;
        using System.Configuration;
        
        namespace mongoNotes.Controllers
        {
            public class HomeController : Controller, IDisposable
            {
                private Dal dal = new Dal();
                private bool disposed = false;
                //
                // GET: /Task/
        
                public ActionResult Index()
                {
                    return View(dal.GetAllNotes());
                }
        
                //
                // GET: /Task/Create
        
                public ActionResult Create()
                {
                    return View();
                }
        
                //
                // POST: /Task/Create
        
                [HttpPost]
                public ActionResult Create(Note note)
                {
                    try
                    {
                        dal.CreateNote(note);
                        return RedirectToAction("Index");
                    }
                    catch
                    {
                        return View();
                    }
                }
        
                public ActionResult About()
                {
                    return View();
                }
        
                # region IDisposable
        
                new protected void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                new protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            this.dal.Dispose();
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
        
            }
        }
    
<a name="deploy"></a>
## 앱 배포

이제 응용 프로그램이 개발되었으므로 이를 호스트할 Azure 앱 서비스에서 웹 앱을 만들고, 웹 앱을 구성하고, 코드를 배포해야 합니다. 이 섹션의 중심이 되는 것은 MongoDB 연결 문자열(URI) 사용입니다. 이 URI로 웹 앱에서 환경 변수를 구성하여 URI가 코드와 별도로 유지되도록 할 것입니다. URI는 데이터베이스에 연결하기 위한 자격 증명을 포함하고 있으므로 중요한 정보로 처리되어야 합니다.

### 새 웹 앱 만들기 및 게시 설정 파일 가져오기
Azure 앱 서비스에서 웹 앱을 만드는 것은 매우 쉽습니다. 특히, Azure에서는 Visual Studio에 대한 게시 프로필을 자동으로 생성합니다.

1. Azure 포털에서 **새로 만들기**를 클릭합니다. ![새로 만들기][button-new]

1. **계산 > 웹앱 > 빠른 생성**을 선택합니다.
	<!-- ![CreateWebApp][screen-mongolab-newwebsite] -->

1. URL 접두사를 입력합니다. 원하는 이름을 선택합니다. 이름은 고유해야 합니다('mongoNotes'는 사용 불가능할 가능성이 높음).

1. **웹 앱 만들기**를 클릭합니다.

1. 웹 앱 만들기가 완료되면 웹 앱 목록에서 웹 앱 이름을 클릭합니다. 웹 앱 대시보드가 표시됩니다. ![WebAppDashboard][screen-mongolab-websitedashboard]

1. **간략 상태**에서 **게시 프로필 다운로드**를 클릭하고 .PublishSettings 파일을 선택한 디렉터리에 저장합니다. ![DownloadPublishProfile][button-website-downloadpublishprofile]

또는 Visual Studio에서 직접 웹 앱을 구성할 수도 있습니다. Azure 계정을 Visual Studio에 연결하면 표시되는 메시지에 따라 Visual Studio에서 웹 앱을 구성합니다. 작업이 완료되면 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하여 Azure에 배포하면 됩니다. 아래 단계에 설명된 대로 MongoLab 연결 문자열을 구성해야 합니다.

### MongoLab 연결 문자열 가져오기

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

### 웹 앱의 환경 변수에 연결 문자열 추가

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

### 웹 앱 게시
1. Visual Studio의 솔루션 탐색기에서 **mongoNotes** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. 게시 대화 상자가 표시됩니다.  
	<!-- ![Publish][dialog-mongolab-vspublish] -->

1. **가져오기**를 클릭하고 선택한 다운로드 디렉터리에서 .PublishSettings 파일을 선택합니다. 이 파일은 게시 대화 상자에 값을 자동으로 채웁니다.

1. **연결 유효성 검사**를 클릭하여 파일을 테스트합니다.

1. 유효성 검사에 성공하면 **게시**를 클릭합니다. 게시가 완료되면 새 브라우저 탭이 열리고 웹 앱이 표시됩니다.

1. 메모 텍스트를 입력하고 **만들기**를 클릭하여 결과를 확인합니다! ![HelloMongoAzure][screen-mongolab-sampleapp]

<a name="manage"></a>
## 데이터베이스 관리

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

축하합니다. MongoLab 호스트 MongoDB 데이터베이스의 지원을 받는 C# ASP.NET 응용 프로그램이 시작되었습니다. 이제 MongoLab 데이터베이스가 있으므로 데이터베이스에 대한 질문이나 우려 사항 또는 MongoDB나 C# 드라이브 자체에 대한 지원을 [support@mongolab.com](mailto:support@mongolab.com)에 문의할 수 있습니다. 여러분의 행운을 빕니다.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[screen-mongolab-sampleapp]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage

 

<!---HONumber=July15_HO3-->