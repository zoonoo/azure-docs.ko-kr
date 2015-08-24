<properties 
	pageTitle="가상 컴퓨터에서 실행되는 MongoDB에 연결되는 Azure에서 웹 앱 만들기" 
	description="Git를 사용하여 Azure 가상 컴퓨터의 MongoDB에 연결된 Azure 앱 서비스에 ASP.NET 앱을 배포하는 방법에 대해 설명하는 자습서입니다."
	tags="azure-portal" 
	services="app-service\web, virtual-machines" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="cephalin"/>


# 가상 컴퓨터에서 실행되는 MongoDB에 연결되는 Azure에서 웹 앱 만들기

Git을 사용하여 Azure 웹 서비스 웹 앱에 ASP.NET 응용 프로그램을 배포할 수 있습니다. 이 자습서에서는 Azure의 가상 컴퓨터에서 실행되는 MongoDB 데이터베이스에 연결되는 간단한 프런트 엔드 ASP.NET MVC 작업 목록 응용 프로그램을 빌드합니다. [MongoDB][MongoDB]는 대중적인 오픈 소스의 고성능 NoSQL 데이터베이스입니다. 개발 컴퓨터에서 ASP.NET 응용 프로그램을 실행하고 테스트한 후에 Git을 사용하여 앱 서비스 웹 앱에 응용 프로그램을 업로드합니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.


## 배경 지식 ##

다음과 관련한 지식이 있으면 이 자습서에 유용하지만 필수 사항은 아닙니다.

* MongoDB의 C# 드라이버. MongoDB에 대한 C# 응용 프로그램 개발에 대한 자세한 내용은 MongoDB [CSharp 언어 센터][MongoC#LangCenter](영문)를 참조하십시오. 
* ASP .NET 웹 응용 프로그램 프레임워크. 자세한 내용은 [ASP.net 웹 사이트][ASP.NET]에서 알아볼 수 있습니다.
* ASP .NET MVC 웹 응용 프로그램 프레임워크. 자세한 내용은 [ASP.NET MVC 웹 사이트][MVCWebSite]에서 알아볼 수 있습니다.
* Azure. [Azure][WindowsAzure]의 내용을 읽어 보고 시작할 수 있습니다.

## 필수 조건 ##

- [Visual Studio Express 2013 for Web][VSEWeb] 또는 [Visual Studio 2013][VSUlt]
- [Azure SDK for .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- 활성 Microsoft Azure 구독

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a>
## 가상 컴퓨터 만들기 및 MongoDB 설치 ##

이 자습서는 Azure에서 가상 컴퓨터를 만든 적이 있는 개발자를 대상으로 합니다. 가상 컴퓨터를 만든 후에는 가상 컴퓨터에 MongoDB를 설치해야 합니다.

* Windows 가상 컴퓨터를 만들고 MongoDB를 설치하려면 [Azure에서 Windows Server를 실행하는 가상 컴퓨터에 MongoDB 설치][InstallMongoOnWindowsVM]를 참조하십시오.
* 또는 Linux 가상 컴퓨터를 만들고 MongoDB를 설치하려면 [Azure에서 CentOS Linux를 실행하는 가상 컴퓨터에 MongoDB 설치][InstallMongoOnCentOSLinuxVM]를 참조하십시오.

Azure에서 가상 컴퓨터를 만들고 MongoDB를 설치한 후에는 가상 컴퓨터의 DNS 이름(예: "testlinuxvm.cloudapp.net") 및 끝점에서 지정한 MongoDB의 외부 포트를 기억해 둬야 합니다. 이 정보는 자습서의 뒷부분에서 필요합니다.

<a id="createapp"></a>
## 응용 프로그램 만들기 ##

이 섹션에서는 Visual Studio를 사용하여 "My Task List"라는 ASP.NET 응용 프로그램을 만들고 초기 배포를 Azure 앱 서비스 웹 앱에 수행합니다. 응용 프로그램 실행은 로컬에서 하지만 Azure의 가상 컴퓨터에 연결하여 해당 위치에서 만든 MongoDB 인스턴스를 사용합니다.

1. Visual Studio에서 **새 프로젝트**를 클릭합니다.

	![시작 페이지 새 프로젝트][StartPageNewProject]

1. **새 프로젝트** 창에서 왼쪽 창에 있는 **Visual C#**을 선택한 후 **웹**을 선택합니다. 가운데 창에서 **ASP.NET 웹 응용 프로그램**을 선택합니다. 맨 아래에서 프로젝트 이름을 "MyTaskListApp"으로 지정한 후 **확인**을 클릭합니다.

	![새 프로젝트 대화 상자][NewProjectMyTaskListApp]

1. **새 ASP.NET 프로젝트** 대화 상자에서 **MVC**를 선택한 후 **확인**을 클릭합니다.

	![MVC 템플릿 선택][VS2013SelectMVCTemplate]

1. Microsoft Azure에 로그인하지 않은 경우 로그인하라는 메시지가 표시됩니다. Azure에 로그인하라는 메시지를 따릅니다.
2. 로그인한 후 앱 서비스 웹 앱 구성을 시작할 수 있습니다. **웹 앱 이름**, **앱 서비스 계획**, **리소스 그룹** 및 **지역**을 지정하고 **확인**을 클릭합니다.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. 프로젝트 생성이 완료된 후에 **Azure 앱 서비스 활동** 창에 표시된 대로 웹 앱이 Azure 앱 서비스에서 만들 때까지 대기합니다. 그런 다음, **지금 MyTaskListApp을 이 웹 앱에 게시**를 클릭합니다.

1. **게시**를 클릭합니다.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

	기본 ASP.NET 응용 프로그램을 Azure 앱 서비스 웹 앱에 게시한 후 브라우저에서 시작됩니다.

## MongoDB C# 드라이버 설치

MongoDB는 드라이버를 통해 C# 응용 프로그램에 대한 클라이언트 쪽 지원을 제공하며, 이 드라이버는 로컬 개발 컴퓨터에 설치해야 합니다. C# 드라이버는 NuGet을 통해 사용할 수 있습니다.

MongoDB C# 드라이버를 설치하려면

1. **솔루션 탐색기**에서 **MyTaskListApp** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Manage NuGetPackages**를 선택합니다.

	![NuGet 패키지 관리][VS2013ManageNuGetPackages]

2. **Manage NuGet 패키지 관리** 창에서 왼쪽 창에 있는 **온라인**을 클릭합니다. 오른쪽의 **온라인 검색** 상자에서 "mongodb.driver"를 입력합니다. **설치**를 클릭하여 드라이버를 설치합니다.

	![MongoDB C# 드라이버 검색][SearchforMongoDBCSharpDriver]

3. **동의함**을 클릭하여 10gen, Inc. 사용 조건에 동의합니다.

4. 드라이버가 설치된 후 **닫기**를 클릭합니다. ![MongoDB C# 드라이버 설치][MongoDBCsharpDriverInstalled]


이제 MongoDB C# 드라이버가 설치되었습니다. **MongoDB.Bson**, **MongoDB.Driver** 및 **MongoDB.Driver.Core** 라이브러리에 대한 참조가 프로젝트에 추가되었습니다.

![MongoDB C# 드라이버 참조][MongoDBCSharpDriverReferences]

## 모델 추가 ##
**솔루션 탐색기**에서 *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 새 **클래스**를 **추가**한 후 클래스 이름을 *TaskModel.cs*로 지정합니다. *TaskModel.cs*에서 기존 코드를 다음 코드로 바꿉니다.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MongoDB.Bson.Serialization.Attributes;
	using MongoDB.Bson.Serialization.IdGenerators;
	using MongoDB.Bson;
	
	namespace MyTaskListApp.Models
	{
	    public class MyTask
	    {
	        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
	        public Guid Id { get; set; }
	
	        [BsonElement("Name")]
	        public string Name { get; set; }
	
	        [BsonElement("Category")]
	        public string Category { get; set; }
	
	        [BsonElement("Date")]
	        public DateTime Date { get; set; }
	
	        [BsonElement("CreatedDate")]
	        public DateTime CreatedDate { get; set; }
	
	    }
	}

## 데이터 액세스 계층 추가 ##
**솔루션 탐색기**에서 *MyTaskListApp* 프로젝트를 마우스 오른쪽 단추로 클릭하고 **DAL**이라는 **새 폴더**를 *추가*합니다. *DAL* 폴더를 마우스 오른쪽 단추로 클릭하고 새 **클래스**를 **추가**합니다. 클래스 파일 이름을 *Dal.cs*로 지정합니다. *Dal.cs*에서 기존 코드를 다음 코드로 바꿉니다.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using System.Configuration;
	
	namespace MyTaskListApp
	{
	    public class Dal : IDisposable
	    {
	        private MongoServer mongoServer = null;
	        private bool disposed = false;
	
	        // To do: update the connection string with the DNS name
			// or IP address of your server. 
			//For example, "mongodb://testlinux.cloudapp.net"
	        private string connectionString = "mongodb://<vm-dns-name>";
	
	        // This sample uses a database named "Tasks" and a 
			//collection named "TasksList".  The database and collection 
			//will be automatically created if they don't already exist.
	        private string dbName = "Tasks";
	        private string collectionName = "TasksList";
	
	        // Default constructor.        
	        public Dal()
	        {
	        }        
	
	        // Gets all Task items from the MongoDB server.        
	        public List<MyTask> GetAllTasks()
	        {
	            try
	            {
	                MongoCollection<MyTask> collection = GetTasksCollection();
	                return collection.FindAll().ToList<MyTask>();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask >();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            MongoCollection<MyTask> collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.Insert(task, SafeMode.True);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private MongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private MongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
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

## 컨트롤러 추가 ##
*솔루션 탐색기*에서 **Controllers\\HomeController.cs** 파일을 열고 기존 코드를 다음으로 바꿉니다.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;
	using MyTaskListApp.Models;
	using System.Configuration;
	
	namespace MyTaskListApp.Controllers
	{
	    public class HomeController : Controller, IDisposable
	    {
	        private Dal dal = new Dal();
	        private bool disposed = false;
	        //
	        // GET: /MyTask/
	
	        public ActionResult Index()
	        {
	            return View(dal.GetAllTasks());
	        }
	
	        //
	        // GET: /MyTask/Create
	
	        public ActionResult Create()
	        {
	            return View();
	        }
	
	        //
	        // POST: /MyTask/Create
	
	        [HttpPost]
	        public ActionResult Create(MyTask task)
	        {
	            try
	            {
	                dal.CreateTask(task);
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

## 스타일 설정 ##
페이지 위쪽에 있는 제목을 변경하려면 **솔루션 탐색기**에서 *Views\\Shared\\\_Layout.cshtml* 파일을 열고 탐색 모음 헤더에 있는 "Application name"을 "My Task List Application"으로 바꿉니다. 다음과 같습니다.

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

작업 목록 메뉴를 설정하려면 *\\Views\\Home\\Index.cshtml* 파일을 열고 기존 코드를 다음 코드로 바꿉니다.
	
	@model IEnumerable<MyTaskListApp.Models.MyTask>
	
	@{
	    ViewBag.Title = "My Task List";
	}
	
	<h2>My Task List</h2>
	
	<table border="1">
	    <tr>
	        <th>Task</th>
	        <th>Category</th>
	        <th>Date</th>
	        
	    </tr>
	
	@foreach (var item in Model) {
	    <tr>
	        <td>
	            @Html.DisplayFor(modelItem => item.Name)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Category)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Date)
	        </td>
	        
	    </tr>
	}
	
	</table>
	<div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


새 작업을 만드는 기능을 추가하려면 *Views\\Home\* 폴더를 마우스 오른쪽 단추로 클릭하고 **보기**를 **추가**합니다. 보기 이름을 *Create*로 지정합니다. 코드를 다음으로 바꿉니다.

	@model MyTaskListApp.Models.MyTask
	
	<script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
	
	@using (Html.BeginForm("Create", "Home")) {
	    @Html.ValidationSummary(true)
	    <fieldset>
	        <legend>New Task</legend>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Name)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Name)
	            @Html.ValidationMessageFor(model => model.Name)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Category)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Category)
	            @Html.ValidationMessageFor(model => model.Category)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Date)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Date)
	            @Html.ValidationMessageFor(model => model.Date)
	        </div>
	
	        <p>
	            <input type="submit" value="Create" />
	        </p>
	    </fieldset>
	}

**솔루션 탐색기**가 다음과 같습니다.

![솔루션 탐색기][SolutionExplorerMyTaskListApp]

## MongoDB 연결 문자열 설정 ##
**솔루션 탐색기**에서 *DAL/Dal.cs* 파일을 엽니다. 다음 코드 줄을 찾습니다.

	private string connectionString = "mongodb://<vm-dns-name>";

`<vm-dns-name>`을 이 자습서의 [가상 컴퓨터 만들기 및 MongoDB 설치][] 단계에서 만든 MongoDB를 실행하는 가상 컴퓨터의 DNS 이름으로 바꿉니다. 가상 컴퓨터의 DNS 이름을 찾으려면 Azure 포털로 이동하여 **가상 컴퓨터**를 선택하고 **DNS 이름**을 찾습니다.

가상 컴퓨터의 DNS 이름이 "testlinuxvm.cloudapp.net"이고 MongoDB가 기본 포트 27017을 수신 대기하는 경우 연결 문자열 코드 줄은 다음과 같습니다.

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

가상 컴퓨터 끝점이 MongoDB에 대해 다른 외부 포트를 지정하는 경우, 이 포트를 연결 문자열에서 지정할 수 있습니다.

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

MongoDB 연결 문자열에 대한 자세한 내용은 [연결][MongoConnectionStrings](영문)을 참조하십시오.

## 로컬 배포 테스트 ##

개발 컴퓨터에서 응용 프로그램을 실행하려면 **디버그** 메뉴에서 **디버깅 시작** 선택하거나 **F5** 키를 누릅니다. IIS Express가 시작되고 브라우저가 열려 응용 프로그램의 홈페이지를 시작합니다. 새 작업을 추가할 수 있습니다. 이 작업은 Azure의 가상 컴퓨터에서 실행되는 MongoDB 데이터베이스에 추가됩니다.

![My Task List 응용 프로그램][TaskListAppBlank]

## Azure 앱 서비스 웹 앱에 게시

이 섹션에서는 Azure 앱 서비스 웹 앱에 변경 내용을 게시 합니다.

1. 솔루션 탐색기에서 **MyTaskListApp**을 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.
2. **게시**를 클릭합니다.

	이제 웹 앱이 Azure 앱 서비스에서 실행되고 Azure 가상 컴퓨터의 MongoDB 데이터베이스에 액세스해야 합니다.

## 요약 ##

이제 ASP.NET 응용 프로그램을 Azure 앱 서비스 웹 앱에 배포했습니다. 웹 앱을 보려면:

1. Azure 포털에 로그인합니다.
2. **웹 앱**을 클릭합니다. 
3. **웹 앱** 목록에서 웹 앱을 선택합니다.

MongoDB에 대한 C# 응용 프로그램 개발에 대한 자세한 내용은 [CSharp 언어 센터][MongoC#LangCenter](영문)를 참조하십시오.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnCentOSLinuxVM]: /manage/linux/common-tasks/mongodb-on-a-linux-vm/
[InstallMongoOnWindowsVM]: /manage/windows/common-tasks/install-mongodb/
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[가상 컴퓨터 만들기 및 MongoDB 설치]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 

<!---HONumber=August15_HO7-->