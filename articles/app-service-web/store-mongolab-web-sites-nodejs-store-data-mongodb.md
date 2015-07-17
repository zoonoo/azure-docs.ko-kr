<properties
	pageTitle="Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 Node.js 웹 앱 만들기"
	description="MongoLab에서 호스트되는 MongoDB 인스턴스에 연결되는 Azure 앱 서비스에 Node.js Azure 웹 앱을 만드는 방법을 알아봅니다."
	tags="azure-classic-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="chrisckchang"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/20/2014"
	ms.author="mwasson"/>






# Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 Node.js 웹 앱 만들기


<p><em>작성자: Eric Sedor, MongoLab</em></p>

모험심이 많은 사용자 여러분, 반갑습니다! MongoDB-as-a-Service에 오신 것을 환영합니다. 이 자습서에서는 다음 작업을 수행합니다.

1. [데이터베이스 프로비전][provision] - Azure 마켓플레이스 [MongoLab](http://mongolab.com) 추가 기능은 Azure 클라우드에 호스트되고 MongoLab 클라우드 데이터베이스 플랫폼에서 관리되는 MongoDB 데이터베이스를 제공합니다.
2. [앱 만들기][create] - 작업 목록을 유지 관리하기 위한 간단한 INode.js 앱입니다.
3. [앱 배포][deploy] - 몇 가지 구성 후크를 연결하여 [Azure 앱 서비스 웹 앱](http://go.microsoft.com/fwlink/?LinkId=529714)에 원활하게 코드를 푸시합니다.
4. [데이터베이스 관리][manage] - 마지막으로 쉽게 데이터를 검색하고, 시각화하고, 수정할 수 있는 MongoLab의 웹 기반 데이터베이스 관리 포털을 보여 줍니다.

언제든 이 자습서를 보면서 질문할 사항이 있으면 [support@mongolab.com](mailto:support@mongolab.com)으로 전자 메일을 보내주십시오.

계속하기 전에 다음 제품이 설치되어 있는지 확인하십시오.

* [Node.js] 버전 0.10.29+

* [Git]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## 빠른 시작
Azure Store에 어느 정도 익숙한 경우 이 섹션을 사용하여 빠른 시작을 수행합니다. 그렇지 않은 경우 아래의 [데이터베이스 프로비전][provision]을 계속 진행합니다.

1. **새로 만들기** > **Marketplace**를 클릭하여 Azure 마켓플레이스를 엽니다.  
<!--- ![Store][button-store] -->
2. **MongoLab** 추가 기능을 클릭합니다.  
![MongoLab][entry-mongolab]
3. 추가 기능 목록에서 **MongoLab** 추가 기능을 클릭하고 **연결 정보**를 클릭합니다.  
![ConnectionInfoButton][button-connectioninfo]  
4. **MONGOLAB_URI**를 클립보드로 복사합니다.  
![ConnectionInfoScreen][screen-connectioninfo]

	>[AZURE.NOTE]이 URI에는 데이터베이스 사용자 이름과 암호가 포함되어 있습니다. 중요한 정보로 처리하고 공유하지 마세요.

5. Azure 웹 응용 프로그램 **구성** 메뉴의 **연결 문자열** 목록에 값을 추가합니다. ![WebAppConnectionStrings][focus-website-connectinfo]
6. **이름**에 **MONGOLAB_URI**를 입력합니다.
7. **Value**에 이전 섹션에서 얻은 연결 문자열을 붙여 넣습니다.
8. 유형 드롭다운에서 기본값 **SQLAzure** 대신 **사용자 지정**을 선택합니다.
9. `npm install mongoose`을(를) 실행하여 MongoDB 노드 드라이버인 Mongoose를 가져옵니다.
10. 코드에서 후크를 설정하여 환경 변수의 MongoLab 연결 URI를 가져와 연결합니다.

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Azure는 **CUSTOMCONNSTR_** 접두사를 원래 선언된 연결 문자열에 추가합니다. 이러한 이유로 코드에서 **MONGOLAB_URI** 대신 **CUSTOMCONNSTR_MONGOLAB_URI.**를 참조합니다.

이제, 전체 자습서에서...

<a name="provision"></a>
## 데이터베이스 프로비전

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## 앱 만들기

이 섹션에서는 개발 환경을 설정하고 Node.js, Express 및 MongoDB를 사용하여 기본 작업 목록 웹 응용 프로그램에 대한 코드를 배포합니다. [Express]는 노드용 보기 컨트롤러 프레임워크를 제공하고 [Mongoose]는 노드에서 MongoDB와 통신하기 위한 드라이버입니다.

### 설정

#### 스캐폴딩 생성 및 모듈 설치

1. 명령줄에서 **tasklist** 디렉터리를 만들어 이동합니다. 이 디렉터리가 프로젝트 디렉터리입니다.
2. 다음 명령을 입력하여 express를 설치합니다.

		npm install express -g

	`-g`은(는) 전역 모드를 나타내며 디렉터리 경로를 지정하지 않고 <strong>express</strong> 모듈을 사용할 수 있도록 하는 데 이 모드를 사용합니다. <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong>를 수신하면 <strong>sudo</strong>를 사용하여 더 높은 권한 수준에서 npm을 실행합니다.

    이 명령의 출력은 다음과 유사합니다.

		express@4.9.1 C:\Users\mongolab\AppData\Roaming\npm\node_modules\express
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── fresh@0.2.4
		├── cookie@0.1.2
		├── range-parser@1.0.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── parseurl@1.3.0
		├── serve-static@1.6.2
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── debug@2.0.0 (ms@0.6.2)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── etag@1.3.1 (crc@3.0.0)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)
		└── type-is@1.5.1 (mime-types@2.0.1)

3. 이 응용 프로그램에 사용할 스캐폴딩을 만들려면 다음과 같은 **express** 명령을 사용합니다.

    	express

    이 자습서에서는 Express v4.x.x를 사용합니다. Express 3 앱 생성기가 시스템에 설치되어 있는 경우 먼저 제거해야 합니다.

    	npm uninstall -g express

    이제 버전 4.x.x용 새 생성기를 설치합니다.

    	npm install -g express-generator

	**express** 명령이 실행된 후 다음과 유사한 출력이 나타납니다.

		create : .
		create : ./package.json
		create : ./app.js
		create : ./public
		create : ./public/javascripts
		create : ./public/images
		create : ./public/stylesheets
		create : ./public/stylesheets/style.css
		create : ./routes
		create : ./routes/index.js
		create : ./routes/users.js
		create : ./views
		create : ./views/index.jade
		create : ./views/layout.jade
		create : ./views/error.jade
		create : ./bin
		create : ./bin/www

		install dependencies:
		$ cd . && npm install


	이 명령이 완료되면 **tasklist** 디렉터리에 새 디렉터리와 파일 몇 개가 생성됩니다.

4. 다음을 입력하여 **package.json** 파일에 설명된 모듈을 설치합니다.

        npm install

    이 명령의 출력은 다음과 유사하게 표시됩니다.

		cookie-parser@1.3.3 node_modules/cookie-parser
		├── cookie@0.1.2
		└── cookie-signature@1.0.5

		debug@2.0.0 node_modules/debug
		└── ms@0.6.2

		serve-favicon@2.1.4 node_modules/serve-favicon
		├── ms@0.6.2
		├── fresh@0.2.4
		└── etag@1.3.1 (crc@3.0.0)

		morgan@1.3.1 node_modules/morgan
		├── basic-auth@1.0.0
		├── depd@0.4.5
		└── on-finished@2.1.0 (ee-first@1.0.5)

		express@4.9.1 node_modules/express
		├── utils-merge@1.0.0
		├── merge-descriptors@0.0.2
		├── cookie@0.1.2
		├── fresh@0.2.4
		├── escape-html@1.0.1
		├── range-parser@1.0.2
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── serve-static@1.6.2
		├── parseurl@1.3.0
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── etag@1.3.1 (crc@3.0.0)
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── type-is@1.5.1 (mime-types@2.0.1)
		└── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)

		body-parser@1.8.2 node_modules/body-parser
		├── media-typer@0.3.0
		├── raw-body@1.3.0
		├── bytes@1.0.0
		├── depd@0.4.5
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── qs@2.2.3
		├── iconv-lite@0.4.4
		└── type-is@1.5.1 (mime-types@2.0.1)

		jade@1.6.0 node_modules/jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── void-elements@1.0.0
		├── mkdirp@0.5.0 (minimist@0.0.8)
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		└── with@3.0.1 (uglify-js@2.4.15)

	**package.json** 파일은 **express** 명령으로 만들어진 파일 중 하나입니다. 이 파일에는 Express 응용 프로그램에 필요한 추가 모듈 목록이 포함되어 있습니다. 나중에 이 응용 프로그램을 Azure 앱 서비스 웹 앱에 배포하는 경우 이 파일을 사용하여 응용 프로그램을 지원하기 위해 Azure에 설치해야 할 모듈을 결정합니다.

5. 이제 다음 명령을 입력하여 Mongoose 모듈을 로컬에 설치하고 이에 대한 항목을 **package.json** 파일에 저장합니다.

		npm install mongoose --save

	이 명령의 출력은 다음과 유사합니다.

		mongoose@3.8.16 node_modules/mongoose
		├── regexp-clone@0.0.1
		├── muri@0.3.1
		├── sliced@0.0.5
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.8.0 (debug@0.7.4)
		└── mongodb@1.4.9 (readable-stream@1.0.31, kerberos@0.0.3, bson@0.2.12)

### 코드

환경 및 스캐폴딩이 준비되었으므로 작업 모델이 포함된 **task.js** 파일을 추가하여 **express** 명령으로 만들어진 기본 응용 프로그램을 확장하겠습니다. 또한 해당 모델을 활용하도록 기존 **app.js**를 수정하고 새 **tasklist.js** 컨트롤러 파일을 만들겠습니다.

#### 모델 만들기

1. **tasklist** 디렉터리에서 **models**라는 새로운 디렉터리를 만듭니다.

2. **models** 디렉터리에서 **task.js**라는 새 파일을 만듭니다. 이 파일에는 응용 프로그램에서 만든 작업 모델이 포함됩니다.

3. 아래 코드를 **task.js** 파일에 추가합니다.

        var mongoose = require('mongoose'),
          Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	      itemName      : String,
	      itemCategory  : String,
	      itemCompleted : { type: Boolean, default: false },
	      itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. **task.js** 파일을 저장하고 닫습니다.

#### 컨트롤러 만들기

1. **tasklist/routes** 디렉터리에서 **tasklist.js**라는 새 파일을 만들고 텍스트 편집기에서 엽니다.

2. 아래 코드를 **tasklist.js**에 추가합니다. 이렇게 하면 **task.js**에 정의된 mongoose 모듈 및 작업 모델이 로드됩니다. TaskList 함수가 **connection** 값을 기반으로 MongoDB 서버에 연결하는 데 사용되며, 메서드 **showTasks**, **addTask** 및 **completeTasks**를 제공합니다.

		var mongoose = require('mongoose'),
 		  task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
		  mongoose.connect(connection);
		}

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    task.find({ itemCompleted : false }, function foundTasks(err, items) {
		    res.render('index', { title: 'My ToDo List', tasks: items })
		    });
		  },

		  addTask: function(req,res) {
		    var item = req.body;
		    var newTask = new task();
		    newTask.itemName = item.itemName;
		    newTask.itemCategory = item.itemCategory;
		    newTask.save(function savedTask(err) {
		      if(err) {
		        throw err;
		      }
		    });
		    res.redirect('/');
		  },


		  completeTask: function(req,res) {
		    var completedTasks = req.body;
		    for(taskId in completedTasks) {
		      if(completedTasks[taskId]=='true') {
		        var conditions = { _id: taskId };
		        var updates = { itemCompleted: completedTasks[taskId] };
		        task.update(conditions, updates, function updatedTask(err) {
		          if(err) {
		            throw err;
		          }
		        });
		      }
		    }
		    res.redirect('/');
		  }
		}

3. **tasklist.js** 파일을 저장합니다.

#### 인덱스 보기 수정

1. 디렉터리를 **views** 디렉터리로 변경하고 **index.jade** 파일을 텍스트 편집기에서 엽니다.

2. **index.jade** 파일 내용을 아래 코드로 바꿉니다. 이렇게 하면 기존 작업을 표시하는 데 사용되는 보기와 새 작업을 추가하고 기존 작업을 완료로 표시하는 데 사용되는 양식이 정의됩니다.

		h1 #{title}
		form(action="/completetask", method="post")
		  table(border="1")
		    tr
		      td Name
		      td Category
		      td Date
		      td Complete
		    each task in tasks
		      tr
		        td #{task.itemName}
		        td #{task.itemCategory}
		        - var day   = task.itemDate.getDate();
		        - var month = task.itemDate.getMonth() + 1;
		        - var year  = task.itemDate.getFullYear();
		        td #{month + "/" + day + "/" + year}
		        td
		          input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		  input(type="submit", value="Update tasks")
		hr
		form(action="/addtask", method="post")
		  table(border="1")
		    tr
		      td Item Name:
		      td
		        input(name="itemName", type="textbox")
		    tr
		      td Item Category:
		      td
		        input(name="itemCategory", type="textbox")
		  input(type="submit", value="Add item")

3. **index.jade** 파일을 저장하고 닫습니다.

#### app.js 바꾸기

1. **tasklist** 디렉터리에서 **app.js** 파일을 텍스트 편집기로 엽니다. 이 파일은 앞에서 **express** 명령을 실행하여 만들었습니다.
2. 아래 코드를 **app.js** 파일의 시작 부분에 추가합니다. 이렇게 하면 MongoDB 서버의 연결 문자열로 **TaskList**를 초기화합니다.

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	두 번째 줄에 유의합니다. mongo 인스턴스에 대한 연결 정보가 포함되어 있고 나중에 구성할 환경 변수에 액세스합니다. 개발을 위해 로컬 mongo 인스턴스를 실행하는 경우 일시적으로 이 값을 `process.env.CUSTOMCONNSTR_MONGOLAB_URI` 대신 "localhost"로 설정할 수 있습니다.

3. 다음 줄을 찾아

		app.use('/', routes);
		app.use('/users', users);

	다음으로 바꿉니다.

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	**tasklist.js**에 정의된 함수가 경로로 추가됩니다.

4. 앱을 초기화하려면 **app.js** 파일의 맨 아래로 이동하여 다음 줄을 추가합니다.

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. **app.js** 파일을 저장합니다.

<a name="deploy"></a>
## 앱 배포

이제 응용 프로그램이 개발되었으므로 이를 호스트할 Azure 앱 서비스에서 웹 앱을 만들고, 웹 앱을 구성하고, 코드를 배포해야 합니다. 이 섹션의 중심이 되는 것은 MongoDB 연결 문자열(URI) 사용입니다. 이 URI로 웹 앱에서 환경 변수를 구성하여 URI가 코드와 별도로 유지되도록 할 것입니다. URI는 데이터베이스에 연결하기 위한 자격 증명을 포함하고 있으므로 중요한 정보로 처리되어야 합니다.

이 섹션의 단계에서는 Mac, Linux 및 Windows용 Azure 명령줄 인터페이스를 사용하여 Azure 앱 서비스에서 새 웹앱을 만든 후 Git를 사용하여 응용 프로그램을 배포합니다. 이러한 단계를 수행하려면 Azure 구독이 있어야 합니다.

### Azure CLI 설치

Azure CLI를 설치하려면 다음 명령을 사용합니다.

	npm install azure-cli -g

<a href="/develop/nodejs/">Azure 개발자 센터</a>에서 이미 <strong>Node.js용 Azure SDK</strong>를 설치한 경우 Azure CLI가 이미 설치되어 있어야 합니다. 자세한 내용은 <a href="../virtual-machines-command-line-tools.md">Azure CLI</a>를 참조하세요.

Azure CLI는 주로 Mac 및 Linux 사용자를 위해 만들어졌지만 Node.js를 기반으로 하며 Node를 실행할 수 있는 모든 시스템에서 작동해야 합니다.

### 게시 설정 가져오기

Azure CLI를 사용하려면 먼저 구독에 대한 정보가 포함된 파일을 다운로드해야 합니다. 다음 단계에 따라 이 파일을 다운로드하여 가져옵니다.

1. 명령줄에서 다음 명령을 입력하여 브라우저를 시작하고 다운로드 페이지로 이동합니다. 메시지가 나타나면 구독과 관련된 계정으로 로그인합니다.

		azure account download

	![다운로드 페이지][download-publishing-settings]

	파일 다운로드가 자동으로 시작되어야 합니다. 그렇지 않은 경우 페이지 처음 부분에서 링크를 클릭하여 수동으로 파일을 다운로드할 수 있습니다.

2. 파일 다운로드가 완료되면 다음 명령을 사용하여 설정을 가져옵니다.

		azure account import <path-to-file>

	이전 단계에서 다운로드한 게시 설정 파일의 경로와 파일 이름을 지정합니다. 명령이 완료되면 다음과 유사한 출력이 표시됩니다.

		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/mongolab/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


3. 게시 설정 파일은 가져오기가 완료되면 더 이상 필요하지 않고 Azure 구독에 대한 중요한 정보를 포함하고 있으므로 가져오기가 완료되면 게시 설정 파일을 삭제해야 합니다.

### 새 웹 앱 만들기 및 코드 푸시

Azure 앱 서비스에서 웹 앱을 작성하는 것은 매우 쉽습니다. 첫 번째 Azure 웹 앱인 경우 포털을 사용해야 합니다. 하나 이상의 웹 사이트가 이미 있는 경우 7단계로 건너뜁니다.

1. Azure 포털에서 **새로 만들기**를 클릭합니다. ![새로 만들기][button-new]
2. **계산 > 웹앱 > 빠른 생성**을 선택합니다.
<!--- ![Create Web App][screen-mongolab-newwebsite] -->
3. URL 접두사를 입력합니다. 원하는 이름을 선택합니다. 이름은 고유해야 합니다('mymongoapp'은 사용 불가능할 가능성이 높음).
4. **웹 앱 만들기**를 클릭합니다.
5. 웹앱 만들기가 완료되면 웹앱 목록에서 웹앱 이름을 클릭합니다. 웹앱 대시보드가 표시됩니다.
<!--- ![Web App Dashboard][screen-mongolab-websitedashboard] -->
6. **간략 상태**에서 **소스 제어에서 배포 설정**을 클릭하고 GitHub를 선택한 다음 원하는 Git 사용자 이름 및 암호를 입력합니다. 웹 앱으로 푸시할 때(9단계) 이 암호를 사용합니다.  
7. 위 단계를 사용하여 웹 앱을 만든 경우 다음 명령으로 프로세스가 완료됩니다. 하지만 웹 앱이 이미 두 개 이상 있는 경우 위 단계를 건너뛰고 동일한 명령을 사용하여 새 웹 앱을 만들 수 있습니다. **tasklist** 프로젝트 디렉터리에서:

		azure site create myuniquewebappname --git  
	'myuniquewebappname'을 웹 앱의 고유한 이름으로 바꿉니다. 웹 앱이 이 명령의 일부로 만들어지면 웹 앱에 배치된다는 데이터 센터에 대한 메시지가 표시됩니다. 사용자의 MongoLab 데이터베이스와 지리적으로 가까운 데이터 센터를 선택합니다.

	`--git` 매개 변수는 다음을 만듭니다. * 로컬 Git 리포지토리가 없는 경우 **tasklist** 폴더에 만듭니다. * 'azure'라는 [Git remote]를 만듭니다. 응용 프로그램을 Azure에 게시하는 데 사용됩니다. * Azure에서 노드 응용 프로그램을 호스트하는 데 사용되는 설정이 포함된 [iisnode.yml] 파일을 만듭니다.* node-modules 폴더가 .git에 게시되지 않도록 하는 .gitignore 파일을 만듭니다.

	이 명령이 완료되면 다음과 유사한 출력이 표시됩니다. **Created web site at**으로 시작하는 줄에 웹 앱의 URL이 있습니다.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquewebappname.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. 다음 명령을 사용하여 추가한 다음 로컬 Git 리포지토리로 파일을 커밋합니다.

		git add .
		git commit -m "adding files"

9. 코드를 푸시합니다.

		git push azure master  

	최신 Git 리포지토리 변경 내용을 Azure 앱 서비스에서 웹 앱으로 푸시하는 경우 **master**가 웹 앱 내용에 사용되므로 이를 대상 분기로 지정해야 합니다. 암호를 입력하라는 메시지가 나타나면 위의 웹 앱에 대한 Git 게시를 설정할 때 만든 암호를 입력합니다.

	다음과 유사한 출력이 표시됩니다. 배포가 수행될 때 Azure에서 모든 npm 모듈을 다운로드합니다.

		Counting objects: 17, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (13/13), done.
		Writing objects: 100% (17/17), 3.21 KiB, done.
		Total 17 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id 'ef276f3042'.
		remote: Preparing files for deployment.
		remote: Running NPM.
		...
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [new branch]      master -> master

이제 거의 완료되었습니다.

### 환경 구성
코드에 있는 process.env.CUSTOMCONNSTR_MONGOLAB_URI를 기억하고 있습니까? 이 환경 변수를 MongoLab 데이터베이스 프로비전 중 Azure에 입력된 값으로 채우겠습니다.

#### MongoLab 연결 문자열 가져오기

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

#### 웹 앱의 환경 변수에 연결 문자열 추가

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

## 성공!

프로젝트 디렉터리에서 `azure site browse`을(를) 실행하여 브라우저를 자동으로 열거나 브라우저를 열고 웹 앱 URL(myuniquewebappname.azurewebsites.net)에 수동으로 이동합니다.

![빈 tasklist가 표시된 웹 페이지][node-mongo-finished]

<a name="manage"></a>
## 데이터베이스 관리

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

축하합니다. MongoLab 호스트 MongoDB 데이터베이스의 지원을 받는 Node.js 응용 프로그램이 시작되었습니다. 이제 MongoLab 데이터베이스가 있으므로 데이터베이스에 대한 질문 사항이나 궁금한 사항이 있는 경우 또는 MongoDB나 노드 드라이버 자체에 대한 지원이 필요한 경우 [support@mongolab.com](mailto:support@mongolab.com)에 문의할 수 있습니다. 여러분의 행운을 빕니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.


[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure CLI]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Create and deploy a Node.js application to Azure Web Sites]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[MongoLab]: http://mongolab.com
[Node.js Web Application with Storage on MongoDB (Virtual Machine)]: /develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png
 

<!----HONumber=62-->