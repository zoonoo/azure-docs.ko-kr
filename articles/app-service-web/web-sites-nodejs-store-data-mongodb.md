<properties
	pageTitle="가상 컴퓨터에 MongoDB를 사용하는 Azure의 Node.js 웹앱 만들기"
	description="MongoDB를 사용하여 Azure에서 호스트되는 Node.js 응용 프로그램에 데이터를 저장하는 방법"
	tags="azure-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="mwasson"/>


# 가상 컴퓨터에 MongoDB를 사용하는 Azure의 Node.js 웹앱 만들기

이 자습서에서는 Azure 가상 컴퓨터에서 호스트되는 [MongoDB]를 사용하여 데이터를 저장하고, [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹앱에서 호스트되는 [노드] 응용 프로그램의 데이터에 액세스하는 방법을 보여 줍니다. [MongoDB]는 대중적인 오픈 소스의 고성능 NoSQL 데이터베이스입니다.

다음 내용을 배웁니다.

* VM 디포에서 Ubuntu 및 MongoDB를 실행하는 가상 컴퓨터 설정 방법
* 노드 응용 프로그램에서 MongoDB에 액세스하는 방법
* Azure CLI를 사용하여 Azure 앱 서비스에서 웹앱 만드는 방법

이 자습서를 따라 작업을 만들고, 검색하고, 완료할 수 있는 간단한 웹 기반 작업 관리 응용 프로그램을 구축합니다. 이 작업은 MongoDB에 저장됩니다.

> [AZURE.NOTE]이 자습서에서는 가상 컴퓨터에 설치된 MongoDB의 인스턴스를 사용합니다. 대신 MongoLabs에서 제공하는 호스트된 MongoDB 인스턴스를 사용하는 경우 [Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 Node.js 웹앱 만들기](store-mongolab-web-sites-nodejs-store-data-mongodb)를 참조하세요.

이 자습서의 프로젝트 파일은 **tasklist**라는 디렉터리에 저장되며 완료된 응용 프로그램은 다음과 유사하게 보입니다.

![빈 tasklist를 표시하는 웹 페이지][node-mongo-finished]

> [AZURE.NOTE]아래의 많은 단계에서는 명령줄 사용을 언급합니다. 이러한 단계의 경우 **Windows PowerShell**(Windows) 또는 **Bash**(Unix Shell)와 같은 운영 체제의 명령줄을 사용하십시오. OS X 시스템에서는 터미널 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

##필수 조건

이 자습서의 단계에서는 최신 버전의 [Node.js][node]를 개발 환경에서 사용해야 합니다.

또한 [Git]는 Azure 웹 사이트에 응용 프로그램을 배포하는 데 사용되므로, 개발 환경의 명령줄에서 사용할 수 있어야 합니다.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

##가상 컴퓨터를 만듭니다.

<!--
After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

새 VM을 만든 후 [MongoDB 설치 가이드][installguides]에 따라 MongoDB를 설치할 수 있지만 MongoDB가 사전 설치된 VM을 Azure 마켓플레이스에서 사용할 수 있습니다. 다음 단계에서는 이러한 많은 VM 템플릿 중 하나를 사용하는 방법을 보여 줍니다.

> [AZURE.NOTE]이 자습서에서 사용하는 커뮤니티 이미지는 OS 디스크에 MongoDB 데이터를 저장합니다. 이는 자습서의 목적에는 충분하지만 MongoDB 데이터를 데이터 디스크에 저장하면 성능이 더욱 향상됩니다. 데이터 디스크를 포함하여 새 VM을 만들고 MongoDB 데이터를 데이터 디스크에 저장하는 단계는 [Azure에서 Linux에 MongoDB 설치][mongodbonazure]를 참조하세요.

1. [Azure 포털][azureportal]에 로그인합니다.

3. **새로 만들기** > **데이터 + 저장소** > **마켓플레이스**를 클릭합니다.

	![VM 디포를 선택하는 스크린샷][selectdepo]

2. 위쪽의 검색 상자에 "mongodb"를 입력한 다음 **MongoDB v2.2.3 on Hardened Ubuntu 12.04 LTS**를 선택합니다. **만들기**를 클릭하여 계속합니다.

	![확정된 ubuntu 이미지에서 mongodb v2.2.3이 선택된 스크린샷][selectedimage]

	페이지 아래의 화살표를 클릭하여 다음 화면으로 진행합니다.

7. VM의 **호스트 이름**, 관리자 **사용자 이름**과 **암호** 및 **리소스 그룹**을 구성합니다. 그런 다음 **선택적 구성**을 클릭합니다.

	![VM 구성의 스크린샷][vmconfig]

8. VM의 추가 끝점을 설정합니다. 이 VM의 MongoDB에 액세스하고 있으므로 다음 정보를 사용하여 새 끝점을 추가합니다.

	* 이름 - MongoDB
	* 프로토콜 - TCP
	* 공용 포트 - 27017
	* 개인 포트 - 27017

	MongoDB 웹 포털을 노출하려면 다음 정보를 사용하여 다른 끝점을 추가합니다.

	* 이름 - MongoDBWeb
	* 프로토콜 - TCP
	* 공용 포트 - 28017
	* 개인 포트 - 28017

	![끝점 구성의 스크린샷][vmendpoint]

9. **확인**을 두 번 클릭한 다음 **만들기**을 클릭하여 VM을 만듭니다.

	가상 컴퓨터가 만들어지면 시작 보드에 표시되며, 이를 클릭하여 VM의 블레이드를 열 수 있습니다. 웹 브라우저에서 **http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/**을 열어 MongoDB가 실행되고 있는지 확인할 수 있습니다. 페이지 아래에 다음과 유사한 서비스에 대한 정보를 표시하는 로그가 나타나야 합니다.

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	로그에서 오류를 표시하는 경우 문제 해결 단계에 대해서는 [MongoDB 설명서][mongodocs](영문)를 참조하십시오.


##설치 모듈 및 스캐폴딩 생성

이 섹션에서는 개발 환경에서 새로운 노드 응용 프로그램을 만들고 npm을 사용하여 모듈 패키지를 추가합니다. 작업 목록 응용 프로그램의 경우 [Express] 및 [Mongoose] 모듈을 사용합니다. Express 모듈은 노드에 대한 모델 보기 컨트롤러 프레임워크를 제공하며, Mongoose는 MongoDB와 통신하기 위한 드라이버입니다.

###express 설치 및 스캐폴딩 생성

1. 명령줄에서 **tasklist** 디렉터리로 변경합니다. **tasklist** 디렉터리가 없으면 디렉터리를 만듭니다.

	> [AZURE.NOTE]이 자습서에서는 **tasklist** 폴더에 대한 참조를 만듭니다. 경로 의미 체계가 운영 체제마다 다르므로, 폴더의 전체 경로는 생략되었습니다. 로컬 파일 시스템에 쉽게 액세스할 수 있는 위치에 이 폴더를 만들어야 합니다(예: **~/node/tasklist** 또는 **c:\node\tasklist**).

2. 다음 명령을 입력하여 express 명령을 설치합니다.

	npm install express-generator -g

	> [AZURE.NOTE]일부 운영 체제에서 '-g' 매개 변수를 사용할 때 ___Error: EPERM, chmod '/usr/local/bin/express'___ 오류 메시지와 관리자 계정으로 실행하라는 요청이 표시될 수 있습니다. 이 경우 `sudo` 명령을 사용하여 더 높은 권한 수준으로 npm을 실행하세요.

    이 명령의 출력은 다음과 유사합니다.

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE]express 모듈을 설치할 때 사용되는 '-g' 매개 변수는 이 모듈을 전역으로 설치합니다. 따라서 ___express___ 명령에 액세스하여 추가 경로 정보를 입력하지 않고도 웹앱 스캐폴딩을 생성할 수 있습니다.

4. 이 응용 프로그램에 사용할 스캐폴딩을 만들려면 다음과 같은 **express** 명령을 사용합니다.

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

3. **tasklist/bin/www** 파일을 **tasklist** 폴더의 **server.js** 파일에 복사합니다. 앱 서비스 웹앱에서는 Node.js 응용 프로그램의 진입점이 **server.js** 또는 **app.js**여야 합니다. **app.js**는 이미 존재하지만 진입점이 아니므로 **server.js**를 사용해야 합니다.

4. **server.js** 파일을 수정하여 다음 줄에서 '.' 문자 중 하나를 제거합니다.

		var app = require('../app');

	수정한 줄은 다음과 같이 표시됩니다.

		var app = require('./app');

	이는 **server.js**(이전의 **bin/www**)가 이제 필수 **app.js** 파일과 동일한 폴더에 있기 때문에 필요합니다.

###추가 모듈 설치

**package.json** 파일은 **express** 명령으로 만들어진 파일 중 하나입니다. 이 파일에는 Express 응용 프로그램에 필요한 추가 모듈 목록이 포함되어 있습니다. 나중에 이 응용 프로그램을 앱 서비스 웹앱에 배포하는 경우 이 파일을 사용하여 응용 프로그램을 지원하기 위해 Azure에 설치해야 할 모듈을 결정합니다.

1. **tasklist** 폴더에서 다음을 사용하여 **package.json** 파일에 설명된 모듈을 설치합니다.

        npm install

    이 명령의 출력은 다음과 유사하게 표시됩니다.

		debug@0.7.4 node_modules\debug

		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0

		morgan@1.0.0 node_modules\morgan
		└── bytes@0.2.1

		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.4 (bytes@0.3.0)
		└── type-is@1.1.0 (mime@1.2.11)

		express@4.0.0 node_modules\express
		├── methods@0.1.0
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── fresh@0.2.2
		├── range-parser@1.0.0
		├── buffer-crc32@0.2.1
		├── qs@0.6.6
		├── cookie@0.1.0
		├── path-to-regexp@0.1.2
		├── send@0.2.0 (mime@1.2.11)
		├── type-is@1.0.0 (mime@1.2.11)
		├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
		└── serve-static@1.0.1 (send@0.1.4)

		jade@1.3.1 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.0 (uglify-js@2.4.13)
		├── with@3.0.0 (uglify-js@2.4.13)
		└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)

	Express 응용 프로그램에 사용되는 기본 모듈이 모두 설치됩니다.

2. 이제 다음 명령을 입력하여 Mongoose 모듈을 로컬에 설치하고 이에 대한 항목을 **package.json** 파일에 저장합니다.

		npm install mongoose --save

	이 명령의 출력은 다음과 유사하게 표시됩니다.

		mongoose@3.8.8 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.5
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.5.3
		└── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)

    > [AZURE.NOTE]C++ bson 파서 설치에 대한 메시지는 무시해도 됩니다.

##노드 응용 프로그램에서 MongoDB 사용

이 섹션에서는 작업에 대한 모듈을 포함하는 **task.js** 파일을 추가하여 **express** 명령으로 만들어진 기본 응용 프로그램을 확장합니다. 또한 해당 모델을 활용하도록 기존 **app.js**를 수정하고 새 **tasklist.js** 컨트롤러 파일을 만들겠습니다.

### 모델 만들기

1. **tasklist** 디렉터리에서 **models**라는 새로운 디렉터리를 만듭니다.

2. **models** 디렉터리에서 **task.js**라는 새 파일을 만듭니다. 이 파일에는 응용 프로그램에서 만든 작업 모델이 포함됩니다.

3. **task.js** 파일의 시작 부분에 필수 라이브러리를 참조하는 다음 코드를 추가합니다.

        var mongoose = require('mongoose'),
	        Schema = mongoose.Schema;

4. 그런 다음, 모델을 정의하고 내보내는 코드를 추가합니다. 이 모델은 MongoDB 데이터베이스와의 조작을 수행하는 데 사용됩니다.

        var TaskSchema = new Schema({
	        itemName      : String,
	        itemCategory  : String,
	        itemCompleted : { type: Boolean, default: false },
	        itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. **task.js** 파일을 저장하고 닫습니다.

###컨트롤러 만들기

1. **tasklist/routes** 디렉터리에서 **tasklist.js**라는 새 파일을 만들고 텍스트 편집기에서 엽니다.

2. **tasklist.js**에 다음 코드를 추가합니다. 이렇게 하면 **task.js**에 정의된 mongoose 모듈 및 작업 모델이 로드됩니다. TaskList 함수는 **connection**을 기반으로 MongoDB 서버에 대한 연결을 만드는 데 사용됩니다.

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. **showTasks**, **addTask** 및 **completeTasks**에 사용된 메서드를 추가하여 **tasklist.js** 파일에 계속 추가합니다.

		TaskList.prototype = {
  		  showTasks: function(req, res) {
      	    task.find({itemCompleted: false}, function foundTasks(err, items) {
      		  res.render('index',{title: 'My ToDo List ', tasks: items})
    		});
  		  },

  		  addTask: function(req,res) {
    		var item = req.body.item;
    		newTask = new task();
    		newTask.itemName = item.name;
    		newTask.itemCategory = item.category;
    		newTask.save(function savedTask(err){
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

### app.js 수정

1. **tasklist** 디렉터리에서 **app.js** 파일을 텍스트 편집기로 엽니다. 이 파일은 앞에서 **express** 명령을 실행하여 만들었습니다.

2. 아래 코드를 **app.js** 파일의 시작 부분에 추가합니다. MongoDB 서버에 대한 연결 문자열로 **TaskList**가 초기화됩니다.

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	두 번째 줄에 유의합니다. mongo 인스턴스에 대한 연결 정보가 포함되어 있고 나중에 구성할 환경 변수에 액세스합니다. 개발을 위해 로컬 mongo 인스턴스를 실행하는 경우 일시적으로 이 값을 process.env.MONGODB_URI 대신 "localhost"로 설정할 수 있습니다.

3. 다음 줄을 찾습니다.

		app.use('/', routes);
		app.use('/users', users);

	위 두 줄을 다음으로 바꿉니다.

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

	**tasklist.js**에 정의된 함수가 경로로 추가됩니다.

4. **app.js** 파일을 저장합니다.

###인덱스 보기 수정

1. 디렉터리를 **views** 디렉터리로 변경하고 **index.jade** 파일을 텍스트 편집기에서 엽니다.

2. **index.jade** 파일 내용을 아래 코드로 바꿉니다. 이렇게 하면 기존 작업을 표시하는 데 사용되는 보기와 새 작업을 추가하고 기존 작업을 완료로 표시하는 데 사용되는 양식이 정의됩니다.

		h1= title
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
		        input(name="item[name]", type="textbox")
		    tr
		      td Item Category:
		      td
		        input(name="item[category]", type="textbox")
		  input(type="submit", value="Add item")

3. **index.jade** 파일을 저장하고 닫습니다.

<!-- ##Run your application locally

To test the application on your local machine, perform the following steps:

1. From the command-line, change directories to the **tasklist** directory.

2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.

	On a Windows system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	On an OS X or Linux-based system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.

2. Use the following command to launch the application locally:

        node app.js

3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. The page should update to display the item in the ToDo List table.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.

7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

##Azure에 응용 프로그램 배포

이 섹션의 단계에서는 Azure 명령줄 도구를 사용하여 새 Azure 앱 서비스에서 새 웹앱을 만든 후 Git를 사용하여 응용 프로그램을 배포합니다. 이러한 단계를 수행하려면 Azure 구독이 있어야 합니다.

> [AZURE.NOTE]Azure 포털을 사용하여 이러한 단계를 수행할 수도 있습니다. Azure 포털을 사용하여 Node.js 응용 프로그램을 배포하는 단계는 [Azure 앱 서비스에서 Node.js 웹앱 빌드 및 배포](web-sites-nodejs-develop-deploy-mac.md)를 참조하세요.

> [AZURE.NOTE]처음으로 앱 서비스 웹앱을 만든 경우 Azure 포털을 사용하여 이 응용 프로그램을 배포해야 합니다.

###Azure CLI(Azure 명령줄 인터페이스) 설치

Azure CLI를 사용하면 Azure 서비스에 대한 관리 작업을 수행할 수 있습니다. 개발 환경에서 Azure CLI를 아직 설치하여 구성하지 않은 경우 [Azure CLI 설치 및 구성](../xplat-cli-install.md)의 지침을 참조하세요.

###앱 서비스 웹앱 만들기

1. 명령줄에서 **tasklist** 디렉터리로 변경합니다.

2. 다음 명령을 사용하여 새 앱 서비스 웹앱을 만듭니다. 'myuniqueappname'을 웹앱의 고유한 이름으로 바꿉니다. 이 값은 결과 웹앱의 URL 일부로 사용됩니다.

		azure site create myuniqueappname --git

	웹앱을 배치할 데이터 센터를 묻는 메시지가 나타납니다. 사용자의 위치와 지리적으로 가까운 데이터 센터를 선택합니다.

	`--git` 매개 변수는 Git 리포지토리가 없는 경우 **tasklist** 폴더에 로컬로 만듭니다. 또한 Azure에 응용 프로그램을 게시하는 데 사용되는 'azure'라는 [Git remote](영문)를 만듭니다. Azure에서 노드 응용 프로그램을 호스트하는 데 사용하는 설정이 포함된 [iisnode.yml]이 만들어집니다. 마지막으로 .git 게시에서 node-modules 폴더를 제외하는 .gitignore 파일도 만들어집니다.

	> [AZURE.NOTE]이미 Git 리포지토리를 포함한 디렉터리에서 이 명령을 실행한 경우 디렉터리가 다시 초기화되지는 않습니다.

	> [AZURE.NOTE]`--git` 매개 변수가 생략되었지만 디렉터리에 Git 리포지토리가 포함된 경우 'azure' 원격이 계속 생성됩니다.

	이 명령이 완료되면 다음과 유사한 출력이 표시됩니다. **Created website at**으로 시작하는 줄에 앱 서비스 웹앱 URL이 있습니다.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

	> [AZURE.NOTE> 구독에 대한 앱 서비스 웹앱을 처음 만드는 경우 포털을 사용하여 웹앱을 만들라는 메시지가 제공됩니다. 자세한 내용은 [Azure 앱 서비스에서 Node.js 웹앱 빌드 및 배포](web-sites-nodejs-develop-deploy-mac.md)를 참조하세요.

###MONGODB_URI 환경 변수 설정

MongoDB 인스턴스에 대한 연결 문자열을 MONGODB_URI 환경 변수에서 사용할 수 있어야 합니다. 웹앱에 대한 이 값을 설정하려면 다음 명령을 사용합니다.

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

웹앱에 대한 새 응용 프로그램 설정이 만들어집니다. 이 설정은 웹앱에서 읽는 MONGODB_URI 환경 변수를 채우는 데 사용됩니다. 'mymongodb.cloudapp.net'의 값을 MongoDB가 설치된 가상 컴퓨터의 이름으로 바꾸십시오.

###응용 프로그램 게시

1. 터미널 창에서 아직 이동하지 않은 경우 **tasklist** 디렉터리로 변경합니다.

2. 다음 명령을 사용하여 로컬 Git 리포지토리로 파일을 커밋합니다.

		git add .
		git commit -m "adding files"

3. 최신 Git 리포지토리 변경 내용을 앱 서비스 웹앱으로 푸시하는 경우 **master**가 웹앱 내용에 사용되므로 이를 대상 분기로 지정해야 합니다.

		git push azure master

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

4. 푸시 작업이 완료되면 `azure site browse` 명령을 사용해 웹앱으로 이동하여 응용 프로그램을 확인합니다.

##다음 단계

이 문서의 단계에서는 MongoDB를 사용하여 정보를 저장하는 프로세스를 설명하지만 Azure 테이블 서비스도 사용할 수 있습니다. 자세한 내용은 [Azure 테이블 서비스를 사용하는 Node.js 웹앱]을 참조하세요.

MongoLab에서 제공하는 MongoDB의 호스트된 인스턴스를 사용하는 방법은 [Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 Node.js 웹앱 만들기](store-mongolab-web-sites-nodejs-store-data-mongodb.md)를 참조하세요.

MongoDB를 보호하는 방법을 알아보려면 [MongoDB 보안][mongosecurity]을 참조하십시오.

##추가 리소스

[Mac 및 Linux용 Azure 명령줄 도구][Build and deploy a Node.js web app in Azure App Service] [Azure 앱 서비스에서 GIT를 사용하여 연속 배포]

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[노드]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure command-line tool for Mac and Linux]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Build and deploy a Node.js web app in Azure App Service]: web-sites-nodejs-develop-deploy-mac.md
[Azure 앱 서비스에서 GIT를 사용하여 연속 배포]: web-sites-publish-source-control.md
[Azure 테이블 서비스를 사용하는 Node.js 웹앱]: storage-nodejs-use-table-storage-web-site.md
[node-mongo-finished]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/web-sites-nodejs-store-data-mongodb/todo_add_item.png
[node-mongo-list-items]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://portal.azure.com
[mongodocs]: http://docs.mongodb.org/manual/
[Azure CLI]: ../xplat-cli.md

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
 

<!---HONumber=July15_HO4-->