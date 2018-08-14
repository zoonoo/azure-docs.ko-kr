---
title: Azure Cosmos DB용 Node.js 웹앱 빌드 | Microsoft Docs
description: 이 Node.js 자습서에서는 Microsoft Azure Cosmos DB를 사용하여 Azure Websites에 호스트된 Node.js Express 웹 응용 프로그램에서 데이터를 저장하고 액세스하는 방법을 설명합니다.
keywords: 응용 프로그램 개발, 데이터베이스 자습서, Node.js 알아보기, Node.js 자습서
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: sngun
ms.openlocfilehash: 9f7744c7743107b3587bd63a6e7681a6f1e42c00
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39608973"
---
# <a name="_Toc395783175"></a>Azure Cosmos DB 및 Node.js SDK(미리 보기)를 사용하여 Node.js 웹 응용 프로그램 빌드
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Node.js - v2.0 미리 보기](sql-api-nodejs-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

이 Node.js 자습서에서는 Azure Cosmos DB SQL API 계정을 사용하여 Azure Websites에 호스트되는 Node.js Express 응용 프로그램의 데이터를 저장하고 액세스하는 방법을 보여줍니다. 이 자습서에서는 작업을 만들고 검색하고 완료할 수 있는 간단한 웹 기반 응용 프로그램(Todo 앱)을 빌드하겠습니다. 작업은 Azure Cosmos DB에 JSON 문서로 저장됩니다. 다음 이미지는 Todo 응용 프로그램의 스크린샷을 보여줍니다.

![이 Node.js 자습서에서 만든 My Todo List 응용 프로그램의 스크린샷](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

이 자습서에서는 Azure Portal을 사용하여 Azure Cosmos DB SQL API 계정을 만드는 방법을 보여줍니다. 그 후에는 Node.js SDK 기반의 웹앱을 빌드하고 실행하여 데이터베이스 및 컨테이너를 만들고 컨테이너에 항목을 추가합니다. 이 자습서에서는 현재 미리 보기로 제공되는 Node.js SDK 2.0 버전을 사용합니다.

또한 [GitHub][GitHub]에서 완전한 샘플을 얻을 수 있습니다. 앱을 실행하는 방법에 대한 지침은 [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) 파일을 참조하세요.

## <a name="_Toc395783176"></a>필수 조건

이 문서의 지침을 따르기 전에 다음이 있는지 확인해야 합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] 버전 6.10 이상
* [Express 생성기](http://www.expressjs.com/starter/generator.html)(`npm install express-generator -g`를 통해 설치 가능)
* [Git][Git].

## <a name="_Toc395637761"></a>1단계: Azure Cosmos DB 데이터베이스 계정 만들기
Azure Cosmos DB 계정을 만들어 시작해 보겠습니다. 계정이 있거나 이 자습서에 Azure Cosmos DB 에뮬레이터를 사용하고 있는 경우 [2단계: 새 Node.js 응용 프로그램 만들기](#_Toc395783178)로 건너뛸 수 있습니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>2단계: 새 Node.js 응용 프로그램 만들기
이제 [Express](http://expressjs.com/) 프레임워크를 사용해서 기본적인 Hello World Node.js 프로젝트를 만드는 방법을 알아보겠습니다.

1. Node.js 명령 프롬프트와 같이 줄겨찾는 터미널을 엽니다.
2. 새 응용 프로그램을 저장하려는 디렉터리로 이동합니다.
3. Express 생성기를 사용해서 **todo**라는 새로운 응용 프로그램을 생성합니다.

   ```bash
   express todo
   ```
4. 새 **todo** 디렉터리를 열고 종속성을 설치합니다.

   ```bash
   cd todo
   npm install
   ```
5. 새 응용 프로그램을 실행합니다.

   ```bash
   npm start
   ```

6. 브라우저에서 [http://localhost:3000](http://localhost:3000)으로 이동하여 새 응용 프로그램을 확인할 수 있습니다.
   
    ![Node.js 알아보기 - 브라우저 창에 표시된 Hello World 응용 프로그램의 스크린샷](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 응용 프로그램을 중지하려면 터미널 창에서 Ctrl+C를 누르고 **y**를 클릭하여 일괄 작업을 종료합니다.

## <a name="_Toc395783179"></a>3단계: 필요한 모듈 설치

**package.json** 파일은 프로젝트 루트에 생성되는 파일 중 하나입니다. 이 파일에는 Node.js 응용 프로그램에 필요한 추가 모듈의 목록이 들어 있습니다. 나중에 이 응용 프로그램을 Azure Websites에 배포할 때, 응용 프로그램을 지원하기 위해 Azure에 설치해야 할 모듈을 결정하는 데 이 파일을 사용합니다. 이 자습서를 진행하려면 패키지 두 개를 더 설치해야 합니다.

1. 터미널을 열고, npm을 통해 **async** 모듈을 설치합니다.

   ```bash
   npm install async --save
   ```

2. npm을 통해 **@azure/cosmos** 모듈을 설치합니다. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>4단계: 노드 응용 프로그램에서 Azure Cosmos DB 서비스 사용
초기 설치 및 구성을 마쳤으니, 다음으로 todo 응용 프로그램이 Azure Cosmos DB와 통신하는 데 필요한 코드를 작성하겠습니다.

### <a name="create-the-model"></a>모델 만들기
1. 프로젝트 디렉터리 루트에서 **models**라는 새 디렉터리를 만듭니다.  

2. **models** 디렉터리에서 **taskDao.js**라는 새 파일을 만듭니다. 이 파일에는 데이터베이스를 만드는 데 필요한 코드와 컨테이너가 포함되어 있으며 Azure Cosmos DB에서 작업을 읽고 업데이트하고 만들고 찾는 방법을 정의합니다. 

3. 다음 코드를 **taskDao.js** 파일에 복사합니다.

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. **taskDao.js** 파일을 저장하고 닫습니다.  

### <a name="create-the-controller"></a>컨트롤러 만들기

1. 프로젝트의 **routes** 디렉터리에서 **tasklist.js**라는 새 파일을 만듭니다.  

2. 아래 코드를 **tasklist.js**에 추가합니다. 이 코드는 **tasklist.js**에서 사용되는 CosmosClient 및 async 모듈을 로드합니다. 또한 **TaskList** 클래스를 정의합니다. 이 클래스는 앞에서 정의한 **TaskDao** 개체의 인스턴스로 전달됩니다.
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. **tasklist.js** 파일을 저장하고 닫습니다.

### <a name="add-configjs"></a>config.js 추가

1. 프로젝트 디렉터리의 루트에서 **config.js**라는 새 파일을 만듭니다. 

2. 다음 코드를 **config.js** 파일에 추가합니다. 이 코드는 응용 프로그램에 필요한 구성 설정 및 값을 정의합니다.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. **config.js** 파일에서 [Microsoft Azure Portal](https://portal.azure.com)에 있는 Azure Cosmos DB 계정의 [키] 페이지에 있는 값을 사용하여 HOST 및 AUTH_KEY 값을 업데이트합니다. 

4. **config.js** 파일을 저장하고 닫습니다.

### <a name="modify-appjs"></a>app.js 수정
1. 프로젝트 디렉터리에서 **app.js** 파일을 엽니다. 이 파일은 이전에 Express 웹 응용 프로그램을 만들 때 생성되었습니다.  

2. 다음 코드를 **app.js** 파일에 추가합니다. 이 코드는 사용할 구성 파일을 정의하고, 이 파일의 값을 곧 사용할 몇 가지 변수로 읽어들입니다. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. 끝으로, **app.js** 파일을 저장하고 닫으면 작업이 거의 완료됩니다.

## <a name="_Toc395783181"></a>5단계: 사용자 인터페이스 작성
이제 사용자가 실제로 응용 프로그램을 조작할 수 있도록 사용자 인터페이스를 작성하겠습니다. 만든 Express 응용 프로그램은 **Jade** 를 뷰 엔진으로 사용합니다. Jade에 대한 자세한 내용은 [http://jade-lang.com/](http://jade-lang.com/)을 참조하세요.

1. **views** 디렉터리의 **layout.jade** 파일은 다른 **.jade** 파일에 대한 전역 템플릿으로 사용됩니다. 이 단계에서는 멋진 모습의 웹 사이트를 쉽게 디자인할 수 있게 해주는 도구 키트인 [Twitter Bootstrap](https://github.com/twbs/bootstrap)을 사용하도록 이 파일을 수정합니다.  

2. **views** 폴더에 있는 **layout.jade** 파일을 열고 파일 내용을 다음으로 바꿉니다.

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    이렇게 하면 응용 프로그램에 대한 일부 HTML을 렌더링하고 콘텐츠 페이지의 레이아웃을 제공할 수 있는 **콘텐츠**라는 **블록**을 만들도록 **Jade** 엔진에 알립니다.

    이 **layout.jade** 파일을 저장하고 닫습니다.

3. 이제 응용 프로그램에 사용되는 뷰인 **index.jade** 파일을 열고 파일 내용을 다음으로 바꿉니다.

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
       form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
   ```

이렇게 하면 레이아웃이 확장되고 앞에서 **layout.jade** 파일에 있던 **content** 자리 표시자의 콘텐츠가 제공됩니다.
   
이 레이아웃에서 HTML 폼 두 개를 만들었습니다.

첫 번째 폼에는 데이터에 대한 테이블 및 컨트롤러의 **/completeTask** 메서드에 게시하여 항목을 업데이트할 수 있게 해주는 단추 및 데이터 테이블이 포함됩니다.
    
두 번째 폼에는 컨트롤러의 **/addtask** 메서드에 게시하여 새 항목을 만들 수 있게 해주는 단추와 2개의 입력 필드가 포함됩니다.

응용 프로그램이 작동하는 데 필요한 모든 작업이 완료되었습니다.

## <a name="_Toc395783181"></a>6단계: 로컬에서 응용 프로그램 실행
1. 로컬 컴퓨터에서 응용 프로그램을 테스트하려면 터미널에서 `npm start`를 실행하여 응용 프로그램을 시작한 다음, [http://localhost:3000](http://localhost:3000) 브라우저 페이지를 새로 고칩니다. 이제 페이지가 아래 이미지처럼 표시됩니다.
   
    ![브라우저 창에 표시된 MyTodo List 응용 프로그램의 스크린샷](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > layout.jade 파일이나 index.jade 파일에서 들여쓰기에 대한 오류가 발생하면 두 파일의 처음 두 줄을 공백 없이 왼쪽에 맞춥니다. 처음 두 줄 앞에 공백이 있으면 제거하고 두 파일을 모두 저장한 다음 브라우저 창을 새로 고칩니다. 

2. [항목], [항목 이름] 및 [범주] 필드를 사용하여 새 작업을 입력한 다음 **항목 추가**를 클릭합니다. 그러면 이러한 속성을 포함한 문서를 Azure Cosmos DB에 만듭니다. 
3. 페이지가 업데이트되어 새로 만든 항목을 ToDo 목록에 표시합니다.
   
    ![할 일 모음에 새 항목이 포함된 응용 프로그램의 스크린샷](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. 작업을 완료하려면 완료 열의 확인란을 선택한 후 **작업 업데이트**를 클릭하면 됩니다. 그러면 이미 만든 문서가 업데이트되고 보기에서 제거됩니다.

5. 응용 프로그램을 중지하려면 터미널 창에서 Ctrl+C를 누른 다음 **Y**를 클릭하여 배치 작업을 종료합니다.

## <a name="_Toc395783182"></a>7단계: Azure Websites에 응용 프로그램 개발 프로젝트 배포
1. 아직 Azure 웹 사이트에 대해 git 리포지토리를 사용하도록 설정하지 않은 경우 사용하도록 설정합니다. [Azure App Service에 로컬 Git 배포](../app-service/app-service-deploy-local-git.md) 항목에서 이 작업을 수행하는 방법에 대한 지침을 찾을 수 있습니다.
2. Azure 웹 사이트를 git 원격으로 추가합니다.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. 원격 푸시로 배포합니다.
   
        git push azure master
4. 몇 초 후에 git이 웹 응용 프로그램 게시를 완료하고 Azure에서 실행 중인 작업을 볼 수 있는 브라우저가 실행됩니다.

    축하합니다! 지금까지 Azure Cosmos DB를 사용하여 첫 Node.js Express 웹 응용 프로그램을 작성하고 Azure Websites에 게시했습니다.

    이 자습서의 참조 응용 프로그램 전체를 다운로드하거나 참조하려면 [GitHub][GitHub]에서 다운로드할 수 있습니다.

## <a name="_Toc395637775"></a>다음 단계

* Azure Cosmos DB를 사용하여 규모 및 성능 테스트를 수행하고 싶으신가요? [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)를 참조하세요.
* [Azure Cosmos DB 계정 모니터링](monitor-accounts.md) 방법에 대해 알아보세요.
* [쿼리 실습](https://www.documentdb.com/sql/demo)의 샘플 데이터 집합에 대해 쿼리를 실행합니다.
* [Azure Cosmos DB 설명서](https://docs.microsoft.com/azure/cosmos-db/)를 살펴봅니다.

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

