<properties urlDisplayName="Website with Storage" pageTitle="테이블 저장소를 사용하는 Node.js 웹 사이트 | Microsoft Azure" metaKeywords="Azure table storage Node.js, Azure Node.js application, Azure Node.js tutorial, Azure Node.js example" description="A tutorial that teaches you how to use the Azure Table service to store data from a Node application hosted on an Azure website." metaCanonical="" services="web-sites,storage" documentationCenter="nodejs" title="Node.js Web Application using the Azure Table Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />





# Azure 테이블 서비스를 사용하는 Node.js 웹 응용 프로그램

이 자습서에서는 Azure 데이터 관리에서 제공하는 테이블 서비스를 사용하여 Azure에서 호스트되는 [node] 응용 프로그램에서 데이터를 저장하고 액세스하는 방법을 보여 줍니다. 이 자습서에서는 이전에 node 및 [Git]를 사용한 경험이 있다고 가정합니다.

다음 내용을 배웁니다.

* npm(node 패키지 관리자)을 사용하여 node 모듈을 설치하는 방법

* Azure 테이블 서비스에 대한 작업 방법

* Mac 및 Linux용 Azure 명령줄 도구를 사용하여 Azure 웹 사이트를 만드는 방법

이 자습서를 따라 작업을 만들고, 검색하고, 완료할 수 있는 간단한 웹 기반 작업 관리 응용 프로그램을 구축합니다. 작업은 테이블 서비스에 저장됩니다.
 
이 자습서의 프로젝트 파일은 **tasklist**라는 디렉터리에 저장되며 완료된 응용 프로그램은 다음과 유사합니다.

![A web page displaying an empty tasklist][node-table-finished]

> [WACOM.NOTE] 이 자습서에서는 **tasklist** 폴더에 대한 참조를 만듭니다. 운영 체제마다 경로 의미 체계가 다르므로, 폴더의 전체 경로는 생략되었습니다. 로컬 파일 시스템에 쉽게 액세스할 수 있는 위치에 이 폴더를 만들어야 합니다(예: **~/node/tasklist** 또는 **c:\node\tasklist**).

> [WACOM.NOTE] 아래의 많은 단계에서는 명령줄 사용을 언급합니다. 이러한 단계에서는 **cmd.exe**(Windows) 또는 **Bash**(Unix Shell)와 같은 운영 체제의 명령줄을 사용하세요. OS X 시스템에서는 터미널 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

##필수 조건

이 문서의 지침을 따르기 전에 다음이 설치되어 있는지 확인해야 합니다.

* [node] 버전 0.10.24 이상

* [Git]

* 텍스트 편집기

* 웹 브라우저

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##저장소 계정 만들기

저장소 계정을 만들려면 다음 단계를 수행합니다. 이 계정은 이 자습서 뒷부분의 지침에서 사용합니다.

1. 웹 브라우저를 열고 [Azure 포털]로 이동합니다. 메시지가 나타나면 Azure 구독 정보로 로그인합니다.

2. 포털 맨 아래에서 **+ 새로 만들기**를 클릭한 다음 **저장소 계정**을 선택합니다.

	![+new][portal-new]

	![storage account][portal-storage-account]

3. **빠른 생성**을 선택한 다음 이 저장소 계정의 URL 및 위치/선호도 그룹을 입력합니다. 이 자습에서는 전역으로 복제할 필요가 없으므로 **지역에서 복제 사용**의 선택을 취소합니다. 마지막으로 "저장소 계정 만들기"를 클릭합니다.

	![quick create][portal-quick-create-storage]

	입력한 URL은 뒷부분의 단계에서 계정 이름으로 참조되므로 기록해 둡니다.

4. 저장소 계정을 만든 후 페이지 맨 아래에서 **키 관리**를 클릭합니다. 그러면 이 저장소 계정의 기본 및 보조 액세스 키가 표시됩니다. 기본 액세스 키를 복사하여 저장한 후 확인 표시를 클릭합니다.

	![access keys][portal-storage-access-keys]

##모듈 설치 및 스캐폴딩 생성

이 섹션에서는 새로운 Node 응용 프로그램을 만들고 npm을 사용하여 모듈 패키지를 추가합니다. 작업 목록 응용 프로그램의 경우 [Express] 및 [Azure] 모듈을 사용합니다. Express 모듈은 node에 모델 보기 컨트롤러 프레임워크를 제공하지만 Azure 모듈은 테이블 서비스에 대한 연결을 제공합니다.

###express 설치 및 스캐폴딩 생성

1. 명령줄에서 디렉터리를 **tasklist** 디렉터리로 변경합니다. **tasklist** 디렉터리가 없으면 만듭니다.

2. 다음을 입력하여 express 명령을 설치합니다.

		npm install express-generator@4.2.0 -g

    > [WACOM.NOTE] 일부 운영 체제에서 '-g' 매개 변수를 사용하면 **Error: EPERM, chmod '/usr/local/bin/express'** 오류와 관리자 권한으로 계정을 실행하라는 요청이 표시될 수 있습니다. 이런 경우 **sudo** 명령을 사용하여 더 높은 권한 수준으로 npm을 실행하세요.

    이 명령의 출력은 다음과 유사하게 표시됩니다.

		express-generator@4.2.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [WACOM.NOTE] express 모듈을 설치할 때 사용되는 '-g' 매개 변수는 이 모듈을 전역으로 설치합니다. **express** 명령에 액세스하여 추가 경로 정보에 입력하지 않고도 웹 사이트 스캐폴딩을 생성할 수 있도록 이 작업을 수행합니다.**

4. 이 응용 프로그램에 사용할 스캐폴딩을 만들려면 다음과 같이 **express** 명령을 사용합니다.

        express

	이 명령의 출력은 다음과 유사하게 표시됩니다.

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

	이 명령이 완료되면 **tasklist** 디렉터리에 몇 개의 새로운 디렉터리 및 파일이 생성되어야 합니다.

###추가 모듈 설치

**package.json** 파일은 **express** 명령으로 만든 파일 중 하나입니다. 이 파일에는 Express 응용 프로그램에 필요한 추가 모듈 목록이 포함되어 있습니다. 나중에 이 응용 프로그램을 Azure 웹 사이트에 배포하는 경우 이 파일을 사용하여 응용 프로그램을 지원하기 위해 Azure에 설치해야 할 모듈을 결정합니다.

1. 명령줄에서 디렉터리를 **tasklist** 폴더로 변경하고 다음을 입력하여 **package.json** 파일에 설명된 모듈을 설치합니다.

        npm install

    이 명령의 출력은 다음과 유사하게 표시됩니다.

		debug@0.7.4 node_modules\debug
		
		static-favicon@1.0.2 node_modules\static-favicon
		
		morgan@1.0.1 node_modules\morgan
		└── bytes@0.3.0
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.7 (string_decoder@0.10.25-1, bytes@1.0.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.2.0 node_modules\express
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── cookie@0.1.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── debug@0.8.1
		├── fresh@0.2.2
		├── qs@0.6.6
		├── range-parser@1.0.0
		├── methods@1.0.0
		├── buffer-crc32@0.2.1
		├── serve-static@1.1.0
		├── path-to-regexp@0.1.2
		├── send@0.3.0 (debug@0.8.0, mime@1.2.11)
		├── type-is@1.1.0 (mime@1.2.11)
		└── accepts@1.0.1 (negotiator@0.4.7, mime@1.2.11)
		
		jade@1.3.1 node_modules\jade
		├── commander@2.1.0
		├── character-parser@1.2.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── with@3.0.0 (uglify-js@2.4.15)


	이 명령은 Express에 필요한 모든 기본 모듈을 설치합니다.

2. 다음 명령을 입력하여 [azure], [node-uuid], [nconf] 및 [async] 모듈을 로컬로 설치하고 모듈에 대한 항목을 **package.json** 파일에 저장합니다.

		npm install azure-storage node-uuid async nconf --save

	이 명령의 출력은 다음과 유사하게 표시됩니다.

		async@0.9.0 node_modules\async

		node-uuid@1.4.1 node_modules\node-uuid
		
		nconf@0.6.9 node_modules\nconf
		├── ini@1.2.1
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)
		
		azure-storage@0.3.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── validator@3.1.0
		├── underscore@1.4.4
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (forever-agent@0.5.2, aws-sign@0.3.0, json-stringify-safe@5.0.0, tunnel-agent@0.3.0, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, form-data@0.1.4, hawk@1.0.0, http-signature@0.10.0)

##node 응용 프로그램에서 테이블 서비스 사용

이 섹션에서는 작업에 대한 모델을 포함하는 **task.js** 파일을 추가하여 **express** 명령으로 만든 기본 응용 프로그램을 확장합니다. 또한 기존 **app.js**를 수정하고 모델을 사용하는 새 **tasklist.js** 파일을 만듭니다.

### 모델 만들기

1. **tasklist** 디렉터리에서 **models**라는 새 디렉터리를 만듭니다.

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

###컨트롤러 만들기

1. **tasklist/routes** 디렉터리에서 **tasklist.js**라는 새 파일을 만들고 텍스트 편집기에서 엽니다.

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
		    var query = new azure.TableQuery()
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

1. **tasklist** 디렉터리에 있는 **app.js** 파일을 텍스트 편집기에서 엽니다. 이 파일은 앞에서 **express** 명령을 실행하여 만들었습니다.

2. 파일의 앞부분에 다음을 추가하여 azure 모듈을 로드하고 테이블 이름인 partitionKey를 설정하고 이 예제에서 사용한 저장소 자격 증명을 설정합니다.

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json'});
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [WACOM.NOTE] nconf는 환경 변수 또는 나중에 만들 **config.json** 파일에서 구성 값을 로드합니다.

3. app.js 파일에서 다음 줄이 보일 때까지 아래로 스크롤합니다.

		app.use('/', routes);
		app.use('/users', users);

	위의 줄을 아래의 코드로 바꿉니다. 그러면 <strong>작업</strong> 의 인스턴스를 저장소 계정에 대한 연결로 초기화합니다. 또한 테이블 서비스와 통신하는 데 사용되는 <strong>TaskList</strong>에 전달됩니다.

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
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

###구성 파일 만들기

**config.json** 파일에는 SQL 데이터베이스에 연결하는 데 사용되는 연결 문자열이 포함되어 있으며, 런타임에 응용 프로그램이 이 파일을 읽습니다. 이 파일을 만들려면 다음 단계를 수행하세요.

1. **tasklist** 디렉터리에서 **config.json**이라는 새 파일을 만들어 텍스트 편집기에서 엽니다.

2. **config.json** 파일의 내용은 다음과 유사하게 표시됩니다.

		{
			"STORAGE_NAME": "storage account name",
			"STORAGE_KEY": "storage access key",
			"PARTITION_KEY": "mytasks",
			"TABLE_NAME": "tasks"
		}

	**storage account name**을 앞에서 만든 저장소 계정의 이름으로 바꿉니다. **storage access key**를 저장소 계정의 기본 액세스 키로 바꿉니다.

3. 파일을 저장합니다.

##로컬에서 응용 프로그램 실행

로컬 컴퓨터에서 응용 프로그램을 테스트하려면 다음 단계를 수행하세요.

1. 명령줄에서 디렉터리를 **tasklist** 디렉터리로 변경합니다.

2. 다음 명령을 사용하여 응용 프로그램을 로컬에서 시작합니다.

        npm start

3. 웹 브라우저를 열고 http://127.0.0.1:3000으로 이동합니다. 그러면 다음과 비슷한 웹 페이지가 표시됩니다.

    ![A webpage displaying an empty tasklist][node-table-finished]

4. **항목 이름** 및 **항목 범주**에 제공된 필드를 사용하여 정보를 입력한 다음 **항목 추가**를 클릭합니다.

5. 페이지가 업데이트되어 ToDo List 테이블의 항목을 표시합니다.

    ![An image of the new item in the list of tasks][node-table-list-items]

6. 작업을 완료하려면 완료 열의 확인란을 선택한 후 **작업 업데이트**를 클릭하면 됩니다.

7. node 프로세스를 중지하려면 명령줄로 이동한 후 **Ctrl** 키와 **C** 키를 누릅니다.

##Azure에 응용 프로그램 배포

이 섹션의 단계에서는 Azure 명령줄 도구를 사용하여 새 Azure 웹 사이트를 만든 후 Git를 사용하여 응용 프로그램을 배포합니다. 이러한 단계를 수행하려면 Azure 구독이 있어야 합니다.

> [WACOM.NOTE] 이러한 단계는 Azure 포털을 사용하여 수행할 수도 있습니다. Azure 포털을 사용하여 Node.js 응용 프로그램을 배포하는 단계에 대해서는 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포]를 참조하세요.

> [WACOM.NOTE] 처음으로 Azure 웹 사이트를 만든 경우 Azure 포털을 사용하여 이 응용 프로그램을 배포해야 합니다.

###Azure 구독 만들기

아직 Azure 구독이 없는 경우 [무료 평가판]에 등록할 수 있습니다. 등록한 후 다음 단계에 따라 이 자습서를 계속합니다.

[WACOM.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

###Mac 및 Linux용 Azure 명령줄 도구 설치

명령줄 도구를 설치하려면 다음 명령을 사용합니다.
	
	npm install azure-cli -g

> [WACOM.NOTE] 자세한 내용은 [Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성](/ko-kr/documentation/articles/xplat-cli/)을 참조하세요.

> [WACOM.NOTE] 명령줄 도구는 주로 Mac 및 Linux 사용자를 위해 만들어졌으나 Node.js를 기반으로 하며 Node를 실행할 수 있는 모든 시스템에서 작동해야 합니다.

###게시 설정 가져오기

Azure에 명령줄 도구를 사용하려면 먼저 구독에 대한 정보가 포함된 파일을 다운로드해야 합니다. 다음 단계에 따라 이 파일을 다운로드하여 가져옵니다.

1. 명령줄에서 디렉터리를 **tasklist** 디렉터리로 변경합니다.

2. 다음 명령을 입력하여 브라우저를 시작하고 다운로드 페이지로 이동합니다. 메시지가 나타나면 구독과 관련된 계정으로 로그인합니다.

		azure account download
	
	![The download page][download-publishing-settings]
	
	파일 다운로드가 자동으로 시작됩니다. 그렇지 않은 경우 페이지 처음 부분에서 링크를 클릭하여 수동으로 파일을 다운로드할 수 있습니다.

3. 파일 다운로드가 완료되면 다음 명령을 사용하여 설정을 가져옵니다.

		azure account import <path-to-file>
		
	이전 단계에서 다운로드한 게시 설정 파일의 경로와 파일 이름을 지정합니다. 명령이 완료되면 다음과 유사한 출력이 표시됩니다.
	
		info:   Executing command account import
		info:   Setting service endpoint to: management.core.windows.net
		info:   Setting service port to: 443
		info:   Found subscription: YourSubscription
		info:   Setting default subscription to: YourSubscription
		warn:   The 'C:\users\username\downloads\YourSubscription-6-7-2012-credentials.publishsettings' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK

4. 게시 설정 파일은 가져오기가 완료되면 더 이상 필요하지 않고 Azure 구독에 대한 중요한 정보를 포함하고 있으므로 가져오기가 완료되면 게시 설정 파일을 삭제해야 합니다.

###Azure 웹 사이트 만들기

1. 명령줄에서 디렉터리를 **tasklist** 디렉터리로 변경합니다.

2. 다음 명령을 사용하여 새 Azure 웹 사이트를 만듭니다.

		azure site create --git
		
	웹 사이트 이름 및 데이터 센터의 위치에 대한 메시지가 표시됩니다. 고유한 이름을 지정하고 현재 위치와 지리적으로 가까운 데이터 센터를 선택합니다.
	
	`--git` 매개 변수는 Azure에 이 웹 사이트에 대한 Git 리포지토리를 만듭니다. 또한 Git 리포지토리가 없는 경우 현재 디렉터리에서 Git 리포지토리를 초기화합니다. 또한 Azure에 응용 프로그램을 게시하는 데 사용되는 'azure'라는[Git remote](영문)를 만듭니다. 마지막으로 Azure에서 노드 응용 프로그램을 호스트하는 데 사용하는 설정이 포함된**web.config** 파일을 만듭니다.
	
	> [WACOM.NOTE] 이미 Git 리포지토리를 포함한 디렉터리에서 이 명령을 실행한 경우 디렉터리가 다시 초기화되지는 않습니다.
	
	> [WACOM.NOTE] `--git` 매개 변수가 생략되었지만 디렉터리에 Git 리포지토리가 포함된 경우 'azure' 원격이 계속 생성됩니다.
	
	이 명령이 완료되면 다음과 유사한 출력이 표시됩니다. **Website created at**으로 시작하는 줄에 웹 사이트에 대한 URL이 있습니다.
	
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

	> [WACOM.NOTE] 구독에서 Azure 웹 사이트를 처음 만드는 경우 포털을 사용하여 웹 사이트를 만들라는 메시지를 받게 됩니다. 자세한 내용은 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포]를 참조하세요.

###응용 프로그램 게시

1. 디렉터리를 변경하지 않은 경우 터미널 창에서 **tasklist** 디렉터리로 변경합니다.

2. 다음 명령을 사용하여 로컬 Git 리포지토리로 파일을 커밋합니다.

		git add .
		git commit -m "adding files"

3. 최신 Git 리포지토리 변경 내용을 Azure 웹 사이트로 푸시하는 경우 **master**가 웹 사이트 콘텐츠에 사용되므로 대상 분기가 되도록 지정해야 합니다.

		git push azure master
	
	배포가 끝나면 다음과 같은 문이 표시됩니다.
	
		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. 푸시 작업이 완료되면 `azure create site` 명령에서 이전에 반환한 웹 사이트 URL로 이동하여 응용 프로그램을 확인합니다.

###환경 변수로 전환

이전에 **config.json** 파일에서 환경 변수를 찾거나 값을 로드하는 코드를 구현했습니다. 다음 단계에서는 응용 프로그램이 환경 변수를 통해 실제로 액세스하는 웹 사이트 구성에 키/값 쌍을 만듭니다.

1. 관리 포털에서 **웹 사이트**를 클릭한 다음 웹 사이트를 선택합니다.

	![Open website dashboard][go-to-dashboard]

2. **구성**을 클릭한 후 페이지의 **앱 설정** 섹션을 찾습니다. 

	![configure link][web-configure]

3. **앱 설정** 섹션에서 **키** 필드에 **STORAGE_NAME**을 입력하고 **값** 필드에 저장소 계정의 이름을 입력합니다. 확인 표시를 클릭하여 다음 필드로 이동합니다. 다음 키와 값에 대해 이 과정을 반복합니다.

	* **STORAGE_KEY** - 저장소 계정의 액세스 키
	
	* **PARTITION_KEY** - 'mytasks'

	* **TABLE_NAME** - 'tasks'

	![app settings][app-settings]

4. 마지막으로 페이지 맨 아래에 있는 **저장** 아이콘을 클릭하여 이 변경 내용을 런타임 환경에 커밋합니다.

	![app settings save][app-settings-save]

5. 명령줄에서 디렉터리를 **tasklist** 디렉터리로 변경하고 다음 명령을 입력하여 **config.json** 파일을 제거합니다.

		git rm config.json
		git commit -m "Removing config file"

6. 다음 명령을 수행하여 변경 내용을 Azure에 배포합니다.

		git push azure master

변경 내용이 Azure에 배포되고 나면 웹 응용 프로그램이 이제 **앱 설정** 항목에서 연결 문자열을 읽으므로 계속 작동해야 합니다. 이를 확인하려면 **앱 설정**의 **STORAGE_KEY** 항목에 대한 값을 잘못된 값으로 변경합니다. 이 값을 저장하고 나면 잘못된 저장소 액세스 키 설정으로 인해 웹 사이트가 실패해야 합니다.

##다음 단계

이 문서의 단계에서는 테이블 서비스를 사용하여 정보를 저장하는 방법에 대해 설명하지만 MongoDB도 사용할 수 있습니다. 자세한 내용은 [MongoDB가 있는 Node.js 웹 응용 프로그램](영문)을 참조하세요.

##추가 리소스

[Mac 및 Linux용 Azure 명령줄 도구]    
[Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포]: /ko-kr/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[Git를 사용하여 Azure 웹 사이트에 게시]: /ko-kr/documentation/articles/web-sites-publish-source-control/
[Azure 개발자 센터]: /ko-kr/develop/nodejs/


[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[무료 평가판]: http://windowsazure.com
[Git remote]: http://git-scm.com/docs/git-remote

[MongoDB를 사용한 Node.js 웹 응용 프로그램]: /ko-kr/documentation/articles/web-sites-nodejs-store-data-mongodb/
[Mac 및 Linux용 Azure 명령줄 도구]: /ko-kr/documentation/articles/xplat-cli/

[Git를 사용하여 Azure 웹 사이트에 게시]: /ko-kr/documentation/articles/web-sites-publish-source-control/
[azure]: https://github.com/Azure/azure-sdk-for-node


[Azure 포털]: http://windowsazure.com


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

[Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포]: /ko-kr/documentation/articles/web-sites-nodejs-develop-deploy-mac/

<!--HONumber=35.1-->
