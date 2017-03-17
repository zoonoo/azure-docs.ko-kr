---
title: "MongoDB API를 사용하여 DocumentDB 웹앱 빌드 | Microsoft 문서"
description: "DocumentDB MongoDB API를 사용하여 온라인 데이터베이스 웹앱을 만드는 NoSQL 자습서입니다."
keywords: "MongoDB 예제"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: c37304efecb2de247a2d63fe244e71c96966ebe0
ms.lasthandoff: 03/08/2017


---
# <a name="web-application-development-with-documentdb-api-for-mongodb"></a>DocumentDB: MongoDB API를 사용한 웹 응용 프로그램 개발
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [MongoDB용 .NET](documentdb-mongodb-application.md)
> * [Node.JS](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

이 예제에서는 .NET을 사용하여 DocumentDB: MongoDB API 웹앱을 빌드하는 방법을 보여 줍니다.

이 예제를 사용하려면 다음을 수행해야 합니다.

* Azure DocumentDB: MongoDB API 계정을 [만듭니다](documentdb-create-mongodb-account.md).
* MongoDB [연결 문자열](documentdb-connect-mongodb-account.md) 정보를 검색합니다.

최소한의 수정으로 [가상 컴퓨터에서 실행되는 MongoDB에 연결하는 Azure에서 웹앱 만들기](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) 자습서를 사용하여 DocumentDB: MongoDB API 계정에 연결되는 MongoDB 응용 프로그램을 신속하게 설치할 수 있습니다(Azure 웹앱에 로컬로 또는 게시됨).  

1. 한 번 수정하여 자습서를 수행합니다.  Dal.cs 코드를 다음으로 바꿉니다.

    ```csharp   
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
    ```

2. 계정 설정 별로 Dal.cs 파일에서 다음 변수를 수정합니다.

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. 앱을 사용합니다.

## <a name="next-steps"></a>다음 단계
* DocumentDB: MongoDB API 계정으로 [MongoChef를 사용](documentdb-mongodb-mongochef.md)하고 [RoboMongo를 사용](documentdb-mongodb-robomongo.md)하는 방법을 알아봅니다.

