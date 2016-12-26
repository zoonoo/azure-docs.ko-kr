---
title: "MongoDB 예제용 DocumentDB | Microsoft 문서"
description: "MongoDB에 대한 DocumentDB의 프로토콜 지원에 대한 예제를 찾습니다."
keywords: "MongoDB 예제"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 8d4c3aaefee502e79dd1ea3e074937bb9a1d4593
ms.openlocfilehash: 703373c2c0090a2c6ffef3433aee3a05f0f41475


---
# <a name="documentdb-protocol-support-for-mongodb-examples"></a>MongoDB에 대한 DocumentDB 프로토콜 지원 예제
이러한 예제를 사용하려면 다음을 수행해야 합니다.

* [만들기](documentdb-create-mongodb-account.md) 
* MongoDB [연결 문자열](documentdb-connect-mongodb-account.md) 정보에 대한 프로토콜 지원을 사용하는 DocumentDB 계정 검색

## <a name="get-started-with-a-sample-nodejs-getting-started-app"></a>샘플 Node.js 시작 앱 시작

1. *app.js* 파일을 만들고 아래 코드를 복사하여 붙여넣습니다.

         var MongoClient = require('mongodb').MongoClient;
         var assert = require('assert');
         var ObjectId = require('mongodb').ObjectID;
         var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';

         var insertDocument = function(db, callback) {
            db.collection('families').insertOne( {
                 "id": "AndersenFamily",
                 "lastName": "Andersen",
                 "parents": [
                     { "firstName": "Thomas" },
                     { "firstName": "Mary Kay" }
                 ],
                 "children": [
                     { "firstName": "John", "gender": "male", "grade": 7 }
                 ],
                 "pets": [
                     { "givenName": "Fluffy" }
                 ],
                 "address": { "country": "USA", "state": "WA", "city": "Seattle" }
             }, function(err, result) {
             assert.equal(err, null);
             console.log("Inserted a document into the families collection.");
             callback();
           });
         };

         var findFamilies = function(db, callback) {
            var cursor =db.collection('families').find( );
            cursor.each(function(err, doc) {
               assert.equal(err, null);
               if (doc != null) {
                  console.dir(doc);
               } else {
                  callback();
               }
            });
         };

         var updateFamilies = function(db, callback) {
            db.collection('families').updateOne(
               { "lastName" : "Andersen" },
               {
                 $set: { "pets": [
                     { "givenName": "Fluffy" },
                     { "givenName": "Rocky"}
                 ] },
                 $currentDate: { "lastModified": true }
               }, function(err, results) {
               console.log(results);
               callback();
            });
         };

         var removeFamilies = function(db, callback) {
            db.collection('families').deleteMany(
               { "lastName": "Andersen" },
               function(err, results) {
                  console.log(results);
                  callback();
               }
            );
         };

         MongoClient.connect(url, function(err, db) {
           assert.equal(null, err);
           insertDocument(db, function() {
             findFamilies(db, function() {
               updateFamilies(db, function() {
                 removeFamilies(db, function() {
                     db.close();
                 });
               });
             });
           });
         });

2. 계정 설정에 따라 *app.js* 파일에서 다음 변수를 수정합니다([연결 문자열](documentdb-connect-mongodb-account.md)을 찾는 방법 자세히 알아보기).
   
         var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
     
3. 즐겨찾는 터미널을 열고 **npm install mongodb --save**를 설치한 다음 **node app.js**로 앱을 실행합니다.

## <a name="get-started-with-a-sample-aspnet-mvc-task-list-application"></a>샘플 ASP.NET MVC 작업 목록 응용 프로그램 시작
최소한의 수정으로 [가상 컴퓨터에서 실행되는 MongoDB에 연결하는 Azure에서 웹앱 만들기](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) 자습서를 사용하여 MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정에 연결된 MongoDB 응용 프로그램을 신속하게 설치할 수 있습니다(Azure 웹앱에 로컬로 또는 게시됨).  

1. 한 번 수정하여 자습서를 수행합니다.  Dal.cs 코드를 다음으로 바꿉니다.
   
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
2. 계정 설정 별로 Dal.cs 파일에서 다음 변수를 수정합니다.
   
         private string userName = "<your user name>";
         private string host = "<your host>";
         private string password = "<your password>";
3. 앱을 사용합니다.

## <a name="next-steps"></a>다음 단계
* MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정이 있는 [MongoChef를 사용](documentdb-mongodb-mongochef.md) 하는 방법을 알아봅니다.



<!--HONumber=Nov16_HO4-->


