<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on MongoLab - Azure" metaKeywords="" description="Learn how to create a Node.js Azure Website that connects to a MongoDB instance hosted on MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="eric@mongolab.com"></tags>

# Azure에서 MongoLab 추가 기능을 사용하는 MongoDB로 Node.js 응용 프로그램 만들기

*작성자: Eric Sedor, MongoLab*

모험심이 많은 사용자 여러분, 반갑습니다! MongoDB-as-a-Service에 오신 것을 환영합니다. 이 자습서에서는 다음 작업을 수행합니다.

1.  [데이터베이스 프로비전][데이터베이스 프로비전] - Azure 저장소 [MongoLab][MongoLab] 추가 기능은 Azure 클라우드에 호스트되고 MongoLab 클라우드 데이터베이스 플랫폼에서 관리되는 MongoDB 데이터베이스를 제공합니다.
2.  [앱 만들기][앱 만들기] - 작업 목록을 유지 관리하기 위한 간단한 INode.js 앱입니다.
3.  [앱 배포][앱 배포] - 몇 가지 구성 후크를 연결하여 원활하게 코드를 푸시합니다.
4.  [데이터베이스 관리][데이터베이스 관리] - 마지막으로 쉽게 데이터를 검색하고, 시각화하고, 수정할 수 있는 MongoLab의 웹 기반 데이터베이스 관리 포털을 보여 줍니다.

언제든 이 자습서를 보면서 질문할 사항이 있으면 [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>]으로 전자 메일을 보내 주십시오.

계속하기 전에 다음 제품이 설치되어 있는지 확인하십시오.

-   [Node.js][Node.js] 버전 0.8.14+

-   [Git][Git]

[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

## 빠른 시작

Azure Store에 어느 정도 익숙한 경우 이 섹션을 사용하여 빠른 시작을 수행합니다. 그렇지 않은 경우 아래의 [데이터베이스 프로비전][데이터베이스 프로비전]을 계속 진행합니다.

1.  Azure Store를 엽니다.
    ![Store][Store]
2.  MongoLab 추가 기능을 클릭합니다.
    ![MongoLab][1]
3.  추가 기능 목록에서 MongoLab 추가 기능을 클릭하고 **연결 정보**를 클릭합니다.
    ![ConnectionInfoButton][ConnectionInfoButton]
4.  MONGOLAB\_URI를 클립보드로 복사합니다.
    ![ConnectionInfoScreen][ConnectionInfoScreen]
    **이 URI에는 데이터베이스 사용자 이름과 암호가 포함되어 있습니다. 중요한 정보로 처리하고 공유하지 마세요.**
5.  Azure 웹 응용 프로그램 구성 메뉴의 연결 문자열 목록에 값을 추가합니다.
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]
6.  **이름**에 MONGOLAB\_URI를 입력합니다.
7.  **값**에 이전 섹션에서 얻은 연결 문자열을 붙여넣습니다.
8.  유형 드롭다운에서 기본값 **SQLAzure** 대신 **사용자 지정**을 선택합니다.
9.  `npm install mongoose`를 실행하여 MongoDB 노드 드라이버인 M ongoose를 가져옵니다.
10. 코드에서 후크를 설정하여 환경 변수의 MongoLab 연결 URI를 가져와 연결합니다.

        var mongoose = require('mongoose');  
        ...
        var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
        ...
        mongoose.connect(connectionString);

참고: Azure는 **CUSTOMCONNSTR\_** 접두사를 원래 선언된 연결 문자열에 추가합니다. 이러한 이유로 코드에서 **MONGOLAB\_URI** 대신 **CUSTOMCONNSTR\_MONGOLAB\_URI.**를 참조합니다.

이제, 전체 자습서에서...

## <a name="provision"></a>데이터베이스 프로비전

[WACOM.INCLUDE [howto-provision-mongolab][howto-provision-mongolab]]

## <a name="create"></a>앱 만들기

이 섹션에서는 개발 환경을 설정하고 Node.js, Express 및 MongoDB를 사용하여 기본 작업 목록 웹 응용 프로그램에 대한 코드를 배포합니다. [Express][Express]는 노드용 보기 컨트롤러 프레임워크를 제공하고 [Mongoose][Mongoose]는 노드에서 MongoDB와 통신하기 위한 드라이버입니다.

### 설정

#### 스캐폴딩 생성 및 모듈 설치

1.  명령줄에서 **tasklist** 디렉터리를 만들어 이동합니다. 이 디렉터리가 프로젝트 디렉터리입니다.
2.  다음 명령을 입력하여 express를 설치합니다.

        npm install express -g

    `-g`는 전역 모드를 나타내며 디렉터리 경로를 지정하지 않고 **express** 모듈을 사용할 수 있도록 하는 데 이 모드를 사용합니다. **Error: EPERM, chmod '/usr/local/bin/express'**를 수신하면 **sudo**를 사용하여 더 높은 권한 수준에서 npm을 실행합니다.

    이 명령의 출력은 다음과 유사하게 표시됩니다.

        express@3.3.4 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
        ├── methods@0.0.1
        ├── fresh@0.1.0
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── debug@0.7.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── send@0.1.3 (mime@1.2.9)
        └── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

3.  이 응용 프로그램에 사용할 스캐폴딩을 만들려면 다음과 같은 **express** 명령을 사용합니다.

    express

    이 명령의 출력은 다음과 유사하게 표시됩니다.

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
        create : ./views
        create : ./views/layout.jade
        create : ./views/index.jade

        dont forget to install dependencies:
        $ cd . && npm install

    이 명령이 완료되면 **tasklist** 디렉터리에 몇 개의 새로운 디렉터리 및 파일이 생성되어야 합니다.

4.  다음을 입력하여 **package.json** 파일에 설명된 모듈을 설치합니다.

        npm install

    이 명령의 출력은 다음과 유사하게 표시됩니다.

        express@3.3.4 node_modules\express
        ├── methods@0.0.1
        ├── fresh@0.1.0
        ├── range-parser@0.0.4
        ├── cookie-signature@1.0.1
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── debug@0.7.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── send@0.1.3 (mime@1.2.9)
        └── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

        jade@0.33.0 node_modules\jade
        ├── character-parser@1.0.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── with@1.1.0 (uglify-js@2.3.6)
        ├── constantinople@1.0.1 (uglify-js@2.3.6)
        ├── transformers@2.0.1 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
        └── monocle@0.1.48 (readdirp@0.2.5)

    **package.json** 파일은 **express** 명령으로 만들어진 파일 중 하나입니다. 이 파일에는 Express 응용 프로그램에 필요한 추가 모듈 목록이 포함되어 있습니다. 나중에 이 응용 프로그램을 Azure 웹 사이트에 배포하는 경우 이 파일을 사용하여 응용 프로그램을 지원하기 위해 Azure에 설치해야 할 모듈을 결정합니다.

5.  이제 다음 명령을 입력하여 Mongoose 모듈을 로컬에 설치하고 이에 대한 항목을 **package.json** 파일에 저장합니다.

        npm install mongoose --save

    이 명령의 출력은 다음과 유사하게 표시됩니다.

        mongoose@3.6.15 node_modules\mongoose
        ├── regexp-clone@0.0.1
        ├── sliced@0.0.3
        ├── muri@0.3.1
        ├── hooks@0.2.1
        ├── mpath@0.1.1
        ├── ms@0.1.0
        ├── mpromise@0.2.1 (sliced@0.0.4)
        └── mongodb@1.3.11 (bson@0.1.9, kerberos@0.0.3)

    C++ bson 파서 설치에 대한 메시지는 무시해도 됩니다.

### 코드

환경 및 스캐폴딩이 준비되었으므로 작업 모델이 포함된 **task.js** 파일을 추가하여 **express** 명령으로 만들어진 기본 응용 프로그램을 확장하겠습니다. 또한 해당 모델을 활용하도록 기존 **app.js**를 수정하고 새 **tasklist.js** 컨트롤러 파일을 만들겠습니다.

#### 모델 만들기

1.  **tasklist** 디렉터리에서 **models**라는 새로운 디렉터리를 만듭니다.

2.  **models** 디렉터리에서 **task.js**라는 새 파일을 만듭니다. 이 파일에는 응용 프로그램에서 만든 작업 모델이 포함됩니다.

3.  아래 코드를 **task.js** 파일에 추가합니다.

        var mongoose = require('mongoose')
          , Schema = mongoose.Schema;

        var TaskSchema = new Schema({
            itemName      : String
          , itemCategory  : String
          , itemCompleted : { type: Boolean, default: false }
          , itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

4.  **task.js** 파일을 저장하고 닫습니다.

#### 컨트롤러 만들기

1.  **tasklist/routes** 디렉터리에서 **tasklist.js**라는 새 파일을 만들고 텍스트 편집기에서 엽니다.

2.  아래 코드를 **tasklist.js**에 추가합니다. 이렇게 하면 **task.js**에 정의된 mongoose 모듈 및 작업 모델이 로드됩니다. TaskList 함수가 **connection** 값을 기반으로 MongoDB 서버에 연결하는 데 사용되며, 메서드 **showTasks**, **addTask** 및 **completeTasks**를 제공합니다.

        var mongoose = require('mongoose')
          , task = require('../models/task.js');

        module.exports = TaskList;

        function TaskList(connection) {
          mongoose.connect(connection);
        }

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

3.  **tasklist.js** 파일을 저장합니다.

#### 인덱스 보기 수정

1.  **views** 디렉터리로 변경하고 **index.jade** 파일을 텍스트 편집기에서 엽니다.

2.  **index.jade** 파일 내용을 아래 코드로 바꿉니다. 이렇게 하면 기존 작업을 표시하는 데 사용되는 보기와 새 작업을 추가하고 기존 작업을 완료로 표시하는 데 사용되는 양식이 정의됩니다.

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
                input(name="item[name]", type="textbox")
            tr
              td Item Category: 
              td 
                input(name="item[category]", type="textbox")
          input(type="submit", value="Add item")

3.  **index.jade** 파일을 저장하고 닫습니다.

#### app.js 바꾸기

1.  **tasklist** 디렉터리에 있는 **app.js** 파일을 텍스트 편집기에서 엽니다. 이 파일은 앞에서 **express** 명령을 실행하여 만든 파일입니다.
2.  아래 코드를 **app.js** 파일의 시작 부분에 추가합니다. 이렇게 하면 MongoDB 서버의 연결 문자열로 **TaskList**를 초기화합니다.

        var TaskList = require('./routes/tasklist');
        var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

    두 번째 줄에 유의합니다. mongo 인스턴스에 대한 연결 정보가 포함되어 있고 나중에 구성할 환경 변수에 액세스합니다. 개발을 위해 로컬 mongo 인스턴스를 실행하는 경우 일시적으로 이 값을 `process.env.CUSTOMCONNSTR_MONGOLAB_URI` 대신 "localhost"로 설정할 수 있습니다.

3.  `app.get`으로 시작하는 줄을 찾아 다음 줄로 바꿉니다.

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    **tasklist.js**에 정의된 함수가 경로로 추가됩니다.

4.  **app.js** 파일을 저장합니다.

## <a name="deploy"></a>앱 배포

이제 응용 프로그램이 개발되었으므로 응용 프로그램을 호스트할 Azure 웹 사이트를 만들고, 웹 사이트를 구성하고, 코드를 배포해야 합니다. 이 섹션의 중심이 되는 것은 MongoDB 연결 문자열(URI) 사용입니다. 이 URI로 웹 사이트에서 환경 변수를 구성하여 URI가 코드와 별도로 유지되도록 할 것입니다. URI는 데이터베이스에 연결하기 위한 자격 증명을 포함하고 있으므로 중요한 정보로 처리되어야 합니다.

이 섹션의 단계에서는 Azure 명령줄 도구를 사용하여 새 Azure 웹 사이트를 만든 후 Git를 사용하여 응용 프로그램을 배포합니다. 이러한 단계를 수행하려면 Azure 구독이 있어야 합니다.

### Mac 및 Linux용 Azure 명령줄 도구 설치

명령줄 도구를 설치하려면 다음 명령을 사용합니다.

    npm install azure-cli -g

[Azure 개발자 센터][Azure 개발자 센터]에서 **Node.js용 Azure SDK**를 이미 설치한 경우 명령줄 도구도 이미 설치되어 있어야 합니다. 자세한 내용은 [Mac 및 Linux용 Azure 명령줄 도구][Mac 및 Linux용 Azure 명령줄 도구](영문)를 참조하십시오.

Azure 명령줄 도구는 주로 Mac 및 Linux 사용자를 위해 만들어졌으나 Node.js를 기반으로 하며 Node를 실행할 수 있는 모든 시스템에서 작동해야 합니다.

### 게시 설정 가져오기

Azure에 명령줄 도구를 사용하려면 먼저 구독에 대한 정보가 포함된 파일을 다운로드해야 합니다. 다음 단계에 따라 이 파일을 다운로드하여 가져옵니다.

1.  명령줄에서 다음 명령을 입력하여 브라우저를 시작하고 다운로드 페이지로 이동합니다. 메시지가 나타나면 구독과 관련된 계정으로 로그인합니다.

        azure account download

    ![다운로드 페이지][다운로드 페이지]

    파일 다운로드가 자동으로 시작됩니다. 그렇지 않은 경우 페이지 처음 부분에서 링크를 클릭하여 수동으로 파일을 다운로드할 수 있습니다.

2.  파일 다운로드가 완료되면 다음 명령을 사용하여 설정을 가져옵니다.

        azure account import <path-to-file>

    이전 단계에서 다운로드한 게시 설정 파일의 경로와 파일 이름을 지정합니다. 명령이 완료되면 다음과 유사한 출력이 표시됩니다.

        info:   Executing command account import
        info:   Found subscription: subscriptionname
        info:   Setting default subscription to: subscriptionname
        warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
        warn:   Remember to delete it now that it has been imported.
        info:   Account publish settings imported successfully
        info:   account import command OK

3.  게시 설정 파일은 가져오기가 완료되면 더 이상 필요하지 않고 Azure 구독에 대한 중요한 정보를 포함하고 있으므로 가져오기가 완료되면 게시 설정 파일을 삭제해야 합니다.

### 새 웹 사이트 만들기 및 코드 푸시

Azure에서는 매우 쉽게 웹 사이트를 만들 수 있습니다. 첫 번째 Azure 웹 사이트인 경우 포털을 사용해야 합니다. 하나 이상의 웹 사이트가 이미 있는 경우 7단계로 건너뜁니다.

1.  Azure 포털에서 **새로 만들기**를 클릭합니다.
    ![New][New]
2.  **계산 \> 웹 사이트 \> 빠른 생성**을 선택합니다.
    ![CreateSite][CreateSite]
3.  URL 접두사를 입력합니다. 원하는 이름을 선택합니다. 이름은 고유해야 합니다('mymongoapp'은 사용 불가능할 가능성이 높음).
4.  **웹 사이트 만들기**를 클릭합니다.
5.  웹 사이트 만들기가 완료되면 웹 사이트 목록에서 웹 사이트 이름을 클릭합니다. 웹 사이트 대시보드가 표시됩니다.
    ![WebSiteDashboard][WebSiteDashboard]
6.  **간략 상태**에서 **Git 게시 설정**을 클릭하고 원하는 Git 사용자 이름 및 암호를 입력합니다. 웹 사이트로 푸시할 때(9단계) 이 암호를 사용합니다.
7.  위 단계를 사용하여 웹 사이트를 만든 경우 다음 명령으로 프로세스가 완료됩니다. 그러나 Azure 웹 사이트가 이미 두 개 이상 있는 경우 위 단계를 건너뛰고 동일한 명령을 사용하여 새 웹 사이트를 만들 수 있습니다. **tasklist** 프로젝트 디렉터리에서:

        azure site create myuniquesitename --git  

    'myuniquesitename'을 웹 사이트의 고유한 사이트 이름으로 바꿉니다. 이 명령의 일부로 웹 사이트를 만들면 해당 사이트가 있을 데이터 센터에 대한 메시지가 표시됩니다. MongoLab 데이터베이스와 지리적으로 가까운 데이터 센터를 선택합니다.

    `--git` 매개 변수는 다음을 만듭니다.
    A. 로컬 Git 리포지토리가 없는 경우 **tasklist** 폴더에 만듭니다.
    A. 'azure'로 명명된 [Git remote][Git remote]를 만듭니다. 응용 프로그램을 Azure에 게시하는 데 사용됩니다.
    A. Azure에서 노드 응용 프로그램을 호스트하는 데 사용되는 설정이 포함된 [iisnode.yml][iisnode.yml] 파일을 만듭니다.
    A. node-modules 폴더가 .git에 게시되지 않도록 하는 .gitignore 파일을 만듭니다.

    이 명령이 완료되면 다음과 유사한 출력이 표시됩니다. **Created website at**으로 시작하는 줄에 웹 사이트 URL이 있습니다.

        info:   Executing command site create
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default web.config file
        info:   Creating a new web site
        info:   Created web site at  mongodbtasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
        info:   site create command OK

8.  다음 명령을 사용하여 추가한 다음 로컬 Git 리포지토리로 파일을 커밋합니다.

        git add .
        git commit -m "adding files"

9.  코드를 푸시합니다.

        git push azure master  

    최신 Git 리포지토리 변경 내용을 Azure 웹 사이트로 푸시하는 경우 **master**가 웹 사이트 내용에 사용되므로 대상 분기가 되도록 지정해야 합니다. 암호를 입력하라는 메시지가 나타나면 위의 웹 사이트에 대한 Git 게시를 설정할 때 만든 암호를 입력합니다.

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

코드에 있는 process.env.CUSTOMCONNSTR\_MONGOLAB\_URI를 기억하고 있습니까? 이 환경 변수를 MongoLab 데이터베이스 프로비전 중 Azure에 입력된 값으로 채우겠습니다.

#### MongoLab 연결 문자열 가져오기

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab][howto-get-connectioninfo-mongolab]]

#### 웹 사이트 환경 변수에 연결 문자열 추가

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab][howto-save-connectioninfo-mongolab]]

## 성공!

프로젝트 디렉터리에서 `azure site browse`를 실행하여 브라우저를 자동으로 열거나 브라우저를 열고 웹 사이트 URL(myuniquesite.azurewebsites.net)로 수동으로 이동합니다.

![빈 tasklist가 표시된 웹 페이지 ][빈 tasklist가 표시된 웹 페이지 ]

## <a name="manage"></a>데이터베이스 관리

[WACOM.INCLUDE [howto-access-mongolab-ui][howto-access-mongolab-ui]]

축하합니다. MongoLab 호스트 MongoDB 데이터베이스의 지원을 받는 Node.js 응용 프로그램이 시작되었습니다. 이제 MongoLab 데이터베이스가 있으므로 데이터베이스에 대한 질문이나 우려 사항 또는 MongoDB나 노드 드라이버 자체에 대한 지원을 [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>]에 문의할 수 있습니다. 여러분의 행운을 빕니다.

  [데이터베이스 프로비전]: #provision
  [MongoLab]: http://mongolab.com
  [앱 만들기]: #create
  [앱 배포]: #deploy
  [데이터베이스 관리]: #manage
  [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>]: mailto:support@mongolab.com
  [Node.js]: http://nodejs.org
  [Git]: http://git-scm.com
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
  [1]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
  [ConnectionInfoButton]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
  [howto-provision-mongolab]: ../includes/howto-provision-mongolab.md
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [Azure 개발자 센터]: /ko-kr/develop/nodejs/
  [Mac 및 Linux용 Azure 명령줄 도구]: /ko-kr/develop/nodejs/how-to-guides/command-line-tools/
  [다운로드 페이지]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
  [New]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
  [CreateSite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
  [Git remote]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [howto-get-connectioninfo-mongolab]: ../includes/howto-get-connectioninfo-mongolab.md
  [howto-save-connectioninfo-mongolab]: ../includes/howto-save-connectioninfo-mongolab.md
  [빈 tasklist가 표시된 웹 페이지 ]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
  [howto-access-mongolab-ui]: ../includes/howto-access-mongolab-ui.md
