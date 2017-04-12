---
title: "Azure 테이블 서비스를 사용하는 Node.js 웹앱"
description: "이 자습서는 Azure 테이블 서비스를 사용하여 Azure 앱 서비스 웹앱에서 호스트되는 Node.js 응용프로그램의 데이터를 저장하는 방법을 설명합니다."
tags: azure-portal
services: app-service\web, storage
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 029e6f46-f586-4309-adbf-71c7b8d537d4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3f0183c2c88819dfc67886676fd8c69b246537b6
ms.lasthandoff: 03/21/2017


---
# <a name="nodejs-web-app-using-the-azure-table-service"></a>Azure 테이블 서비스를 사용하는 Node.js 웹앱
## <a name="overview"></a>개요
이 자습서에서는 Azure Data Management에서 제공하는 Table service를 사용하여 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 웹앱에서 호스트되는 [node] 응용 프로그램에서 데이터를 저장하고 액세스하는 방법을 보여 줍니다. 이 자습서에서는 이전에 node 및 [Git]를 사용한 경험이 있다고 가정합니다.

다음 내용을 배웁니다.

* npm(node 패키지 관리자)을 사용하여 node 모듈을 설치하는 방법
* Azure 테이블 서비스에 대한 작업 방법
* Azure CLI를 사용하여 웹앱을 만드는 방법

이 자습서를 따라 작업을 만들고, 검색하고, 완료할 수 있는 간단한 웹 기반 "할 일 모음" 응용프로그램을 구축합니다. 작업은 테이블 서비스에 저장됩니다.

다음은 완성된 응용프로그램입니다.

![빈 tasklist가 표시된 웹 페이지][node-table-finished]

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](https://azure.microsoft.com/try/app-service/)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
이 문서의 지침을 따르기 전에 다음이 설치되어 있는지 확인합니다.

* [node] 버전 0.10.24 이상
* [Git]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="create-a-storage-account"></a>저장소 계정 만들기
Azure 저장소 계정을 만듭니다. 앱에서는 할 일 항목을 저장하기 위해 이 계정을 사용합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 포털의 왼쪽 아래에서 **새로 만들기** 아이콘을 클릭한 다음 **데이터 + 저장소** > **저장소**를 클릭합니다. 저장소 계정에 고유한 이름을 지정하고 이를 위한 새 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다.
   
      ![새 단추](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)
   
    저장소 계정이 만들어지면 **알림** 단추가 녹색 **성공**으로 깜박이고 저장소 계정의 블레이드가 열려 새로 만든 리소스 그룹에 속한 것을 보여줍니다.
3. 저장소 계정의 블레이드에서 **설정** > **키**를 클릭합니다. 기본 액세스 키를 클립보드에 복사합니다.
   
    ![액세스 키][portal-storage-access-keys]

## <a name="install-modules-and-generate-scaffolding"></a>모듈 설치 및 스캐폴딩 생성
이 섹션에서는 새로운 Node 응용 프로그램을 만들고 npm을 사용하여 모듈 패키지를 추가합니다. 이 응용 프로그램의 경우 [Express] 및 [Azure] 모듈을 사용합니다. Express 모듈은 node에 모델 보기 컨트롤러 프레임워크를 제공하지만 Azure 모듈은 테이블 서비스에 대한 연결을 제공합니다.

### <a name="install-express-and-generate-scaffolding"></a>express 설치 및 스캐폴딩 생성
1. 명령줄에서 **tasklist** 라는 새 디렉터리를 만들고 해당 디렉터리로 전환합니다.  
2. 다음 명령을 입력하여 Express 모듈을 설치합니다.
   
        npm install express-generator@4.2.0 -g
   
    운영 체제에 따라 명령 앞에 'sudo'를 배치해야 할 수 있습니다.
   
        sudo npm install express-generator@4.2.0 -g
   
    출력은 다음 예제와 유사합니다.
   
        express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)
   
   > [!NOTE]
   > '-g' 매개 변수는 모듈을 전역적으로 설치합니다. 이와 같이 **express** 를 사용하여 추가 경로 정보를 입력하지 않고도 웹앱 스캐폴딩을 생성할 수 있습니다.
   > 
   > 
3. 응용프로그램에 대한 스캐폴딩을 만들려면 **express** 명령을 입력하세요.
   
        express
   
    이 명령의 출력은 다음 예제와 유사합니다.
   
           create : .
           create : ./package.json
           create : ./app.js
           create : ./public
           create : ./public/images
           create : ./routes
           create : ./routes/index.js
           create : ./routes/users.js
           create : ./public/stylesheets
           create : ./public/stylesheets/style.css
           create : ./views
           create : ./views/index.jade
           create : ./views/layout.jade
           create : ./views/error.jade
           create : ./public/javascripts
           create : ./bin
           create : ./bin/www
   
           install dependencies:
             $ cd . && npm install
   
           run the app:
             $ DEBUG=my-application ./bin/www
   
    이제 **tasklist** 디렉터리에 몇 개의 새 디렉터리 및 파일이 있습니다.

### <a name="install-additional-modules"></a>추가 모듈 설치
**express**로 만든 파일 중 하나는 **package.json**입니다. 이 파일에는 모듈 종속성 목록이 포함됩니다. 나중에 이 응용프로그램을 앱 서비스 웹앱에 배포하는 경우 이 파일을 사용하여 Azure에 설치해야 할 모듈을 결정합니다.

명령줄에서 다음 명령을 입력하여 **package.json** 파일에 설명된 모듈을 설치합니다. 'sudo'를 사용해야 할 수 있습니다.

    npm install

이 명령의 출력은 다음 예제와 유사합니다.

    debug@0.7.4 node_modules\debug

    cookie-parser@1.0.1 node_modules\cookie-parser
    ├── cookie-signature@1.0.3
    └── cookie@0.1.0

    [...]


이제 다음 명령을 입력하여 [azure], [node-uuid], [nconf] 및 [async] 모듈을 설치합니다.

    npm install azure-storage node-uuid async nconf --save

**--save** 플래그는 이러한 모듈에 대한 항목을 **package.json** 파일에 추가합니다.

이 명령의 출력은 다음 예제와 유사합니다.

    async@0.9.0 node_modules\async

    node-uuid@1.4.1 node_modules\node-uuid

    nconf@0.6.9 node_modules\nconf
    ├── ini@1.2.1
    ├── async@0.2.9
    └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

    [...]


## <a name="create-the-application"></a>응용 프로그램 만들기
이제 응용프로그램을 빌드할 준비가 되었습니다.

### <a name="create-a-model"></a>모델 만들기
*모델* 은 응용프로그램에서 데이터를 나타내는 개체입니다. 응용프로그램의 경우 모델만 할 일 목록에서 항목을 나타내는 작업 개체입니다. 작업에는 다음 필드가 포함됩니다.

* PartitionKey
* RowKey
* 이름(문자열)
* 범주(문자열)
* 완료됨(부울)

**PartitionKey** 및 **RowKey**는 테이블 키로 Table Service에서 사용됩니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](https://msdn.microsoft.com/library/azure/dd179338.aspx)를 참조하세요.

1. **tasklist** 디렉터리에서 **models**라는 새로운 디렉터리를 만듭니다.
2. **models** 디렉터리에서 **task.js**라는 새 파일을 만듭니다. 이 파일에는 응용 프로그램에서 만든 작업 모델이 포함됩니다.
3. **task.js** 파일의 시작 부분에 필수 라이브러리를 참조하는 다음 코드를 추가합니다.
   
        var azure = require('azure-storage');
          var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;
4. Task 개체를 정의하고 내보내는 다음 코드를 추가합니다. 이 개체가 테이블에 연결하는 작업을 수행합니다.
   
          module.exports = Task;
   
        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };
5. 다음 코드를 추가하여 Task 개체에서 추가 메서드를 정의합니다. 이 메서드가 테이블에 저장된 데이터에 대한 조작을 허용합니다.
   
        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },
   
          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };
            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },
   
          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }
6. **task.js** 파일을 저장하고 닫습니다.

### <a name="create-a-controller"></a>컨트롤러 만들기
*컨트롤러* 는 HTTP 요청을 처리하고 HTML 응답을 렌더링합니다.

1. **tasklist/routes** 디렉터리에서 **tasklist.js**라는 새 파일을 만들고 텍스트 편집기에서 엽니다.
2. 아래 코드를 **tasklist.js**에 추가합니다. 이 코드는 **tasklist.js**에 사용되는 azure 및 async 모듈을 로드합니다. 또한 **TaskList** 함수를 정의합니다. 이 함수에 앞서 정의한 **Task** 개체의 인스턴스가 전달됩니다.
   
        var azure = require('azure-storage');
        var async = require('async');
   
        module.exports = TaskList;
3. **TaskList** 개체를 정의합니다.
   
        function TaskList(task) {
          this.task = task;
        }
4. 다음 메소드를 **TaskList**에 추가합니다.
   
        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = new azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },
   
          addTask: function(req,res) {
            var self = this;
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },
   
          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }

### <a name="modify-appjs"></a>app.js 수정
1. **tasklist** 디렉터리에서 **app.js** 파일을 엽니다. 이 파일은 앞에서 **express** 명령을 실행하여 만들었습니다.
2. 파일의 앞부분에 다음을 추가하여 azure 모듈을 로드하고 테이블 이름인 파티션 키를 설정하고 이 예제에서 사용한 저장소 자격 증명을 설정합니다.
   
        var azure = require('azure-storage');
        var nconf = require('nconf');
        nconf.env()
             .file({ file: 'config.json', search: true });
        var tableName = nconf.get("TABLE_NAME");
        var partitionKey = nconf.get("PARTITION_KEY");
        var accountName = nconf.get("STORAGE_NAME");
        var accountKey = nconf.get("STORAGE_KEY");
   
   > [!NOTE]
   > nconf는 환경 변수 또는 나중에 만들 **config.json** 파일에서 구성 값을 로드합니다.
   > 
   > 
3. app.js 파일에서 다음 줄이 보일 때까지 아래로 스크롤합니다.
   
        app.use('/', routes);
        app.use('/users', users);
   
    위의 줄을 아래의 코드로 바꿉니다. 이 코드는 저장소 계정에 대한 연결을 사용하여 <strong>Task</strong>의 인스턴스를 초기화합니다. 이 인스턴스는 <strong>TaskList</strong>로 전달되어 테이블 서비스와의 통신에 사용됩니다.
   
        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
        var taskList = new TaskList(task);
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
4. **app.js** 파일을 저장합니다.

### <a name="modify-the-index-view"></a>인덱스 보기 수정
1. **tasklist/views/index.jade** 파일을 텍스트 편집기에서 엽니다.
2. 파일의 전체 내용을 다음 코드로 바꿉니다. 이렇게 하면 기존 작업을 표시하는 보기와 새 작업을 추가하고 기존 작업을 완료로 표시하는 양식이 정의됩니다.
   
        extends layout
   
        block content
          h1= title
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
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name:
            input(name="item[name]", type="textbox")
            label Item Category:
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item
3. **index.jade** 파일을 저장하고 닫습니다.

### <a name="modify-the-global-layout"></a>전역 레이아웃 수정
**views** 디렉터리의 **layout.jade** 파일은 다른 **.jade** 파일에 대한 전역 템플릿입니다. 이 단계에서는 멋진 모습의 웹앱을 쉽게 디자인할 수 있게 해주는 도구 키트인 [Twitter Bootstrap](https://github.com/twbs/bootstrap)을 사용하도록 이 파일을 수정합니다.

[Twitter Bootstrap](http://getbootstrap.com/)용 파일을 다운로드하여 추출합니다. Bootstrap **css** 폴더의 **bootstrap.min.css** 파일을 응용 프로그램의 **public/stylesheets** 디렉터리에 복사합니다.

**views** 폴더에서 **layout.jade**를 열고 전체 내용을 다음으로 바꿉니다.

    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body.app
        nav.navbar.navbar-default
          div.navbar-header
          a.navbar-brand(href='/') My Tasks
        block content

### <a name="create-a-config-file"></a>구성 파일 만들기
앱을 로컬로 실행하기 위해 Azure 저장소 자격 증명을 구성 파일에 배치합니다. 다음 JSON으로 **config.json**이라는 이름의 파일을 만듭니다.

    {
        "STORAGE_NAME": "<storage account name>",
        "STORAGE_KEY": "<storage access key>",
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

**저장소 계정 이름**을 앞서 만든 저장소 계정 이름으로 바꾸고 **저장소 액세스 키**를 저장소 계정의 기본 선택키로 바꿉니다. 예:

    {
        "STORAGE_NAME": "nodejsappstorage",
        "STORAGE_KEY": "KG0oDd..."
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

다음과 같이 이 파일을 *tasklist* 디렉터리보다 **한 디렉터리 높은 단계** 에 저장합니다.

    parent/
      |-- config.json
      |-- tasklist/

공용이 될 수 있는 원본 제어기에 구성 파일을 확인하지 않도록 하기 위해서입니다. Azure에 앱을 배포할 때 구성 파일 대신 환경 변수를 사용합니다.

## <a name="run-the-application-locally"></a>로컬에서 응용 프로그램 실행
로컬 컴퓨터에서 응용 프로그램을 테스트하려면 다음 단계를 수행하세요.

1. 명령줄에서 **tasklist** 디렉터리로 변경합니다.
2. 다음 명령을 사용하여 응용 프로그램을 로컬에서 시작합니다.
   
        npm start
3. 웹 브라우저를 열고 http://127.0.0.1:3000 으로 이동합니다.
   
    다음 예제와 유사한 웹 페이지가 나타납니다.
   
    ![빈 tasklist가 표시된 웹 페이지][node-table-finished]
4. 새 할 일 항목을 만들려면 이름 및 범주를 입력하고 **항목 추가**를 클릭합니다. 
5. 작업을 완료로 표시하려면 **완료**에 표시하고 **작업 업데이트**를 클릭합니다.
   
    ![작업 목록의 새 항목 이미지][node-table-list-items]

응용프로그램이 로컬로 실행함에도 불구하고 Azure 테이블 서비스에 해당 데이터를 저장합니다.

## <a name="deploy-your-application-to-azure"></a>Azure에 응용 프로그램 배포
이 섹션의 단계는 Azure 명령줄 도구를 사용하여 앱 서비스에서 새 웹앱을 만든 다음 Git을 사용하여 응용프로그램을 배포합니다. 이러한 단계를 수행하려면 Azure 구독이 있어야 합니다.

> [!NOTE]
> 이러한 단계는 [Azure 포털](https://portal.azure.com/)을 사용하여 수행할 수도 있습니다. [Azure 앱 서비스에서 Node.js 웹앱 빌드 및 배포]를 참조하세요.
> 
> 처음으로 만든 웹앱인 경우 Azure 포털을 사용하여 이 응용프로그램을 배포해야 합니다.
> 
> 

시작하려면 명령줄에서 다음 명령을 입력하여 [Azure CLI] 를 설치합니다.

    npm install azure-cli -g

### <a name="import-publishing-settings"></a>게시 설정 가져오기
이 단계에서는 구독에 대한 정보를 포함하는 파일을 다운로드합니다.

1. 다음 명령을 입력합니다.
   
        azure login
   
    이 명령은 브라우저를 시작하고 다운로드 페이지로 이동합니다. 메시지가 나타나면 Azure 구독과 관련된 계정으로 로그인합니다.
   
    <!-- ![The download page][download-publishing-settings] -->
   
    파일 다운로드가 자동으로 시작됩니다. 그렇지 않은 경우 페이지 처음 부분에서 링크를 클릭하여 수동으로 파일을 다운로드할 수 있습니다. 파일을 저장하고 파일 경로를 기록해 둡니다.
2. 다음 명령을 입력하여 설정을 가져옵니다.
   
        azure account import <path-to-file>
   
    이전 단계에서 다운로드한 게시 설정 파일의 경로와 파일 이름을 지정합니다.
3. 설정을 가져온 후에는 게시 설정 파일을 삭제합니다. 이 파일은 더 이상 필요하지 않으며 Azure 구독과 관련된 중요한 정보가 포함됩니다.

### <a name="create-an-app-service-web-app"></a>앱 서비스 웹앱 만들기
1. 명령줄에서 **tasklist** 디렉터리로 변경합니다.
2. 다음 명령을 사용하여 새 웹앱을 만듭니다.
   
        azure site create --git
   
    웹앱 이름 및 위치를 묻는 메시지가 나타납니다. 고유한 이름을 제공하고 Azure 저장소 계정과 동일한 지리적 위치를 선택합니다.
   
    `--git` 매개 변수는 Azure에 이 웹앱에 대한 Git 리포지토리를 만듭니다. 현재 디렉터리에 존재하는 항목이 없는 경우 Git 리포지토리를 초기화하고 'azure'라는 [Git 원격] 을 추가하여 Azure에 응용프로그램을 게시하는 데 사용합니다. 마지막으로 Azure에서 노드 응용프로그램을 호스트하는 데 사용하는 설정이 포함된 **web.config** 파일을 만듭니다. `--git` 매개 변수를 생략하지만 해당 디렉터리가 Git 리포지토리를 포함하는 경우에도 이 명령은 여전히 'azure' 원격을 만듭니다.
   
    이 명령이 완료되면 다음과 유사한 출력이 표시됩니다. **Website created at** 으로 시작하는 줄에 웹앱의 URL이 있습니다.
   
        info:   Executing command site create
        help:   Need a site name
        Name: TableTasklist
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default .gitignore file
        info:   Creating a new web site
        info:   Created web site at  tabletasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
        info:   site create command OK
   
   > [!NOTE]
   > 구독에 대한 앱 서비스 웹앱을 처음 만드는 경우 Azure 포털을 사용하여 웹앱을 만들라고 안내됩니다. 자세한 내용은 [Azure 앱 서비스에서 Node.js 웹앱 빌드 및 배포]를 참조하세요.
   > 
   > 

### <a name="set-environment-variables"></a>환경 변수 설정
이 단계에서는 Azure에서 웹앱 구성에 환경 변수를 추가합니다.
명령줄에서 다음을 입력합니다.

    azure site appsetting add
        STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


**<storage account name>**을 앞서 만든 저장소 계정 이름으로 바꾸고 **<storage access key>**를 저장소 계정의 기본 선택키로 바꿉니다. (이전에 만든 config.json 파일과 동일한 값을 사용합니다.)

또는 [Azure 포털](https://portal.azure.com/)에서 환경 변수를 설정할 수 있습니다.

1. **찾아보기** > **Web Apps** > 사용자의 웹앱 이름을 클릭하여 웹앱 블레이드를 엽니다.
2. 웹앱 블레이드에서 **모든 설정** > **응용 프로그램 설정**을 클릭합니다.
   
     <!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->
3. **앱 설정** 섹션으로 스크롤하여 키/값 쌍을 추가합니다.
   
     ![앱 설정](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)
4. **저장**을 클릭합니다.

### <a name="publish-the-application"></a>응용 프로그램 게시
앱을 게시하려면 코드 파일을 Git으로 커밋한 다음 azure/master로 푸시합니다.

1. 배포 자격 증명을 설정합니다.
   
        azure site deployment user set <name> <password>
2. 응용프로그램 파일을 추가 및 커밋합니다.
   
        git add .
        git commit -m "adding files"
3. 앱 서비스 웹앱으로 커밋을 푸시합니다.
   
        git push azure master
   
    대상 분기로 **master** 를 사용합니다. 배포가 끝나면 다음 예제와 유사한 문이 표시됩니다.
   
        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
          * [new branch]      master -> master
4. 푸시 작업이 완료되면 `azure create site` 명령에서 이전에 반환한 웹앱 URL로 이동하여 응용프로그램을 확인합니다.

## <a name="next-steps"></a>다음 단계
이 문서의 단계에서는 Table Service를 사용하여 정보를 저장하는 방법에 대해 설명하지만 [MongoDB](https://mlab.com/azure/)도 사용할 수 있습니다. 

## <a name="additional-resources"></a>추가 리소스
[Azure CLI]

## <a name="whats-changed"></a>변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URLs -->

[Azure 앱 서비스에서 Node.js 웹앱 빌드 및 배포]: web-sites-nodejs-develop-deploy-mac.md
[Azure Developer Center]: /develop/nodejs/

[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git 원격]: http://git-scm.com/docs/git-remote

[Azure CLI]:../cli-install-nodejs.md

[azure]: https://github.com/Azure/azure-sdk-for-node
[node-uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[async]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md

<!-- Image References -->

[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png
[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png

