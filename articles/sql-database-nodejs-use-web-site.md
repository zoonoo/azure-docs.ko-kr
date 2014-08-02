<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Web site with SQL Database" pageTitle="Node.js web site with SQL Database - Azure tutorial" metaKeywords="" description="Learn how to create a Node.js website that accesses a SQL Database and is deployed to Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="Node.js" title="Node.js Web Application using the Azure SQL Database" authors="" solutions="" manager="" editor="" />

Azure SQL 데이터베이스를 사용하는 Node.js 웹 응용 프로그램
==========================================================

이 자습서에서는 Azure 데이터 관리에서 제공하는 SQL 데이터베이스를 사용하여 Azure에서 호스트되는 [node](http://nodejs.org) 응용 프로그램에서 데이터를 저장하고 액세스하는 방법을 보여 줍니다. 이 자습서에서는 이전에 node 및 [Git](http://git-scm.com)을 사용한 경험이 있다고 가정합니다.

다음 내용을 배웁니다.

-   Azure 관리 포털을 사용하여 Azure 웹 사이트 및 SQL 데이터베이스를 만드는 방법

-   npm(node 패키지 관리자)을 사용하여 node 모듈을 설치하는 방법

-   node-sqlserver 모듈을 사용하여 SQL 데이터베이스 작업을 수행하는 방법

-   앱 설정을 사용하여 응용 프로그램의 런타임 값을 지정하는 방법

이 자습서를 따라 작업을 만들고, 검색하고, 완료할 수 있는 간단한 웹 기반 작업 관리 응용 프로그램을 구축합니다. 이 작업은 SQL 데이터베이스에 저장됩니다.

이 자습서의 프로젝트 파일은 **tasklist**라는 디렉터리에 저장되며 완료된 응용 프로그램은 다음과 유사하게 보입니다.

![빈 tasklist를 표시하는 웹 페이지](./media/sql-database-nodejs-use-web-site/sql_todo_final.png)

**참고**

이 자습서에 사용된 Microsoft Driver for Node.JS for SQL Server는 현재 미리 보기 릴리스로 제공되며 Microsoft Windows 및 Azure 운영 체제에서만 사용 가능한 런타임 구성 요소를 기반으로 합니다.

**참고**

이 자습서에서는 **tasklist** 폴더에 대한 참조를 만듭니다. 경로 의미 체계가 운영 체제마다 다르므로, 폴더의 전체 경로는 생략되었습니다. 로컬 파일 시스템에 쉽게 액세스할 수 있는 위치에 이 폴더를 만들어야 합니다(예: **\~/node/tasklist** 또는 **c:\\node\\tasklist**).

**참고**

아래의 많은 단계에서는 명령줄 사용을 언급합니다. 이러한 단계의 경우 **cmd.exe**(Windows) 또는 **Bash**(Unix Shell)와 같은 운영 체제의 명령줄을 사용하십시오. OS X 시스템에서는 터미널 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

필수 조건
---------

이 문서의 지침을 따르기 전에 다음이 설치되어 있는지 확인해야 합니다.

-   [node](http://nodejs.org) 버전 0.6.14 이상

-   [Git](http://git-scm.com)

-   Microsoft SQL Server Native Client 라이브러리 - [Microsoft SQL Server 2012 기능 팩](http://www.microsoft.com/en-us/download/details.aspx?id=29065)의 일부로 제공됨

-   텍스트 편집기

-   웹 브라우저

웹 사이트와 데이터베이스 만들기
-------------------------------

Azure 웹 사이트 및 SQL 데이터베이스를 만들려면 다음 단계를 따르십시오.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인합니다.
2.  포털의 왼쪽 아래에서 **+ 새로 만들기** 아이콘을 클릭합니다.

    ![새 Azure 웹 사이트 만들기](./media/sql-database-nodejs-use-web-site/new_website.jpg)

3.  **웹 사이트**를 클릭한 후 **사용자 지정 만들기**를 클릭합니다.

    ![새 웹 사이트 사용자 지정 만들기](./media/sql-database-nodejs-use-web-site/custom_create.png)

    **URL**에 대한 값을 입력하고 **데이터베이스** 드롭다운에서 **새 SQL 데이터베이스 만들기**를 선택한 후 **지역** 드롭다운에서 웹 사이트에 대한 데이터 센터를 선택합니다. 대화 상자 맨 아래에 있는 화살표를 클릭합니다.

    ![웹 사이트 세부 정보 입력](./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg)

4.  데이터베이스 **이름**에 값을 입력하고 **버전**[(WEB 또는 BUSINESS)](http://msdn.microsoft.com/en-us/library/windowsazure/ee621788.aspx), 데이터베이스의 **최대 크기**, **데이터 정렬** 및 **새 SQL 데이터베이스 서버**를 선택합니다. 대화 상자 맨 아래에 있는 화살표를 클릭합니다.

    ![SQL 데이터베이스 설정 입력](./media/sql-database-nodejs-use-web-site/database_settings.jpg)

5.  관리자 이름 및 암호를 입력하고 암호를 확인한 후에 새 SQL 데이터베이스 서버를 만들 지역을 선택하고 **Azure 서비스가 서버에 액세스할 수 있도록 허용합니다.** 상자를 선택합니다.

    ![새 SQL 데이터베이스 서버 만들기](./media/sql-database-nodejs-use-web-site/create_server.jpg)

    웹 사이트가 만들어지면 **Creation of Web Site '[SITENAME]' completed successfully** 텍스트가 표시됩니다. 이제 Git 게시를 사용하도록 설정할 수 있습니다.

6.  웹 사이트 목록에 표시되는 웹 사이트의 이름을 클릭하여 웹 사이트의 빠른 시작 대시보드를 엽니다.

    ![웹 사이트 대시보드 열기](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

7.  빠른 시작 페이지 맨 아래에 있는 **Git 게시 설정**을 클릭합니다.

    ![Git 게시 설정](./media/sql-database-nodejs-use-web-site/setup_git_publishing.png)

8.  Git 게시를 사용하도록 설정하려면 사용자 이름 및 암호를 지정해야 합니다. 만든 사용자 이름 및 암호를 기록해 둡니다. 이전에 Git 리포지토리를 설정한 경우 이 단계를 건너뜁니다.

    ![게시 자격 증명 만들기](./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png)

    리포지토리를 설정하는 데 몇 초 정도 걸립니다.

9.  리포지토리가 준비되면 응용 프로그램 파일을 리포지토리에 푸시하는 지침이 표시됩니다. 이러한 지침은 나중에 필요하므로 기록해 둡니다.

    ![Git 지침](./media/sql-database-nodejs-use-web-site/git-instructions.png)

SQL 데이터베이스 연결 정보 가져오기
-----------------------------------

Azure 웹 사이트에서 실행되는 SQL 데이터베이스 인스턴스에 연결하려면 연결 정보가 필요합니다. SQL 데이터베이스 연결 정보를 가져오려면 다음 단계를 따르십시오.

1.  Azure 관리 포털에서 **연결된 리소스**를 클릭한 후 데이터베이스 이름을 클릭합니다.

    ![연결된 리소스](./media/sql-database-nodejs-use-web-site/linked_resources.jpg)

2.  **연결 문자열 보기**를 클릭합니다.

    ![연결 문자열](./media/sql-database-nodejs-use-web-site/connection_string.jpg)

3.  이에 따라 표시되는 대화 상자의 **ODBC** 섹션에 있는 연결 문자열이 나중에 사용될 것이므로 기록해 둡니다.

작업 테이블 디자인
------------------

tasklist 응용 프로그램에 대한 항목을 저장하는 데 사용할 데이터베이스 테이블을 만들려면 다음 단계를 수행하십시오.

1.  Azure 관리 포털에서 SQL 데이터베이스를 선택한 후 페이지 맨 아래에서 **관리**를 클릭합니다. 현재 IP가 방화벽 규칙에 속하지 않는다는 메시지가 표시되면 **확인**을 선택하여 IP 주소를 추가합니다.

    ![관리 단추](./media/sql-database-nodejs-use-web-site/sql-manage.png)

2.  앞에서 데이터베이스 서버를 만들 때 선택한 로그인 이름 및 암호를 사용하여 로그인합니다.

    ![데이터베이스 관리 로그인](./media/sql-database-nodejs-use-web-site/sqlazurelogin.png)

3.  페이지 왼쪽 아래에서 **디자인**을 선택한 후 **새 테이블**을 선택합니다.

    ![새 테이블](./media/sql-database-nodejs-use-web-site/new-table.png)

4.  **테이블 이름**으로 'tasks'를 입력하고 **ID**에 대해 **Is Identity?**를 선택합니다.

    ![테이블 이름이 tasks로 설정되고 ID 여부가 선택된 모습](./media/sql-database-nodejs-use-web-site/table-name-identity.png)

5.  **Column1**을 **name**으로 변경하고 **Column2**를 **category**로 변경합니다. **열 추가** 단추를 클릭하여 새 열을 두 개 추가합니다. 첫 번째 새 열의 이름은 **created**이고 형식은 **date**여야 합니다. 두 번째 새 열의 이름은 **completed**이고 형식은 **bit**여야 합니다. 두 새 열을 모두 **Is Required?**로 표시해야 합니다.

    ![completed table design](./media/sql-database-nodejs-use-web-site/table-columns.png)

6.  **저장** 단추를 클릭하여 변경 내용을 테이블에 저장합니다. 이제 SQL 데이터베이스 관리 페이지를 닫을 수 있습니다.

설치 모듈 및 스캐폴딩 생성
--------------------------

이 섹션에서는 새로운 Node 응용 프로그램을 만들고 npm을 사용하여 모듈 패키지를 추가합니다. 작업 목록 응용 프로그램의 경우 [express](http://expressjs.com) 및 [node-sqlserver](https://github.com/WindowsAzure/node-sqlserver) 모듈을 사용합니다. Express 모듈은 node에 대한 모델 보기 컨트롤러 프레임워크를 제공하며, node-sqlserver 모듈은 Azure SQL 데이터베이스에 대한 연결을 제공합니다.

### express 설치 및 스캐폴딩 생성

1.  명령줄에서 **tasklist** 디렉터리로 변경합니다. **tasklist** 디렉터리가 없으면 디렉터리를 만듭니다.

2.  다음 명령을 입력하여 express를 설치합니다.

         npm install express -g

    **참고**

    일부 운영 체제에서 '-g' 매개 변수를 사용하면 **Error: EPERM, chmod '/usr/local/bin/express'** 오류 메시지와 관리자 계정으로 실행하라는 요청이 표시될 수 있습니다. 이런 경우 **sudo** 명령을 사용하여 더 높은 권한 수준으로 npm을 실행하십시오.

    이 명령의 출력은 다음과 유사하게 표시됩니다.

         express@2.5.9 /usr/local/lib/node_modules/express
         ├── mime@1.2.4
         ├── mkdirp@0.3.0
         ├── qs@0.4.2
         └── connect@1.8.7

    **참고**

    express 모듈을 설치할 때 사용되는 '-g' 매개 변수는 이 모듈을 전역으로 설치합니다. **express** 명령에 액세스하여 추가 경로 정보에 입력하지 않고도 웹 사이트 스캐폴딩을 생성할 수 있도록 이 작업을 수행합니다.

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

### 추가 모듈 설치

1.  명령줄에서 디렉터리를 **tasklist** 폴더로 변경하고 다음을 입력하여 **package.json** 파일에 설명된 모듈을 설치합니다.

         npm install

    이 명령의 출력은 다음과 유사하게 표시됩니다.

         express@2.5.8 ./node_modules/express
         ├── mime@1.2.4
         ├── qs@0.4.2
         ├── mkdirp@0.3.0
         └── connect@1.8.7
         jade@0.26.0 ./node_modules/jade
         ├── commander@0.5.2
         └── mkdirp@0.3.0

    이 명령은 Express에 필요한 모든 기본 모듈을 설치합니다.

2.  이제 다음 명령을 사용하여 nconf 모듈을 추가합니다. 이 모듈은 응용 프로그램이 구성 파일에서 데이터베이스 연결 문자열을 읽는 데 사용됩니다.

    npm install nconf -save

3.  이제 [다운로드 센터](http://www.microsoft.com/en-us/download/details.aspx?id=29995)에서 Microsoft Driver for Node.JS for SQL Server의 이진 버전을 다운로드합니다.

4.  **tasklist\\node\_modules** 디렉터리에 보관 파일을 추출합니다.

5.  **tasklist\\node\_modules** 디렉터리에서 **msnodesql-install.cmd** 파일을 실행합니다. 그러면 **node\_modules**에 **msnodesql** 하위 디렉터리가 만들어지고 드라이버 파일이 새 디렉터리 구조로 이동됩니다.

6.  **msnodesql-install.cmd** 파일은 더 이상 필요하지 않으므로 삭제합니다.

node 응용 프로그램에서 SQL 데이터베이스 사용
--------------------------------------------

이 섹션에서는 **express** 명령으로 만든 기본 응용 프로그램을 확장하여 기존 **app.js**를 수정하고 새 **index.js** 파일을 만들어 앞에서 만든 데이터베이스를 사용합니다.

### 컨트롤러 수정

1.  **tasklist/routes** 디렉터리에 있는 **index.js** 파일을 텍스트 편집기에서 엽니다.

2.  **index.js** 파일의 기존 코드를 다음 코드로 바꿉니다. 그러면 msnodesql 모듈이 로드된 후 nconf 모듈이 로드되고 nconf를 사용하여 **SQL\_CONN**이라는 환경 변수나 **config.json** 파일의 **SQL\_CONN** 값에서 연결 문자열이 로드됩니다.

         var sql = require('msnodesql')
             , nconf = require('nconf');

         nconf.env()
              .file({ file: 'config.json' });
         var conn = nconf.get("SQL_CONN");

3.  **index** 및 **updateItem** 메서드를 추가하여 **index.js** 파일에 대한 추가를 계속합니다. **index** 메서드는 데이터베이스에서 완료되지 않은 모든 작업을 반환하고, **updateItem**은 선택된 작업을 완료됨으로 표시합니다.

         exports.index = function(req, res) {
             var select = "select * from tasks where completed = 0";
             sql.query(conn, select, function(err, items) {
                 if(err)
                     throw err;
                 res.render('index', { title: 'My ToDo List ', tasks: items });
             });
         };

         exports.updateItem = function(req, res) {
             var item = req.body.item;
             if(item) {
                 var insert = "insert into tasks (name, category, created, completed) values (
         , 
         , GETDATE(), 0)";
                 sql.query(conn, insert, [item.name, item.category], function(err) {
                     if(err)
                         throw err;
                     res.redirect('/');
                 });
             } else {
                 var completed = req.body.completed;
                 if(!completed.forEach)
                     completed = [completed];
                 var update = "update tasks set completed = 1 where id in (" + completed.join(",") + ")";
                 sql.query(conn, update, function(err) {
                     if(err)
                         throw err;
                     res.redirect('/');
                 });
             }
         }

4.  **index.js** 파일을 저장합니다.

### app.js 수정

1.  **tasklist** 디렉터리에 있는 **app.js** 파일을 텍스트 편집기에서 엽니다. 이 파일은 앞에서 **express** 명령을 실행하여 만든 파일입니다.

2.  app.js 파일에서 아래의 코드가 보일 때까지 아래로 스크롤합니다.

         app.configure('development', function(){
	     app.use(express.errorHandler());
         });

3.  이제 다음 코드를 삽입합니다.

         app.get('/', routes.index);
         app.post('/', routes.updateItem);

그러면 이전에 **index.js** 파일에 추가한 **updateItem** 메서드에 대한 새 경로가 추가됩니다.

1.  **app.js** 파일을 저장합니다.

### 인덱스 보기 수정

1.  **views** 디렉터리로 변경하고 **index.jade** 파일을 텍스트 편집기에서 엽니다.

2.  **index.jade** 파일 내용을 아래 코드로 바꿉니다. 이렇게 하면 기존 작업을 표시하는 데 사용되는 보기와 새 작업을 추가하고 기존 작업을 완료로 표시하는 데 사용되는 양식이 정의됩니다.

         h1= title
         br

         form(action="/", method="post")
           table(class="table table-striped table-bordered")
             thead
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
             tbody
             each task in tasks
               tr
                 td #{task.name}
                 td #{task.category}
                 td #{task.created}
                 td
                   input(type="checkbox", name="completed", value="#{task.ID}", checked=task.completed == 1)
           button(type="submit", class="btn") Update tasks
         hr

         form(action="/", method="post", class="well")
           label Item Name:
           input(name="item[name]", type="textbox")
           label Item Category:
           input(name="item[category]", type="textbox")
           br
           button(type="submit", class="btn") Add Item

3.  **index.jade** 파일을 저장하고 닫습니다.

### 전역 레이아웃 수정

**views** 디렉터리의 **layout.jade** 파일은 다른 **.jade** 파일에 대한 전역 템플릿으로 사용됩니다. 이 단계에서는 멋진 모습의 웹 사이트를 쉽게 디자인할 수 있게 해주는 도구 키트인 [Twitter Bootstrap](https://github.com/twbs/bootstrap)을 사용하도록 이 파일을 수정합니다.

1.  [Twitter Bootstrap](http://getbootstrap.com/)용 파일을 다운로드하여 추출합니다. **bootstrap\\css** 폴더의 **bootstrap.min.css** 파일을 tasklist 응용 프로그램의 **public\\stylesheets** 디렉터리에 복사합니다.

2.  **views** 폴더에 있는 **layout.jade**를 텍스트 편집기에서 열어 내용을 다음으로 바꿉니다.

         !!!html
         html
           head
             title= title
             meta(http-equiv='X-UA-Compatible', content='IE=10')
             link(rel='stylesheet', href='/stylesheets/style.css')
             link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
           body(class='app')
             div(class='navbar navbar-fixed-top')
               .navbar-inner
                 .container
                   a(class='brand', href='/') My Tasks
             .container!= body

3.  **layout.jade** 파일을 저장합니다.

### 구성 파일 만들기

**config.json** 파일에는 SQL 데이터베이스에 연결하는 데 사용되는 연결 문자열이 포함되어 있으며, 런타임에 **index.js** 파일이 이 파일을 읽습니다. 이 파일을 만들려면 다음 단계를 수행하십시오.

1.  **tasklist** 디렉터리에서 새 파일 **config.json**을 만들어 텍스트 편집기에서 엽니다.

2.  **config.json** 파일의 내용은 다음과 유사하게 표시됩니다.

         {
           "SQL_CONN" : "connection_string"
         }

    **connection\_string**을 앞에서 반환된 ODBC 연결 문자열 값으로 바꿉니다.

3.  파일을 저장합니다.

로컬에서 응용 프로그램 실행
---------------------------

로컬 컴퓨터에서 응용 프로그램을 테스트하려면 다음 단계를 수행하십시오.

1.  명령줄에서 **tasklist** 디렉터리로 변경합니다.

2.  다음 명령을 사용하여 응용 프로그램을 로컬에서 시작합니다.

         node app.js

3.  웹 브라우저를 열고 http://127.0.0.1:3000으로 이동합니다. 그러면 다음과 유사한 웹 페이지가 표시되어야 합니다.

    ![빈 tasklist가 표시된 웹 페이지](./media/sql-database-nodejs-use-web-site/sql_todo_empty.png)

4.  **항목 이름** 및 **항목 범주**에 대해 제공된 필드를 사용하여 정보를 입력한 후 **항목 추가**를 클릭합니다.

5.  페이지가 업데이트되어 ToDo List의 항목을 표시해야 합니다.

    ![작업 목록의 새 항목 이미지](./media/sql-database-nodejs-use-web-site/sql_todo_list.png)

6.  작업을 완료하려면 완료 열의 확인란을 선택한 후 **작업 업데이트**를 클릭하면 됩니다.

7.  node 프로세스를 중지하려면 명령줄로 이동한 후 **CTRL** 키와 **C** 키를 누릅니다.

Azure에 응용 프로그램 배포
--------------------------

이 섹션에서는 웹 사이트를 만든 후 받은 배포 단계를 사용하여 응용 프로그램을 Azure에 게시합니다.

### 응용 프로그램 게시

1.  명령줄에서 아직 이동하지 않은 경우 **tasklist** 디렉터리로 변경합니다.

2.  다음 명령을 사용하여 응용 프로그램의 로컬 git 리포지토리를 초기화하고 응용 프로그램 파일을 이 리포지토리에 추가한 후 마지막으로 파일을 Azure에 푸시합니다.

         git init
         git add .
         git commit -m "adding files"
         git remote add azure [URL for remote repository]
         git push azure master

    배포가 끝나면 다음과 같은 문이 표시됩니다.

         To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
	     \* [new branch] 	master -\> master

3.  푸시 작업이 완료되면 **http://[사이트 이름].azurewebsites.net/**으로 이동하여 응용 프로그램을 봅니다.

### 환경 변수로 전환

앞에서 연결 문자열에 대한 **SQL\_CONN** 환경 변수를 찾거나 **config.json** 파일에서 값을 로드하는 코드를 구현했습니다. 다음 단계에서는 응용 프로그램이 환경 변수를 통해 실제로 액세스하는 웹 사이트 구성에 키/값 쌍을 만듭니다.

1.  Azure 관리 포털에서 **웹 사이트**를 클릭한 후 웹 사이트를 선택합니다.

    ![웹 사이트 대시보드 열기](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

2.  **구성**을 클릭한 후 페이지의 **앱 설정** 섹션을 찾습니다.

    ![구성 링크](./media/sql-database-nodejs-use-web-site/sql-task-configure.png)

3.  **앱 설정** 섹션에서 **키** 필드에 **SQL\_CONN**을 입력하고 **값** 필드에 ODBC 연결 문자열을 입력합니다. 마지막으로 확인 표시를 클릭합니다.

    ![앱 설정](./media/sql-database-nodejs-use-web-site/appsettings.png)

4.  마지막으로 페이지 맨 아래에 있는 **저장** 아이콘을 클릭하여 이 변경 내용을 런타임 환경에 커밋합니다.

    ![앱 설정 저장](./media/sql-database-nodejs-use-web-site/savebutton.png)

5.  명령줄에서 디렉터리를 **tasklist** 디렉터리로 변경하고 다음 명령을 입력하여 **config.json** 파일을 제거합니다.

         git rm config.json
         git commit -m "Removing config file"

6.  다음 명령을 수행하여 변경 내용을 Azure에 배포합니다.

         git push azure master

변경 내용이 Azure에 배포되고 나면 웹 응용 프로그램이 이제 **앱 설정** 항목에서 연결 문자열을 읽으므로 계속 작동해야 합니다. 이를 확인하려면 **앱 설정**의 **SQL\_CONN** 항목 값을 잘못된 값으로 변경하십시오. 이 값을 저장하고 나면 잘못된 연결 문자열로 인해 웹 사이트가 실패해야 합니다.

다음 단계
---------

-   [MongoDB를 사용한 Node.js 웹 응용 프로그램](../store-mongolab-web-sites-nodejs-store-data-mongodb/)(영문)

-   [테이블 저장소를 사용한 Node.js 웹 응용 프로그램](/en-us/develop/nodejs/tutorials/web-site-with-storage/)

추가 리소스
-----------

[Mac 및 Linux용 Azure 명령줄 도구](/en-us/develop/nodejs/how-to-guides/command-line-tools/)