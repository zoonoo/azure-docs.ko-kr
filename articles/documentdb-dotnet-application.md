<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="DocumentDB를 사용하여 ASP.NET MVC로 웹 응용 프로그램 작성 | Azure" description="DocumentDB를 사용하여 할 일 목록 웹 응용 프로그램을 빌드하는 방법에 대해 알아봅니다. Azure에서 호스팅되는 ASP.NET MVC 웹 응용 프로그램의 데이터를 저장하고 액세스합니다."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Build a web application with ASP.NET MVC using DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Overview</a>
==============================================================================================

<a name="_Toc395637759">Scenario</a>
------------------------------------

To highlight how customers can efficiently leverage Azure DocumentDB to store and query JSON documents, this document provides an end-to-end walk-through of building a ToDo List web application using Azure Document DB.

This walk-through shows you how to use DocumentDB service provided by Azure to store and access data from an ASP.NET MVC web application hosted on Azure and presumes that you have some prior experience using ASP.NET MVC and Azure Websites.

You will learn:

1\. Creating and provisioning a DocumentDB Account

2\. Creating a ASP.NET MVC Application

3\. Connecting to and using Azure DocumentDB from your web application

4\. Deploying the Web Application to Azure Websites

By following this walk-through, you will build a simple web-based task-management application that allows creating, retrieving and completing of tasks. The tasks will be stored as JSON documents in Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Before following the instructions in this article, you should ensure that you have the following installed:

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (or [Visual Studio Express][] which is the free version)

Azure SDK for .NET version 2.3 or higher, available through [Microsoft Web Platform Installer][]

All the screen shots in this document have been taken using Visual Studio 2013 with Update 3 applied and Azure SDK for .NET version 2.4. If your system is configured with different versions it is possible that your screens and options won't match entirely, but if you meet the above prerequisites this solution should work.

<a name="_Toc395637761">Create a DocumentDB database account</a>
================================================================

To provision a DocumentDB database account in Azure, open the Azure Management Portal and either Click the Azure Gallery tile on the homepage or click "+" in the lower left hand corner of the screen.

![Alt text](./media/documentdb-dotnet-application/image2.png)


This will open the Azure Gallery, where you can select from the many available Azure services. In the Gallery, select "Data, storage and backup" from the list of categories.

![Alt text](./media/documentdb-dotnet-application/image3.png)

From here, select the option for Azure DocumentDB

![Alt text](./media/documentdb-dotnet-application/image4.png)


Then select "Create" from the bottom of the screen

![Alt text](./media/documentdb-dotnet-application/image5.png)

This will open up the "New DocumentDB" blade where you can specify the name, region, scale, resource group and other settings for your new account.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Once you're done supplying the values for your account, click "Create" and the provisioning process will begin creating your database account.
When the provisioning process is complete, you should see a notification appear in the notifications area of the portal and the tile on your start screen (if you selected to create one) will change to show the completed action.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Once provisioning is complete, clicking the DocumentDB tile from the start screen will bring up the main blade for this newly created DocumentDB account.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Using the "Keys" button, access your endpoint URL and the Primary Key, copy these to your clipboard and keep them handy as we will use these values in the web application we will use these values in the web application we will create next.

We will now walk through how to create a new ASP.NET MVC application from the ground-up. For your reference the complete solution can be downloaded [here].

<a name="_Toc395637762">Create a new ASP.NET MVC application</a>
================================================================

In Visual Studio, click File - New Project and select the option to create a new ASP.NET MVC Web Application.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Select where you would like to create the project, what you want the project to be called (I'm using "todo") and click Ok.

![Alt text](./media/documentdb-dotnet-application/image11.png)


If you plan on hosting your application in Azure then select the box on the lower right to "Host in the cloud". We've selected to host in the cloud, and run the application hosted in an Azure Website. Selecting this option will pre-provision an Azure Website for you and make life a lot easier when it comes times to deploy the final working application. If you want to host this elsewhere or don't want to configure Azure upfront, then just un-check the "Host in the Cloud" option.

Select OK and let Visual Studio do its thing around scaffolding the empty ASP.NET MVC template. If you chose to host this in the Cloud you will see at least one additional screen asking you to login to your Azure account and provide some values for your new Website. Proceed to supply all these Azure values and continue. 

I haven't chosen a "Database server" here because we're not using an Azure SQL Database Server here, we're going to be creating a new Azure DocumentDB Account later on in the management portal. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Once Visual Studio has finished creating the boilerplate MVC application you have an empty ASP.NET application that you can run locally.

We'll skip running locally because I'm sure we've all seen the ASP.NET "Hello World" application. Let's go straight to adding DocumentDB to this project and building our application.

</h1>
<a name="_Toc395637767">Adding DocumentDB to your project</a>
=============================================================

That takes care of most of the ASP.NET MVC plumbing that we need for
this solution. Now let's get to the real purpose of this tutorial, adding Azure DocumentDB to our web application.

### 

### <a name="_Toc395637764">Installing the NuGet package</a>

The DocumentDB .NET SDK is packaged and distributed as a NuGet package. Using the NuGet package manager in Visual Studio (which you can get to by Right-Clicking on the Project and choosing "Manage NuGet Packages"

![Alt text](./media/documentdb-dotnet-application/image21.png)

Search for Online for "Azure DocumentDB" and install the package. This will download and install the DocumentDB package as well as all dependencies, like Newtonsoft.Json.

**NOTE:** While the service is still in preview, the NuGet package is marked as "Prerelease" so you need to include the option to "Include Prerelease", else you will not find the package. 

Alternatively you could use the Package Command Console to install the package by typing;

    Install-Package Microsoft.Azure.Documents.Client -Pre

Once installed your Visual Studio solution should resemble the following with two new references added;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Setting up the ASP.NET MVC application</a>
==================================================================

### 

### <a name="_Toc395637764">Add a Model</a>

Let's begin by creating the **M** in MVC, the model. In Solution Explorer, right-click the *Models* folder and then click **Add**, then **Class**

![Alt text](./media/documentdb-dotnet-application/image12.png)

Name your new Class, **Item** and then add the following code in to this new Class

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

All data in DocumentDB is passed over the wire, and stored, as JSON. To control the way your objects are serialized/deserialized by JSON.NET you can use the JsonProperty attribute as demonstrated in the Item class we just created. You don't **have** to do this but i wanted to ensure that my properties followed the JSON camelCase naming conventions. 

Not only can you control the format of the property name when it goes in to JSON, but you can entirely rename your .NET properties like I did with the Description property. 

You can, if you like, use JsonConverter objects here as well to completely control how serialization is handled.  

In order to get Visual Studio to resolve the "JsonProperty" attribute used here you need to add the following using statement to the using section of your class file;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Add a Controller</a>

That takes care of the M, now let's create the **C** in MVC, a controller class.
In **Solution Explorer**, right-click the *Controllers* folder and then click **Add**, then **Controller**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


In the **Add Scaffold** dialog box, click **MVC 5 Controller - Empty.** Click **Add.**

![Alt text](./media/documentdb-dotnet-application/image15.png)

Name your new Controller, **ItemController.**

Visual Studio will now add the ItemController your Solution Explorer should look like similar to below.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Add Views</a>

And finally, let's create the **V** in MVC, a view.


#### Add Item Index View

Expand the ***Views***  folder in Solution Explorer and location the (empty) Item folder which Visual Studio would've created for you when you added the *ItemController* earlier. Right click on ***Item*** and choose to Add a new View.

![Alt text](./media/documentdb-dotnet-application/image17.png)

In the "Add View" dialog. Call your view "***Index***", use the ***List*** Template, select the ***Item (todo.Models)*** which we created earlier as the class and finally use the ***~Views/Shared/_Layout.cshtml*** in the Solution as the Layout page.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Once all these values are set, click Add and let Visual Studio create your view for you. Visual Studio will create a template view. Once it is done, it will open the cshtml file created. We can close this document in Visual Studio as we will come back to it later.

#### Add New Item View

In a similar fashion to above, create a new View for creating new Items as per the example shown below;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Add Edit Item View

<a name="_Toc395888515"></a>
============================

And finally, add one last View for editing an Item in the same way as before;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Once this is done, close the cshtml documents in Visual Studio as we will return to these Views later.

</h1>
<a name="_Toc395637769">Wiring up DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Listing Incomplete Items</a>

Open the **ItemController** and remove all the code within the class (but leave the class) that Visual Studio added. We'll rebuild it piece by piece using DocumentDB.

Add the following code snippet within the now empty ItemController class;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

This code also uses a "pseudo repository" class called DocumentDBRepository which we have yet to create. This is actually just a Helper class that contains all the DocumentDB specific code. For the purposes of this walk-through we aren't going to implement a full data access layer with dependency injection, and factories and repository patterns, as you would probably be doing if you were building a real world application. 
For the purposes of this walk-through we're just going to put all the data access logic directly in to one project to keep things simple and focus on the DocumentDB specific bits.

Add a new Class to your project and call it **DocumentDBRepository.**
Replace the code in the class file with the following;

    public static class DocumentDBRepository
    {
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

#### 

Spend some time resolving all the namespaces in Visual Studio. All namespaces will be easy to resolve in Visual Studio as long as the NuGet package was installed successfully. The references to the ReadOrCreateDatabase and ReadOrCreateCollection methods will remain unresolved until we have added them, which comes next. 

There are two method calls used here for reading or creating DocumentDB Databases and Document Collections.
So add the following two methods to this class;

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

</h1>

#### 

This takes care setting up the Database, a DocumentCollection, and creating some code to connect to DocumentDB through the DocumentClient. 

We're reading some values from configuration, so open the **web.config** and add the following lines under the
<AppSettings\> section;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

이제 작업을 수행하기 위한 코드를 추가하겠습니다. 
ToDo List 응용 프로그램을 사용하여 수행하려는 첫 번째 작업은 완료되지 않은 항목을 표시하는 것입니다. 아래의 메서드가 이러한 작업을 수행하므로 이 메서드를 복사하여 작업할 리포지토리 클래스 내의 아무 곳에나 붙여넣으세요.
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
*CreateDocumentQuery*대한 참조는 다음 using 지시문을 수동으로 추가해야 하는 경우에 확인됩니다.

    using Microsoft.Azure.Documents.Linq;

이때 오류 없이 솔루션을 작성할 수 있어야 합니다.

지금 응용 프로그램을 실행하는 경우 Home 컨트롤러 및 해당 컨트롤러의 인덱스 뷰로 이동합니다. 이것은 시작할 때 선택한 MVC 템플릿 프로젝트에 대한 기본 동작이지만 여기서는 사용하지 않습니다. 이 동작을 변경하기 위해 이 MVC 응용 프로그램의 라우팅을 변경하겠습니다.

***App\_Start\RouteConfig.cs***를 열고 "defaults:"으로 시작하는 줄을 찾습니다. 다음과 같이 변경합니다.

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

이 구문은 이제 ASP.NET MVC에 라우팅 동작을 제어하기 위한 URL에 값이 지정되지 않은 경우 "Home" 대신 "Item"을 컨트롤러로 사용하고 사용자 인덱스를 뷰로 사용하라고 지시합니다.
이제 응용 프로그램을 실행하면 **ItemController**를 호출하고 **GetIncompleteItems** 메서드의 결과를 뷰\항목\인덱스 뷰에 반환합니다. 

이 프로젝트를 지금 작성하여 실행하면 이제 다음과 같이 표시됩니다.    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">항목 추가</a>

빈 그리드 외에 확인할 항목이 있도록 데이터베이스에 일부 항목을 추가하겠습니다.

이미 만들기용 뷰와 사용자를 만들기 뷰로 이동하는 인덱스 뷰의 단추가 있습니다. DocumentDB에 레코드를 저장하기 위해 컨트롤러 및 리포지토리에 일부 코드를 추가하겠습니다.

***ItemController.cs***를 열고 Create 작업에 대해 수행할 작업을 ASP.NET MVC에 알리는 다음 코드 조각을 추가합니다. 이 경우 앞에서 만든 관련 Create.cshtml 뷰를 렌더링합니다.

    public ActionResult Create()
    { 
	return View(); 
    }

이제 만들기 뷰의 제출을 수락하는 코드를 이 컨트롤러에 더 추가해야 합니다.

이 컨트롤러에 대한 폼 POST의 처리 방법을 ASP.NET MVC에 알리는 다음 코드 블록을 추가합니다.
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**보안 정보**: [ValidateAntiForgeryToken] 특성은 여기서 교차 사이트 요청 위조 공격으로부터 이 응용 프로그램을 보호하는 데 사용됩니다. 이 특성을 추가하는 것 외에 뷰가 이 위조 방지 토큰과 작동하도록 해야 합니다. 이 주제에 대한 자세한 내용과 이를 올바르게 구현하는 방법의 예는 [교차 사이트 요청 위조 방지][](영문)를 참조하세요. 이 문서 끝에 있는 다운로드 링크의 소스 코드는 완전히 구현되어 있습니다.

**보안 정보**: 또한 메서드 매개 변수에 [Bind] 특성을 사용하여 과도한 게시 공격으로부터 보호할 수 있습니다. 자세한 내용은 [ASP.NET MVC의 기본 CRUD 작업][](영문)을 참조하세요.

이제 필요한 내용이 구현되었으므로 항목 컨트롤러가   폼에서 리포지토리 클래스의 CreateDocument 메서드로 항목을 안전하게 전달하므로 다음 메서드를 DocumentDBRepository 클래스에 추가합니다.

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

이 메서드는 단순히 전달된 개체를 받아서 DocumentDB에 저장합니다.
데이터베이스에 새 항목을 추가하는 데 필요한 코드가 완성되었습니다.


### <a name="_Toc395637772">항목 편집</a>

마지막으로 수행할 작업은 데이터베이스에서 항목을 편집하고 작업이 완료되면 완료로 표시하는 기능입니다. 추가와 마찬가지로 편집용 뷰는 이미 추가되었으므로 다시 컨트롤러와 DocumentDBRepository 클래스에 코드를 더 추가하기만 하면 됩니다.

ItemController 클래스에 다음을 추가합니다.

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

첫 번째 메서드는 사용자가 인덱스 뷰에서 "편집" 링크를 클릭할 때 발생하는 Http Get을 처리합니다. 이 메서드는 DocumentDB에서 문서를 가져와 편집 뷰에 전달합니다.

그런 다음 편집 뷰는 IndexController에 Http Post를 수행합니다. 
추가한 두 번째 메서드는 데이터베이스에 저장되도록 업데이트된 개체를 DocumentDB에 전달하여 이를 처리합니다.

DocumentDBRepository 클래스에 다음을 추가합니다.

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

두 메서드 중 첫 번째 메서드가 DocumentDB에서 항목을 가져오며, 이 항목이 다시 ItemController 및 편집 뷰로 전달됩니다.

방금 추가한 두 메서드 중 두 번째 메서드는 DocumentDB의 문서를 ItemController에서 전달된 문서 버전으로 바꿉니다.

응용 프로그램을 실행하는 데 필요한 모든 작업(완료되지 않은 항목 나열, 새 항목 추가 및 항목 편집)이 완료되었습니다.

</h3>
<a name="_Toc395637773">로컬에서 응용 프로그램 실행</a>
=========================================================

### 

로컬 컴퓨터에서 응용 프로그램을 테스트하려면 Visual Studio에서 F5 키를 누릅니다. 응용 프로그램이 작성되고 이전과 같이 빈 그리드 페이지가 표시된 브라우저가 시작됩니다.

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\.항목, 항목 이름 및 범주에 대해 제공된 필드를 사용하여 정보를 입력하고 **"새로 만들기"** 링크를 클릭한 다음 일부 값을 제공합니다. "완료" 확인란을 선택 취소된 상태로 둡니다. 그렇지 않으면 새 항목이 완료 상태로 추가되며 초기 목록에 나타나지 않습니다.

![Alt text](./media/documentdb-dotnet-application/image25.png)

만들기를 누르면 다시 인덱스 페이지로 리디렉션되며 해당 항목이 목록에 표시됩니다.

![Alt text](./media/documentdb-dotnet-application/image26.png)

Todo 목록에 항목을 더 추가해도 됩니다.

2\.목록에서 항목 옆의 "편집"을 클릭합니다. 편집 뷰로 이동되며, 여기서 "Completed" 플래그를 비롯한 개체 속성을 업데이트할 수 있습니다. 이 경우 항목이 완료로 표시되며 완료되지 않은 작업 목록에서 제거됩니다.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.작업을 완료하려면 확인란을 선택하고 **저장**을 클릭하면 됩니다. 다시 목록 페이지로 리디렉션되며 이제 해당 항목이 목록에 더 이상 표시되지 않습니다.

</h3>
<a name="_Toc395637774">Azure 웹 사이트에 응용 프로그램 배포</a>
================================================================

### 

이제 완료된 응용 프로그램이 DocumentDB에 대해 올바르게 작동하므로 Azure 웹 사이트에 배포하겠습니다.

빈 ASP.NET MVC 프로젝트를 만들 때 "클라우드에 호스트"를 선택한 경우 Visual Studio에서 대부분의 작업을 수행하므로 쉽게 배포할 수 있습니다. 이 응용 프로그램을 게시하려면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고(로컬에서 실행하고 있지 않은지 확인) 게시를 선택하기만 하면 됩니다.

![Alt text](./media/documentdb-dotnet-application/image28.png)

사용자 자격 증명에 따라 모든 항목이 이미 구성되어 있어야 합니다. 실제로 Azure의 표시된 "대상 URL"에 웹 사이트가 이미 만들어져 있으므로 **게시**를 클릭하기만 하면 됩니다.

![Alt text](./media/documentdb-dotnet-application/image29.png)

몇 초 후에 Visual Studio에서 웹 응용 프로그램 게시를 완료하고 브라우저를 시작하며, Azure에서 실행되는 작업 내용을 확인할 수 있습니다.

</h3>



<a name="_Toc395637775">결론</a>
======================================

### 

축하합니다. 지금까지 Azure DocumentDB를 사용하여 첫 ASP.NET MVC 응용 프로그램을 작성하고 Azure 웹 사이트에 게시했습니다. 전체 응용 프로그램의 소스 코드(여기서 건너뛴 세부 정보 및 삭제 기능 포함)는 [여기][]서 다운로드할 수 있습니다.


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
[Microsoft 웹 플랫폼 설치 관리자]: http://www.microsoft.com/web/downloads/platform.aspx
[시작]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[교차 사이트 요청 위조 방지]: http://go.microsoft.com/fwlink/?LinkID=517254
[ASP.NET MVC의 기본 CRUD 작업]: http://go.microsoft.com/fwlink/?LinkId=317598
