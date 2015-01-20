<properties urlDisplayName="Website with MongoDB" pageTitle="VM의 MongoDB를 사용하는 Node.js 웹 사이트 - Azure 자습서" metaKeywords="Azure 자습서 MongoDB, MongoDB 저장소 데이터, 데이터 MongoDB 노드 액세스, Azure 노드 앱" description="MongoDB를 사용하여 Azure에서 호스팅되는 Node 응용 프로그램에서 데이터를 저장하고 액세스하는 방법에 대해 설명하는 자습서입니다." metaCanonical="http://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="larryfr"  solutions="" writer="" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


# 가상 컴퓨터에 MongoDB를 사용하는 Azure의 Node.js 응용 프로그램 만들기

이 자습서에서는 Azure 가상 컴퓨터에 호스트되는 [MongoDB]를 사용하여 데이터를 저장하고, Azure 웹 사이트에 호스트되는 [노드] 응용 프로그램의 데이터에 액세스하는 방법을 보여 줍니다. [MongoDB]는 대중적인 오픈 소스의 고성능 NoSQL 데이터베이스입니다.

다음 내용을 배웁니다.

* VM 디포에서 Ubuntu 및 MongoDB를 실행하는 가상 컴퓨터 설정 방법
* 노드 응용 프로그램에서 MongoDB에 액세스하는 방법
* Azure용 교차 플랫폼 도구를 사용하여 Azure 웹 사이트를 만드는 방법

이 자습서를 따라 작업을 만들고, 검색하고, 완료할 수 있는 간단한 웹 기반 작업 관리 응용 프로그램을 구축합니다. 이 작업은 MongoDB에 저장됩니다.

> [WACOM.NOTE] 이 자습서에서는 가상 컴퓨터에 설치된 MongoDB의 인스턴스를 사용합니다. MongoLabs에서 제공하는 호스트된 MongoDB 인스턴스를 사용하려는 경우 <a href="/ko-kr/develop/nodejs/tutorials/website-with-mongodb-mongolab/">Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 Node.js 응용 프로그램 만들기</a>를 참조하세요.
 
이 자습서의 프로젝트 파일은 **tasklist**라는 디렉터리에 저장되며 완료된 응용 프로그램은 다음과 유사하게 보입니다.

![A web page displaying an empty tasklist][node-mongo-finished]

> [WACOM.NOTE] 아래의 많은 단계에서는 명령줄 사용을 언급합니다. 이러한 단계의 경우 __Windows PowerShell__(Windows) 또는 __Bash__(Unix Shell)와 같은 운영 체제의 명령줄을 사용합니다. OS X 시스템에서는 터미널 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

##필수 조건

이 자습서의 단계에서는 최신 버전의 [Node.js][node]를 개발 환경에서 사용해야 합니다.

또한 [Git]는 Azure 웹 사이트에 응용 프로그램을 배포하는 데 사용되므로, 개발 환경의 명령줄에서 사용할 수 있어야 합니다.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##가상 컴퓨터 만들기

<!--This tutorial assumes you have created a virtual machine in Azure. After creating the virtual machine you need to install MongoDB on the virtual machine:

* To create a Linux virtual machine and install MongoDB, see [Installing MongoDB on a Linux Virtual machine].

After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

[MongoDB 설치 가이드][installguides]를 따라서 새로운 VM을 만든 후 MongoDB를 설치할 수 있지만 이 작업의 대부분은 이미 커뮤니티에서 수행되었으며 VM 디포에서 사용 가능합니다. 다음 단계에서는 Mongo DB를 이미 설치하고 구성한 VM 디포의 이미지를 사용하는 방법을 설명합니다. 

> [WACOM.NOTE] 이 자습서에서 사용하는 커뮤니티 이미지는 OS 디스크에 MongoDB 데이터를 저장합니다. 이는 자습서의 목적에는 충분하지만 MongoDB 데이터를 데이터 디스크에 저장하면 성능이 더욱 향상됩니다. 데이터 디스크를 포함하여 새 VM을 만들고 MongoDB 데이터를 데이터 디스크에 저장하는 단계는 [Azure에서 Linux에 MongoDB 설치][mongodbonazure]를 참조하세요.

1. [Azure 관리 포털][azureportal]에 로그인한 다음 __가상 컴퓨터__, __이미지__, __VM 디포__를 차례로 선택합니다.

	![screenshot of selecting VM Depot][selectdepo]

2. MongoDB를 포함하는 이미지를 선택합니다. 이 경우, Ubuntu를 선택하여 Ubuntu Linux 배포를 기반으로 이미지만으로 목록의 범위를 좁혔습니다. 마지막으로, 확정된 Ubuntu 이미지에서 MongoDB v2.2.3을 선택했습니다.

	![screenshot of selected mongodb v2.2.3 on hardened ubuntu image][selectedimage]

	> [WACOM.NOTE] 이미지에 대한 모든 정보를 보려면 __자세히__를 선택해야 합니다. 일부 이미지의 경우 이미지를 사용하여 VM을 만든 후 추가 구성을 해야 할 수 있습니다.

	페이지 아래의 화살표를 클릭하여 다음 화면으로 진행합니다.

3. 이 이미지용 VHD를 저장하는 데 사용할 지역 및 저장소 계정을 선택합니다. 확인 표시를 클릭하여 계속합니다.
	
	![screenshot of choose a storage account][selectstorage]

	> [WACOM.NOTE] VM 디포에서 지정된 저장소 계정으로 이미지를 복사하는 복사 프로세스가 시작됩니다. 15분 이상의 시간이 걸릴 수 있습니다.

4. 이미지의 상태가 __등록 보류 중__으로 변경되면 __등록__을 선택하고 새 이미지의 식별 이름을 입력합니다. 확인 표시를 클릭하여 계속합니다.

	![screenshot of registering an image][register]

5. 이미지의 상태가 __사용할 수 있음__으로 변경되면 __+ 새로 만들기__, __가상 컴퓨터__, __갤러리에서__를 차례로 선택합니다. __이미지 선택__ 메시지가 표시되면 __내 이미지__를 선택한 후 이전 단계에서 만든 이미지를 선택합니다. 화살표를 클릭하여 계속합니다.

	![screenshot of the image][myimage]

6. VM, 크기, 사용자 이름을 제공합니다. 화살표를 클릭하여 계속합니다.

	![screenshot of the vm name, user name, etc.][vmname]

	>[WACOM.NOTE] 이 자습서에서는 SSH를 사용하여 원격으로 VM에 연결할 필요는 없습니다. SSH와 함께 인증서를 사용하는 데 익숙지 않은 경우 **암호 사용**을 선택하고 암호를 제공합니다.
	>
	> Azure의 Linux VM 및 SSH 사용에 대한 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법][sshazure]을 참조하세요.

7. 새로운 또는 기존 클라우드 서비스를 사용할지 여부와 VM을 만들 지역을 선택합니다. 화살표를 클릭하여 계속합니다.

	![screenshot of the vm configuration][vmconfig]

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
	
	마지막으로, 확인 표시를 선택하여 가상 컴퓨터를 구성합니다.

	![screenshot of the endpoint configuration][vmendpoint]

9. 가상 컴퓨터 상태가 __실행 중__으로 변경되면 웹 브라우저에서 __http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/__을 열어 MongoDB가 실행되고 있는지 확인합니다. 페이지 아래에 다음과 유사한 서비스에 대한 정보를 표시하는 로그가 나타나야 합니다.

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	로그에서 오류를 표시하는 경우 문제 해결 단계에 대해서는 [MongoDB 설명서(영문)][mongodocs]를 참조하세요.


##모듈 설치 및 스캐폴딩 생성

이 섹션에서는 개발 환경에서 새로운 노드 응용 프로그램을 만들고 npm을 사용하여 모듈 패키지를 추가합니다. 작업 목록 응용 프로그램의 경우 [Express] 및 [Mongoose] 모듈을 사용합니다. Express 모듈은 노드에 대한 모델 보기 컨트롤러 프레임워크를 제공하며, Mongoose는 MongoDB와 통신하기 위한 드라이버입니다.

###express 설치 및 스캐폴딩 생성

1. 명령줄에서 **tasklist** 디렉터리로 변경합니다. **tasklist** 디렉터리가 없으면 디렉터리를 만듭니다.

	> [WACOM.NOTE] 이 자습서에서는 __tasklist__ 폴더에 대한 참조를 만듭니다. 운영 체제마다 경로 의미 체계가 다르므로, 폴더의 전체 경로는 생략되었습니다. 로컬 파일 시스템에 쉽게 액세스할 수 있는 위치에 이 폴더를 만들어야 합니다(예: __~/node/tasklist__ 또는 __c:\node\tasklist__).

2. 다음 명령을 입력하여 express 명령을 설치합니다.

	npm install express-generator -g
 
	> [WACOM.NOTE] 일부 운영 체제에서 '-g' 매개 변수를 사용하면 __Error: EPERM, chmod '/usr/local/bin/express'__ 오류 메시지와 관리자 계정으로 실행하라는 요청이 표시될 수 있습니다. 이런 경우 'sudo' 명령을 사용하여 더 높은 권한 수준으로 npm을 실행합니다.

    이 명령의 출력은 다음과 유사하게 표시됩니다.

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)                                                                         
 
	> [WACOM.NOTE] express 모듈을 설치할 때 사용되는 '-g' 매개 변수는 이 모듈을 전역으로 설치합니다. __express__ 명령에 액세스하여 추가 경로 정보에 입력하지 않고도 웹 사이트 스캐폴딩을 생성할 수 있도록 이 작업을 수행합니다.

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

3. **tasklist/bin/www** 파일을 **tasklist** 폴더의 **server.js** 파일에 복사합니다. Azure 웹 사이트에서는 Node.js 응용 프로그램의 진입점이 **server.js** 또는 **app.js**여야 합니다. **app.js**는 이미 존재하지만 진입점이 아니므로 **server.js**를 사용해야 합니다.

4. **server.js** 파일을 수정하여 다음 줄에서 '.' 문자 중 하나를 제거합니다.

		var app = require('../app');

	수정한 줄은 다음과 같이 표시됩니다.

		var app = require('./app');

	이는 **server.js**(이전의 **bin/www**)가 이제 필수 **app.js** 파일과 동일한 폴더에 있기 때문에 필요합니다.

###추가 모듈 설치

**package.json** 파일은 **express** 명령으로 만들어진 파일 중 하나입니다. 이 파일에는 Express 응용 프로그램에 필요한 추가 모듈 목록이 포함되어 있습니다. 나중에 이 응용 프로그램을 Azure 웹 사이트에 배포하는 경우 이 파일을 사용하여 응용 프로그램을 지원하기 위해 Azure에 설치해야 할 모듈을 결정합니다.
	
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

    > [WACOM.NOTE] C++ bson 파서 설치에 대한 메시지는 무시해도 됩니다.

##노드 응용 프로그램에서 MongoDB 사용

이 섹션에서는 작업에 대한 모델을 포함하는 **task.js** 파일을 추가하여 **express** 명령으로 만든 기본 응용 프로그램을 확장합니다. 또한 해당 모델을 활용하도록 기존 **app.js**를 수정하고 새 **tasklist.js** 컨트롤러 파일을 만들겠습니다.

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

2. **tasklist.js**에 다음 코드를 추가합니다. 이렇게 하면 **task.js**에 정의된 mongoose 모듈 및 작업 모델이 로드됩니다. TaskList 함수는 **connection** 값을 기준으로 MongoDB 서버에 대한 연결을 만드는 데 사용됩니다.

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

1. **tasklist** 디렉터리에 있는 **app.js** 파일을 텍스트 편집기에서 엽니다. 이 파일은 앞에서 **express** 명령을 실행하여 만든 파일입니다.

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

1. **views** 디렉터리로 변경하고 **index.jade** 파일을 텍스트 편집기에서 엽니다.

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

로컬 컴퓨터에서 응용 프로그램을 테스트하려면 다음 단계를 수행하세요.

1. 명령줄에서 **tasklist** 디렉터리로 변경합니다.

2. MongoDB 호스트하는 가상 컴퓨터를 가리키도록 개발 환경의 MONGODB_URI 환경 변수를 설정합니다. 아래 예제에서 __mymongodb__를 가상 컴퓨터 이름으로 바꿉니다.

	Windows 시스템에서는 다음을 사용하여 환경 변수를 설정합니다.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	OS X 또는 Linux 기반 시스템에서는 다음을 사용하여 환경 변수를 설정합니다.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	그러면 응용 프로그램이 앞서 만든 __mymongodb.cloudapp.net__ 가상 컴퓨터의 MongoDB에 연결하고 'tasks'라는 DB를 사용합니다.

2. 다음 명령을 사용하여 응용 프로그램을 로컬에서 시작합니다.

        node app.js

3. 웹 브라우저를 열고 http://localhost:3000으로 이동합니다. 그러면 다음과 비슷한 웹 페이지가 표시됩니다.

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. **항목 이름** 및 **항목 범주**에 대해 제공된 필드를 사용하여 정보를 입력한 후 **항목 추가**를 클릭합니다.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. 페이지가 업데이트되어 ToDo List 테이블의 항목을 표시합니다.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. 작업을 완료하려면 완료 열의 확인란을 선택하고 **작업 업데이트**를 클릭하면 됩니다. **작업 업데이트**를 클릭한 후 시각적인 변화는 없지만 MongoDB의 문서 항목이 이제 완료된 것으로 표시됩니다.

7. node 프로세스를 중지하려면 명령줄로 이동한 후 **Ctrl** 키와 **C** 키를 누릅니다. -->

##Azure에 응용 프로그램 배포

이 섹션의 단계에서는 Azure 명령줄 도구를 사용하여 새 Azure 웹 사이트를 만든 후 Git를 사용하여 응용 프로그램을 배포합니다. 이러한 단계를 수행하려면 Azure 구독이 있어야 합니다.

> [WACOM.NOTE] 이러한 단계는 Azure 포털을 사용하여 수행할 수도 있습니다. Azure 포털을 사용하여 Node.js 응용 프로그램을 배포하는 단계에 대해서는 <a href="/ko-kr/develop/nodejs/tutorials/create-a-website-(mac)/">Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포</a>를 참조하세요.

> [WACOM.NOTE] 처음으로 Azure 웹 사이트를 만든 경우 Azure 포털을 사용하여 이 응용 프로그램을 배포해야 합니다.

###Azure 크로스 플랫폼 명령줄 인터페이스 설치

Azure 크로스 플랫폼 명령줄 인터페이스(xplat-cli)에서 Azure 서비스에 대한 관리 작업을 수행할 수 있습니다. 개발 환경에서 xplat-cli를 아직 설치하여 구성하지 않은 경우 [Azure 크로스 플랫폼 명령줄 인터페이스 설치 및 구성][xplatcli]의 지침을 참조하세요.

###Azure 웹 사이트 만들기

1. 명령줄에서 **tasklist** 디렉터리로 변경합니다.

2. 다음 명령을 사용하여 새 Azure 웹 사이트를 만듭니다. 'myuniquesitename'을 웹 사이트의 고유한 사이트 이름으로 바꿉니다. 이 값은 생성되는 웹 사이트의 URL 일부로 사용됩니다.

		azure site create myuniquesitename --git
		
	사이트가 위치할 데이터 센터를 선택하라는 메시지가 표시됩니다. 현재 지역에 지리적으로 가까운 데이터 센터를 선택하십시오.
	
	'--git' 매개 변수는 Git 리포지토리가 없는 경우 **tasklist** 폴더에 로컬로 만듭니다. 또한 Azure에 응용 프로그램을 게시하는 데 사용되는 'azure'라는 [Git remote](영문)를 만듭니다. Azure에서 노드 응용 프로그램을 호스트하는 데 사용하는 설정이 포함된 [iisnode.yml]이 생성됩니다. 마지막으로 .git 게시에서 node-modules 폴더를 제외하는 .gitignore 파일도 만들어집니다.
	
	> [WACOM.NOTE] 이미 Git 리포지토리를 포함한 디렉터리에서 이 명령을 실행한 경우 디렉터리가 다시 초기화되지는 않습니다.
	
	> [WACOM.NOTE] '--git' 매개 변수가 생략되었지만 디렉터리에 Git 리포지토리가 포함된 경우 'azure' 원격이 계속 생성됩니다.
	
	이 명령이 완료되면 다음과 유사한 출력이 표시됩니다. **Created website at**으로 시작하는 줄에 웹 사이트 URL이 있습니다.

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

	> [WACOM.NOTE> 구독에서 Azure 웹 사이트를 처음 만드는 경우 포털을 사용하여 웹 사이트를 만들라는 메시지를 받게 됩니다. 자세한 내용은 <a href="/ko-kr/develop/nodejs/tutorials/create-a-website-(mac)/">Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포</a>를 참조하세요.

###MONGODB_URI 환경 변수 설정

MongoDB 인스턴스에 대한 연결 문자열을 MONGODB_URI 환경 변수에서 사용할 수 있어야 합니다. 웹 사이트에 대한 이 값을 설정하려면 다음 명령을 사용하세요.

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

웹 사이트에 대한 새로운 응용 프로그램 설정이 만들어지며, 이 설정은 웹 사이트에서 읽는 MONGODB_URI 환경 변수를 채우는 데 사용됩니다. 'mymongodb.cloudapp.net'의 값을 MongoDB가 설치된 가상 컴퓨터의 이름으로 바꾸십시오.

###응용 프로그램 게시

1. 터미널 창에서 아직 이동하지 않은 경우 **tasklist** 디렉터리로 변경합니다.

2. 다음 명령을 사용하여 로컬 Git 리포지토리로 파일을 커밋합니다.

		git add .
		git commit -m "adding files"

3. 최신 Git 리포지토리 변경 내용을 Azure 웹 사이트로 푸시하는 경우 **master**가 웹 사이트 내용에 사용되므로 대상 분기가 되도록 지정해야 합니다.

		git push azure master
	
	You will see output similar to the following. As the deployment takes place Azure will download all npm modules. 

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
 
4. 푸시 작업이 완료되면 'azure site browse' 명령을 사용하여 웹 사이트로 이동하여 응용 프로그램을 봅니다.

##다음 단계

이 문서의 단계에서는 MongoDB를 사용하여 정보를 저장하는 프로세스를 설명하지만 Azure 테이블 서비스도 사용할 수 있습니다. 자세한 내용은 [Azure 테이블 서비스를 사용하는 Node.js 웹 응용 프로그램]을 참조하세요.

MongoLab에서 제공하는 MongoDB의 호스트된 인스턴스를 사용하는 방법을 알아보려면 [Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 Node.js 응용 프로그램 만들기]를 참조하세요(/ko-kr/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

MongoDB를 보호하는 방법을 알아보려면 [MongoDB 보안][mongosecurity]을 참조하세요.

##추가 리소스

[Mac 및 Linux용 Azure 명령줄 도구]    
[Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포]    
[Git를 사용하여 Azure 웹 사이트에 게시]    

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /ko-kr/pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Mac 및 Linux용 Azure 명령줄 도구]: /ko-kr/develop/nodejs/how-to-guides/command-line-tools/
[Azure 개발자 센터]: /ko-kr/develop/nodejs/
[Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포]: /ko-kr/develop/nodejs/tutorials/create-a-website-(mac)/
[Git를 사용하여 Azure 웹 사이트에 게시]: /ko-kr/develop/nodejs/common-tasks/publishing-with-git/
[Linux 가상 컴퓨터에 MongoDB 설치]: /ko-kr/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[Azure 테이블 서비스를 사용하는 Node.js 웹 응용 프로그램]: /ko-kr/develop/nodejs/tutorials/web-site-with-storage/
[node-mongo-finished]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_add_item.png
[node-mongo-list-items]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://manage.windowsazure.com/
[mongodocs]: http://docs.mongodb.org/manual/
[xplatcli]: /ko-kr/documentation/articles/xplat-cli/

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[sshazure]: http://www.windowsazure.com/ko-kr/documentation/articles/linux-use-ssh-key/
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/ 

<!--HONumber=35.2-->
