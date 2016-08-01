<properties 
	pageTitle="MongoDB 예제에 대한 DocumentDB | Microsoft Azure" 
	description="MongoDB에 대한 DocumentDB의 프로토콜 지원에 대한 예제를 찾습니다." 
	keywords="MongoDB 예제"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# MongoDB에 대한 DocumentDB 프로토콜 지원 예제
이러한 예제를 사용하려면 다음을 수행해야 합니다.

- MongoDB에 대한 프로토콜 지원을 사용하는 Azure DocumentDB 계정 [만들기](documentdb-create-mongodb-account.md)
- MongoDB [연결 문자열](documentdb-connect-mongodb-account.md) 정보에 대한 프로토콜 지원을 사용하는 DocumentDB 계정 검색

## 샘플 ASP.NET MVC 작업 목록 응용 프로그램 시작

최소한의 수정으로 [가상 컴퓨터에서 실행되는 MongoDB에 연결하는 Azure에서 웹앱 만들기](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) 자습서를 사용하여 MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정에 연결된 MongoDB 응용 프로그램을 신속하게 설치할 수 있습니다(Azure 웹앱에 로컬로 또는 게시됨).

1. 한 번 수정하여 자습서를 수행합니다. Dal.cs 코드를 다음으로 바꿉니다.
	
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		using MyTaskListApp.Models;
		using MongoDB.Driver;
		using MongoDB.Bson;
		using System.Configuration;
		using System.Security.Authentication;

		namespace MyTaskListApp
		{
		    public class Dal : IDisposable
	    	{
        		//private MongoServer mongoServer = null;
        		private bool disposed = false;

		        // To do: update the connection string with the DNS name
        		// or IP address of your server. 
        		//For example, "mongodb://testlinux.cloudapp.net
        		private string connectionString = "mongodb://localhost:27017";
        		private string userName = "<your user name>";
        		private string host = "<your host>";
        		private string password = "<your password>";

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
                		var collection = GetTasksCollection();
                		return collection.Find(new BsonDocument()).ToList();
            		}
            		catch (MongoConnectionException)
            		{
                		return new List<MyTask>();
            		}
        		}

        		// Creates a Task and inserts it into the collection in MongoDB.
        		public void CreateTask(MyTask task)
        		{
            		var collection = GetTasksCollectionForEdit();
            		try
            		{
                		collection.InsertOne(task);
            		}
            		catch (MongoCommandException ex)
            		{
                		string msg = ex.Message;
            		}
        		}
		
		        private IMongoCollection<MyTask> GetTasksCollection()
		        {
            		MongoClientSettings settings = new MongoClientSettings();
            		settings.Server = new MongoServerAddress(host, 10250);
            		settings.UseSsl = true;
            		settings.SslSettings = new SslSettings();
            		settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
		
            		MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
            		MongoIdentityEvidence evidence = new PasswordEvidence(password);
		
            		settings.Credentials = new List<MongoCredential>()
            		{
                		new MongoCredential("SCRAM-SHA-1", identity, evidence)
            		};

            		MongoClient client = new MongoClient(settings);
            		var database = client.GetDatabase(dbName);
            		var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
            		return todoTaskCollection;
        		}
		
		        private IMongoCollection<MyTask> GetTasksCollectionForEdit()
		        {
            		MongoClientSettings settings = new MongoClientSettings();
            		settings.Server = new MongoServerAddress(host, 10250);
            		settings.UseSsl = true;
            		settings.SslSettings = new SslSettings();
            		settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
		
            		MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
            		MongoIdentityEvidence evidence = new PasswordEvidence(password);
		
            		settings.Credentials = new List<MongoCredential>()
            		{
                		new MongoCredential("SCRAM-SHA-1", identity, evidence)
            		};
            		MongoClient client = new MongoClient(settings);
            		var database = client.GetDatabase(dbName);
            		var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
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
                		}
            		}

            		this.disposed = true;
        		}

        		# endregion
    		}
		}

2.	계정 설정 별로 Dal.cs 파일에서 다음 변수를 수정합니다.

        private string userName = "<your user name>";
    	private string host = "<your host>";
        private string password = "<your password>";

3. 앱을 사용합니다.

## 다음 단계

- MongoDB [미리 보기 개발 지침](documentdb-mongodb-guidelines.md)에 대한 프로토콜 지원을 사용하는 DocumentDB에 대해 읽습니다.
- MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정이 있는 [MongoChef를 사용](documentdb-mongodb-mongochef.md)하는 방법을 알아봅니다.

 

<!---HONumber=AcomDC_0720_2016-->