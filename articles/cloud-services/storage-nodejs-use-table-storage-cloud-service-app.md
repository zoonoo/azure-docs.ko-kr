<properties 
	pageTitle="테이블 저장소를 사용하는 웹 앱(Node.js) | Microsoft Azure" 
	description="Azure 저장소 서비스 및 Azure 모듈을 추가해 Express를 사용하여 웹 앱 빌드 자습서를 기반으로 응용 프로그램을 빌드하는 자습서입니다." 
	services="cloud-services, storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="mwasson"/>






# 저장소를 사용하는 Node.js 웹 응용 프로그램

이 자습서에서는 Node.js용 Azure 클라이언트 라이브러리를 사용하여 데이터 관리 서비스로 작업하여 [Express를 사용하는 Node.js웹 응용 프로그램] 자습서에서 만든 응용 프로그램을 확장합니다. Azure에 배포할 수 있는 웹 기반 작업 목록 응용 프로그램을 만들도록 응용 프로그램을 확장합니다. 작업 목록을 통해 사용자는 작업을 가져오고 새 작업을 추가하고 작업을 완료로 표시할 수 있습니다.

작업 항목은 Azure 저장소에 저장됩니다. Azure 저장소는 내결함성과 고가용성이 있는 구조화되지 않은 데이터 저장소를 제공합니다. Azure 저장소에는 데이터를 저장하고 액세스할 수 있는 일부 데이터 구조가 포함되며, Node.js용 Azure SDK에 포함된 API 또는 REST API를 통해 저장소 서비스를 활용할 수 있습니다. 자세한 내용은 [Azure에 데이터 저장 및 액세스]를 참조하세요.

이 자습서는 [Node.js 웹 응용 프로그램] 및 [Express로Node.js 빌드][Express를 사용하는 Node.js 웹 응용 프로그램] 자습서를 완료했다고 가정합니다.

다음 내용을 배웁니다.

-   Jade 템플릿 엔진으로 작업하는 방법
-   Azure 데이터 관리 서비스로 작업하는 방법

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![The completed web page in internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Web.Config에서 저장소 자격 증명 설정

Azure 저장소에 액세스하려면 저장소 자격 증명을 전달해야 합니다. 이렇게 하려면 web.config 응용 프로그램 설정을 활용합니다.
이러한 설정은 환경 변수로서 노드에 전달된 다음 Azure SDK에서 읽습니다.

> [AZURE.NOTE] 저장소 자격 증명은 응용 프로그램이 Azure에 배포될 경우에만 사용됩니다. 에뮬레이터에서 실행 중이면 응용 프로그램은 저장소 에뮬레이터를 사용합니다.

다음 단계에 따라 저장소 계정 자격 증명을 가져와 web.config 설정에 추가합니다.

1.  아직 열려 있지 않으면 **시작** 메뉴에서 **모든 프로그램, Azure**를 확장하고 **Azure PowerShell**을 마우스 오른쪽 단추로 클릭한 다음 **관리자 권한으로 실행**을 선택하여 Azure PowerShell을 시작합니다.

2.  응용 프로그램이 포함된 폴더로 디렉터리를 변경합니다. 예를 들면 C:\\node\\tasklist\\WebRole1과 같습니다.

3.  Azure Powershell 창에서 다음 cmdlet을 입력하여 저장소 계정 정보를 가져옵니다.

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

	이는 호스티드 서비스와 연결된 저장소 계정 및 계정 키 목록을 가져옵니다.

	> [AZURE.NOTE] 서비스를 배포할 때 Azure SDK가 저장소 계정을 만들기 때문에 저장소 계정은 이전 가이드에서 응용 프로그램을 배포해서 이미 있어야 합니다.

4.  응용 프로그램이 Azure에 배포될 때 사용되는 환경 설정을 포함하는 **ServiceDefinition.csdef** 파일을 엽니다.

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  **Environment** 요소 아래에 다음 블록을 삽입하고 {STORAGE ACCOUNT} 및 {STORAGE ACCESS KEY}를 배포에 사용할 저장소 계정의 계정 이름과 기본 키로 바꿉니다.

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

	![The web.cloud.config file contents](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  파일을 저장하고 메모장을 닫습니다.

###추가 모듈 설치

2. 다음 명령을 사용하여 [azure], [node-uuid], [nconf] 및 [async] 모듈을 로컬로 설치하고 모듈에 대한 항목을 **package.json** 파일에 저장합니다.

	    PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

	이 명령의 출력은 다음과 유사하게 표시됩니다.

		node-uuid@1.4.1 node_modules\node-uuid

		nconf@0.6.9 node_modules\nconf
		├── ini@1.1.0
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##node 응용 프로그램에서 테이블 서비스 사용

이 섹션에서는 작업에 대한 모델을 포함하는 **task.js** 파일을 추가하여 **express** 명령으로 만든 기본 응용 프로그램을 확장합니다. 또한 기존 **app.js**를 수정하고 모델을 사용하는 새 **tasklist.js** 파일을 만듭니다.

### 모델 만들기

1. **WebRole1** 디렉터리에서 **models**라는 새 디렉터리를 만듭니다.

2. **models** 디렉터리에서 **task.js**라는 새 파일을 만듭니다. 이 파일에는 응용 프로그램에서 만든 작업 모델이 포함됩니다.

3. **task.js** 파일의 시작 부분에 필수 라이브러리를 참조하는 다음 코드를 추가합니다.

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. 그런 다음, Task 개체를 정의하고 내보내는 코드를 추가합니다. 이 개체가 테이블에 연결하는 작업을 수행합니다.

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

5. 이제 다음 코드를 추가하여 Task 개체에서 추가 메서드를 정의합니다. 이 메서드가 테이블에 저장된 데이터에 대한 조작을 허용합니다.

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
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

###컨트롤러 만들기

1. **WebRole1/routes** 디렉터리에서 **tasklist.js**라는 새 파일을 만들고 텍스트 편집기에서 엽니다.

2. 다음 코드를 **tasklist.js**에 추가합니다. 그러면 **tasklist.js**에서 사용되는 azure 및 async 모듈이 로드됩니다. 또한 **TaskList** 함수를 정의합니다. 이 함수에는 앞에서 정의한 **Task** 개체의 인스턴스가 전달됩니다.

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

		function TaskList(task) {
		  this.task = task;
		}

2. 사용된 메서드를 **showTasks**, **addTask** 및 **completeTasks**에 추가하여 **tasklist.js** 파일에 계속 추가합니다.

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = azure.TableQuery()
		      .where('completed eq ?', false);
		    self.task.find(query, function itemsFound(error, items) {
		      res.render('index',{title: 'My ToDo List ', tasks: items});
		    });
		  },

		  addTask: function(req,res) {
		    var self = this      
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

3. **tasklist.js** 파일을 저장합니다.

### app.js 수정

1. **WebRole1** 디렉터리에 있는 **app.js** 파일을 텍스트 편집기에서 엽니다. 

2. 파일 시작 부분에 다음을 추가하여 azure 모듈을 로드하고 테이블 이름과 파티션 키를 설정합니다.

		var azure = require('azure-storage');
		var tableName = 'tasks';
		var partitionKey = 'hometasks';

3. app.js 파일에서 다음 줄이 보일 때까지 아래로 스크롤합니다.

		app.use('/', routes);
		app.use('/users', users);

	위의 줄을 아래의 코드로 바꿉니다. 이 코드는 저장소 계정에 대한 연결을 사용하여 <strong>Task</strong>의 인스턴스를 초기화합니다. 이 인스턴스는 <strong>TaskList</strong>로 전달되어 테이블 서비스와의 통신에 사용됩니다.

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));
	
4. **app.js** 파일을 저장합니다.

###인덱스 보기 수정

1. 디렉터리를 **views** 디렉터리로 변경하고 **index.jade** 파일을 텍스트 편집기에서 엽니다.

2. **index.jade** 파일의 내용을 아래 코드로 바꿉니다. 이렇게 하면 기존 작업을 표시하는 데 사용되는 보기와 새 작업을 추가하고 기존 작업을 완료로 표시하는 데 사용되는 양식이 정의됩니다.

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
		      if tasks != []
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

3. **index.js** 파일을 저장하고 닫습니다.

###전역 레이아웃 수정

**views** 디렉터리의 **layout.jade** 파일은 다른 **.jade** 파일의 전역 템플릿으로 사용됩니다. 이 단계에서는 멋진 모습의 웹 사이트를 쉽게 디자인할 수 있게 해주는 도구 키트인 [Twitter Bootstrap](https://github.com/twbs/bootstrap)을 사용하도록 이 파일을 수정합니다.

1. [Twitter Bootstrap](http://getbootstrap.com/)용 파일을 다운로드하여 추출합니다. **bootstrap.min.css** 파일을 **bootstrap\\dist\\css** 폴더에서 tasklist 응용 프로그램의 **public\\stylesheets** 디렉터리에 복사합니다.

2. **views** 폴더에 있는 **layout.jade**를 텍스트 편집기에서 열고 내용을 다음으로 바꿉니다.

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

3. **layout.jade** 파일을 저장합니다.

### 에뮬레이터에서 응용 프로그램 실행

에뮬레이터에서 응용 프로그램을 시작하려면 다음 명령을 사용합니다.

	PS C:\node\tasklist\WebRole1> start-azureemulator -launch

브라우저가 열리며 다음 페이지를 표시합니다.

![A web paged titled My Task List with a table containing tasks and fields to add a new task.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

양식을 사용하여 항목을 추가하거나 기존 항목을 완료됨으로 표시하여 제거합니다.

## Azure에 응용 프로그램 게시


Windows PowerShell 창에서 다음 cmdlet을 호출하여 호스티드 서비스를 Azure에 다시 배포합니다.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

**myuniquename**을 이 응용 프로그램의 고유한 이름으로 바꿉니다. **datacentername**을 **West US**와 같은 Azure 데이터 센터 이름으로 바꿉니다.

배포가 완료된 후 다음과 유사한 응답이 표시됩니다.

	PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
	WARNING: Publishing tasklist to Windows Azure. This may take several minutes...
	WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
	WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
	WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
	WARNING: 2:19:01 PM - Connecting...
	WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
	WARNING: 2:19:40 PM - Upgrading...
	WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
	WARNING: 2:22:48 PM - Initializing...
	WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
	WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

이전과 같이 **-launch** 옵션을 지정했으므로 게시가 완료되면 브라우저가 열리고 Azure에서 실행 중인 응용 프로그램이 표시됩니다.

![A browser window displaying the My Task List page. The URL indicates the page is now being hosted on Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## 응용 프로그램 중지 및 삭제

추가 비용을 방지하거나 다른 응용 프로그램을 빌드 및 배포할 수 있도록 이전에 배포한 응용 프로그램을 무료 평가판 기간 동안 사용하지 않도록 설정할 수 있습니다.

Azure는 사용된 서버 시간의 시간당 웹 역할 인스턴스 요금을 청구합니다.
서버 시간은 응용 프로그램이 배포된 다음에 사용되며 인스턴스가 실행되지 않고 중지된 상태인 경우에도 사용됩니다.

다음 단계에 따라 응용 프로그램을 중지 및 제거할 수 있습니다.

1.  Windows PowerShell 창에서, 이전 섹션에서 만든 서비스 배포를 다음 cmdlet을 사용하여 중지합니다.

        PS C:\node\tasklist\WebRole1> Stop-AzureService

	서비스를 중지하려면 몇 분 정도 걸릴 수 있습니다. 서비스가 중지되면 서비스가 중지되었다는 메시지가 표시됩니다.

2.  서비스를 삭제하려면 다음 cmdlet을 호출합니다.

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

	메시지가 표시되면 **Y**를 입력하여 서비스를 삭제합니다.

	서비스를 삭제하려면 몇 분 정도 걸릴 수 있습니다. 서비스가 삭제되면 서비스가 삭제되었다는 메시지가 표시됩니다.

  [Express를 사용하는 Node.js 웹 응용 프로그램]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Node.js Web Application]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 

<!--HONumber=42-->
 