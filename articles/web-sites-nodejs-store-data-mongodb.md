<properties linkid="develop-node-website-with-mongodb-mac" urlDisplayName="Web site with MongoDB" pageTitle="Node.js web site with MongoDB on a VM - Azure tutorial" metaKeywords="Azure tutorial MongoDB, MongoDB store data, access data MongoDB Node, Azure Node app" description="A tutorial that teaches you how to use MongoDB to store and access data from a Node application hosted on Azure." metaCanonical="http://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="Node.js" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="" solutions="" writer="" manager="" editor="" />

가상 컴퓨터에 MongoDB를 사용하는 Azure의 Node.js 응용 프로그램 만들기
=====================================================================

이 자습서에서는 Azure 가상 컴퓨터에 호스트되는 [MongoDB](http://www.mongodb.org)를 사용하여 데이터를 저장하고, Azure 웹 사이트에 호스트되는 [노드](http://nodejs.org) 응용 프로그램의 데이터에 액세스하는 방법을 보여 줍니다. [MongoDB](http://www.mongodb.org)는 대중적인 오픈 소스의 고성능 NoSQL 데이터베이스입니다.

다음 내용을 배웁니다.

-   VM 디포에서 Ubuntu 및 MongoDB를 실행하는 가상 컴퓨터 설정 방법
-   노드 응용 프로그램에서 MongoDB에 액세스하는 방법
-   Azure용 교차 플랫폼 도구를 사용하여 Azure 웹 사이트를 만드는 방법

이 자습서를 따라 작업을 만들고, 검색하고, 완료할 수 있는 간단한 웹 기반 작업 관리 응용 프로그램을 구축합니다. 이 작업은 MongoDB에 저장됩니다.

> [WACOM.NOTE] 이 자습서에서는 가상 컴퓨터에 설치된 MongoDB의 인스턴스를 사용합니다. MongoLabs에서 제공하는 호스트된 MongoDB 인스턴스를 사용하는 경우 [MongoLab 추가 기능을 사용하여 MongoDB가 포함된 Azure의 Node.js 응용 프로그램 만들기](/en-us/develop/nodejs/tutorials/website-with-mongodb-mongolab/)를 참조하십시오.

이 자습서의 프로젝트 파일은 **tasklist**라는 디렉터리에 저장되며 완료된 응용 프로그램은 다음과 유사하게 보입니다.

![빈 tasklist를 표시하는 웹 페이지](./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png)

> [WACOM.NOTE] 아래의 많은 단계에서는 명령줄 사용을 언급합니다. 이러한 단계의 경우 **Windows PowerShell**(Windows) 또는 **Bash**(Unix Shell)와 같은 운영 체제의 명령줄을 사용하십시오. OS X 시스템에서는 터미널 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

필수 조건
---------

이 자습서의 단계에서는 최신 버전의 [Node.js](http://nodejs.org)를 개발 환경에서 사용해야 합니다.

또한 [Git](http://git-scm.com)는 Azure 웹 사이트에 응용 프로그램을 배포하는 데 사용되므로, 개발 환경의 명령줄에서 사용할 수 있어야 합니다.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

가상 컴퓨터 만들기
------------------

[MongoDB 설치 가이드](http://docs.mongodb.org/manual/installation/)를 따라서 새로운 VM을 만든 후 MongoDB를 설치할 수 있지만 이 작업의 대부분은 이미 커뮤니티에서 수행되었으며 VM 디포에서 사용 가능합니다. 다음 단계에서는 Mongo DB를 이미 설치하고 구성한 VM 디포의 이미지를 사용하는 방법을 설명합니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인하고, **가상 컴퓨터**, **이미지**, **VM 디포**를 차례로 선택합니다.

    ![VM 디포를 선택하는 스크린샷](./media/web-sites-nodejs-store-data-mongodb/browsedepot.png)

2.  MongoDB를 포함하는 이미지를 선택합니다. 이 경우, Ubuntu를 선택하여 Ubuntu Linux 배포를 기반으로 이미지만으로 목록의 범위를 좁혔습니다. 마지막으로, 확정된 Ubuntu 이미지에서 MongoDB v2.2.3을 선택했습니다.

    ![확정된 ubuntu 이미지에서 mongodb v2.2.3이 선택된 스크린샷](./media/web-sites-nodejs-store-data-mongodb/selectimage.png)

    > [WACOM.NOTE] 이미지에 대한 모든 정보를 보려면 **자세히**를 선택해야 합니다. 일부 이미지의 경우 이미지를 사용하여 VM을 만든 후 추가 구성을 해야 할 수 있습니다.

    페이지 아래의 화살표를 클릭하여 다음 화면으로 진행합니다.

3.  이 이미지용 VHD를 저장하는 데 사용할 지역 및 저장소 계정을 선택합니다. 확인 표시를 클릭하여 계속합니다.

    ![저장소 계정을 선택하는 스크린샷](./media/web-sites-nodejs-store-data-mongodb/storageaccount.png)

    > [WACOM.NOTE] VM 디포에서 지정된 저장소 계정으로 이미지를 복사하는 복사 프로세스가 시작됩니다. 15분 이상의 시간이 걸릴 수 있습니다.

4.  이미지의 상태가 **등록 보류 중**으로 변경되면 **등록**을 선택하고 새 이미지의 식별 이름을 입력합니다. 확인 표시를 클릭하여 계속합니다.

    ![이미지를 등록하는 스크린샷](./media/web-sites-nodejs-store-data-mongodb/register.png)

5.  이미지의 상태가 **사용할 수 있음**으로 변경되면 **+ 새로 만들기**, **가상 컴퓨터**, **갤러리에서**를 차례로 선택합니다. **이미지 선택** 메시지가 표시되면 **내 이미지**를 선택한 후 이전 단계에서 만든 이미지를 선택합니다. 화살표를 클릭하여 계속합니다.

    ![이미지의 스크린샷](./media/web-sites-nodejs-store-data-mongodb/myimages.png)

6.  VM, 크기, 사용자 이름을 제공합니다. 화살표를 클릭하여 계속합니다.

    ![VM 이름, 사용자 이름 등에 대한 스크린샷](./media/web-sites-nodejs-store-data-mongodb/vmname.png)

    > [WACOM.NOTE] 이 자습서에서는 SSH를 사용하여 원격으로 VM에 연결할 필요는 없습니다. SSH와 함께 인증서를 사용하는 데 익숙지 않은 경우 **암호 사용**을 선택하고 암호를 제공합니다.
    >
    > Azure의 Linux VM 및 SSH 사용에 대한 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](http://www.windowsazure.com/ko-kr/documentation/articles/linux-use-ssh-key/)을 참조하십시오.

7.  새로운 또는 기존 클라우드 서비스를 사용할지 여부와 VM을 만들 지역을 선택합니다. 화살표를 클릭하여 계속합니다.

    ![VM 구성의 스크린샷](./media/web-sites-nodejs-store-data-mongodb/vmconfig.png)

8.  VM의 추가 끝점을 설정합니다. 이 VM의 MongoDB에 액세스하고 있으므로 다음 정보를 사용하여 새 끝점을 추가합니다.

    -   이름 - MongoDB
    -   프로토콜 - TCP
    -   공용 포트 - 27017
    -   개인 포트 - 27017

    MongoDB 웹 포털을 노출하려면 다음 정보를 사용하여 다른 끝점을 추가합니다.

    -   이름 - MongoDBWeb
    -   프로토콜 - TCP
    -   공용 포트 - 28017
    -   개인 포트 - 28017

    마지막으로, 확인 표시를 선택하여 가상 컴퓨터를 구성합니다.

    ![끝점 구성의 스크린샷](./media/web-sites-nodejs-store-data-mongodb/endpoints.png)

9.  가상 컴퓨터 상태가 **실행 중**으로 변경되면 웹 브라우저에서 **http://&lt;YourVMDNSName\>.cloudapp.net:28017/**을 열어 MongoDB가 실행되고 있는지 확인합니다. 페이지 아래에 다음과 유사한 서비스에 대한 정보를 표시하는 로그가 나타나야 합니다.

         Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
            18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
            18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
            18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
            18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
            18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
            18:57:16 [initandlisten] recover : no journal files present, no recovery needed
            18:57:17 [initandlisten] waiting for connections on port 27017

    로그에서 오류를 표시하는 경우 문제 해결 단계에 대해서는 [MongoDB 설명서](http://docs.mongodb.org/manual/)(영문)를 참조하십시오.

설치 모듈 및 스캐폴딩 생성
--------------------------

이 섹션에서는 개발 환경에서 새로운 노드 응용 프로그램을 만들고 npm을 사용하여 모듈 패키지를 추가합니다. 작업 목록 응용 프로그램의 경우 [Express](http://expressjs.com) 및 [Mongoose](http://mongoosejs.com) 모듈을 사용합니다. Express 모듈은 노드에 대한 모델 보기 컨트롤러 프레임워크를 제공하며, Mongoose는 MongoDB와 통신하기 위한 드라이버입니다.

### express 설치 및 스캐폴딩 생성

1.  명령줄에서 **tasklist** 디렉터리로 변경합니다. **tasklist** 디렉터리가 없으면 디렉터리를 만듭니다.

    > [WACOM.NOTE] 이 자습서에서는 **tasklist** 폴더를 사용합니다. 경로 의미 체계가 운영 체제마다 다르므로, 폴더의 전체 경로는 생략되었습니다. 로컬 파일 시스템에 쉽게 액세스할 수 있는 위치에 이 폴더를 만들어야 합니다(예: **\~/node/tasklist** 또는 **c:\\node\\tasklist**).

2.  다음 명령을 입력하여 express를 설치합니다.

    npm install express -g

    > [WACOM.NOTE] 일부 운영 체제에서 '-g' 매개 변수를 사용하면 ***Error: EPERM, chmod '/usr/local/bin/express'*** 오류 메시지와 관리자 계정으로 실행하라는 요청이 표시될 수 있습니다. 이런 경우 `sudo` 명령을 사용하여 더 높은 권한 수준으로 npm을 실행하십시오.

    이 명령의 출력은 다음과 유사합니다.

         express@3.5.0 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express                                        
         ├── methods@0.1.0                                                                                              
         ├── merge-descriptors@0.0.2                                                                                    
         ├── fresh@0.2.2                                                                                                
         ├── cookie-signature@1.0.3                                                                                     
         ├── range-parser@1.0.0                                                                                         
         ├── debug@0.7.4                                                                                                
         ├── buffer-crc32@0.2.1                                                                                         
         ├── cookie@0.1.1                                                                                               
         ├── mkdirp@0.3.5                                                                                               
         ├── commander@1.3.2 (keypress@0.1.0)                                                                           
         ├── send@0.2.0 (mime@1.2.11)                                                                                   
         └── connect@2.14.1 (response-time@1.0.0, pause@0.0.1, connect-timeout@1.0.0, method-override@1.0.0, vhost@1.0.0, static-favicon@1.0.0, morgan@1.0.0, serve-static@1.0.2, basic-auth-connect@1.0.0, qs@0.6.6, bytes@0.2.1, raw-body@1.1.3, errorhandler@1.0.0, cookie-parser@1.0.1, express-session@1.0.2, compression@1.0.0, csurf@1.0.0, serve-index@1.0.1, multiparty@2.2.0)                                                                              

    > [WACOM.NOTE] express 모듈을 설치할 때 사용되는 '-g' 매개 변수는 이 모듈을 전역으로 설치합니다. ***express*** 명령에 액세스하여 추가 경로 정보에 입력하지 않고도 웹 사이트 스캐폴딩을 생성할 수 있도록 이 작업을 수행합니다.

3.  이 응용 프로그램에 사용할 스캐폴딩을 만들려면 다음과 같은 **express** 명령을 사용합니다.

    express

    이 명령의 출력은 다음과 유사합니다.

            create : .
            create : ./package.json
            create : ./app.js
            create : ./public
            create : ./public/stylesheets
            create : ./public/stylesheets/style.css
            create : ./public/images
            create : ./public/javascripts
            create : ./routes
            create : ./routes/index.js
            create : ./routes/user.js
            create : ./views
            create : ./views/layout.jade
            create : ./views/index.jade

            install dependencies:
              $ cd . && npm install

            run the app:
              $ node app

    이 명령이 완료되면 **tasklist** 디렉터리에 새 디렉터리와 파일 몇 개가 생성됩니다.

### 추가 모듈 설치

**package.json** 파일은 **express** 명령으로 만들어진 파일 중 하나입니다. 이 파일에는 Express 응용 프로그램에 필요한 추가 모듈 목록이 포함되어 있습니다. 나중에 이 응용 프로그램을 Azure 웹 사이트에 배포하는 경우 이 파일을 사용하여 응용 프로그램을 지원하기 위해 Azure에 설치해야 할 모듈을 결정합니다.

1.  **tasklist** 폴더에서 다음을 사용하여 **package.json** 파일에 설명된 모듈을 설치합니다.

         npm install

    이 명령의 출력은 다음과 유사합니다.

         express@3.5.0 node_modules\express                                                                            
         ├── methods@0.1.0                                                                                             
         ├── merge-descriptors@0.0.2                                                                                   
         ├── cookie-signature@1.0.3                                                                                    
         ├── fresh@0.2.2                                                                                               
         ├── debug@0.7.4                                                                                               
         ├── range-parser@1.0.0                                                                                        
         ├── buffer-crc32@0.2.1                                                                                        
         ├── cookie@0.1.1                                                                                              
         ├── mkdirp@0.3.5                                                                                              
         ├── commander@1.3.2 (keypress@0.1.0)                                                                          
         ├── send@0.2.0 (mime@1.2.11)                                                                                  
         └── connect@2.14.1 (response-time@1.0.0, pause@0.0.1, connect-timeout@1.0.0, method-override@1.0.0, vhost@1.0., static-favicon@1.0.0, qs@0.6.6, morgan@1.0.0, basic-auth-connect@1.0.0, serve-static@1.0.2, bytes@0.2.1, rawbody@1.1.3, errorhandler@1.0.0, cookie-parser@1.0.1, csurf@1.0.0, compression@1.0.0, express-session@1.0.2, seve-index@1.0.1, multiparty@2.2.0)                                                                             
                                                                                                                  
         jade@1.3.0 node_modules\jade                                                                                  
         ├── character-parser@1.2.0                                                                                    
         ├── commander@2.1.0                                                                                           
         ├── mkdirp@0.3.5                                                                                              
         ├── monocle@1.1.51 (readdirp@0.2.5)                                                                           
         ├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)                                            
         ├── with@3.0.0 (uglify-js@2.4.12)                                                                             
         └── constantinople@2.0.0 (uglify-js@2.4.12)                                                                   

    Express 응용 프로그램에 사용되는 기본 모듈이 모두 설치됩니다.

2.  이제 다음 명령을 입력하여 Mongoose 모듈을 로컬에 설치하고 이에 대한 항목을 **package.json** 파일에 저장합니다.

         npm install mongoose --save

    이 명령의 출력은 다음과 유사합니다.

         mongoose@3.8.8 node_modules\mongoose                     
         ├── regexp-clone@0.0.1                                   
         ├── muri@0.3.1                                           
         ├── sliced@0.0.5                                         
         ├── hooks@0.2.1                                          
         ├── mpath@0.1.1                                          
         ├── mpromise@0.4.3                                       
         ├── ms@0.1.0                                             
         ├── mquery@0.5.3 (debug@0.7.4)                           
         └── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)          

    > [WACOM.NOTE] C++ bson 파서 설치에 대한 메시지는 무시할 수 있습니다.

노드 응용 프로그램에서 MongoDB 사용
-----------------------------------

이 섹션에서는 작업에 대한 모듈을 포함하는 **task.js** 파일을 추가하여 **express** 명령으로 만들어진 기본 응용 프로그램을 확장합니다. 또한 해당 모델을 활용하도록 기존 **app.js**를 수정하고 새 **tasklist.js** 컨트롤러 파일을 만들겠습니다.

### 모델 만들기

1.  **tasklist** 디렉터리에서 **models**라는 새로운 디렉터리를 만듭니다.

2.  **models** 디렉터리에서 **task.js**라는 새 파일을 만듭니다. 이 파일에는 응용 프로그램에서 만든 작업 모델이 포함됩니다.

3.  **task.js** 파일의 시작 부분에 필수 라이브러리를 참조하는 다음 코드를 추가합니다.

         var mongoose = require('mongoose'),
             Schema = mongoose.Schema;

4.  그런 다음, 모델을 정의하고 내보내는 코드를 추가합니다. 이 모델은 MongoDB 데이터베이스와의 조작을 수행하는 데 사용됩니다.

         var TaskSchema = new Schema({
             itemName      : String,
             itemCategory  : String,
             itemCompleted : { type: Boolean, default: false },
             itemDate      : { type: Date, default: Date.now }
         });

         module.exports = mongoose.model('TaskModel', TaskSchema);

5.  **task.js** 파일을 저장하고 닫습니다.

### 컨트롤러 만들기

1.  **tasklist/routes** 디렉터리에서 **tasklist.js**라는 새 파일을 만들고 텍스트 편집기에서 엽니다.

2.  **tasklist.js**에 다음 코드를 추가합니다. 이렇게 하면 **task.js**에 정의된 mongoose 모듈 및 작업 모델이 로드됩니다. TaskList 함수는 **connection**을 기반으로 MongoDB 서버에 대한 연결을 만드는 데 사용됩니다.

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

3.  **showTasks**, **addTask** 및 **completeTasks**에 사용된 메서드를 추가하여 **tasklist.js** 파일에 계속 추가합니다.

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

1.  **tasklist.js** 파일을 저장합니다.

### app.js 수정

1.  **tasklist** 디렉터리에서 **app.js** 파일을 텍스트 편집기로 엽니다. 이 파일은 앞에서 **express** 명령을 실행하여 만들었습니다.

2.  아래 코드를 **app.js** 파일의 시작 부분에 추가합니다. MongoDB 서버에 대한 연결 문자열로 **TaskList**가 초기화됩니다.

         var TaskList = require('./routes/tasklist');
         var taskList = new TaskList(process.env.MONGODB_URI);

    두 번째 줄에 유의합니다. mongo 인스턴스에 대한 연결 정보가 포함되어 있고 나중에 구성할 환경 변수에 액세스합니다. 개발을 위해 로컬 mongo 인스턴스를 실행하는 경우 일시적으로 이 값을 process.env.MONGODB\_URI 대신 "localhost"로 설정할 수 있습니다.

3.  `app.get`으로 시작하는 줄을 찾아 다음 줄로 바꿉니다.

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

    **tasklist.js**에 정의된 함수가 경로로 추가됩니다.

4.  **app.js** 파일을 저장합니다.

### 인덱스 보기 수정

1.  디렉터리를 **views** 디렉터리로 변경하고 **index.jade** 파일을 텍스트 편집기에서 엽니다.

2.  **index.jade** 파일 내용을 아래 코드로 바꿉니다. 이렇게 하면 기존 작업을 표시하는 데 사용되는 보기와 새 작업을 추가하고 기존 작업을 완료로 표시하는 데 사용되는 양식이 정의됩니다.

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

3.  **index.jade** 파일을 저장하고 닫습니다.

Azure에 응용 프로그램 배포
--------------------------

이 섹션의 단계에서는 Azure 명령줄 도구를 사용하여 새 Azure 웹 사이트를 만든 후 Git를 사용하여 응용 프로그램을 배포합니다. 이러한 단계를 수행하려면 Azure 구독이 있어야 합니다.

> [WACOM.NOTE] 이러한 단계는 Azure 포털을 사용하여 수행할 수도 있습니다. Azure 포털을 사용하여 Node.js 응용 프로그램을 배포하는 단계에 대해서는 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/)를 참조하십시오.

> [WACOM.NOTE] 처음으로 Azure 웹 사이트를 만든 경우 Azure 포털을 사용하여 이 응용 프로그램을 배포해야 합니다.

### Azure 크로스 플랫폼 명령줄 인터페이스 설치

Azure 크로스 플랫폼 명령줄 인터페이스(xplat-cli)에서 Azure 서비스에 대한 관리 작업을 수행할 수 있습니다. 개발 환경에서 xplat-cli를 아직 설치하여 구성하지 않은 경우 [Azure 크로스 플랫폼 명령줄 인터페이스 설치 및 구성](/ko-kr/documentation/articles/xplat-cli/)의 지침을 참조하십시오.

### Azure 웹 사이트 만들기

1.  명령줄에서 **tasklist** 디렉터리로 변경합니다.

2.  다음 명령을 사용하여 새 Azure 웹 사이트를 만듭니다. 'myuniquesitename'을 웹 사이트의 고유한 사이트 이름으로 바꿉니다. 이 값은 생성되는 웹 사이트의 URL 일부로 사용됩니다.

         azure site create myuniquesitename --git

    사이트가 위치할 데이터 센터를 선택하라는 메시지가 표시됩니다. 현재 지역에 지리적으로 가까운 데이터 센터를 선택하십시오.

    `--git` 매개 변수는 Git 리포지토리가 없는 경우 **tasklist** 폴더에 로컬로 만듭니다. 또한 Azure에 응용 프로그램을 게시하는 데 사용되는 'azure'라는 [Git remote](http://git-scm.com/docs/git-remote)(영문)를 만듭니다. Azure에서 노드 응용 프로그램을 호스트하는 데 사용하는 설정이 포함된 [iisnode.yml](https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml)이 만들어집니다. 마지막으로 .git 게시에서 node-modules 폴더를 제외하는 .gitignore 파일도 만들어집니다.

    > [WACOM.NOTE] 이미 Git 리포지토리를 포함하는 디렉터리에서 이 명령을 실행한 경우 디렉터리가 다시 초기화되지는 않습니다.

    > [WACOM.NOTE] '--git' 매개 변수가 생략된 경우 디렉터리에 Git 리포지토리가 포함되며 'azure' 원격이 계속 만들어집니다.

    이 명령이 완료되면 다음과 유사한 출력이 표시됩니다. **Created web site at**으로 시작하는 줄에 웹 사이트 URL이 있습니다.

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

    > [WACOM.NOTE\> 구독에서 Azure 웹 사이트를 처음 만드는 경우 포털을 사용하여 웹 사이트를 만들라는 메시지를 받게 됩니다. 자세한 내용은 [Node.js를 만들어 Azure 웹 사이트에 배포](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/)를 참조하십시오.

### MONGODB\_URL 환경 변수 설정

MongoDB 인스턴스에 대한 연결 문자열을 MONGODB\_URI 환경 변수에서 사용할 수 있어야 합니다. 웹 사이트에 대한 이 값을 설정하려면 다음 명령을 사용하십시오.

    azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

웹 사이트에 대한 새로운 응용 프로그램 설정이 만들어지며, 이 설정은 웹 사이트에서 읽는 MONGODB\_URI 환경 변수를 채우는 데 사용됩니다. 'mymongodb.cloudapp.net'의 값을 MongoDB가 설치된 가상 컴퓨터의 이름으로 바꾸십시오.

### 응용 프로그램 게시

1.  터미널 창에서 아직 이동하지 않은 경우 **tasklist** 디렉터리로 변경합니다.

2.  다음 명령을 사용하여 로컬 Git 리포지토리로 파일을 커밋합니다.

         git add .
         git commit -m "adding files"

3.  최신 Git 리포지토리 변경 내용을 Azure 웹 사이트로 푸시하는 경우 **master**가 웹 사이트 내용에 사용되므로 대상 분기가 되도록 지정해야 합니다.

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

4.  푸시 작업이 완료되면 `azure site browse` 명령을 사용하여 웹 사이트로 이동하여 응용 프로그램을 봅니다.

다음 단계
---------

이 문서의 단계에서는 MongoDB를 사용하여 정보를 저장하는 프로세스를 설명하지만 Azure 테이블 서비스도 사용할 수 있습니다. 자세한 내용은 [Azure 테이블 서비스를 사용하는 Node.js 웹 응용 프로그램](/en-us/develop/nodejs/tutorials/web-site-with-storage/)을 참조하십시오.

MongoLab에서 제공하는 MongoDB의 호스트된 인스턴스를 사용하는 방법을 알아보려면 [MongoLab 추가 기능을 사용하여 MongoDB가 포함된 Azure의 Node.js 응용 프로그램 만들기](/en-us/develop/nodejs/tutorials/website-with-mongodb-mongolab/)를 참조하십시오.

MongoDB를 보호하는 방법을 알아보려면 [MongoDB 보안](http://docs.mongodb.org/manual/security/)을 참조하십시오.

추가 리소스
-----------

[Mac 및 Linux용 Azure 명령줄 도구](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/)
 [Git로 Azure 웹 사이트에 게시](/en-us/develop/nodejs/common-tasks/publishing-with-git/)

