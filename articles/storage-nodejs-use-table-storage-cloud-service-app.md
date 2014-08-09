<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Web App with Storage" pageTitle="Web app with table storage (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="A tutorial that builds on the Web App with Express tutorial by adding Azure Storage services and the Azure module." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Node.js Web Application using Storage" authors="" solutions="" manager="" editor="" />

저장소를 사용하는 Node.js 웹 응용 프로그램
==========================================

이 자습서에서는 Node.js용 Azure 클라이언트 라이브러리를 사용하여 데이터 관리 서비스로 작업하여 [Express를 사용하는 Node.js 웹 응용 프로그램](http://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/web-app-with-express/) 자습서에서 만든 응용 프로그램을 확장합니다. Azure에 배포할 수 있는 웹 기반 작업 목록 응용 프로그램을 만들도록 응용 프로그램을 확장합니다. 작업 목록을 통해 사용자는 작업을 가져오고 새 작업을 추가하고 작업을 완료로 표시할 수 있습니다.

작업 항목은 Azure 저장소에 저장됩니다. Azure 저장소는 내결함성과 고가용성이 있는 구조화되지 않은 데이터 저장소를 제공합니다. Azure 저장소에는 데이터를 저장하고 액세스할 수 있는 일부 데이터 구조가 포함되며, Node.js용 Azure SDK에 포함된 API 또는 REST API를 통해 저장소 서비스를 활용할 수 있습니다. 자세한 내용은 [Azure에 데이터 저장 및 액세스](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx)를 참조하십시오.

이 자습서는 [Node.js 웹 응용 프로그램](http://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/getting-started/) 및 [Express로 Node.js 빌드](http://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/web-app-with-express/) 자습서를 완료했다고 가정합니다.

다음 내용을 배웁니다.

-   Jade 템플릿 엔진으로 작업하는 방법
-   Azure 데이터 관리 서비스로 작업하는 방법

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Internet Explorer의 완료된 웹 페이지](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

Web.Config에서 저장소 자격 증명 설정
------------------------------------

Azure 저장소에 액세스하려면 저장소 자격 증명을 전달해야 합니다. 이렇게 하려면 web.config 응용 프로그램 설정을 활용합니다. 이러한 설정은 환경 변수로서 노드에 전달된 다음 Azure SDK에서 읽습니다.

**참고**

저장소 자격 증명은 응용 프로그램이 Azure에 배포될 경우에만 사용됩니다. 에뮬레이터에서 실행 중이면 응용 프로그램은 저장소 에뮬레이터를 사용합니다.

다음 단계에 따라 저장소 계정 자격 증명을 가져와 web.config 설정에 추가합니다.

1.  설정이 열려 있지 않은 경우 **모든 프로그램, Azure**를 확장하고 **Azure PowerShell**을 마우스 오른쪽 단추로 클릭한 다음 **관리자 권한으로 실행**을 선택하여 **시작** 메뉴에서 Azure PowerShell을 시작합니다.

2.  응용 프로그램이 포함된 폴더로 디렉터리를 변경합니다. 예: C:\\node\\tasklist\\WebRole1

3.  Azure Powershell 창에서 다음 cmdlet을 입력하여 저장소 계정 정보를 가져옵니다.

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    이는 호스티드 서비스와 연결된 저장소 계정 및 계정 키 목록을 가져옵니다.

    **참고**

    서비스를 배포할 때 Azure SDK가 저장소 계정을 만들기 때문에 저장소 계정은 이전 가이드에서 응용 프로그램을 배포해서 이미 있어야 합니다.

4.  응용 프로그램을 Azure에 배포할 때 사용되는 환경 설정이 포함된 web.cloud.config 파일을 엽니다.

        PS C:\node\tasklist\WebRole1> notepad web.cloud.config

5.  **configuration** 요소 아래에 다음 블록을 삽입하고 {STORAGE ACCOUNT} 및 {STORAGE ACCESS KEY}를 배포에 사용할 저장소 계정의 계정 이름 및 기본 키로 대체합니다.

        <appSettings>
          <add key="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}"/>
          <add key="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}"/>
        </appSettings>

    ![web.cloud.config 파일 콘텐츠](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  파일을 저장하고 메모장을 닫습니다.

모듈 설치
---------

Azure 데이터 관리 서비스를 사용하려면 노드용 Azure 모듈을 설치해야 합니다. UUID(Universally Unique Identifier)를 생성하는 데 node-uuid 모듈이 사용되므로 이 모듈도 설치해야 합니다. 이러한 모듈을 설치하려면 아래 명령을 입력합니다.

    PS C:\node\tasklist\WebRole1> npm install node-uuid azure

명령이 완료되면 모듈이 **node\_modules** 폴더에 추가됩니다. 다음 단계에 따라 응용 프로그램에서 이러한 모듈을 사용합니다.

1.  server.js 파일을 엽니다.

        PS C:\node\tasklist\WebRole1> notepad server.js

2.  express.createServer()로 끝나는 줄 다음에 아래 코드를 추가하여 node-uuid, home 및 azure 모듈을 포함합니다. home 모듈은 아직 없지만 곧 만들게 됩니다.

    ![line app = modules.exports 줄이 강조 표시된 server.js 코드](./media/storage-nodejs-use-table-storage-cloud-service-app/node38.png)

        var uuid = require('node-uuid');
        var Home = require('./home');
        var azure = require('azure');

3.  저장소 계정 및 액세스 키 정보를 전달하는 저장소 테이블 클라이언트를 만드는 코드를 추가합니다.

    **참고**

    에뮬레이터에서 실행 중이면 SDK는 web.config를 통해 저장소 계정 정보를 받았더라도 자동으로 에뮬레이터를 사용합니다.

        var client = azure.createTableService();

4.  그런 다음, 작업이라는 Azure 저장소에 테이블을 만듭니다. 아래 논리는 테이블이 없으면 새로 만들고 일부 기본 데이터로 테이블을 채웁니다.

        //table creation
        client.createTableIfNotExists('tasks', function(error){
            if(error) {
                throw error;
            }

            var item = {
                name: 'Add readonly task list',
                category: 'Site work',
                date: '12/01/2011',
                RowKey: uuid(),
                PartitionKey : 'partition1',
                completed: false
            };

            client.insertEntity('tasks', item, function(){});

        });

5.  경로 섹션의 기존 코드를 아래 코드로 바꾸면 home 컨트롤러 인스턴스가 만들어지고 **/** 또는 **/home**에 대한 모든 요청의 경로가 만든 인스턴스에 지정됩니다.

    ![경로 섹션이 강조 표시된 server.js 파일](./media/storage-nodejs-use-table-storage-cloud-service-app/node39.png)

        var home = new Home(client);
        app.get('/', home.showItems.bind(home));
        app.get('/home', home.showItems.bind(home));

    요청 인라인을 처리하는 대신 명령을 Home 개체에 지금 위임하는 중입니다. **bind** 명령은 이러한 참조를 home 컨트롤러 내에서 로컬로 제대로 확인하는 데 필요합니다.

Home 컨트롤러 만들기
--------------------

이제 작업 목록 사이트에 대한 모든 요청을 처리하는 home 컨트롤러를 만들어야 합니다. 다음 단계에 따라 컨트롤러를 만듭니다.

1.  메모장에서 새 home.js 파일을 만듭니다. 이 파일에는 작업 목록의 논리를 전달하는 컨트롤러 코드가 포함됩니다.

        PS C:\node\tasklist\WebRole1> notepad home.js

2.  내용을 아래 코드로 바꾸고 파일을 저장합니다. 아래 코드는 javascript 모듈 패턴을 사용합니다. 이 패턴은 Home 함수를 내보냅니다. Home 프로토타입에는 실제 요청을 처리할 함수가 포함되어 있습니다.

        var azure=require('azure');
        module.exports = Home;

        function Home (client) {
            this.client = client;
        };

        Home.prototype = {
            showItems: function(req, res) {
                var self = this;
                this.getItems(false, function (resp, tasklist) {
                    if (!tasklist) {
                        tasklist = [];
                    }           
                    self.showResults(res, tasklist);
                });
            },

            getItems: function (allItems, callback) {
                var query = azure.TableQuery
                    .select()
                    .from('tasks');
                
                if (!allItems) {
                    query = query.where('completed eq 
            ', 'false');
                }
                this.client.queryEntities(query, callback);
             },

             showResults: function (res, tasklist) {
                res.render('home', { 
                    title: 'Todo list', 
                    layout : false, 
                    tasklist: tasklist });
             },
        };

    이제 home 컨트롤러에 세 가지 함수가 포함됩니다.

    -   *showItems*는 요청을 처리합니다.
    -   *getItems*는 테이블 클라이언트를 사용하여 작업 테이블에서 열린 작업 항목을 가져옵니다. 추가 필터를 쿼리에 적용할 수 있습니다. 예를 들어 위 쿼리 필터는 completed가 false와 같은 작업만 표시합니다.
    -   *showResults*는 Express render 함수를 호출하여 다음 섹션에서 만들 홈 보기를 사용하여 페이지를 렌더링합니다.

### 홈 보기 수정

Jade 템플릿 엔진은 HTML보다는 간단한 표시 구문을 사용하며 Express로 작업하기 위한 기본 엔진입니다. 다음 단계에 따라 표시된 작업 목록 항목을 지원하는 보기를 만듭니다.

1.  Windows PowerShell 명령 창에서 다음 명령을 사용하여 home.jade 파일을 편집합니다.

        PS C:\node\tasklist\WebRole1\views> notepad home.jade

2.  home.jade 파일의 내용을 아래 코드로 바꾸고 파일을 저장합니다. 아래 양식에는 작업 항목을 읽고 업데이트하는 기능이 포함되어 있습니다. 현재 home 컨트롤러는 읽기만 지원하며 나중에 변경합니다. form에는 작업 목록의 각 항목에 대한 세부 정보가 포함되어 있습니다.

        html
        head
            title Index
        body
            h1 My ToDo List

            form
                table(border="1")
                    tr
                        td Name
                        td Category
                        td Date
                        td Complete

                        each item in tasklist
                            tr
                                td #{item.name}
                                td #{item.category} 
                                td #{item.date} 
                                td 
                                    input(type="checkbox", name="completed", value="#{item.RowKey}") 

계산 에뮬레이터에서 응용 프로그램 실행
--------------------------------------

1.  Windows PowerShell 창에서 다음 cmdlet을 입력하여 계산 에뮬레이터에서 서비스를 시작하고 서비스를 호출하는 웹 페이지를 표시합니다.

        PS C:\node\tasklist\WebRole1> Start-AzureEmulator -launch

    Azure 저장소에서 가져온 작업 항목을 표시하며 브라우저가 다음 페이지를 표시합니다.

    ![하나의 테이블 항목과 함께 내 작업 목록 페이지를 표시하는 Internet Explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/node40.png)

새 작업 기능 추가
-----------------

이 섹션에서는 응용 프로그램을 업데이트하여 새 작업 항목 추가를 지원합니다.

### Server.js에 새 경로 추가

server.js 파일에서 **/home**의 마지막 경로 항목 뒤에 다음 줄을 추가한 후 파일을 저장합니다.

![홈 경로를 포함하는 줄이 강조 표시된 server.js 파일](./media/storage-nodejs-use-table-storage-cloud-service-app/node41.png)

        app.post('/home/newitem', home.newItem.bind(home));

    경로 섹션은 다음과 유사해야 합니다.

       // Routes

       var home = new Home(client);
       app.get('/', home.showItems.bind(home));
       app.get('/home', home.showItems.bind(home));
       app.post('/home/newitem', home.newItem.bind(home));

### Node-UUID 모듈 추가

node-uuid 모듈을 사용하여 고유 식별자를 만들려면 home.js 파일의 맨 위에서 모듈을 내보낸 첫 번째 줄 밑에 다음 줄을 추가합니다.

![module.exports = Home 줄이 강조 표시된 home.js 파일](./media/storage-nodejs-use-table-storage-cloud-service-app/node42.png)

       var uuid = require('node-uuid');

### Adding the New Item Function to Home 컨트롤러에 새 항목 함수 추가

새 항목 기능을 구현하려면 **newItem** 함수를 만듭니다. home.js 파일에서 마지막 함수 뒤에 다음 코드를 붙여넣은 후 파일을 저장합니다.

![강조 표시된 showresults 함수](./media/storage-nodejs-use-table-storage-cloud-service-app/node43.png)

       newItem: function(req, res) {
           var self = this;
           var createItem = function (resp, tasklist) {
               if (!tasklist) {
                   tasklist = [];
               }

               var count = tasklist.length;

               var item = req.body.item;
               item.RowKey = uuid();
               item.PartitionKey = 'partition1';
               item.completed = false;

               self.client.insertEntity('tasks', item, function (error) {
                   if(error) {  
                       throw error;
                   }
                   self.showItems(req, res);
               });
           };

           this.getItems(true, createItem);
       },

**newItem** 함수는 다음 작업을 수행합니다.

-   게시한 항목을 본문에서 추출합니다.
-   새 항목의 **RowKey** 및 **PartitionKey** 값을 설정합니다. 이러한 값은 항목을 Azure 테이블에 삽입하는 데 필요합니다. **RowKey** 값에 대해 UUID가 생성됩니다.
-   **insertEntity** 함수를 호출하여 항목을 작업 테이블에 삽입합니다.
-   **getItems** 함수를 호출하여 페이지를 렌더링합니다.

### 홈 보기에 새 항목 양식 추가

이제 사용자가 항목을 추가할 수 있도록 새 양식을 추가하여 보기를 업데이트합니다. home.jade 파일에서 파일의 끝에 다음 코드를 붙여넣고 저장합니다.

**참고**

Jade에서 공백은 중요하므로 아래에서 어느 공백도 제거하지 마십시오.

        hr
        form(action="/home/newitem", method="post")
            table(border="1")    
                tr
                    td Item Name: 
                    td 
                        input(name="item[name]", type="textbox")
                tr
                    td Item Category: 
                    td 
                        input(name="item[category]", type="textbox")
                tr
                    td Item Date: 
                    td 
                        input(name="item[date]", type="textbox")
            input(type="submit", value="Add item")

### 에뮬레이터에서 응용 프로그램 실행

1.  Azure 에뮬레이터가 이미 실행 중이므로 업데이트된 응용 프로그램을 찾을 수 있습니다.

        PS C:\node\tasklist\WebRole1> start http://localhost:81/home

    브라우저가 열리며 다음 페이지를 표시합니다.

    ![작업 및 새 작업을 추가할 필드를 포함하는 테이블이 있는 내 작업 목록 웹 페이지](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

2.  다음을 입력합니다. **항목 이름:** "새 작업 기능", **항목 범주:** "사이트 작업"?, **항목 날짜:** "12/02/2011". 그런 다음 **항목 추가**를 클릭합니다.

    항목이 Azure 저장소의 작업 테이블에 추가되고 아래 스크린샷처럼 표시됩니다.

    ![작업을 목록에 추가한 후 작업을 포함하는 테이블이 있는 내 작업 목록 웹 페이지](./media/storage-nodejs-use-table-storage-cloud-service-app/node45.png)

Azure에 응용 프로그램 다시 게시
-------------------------------

이제 응용 프로그램이 완료되었으므로 기존 호스티드 서비스에 대한 배포를 업데이트하여 응용 프로그램을 Azure에 게시합니다.

1.  Windows PowerShell 창에서 다음 cmdlet을 호출하여 호스티드 서비스를 Azure에 다시 배포합니다. 저장소 설정 및 위치는 이전에 저장되었으며 다시 입력할 필요가 없습니다.

        PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

    배포가 완료된 후 다음과 유사한 응답이 표시됩니다.

    ![배포 중에 표시된 상태 메시지](./media/storage-nodejs-use-table-storage-cloud-service-app/node35.png)

    앞서와 같이, **-launch** 옵션을 지정했으므로 브라우저가 열리며 게시가 완료될 때 Azure에서 실행 중인 응용 프로그램이 표시됩니다.

    ![내 작업 목록 페이지를 표시하는 브라우저 창. URL은 페이지가 Azure에서 호스트되는 중임을 나타냅니다.](./media/storage-nodejs-use-table-storage-cloud-service-app/node47.png)

응용 프로그램 중지 및 삭제
--------------------------

추가 비용을 방지하거나 다른 응용 프로그램을 빌드 및 배포할 수 있도록 이전에 배포한 응용 프로그램을 무료 평가판 기간 동안 사용하지 않도록 설정할 수 있습니다.

Azure는 사용된 서버 시간의 시간당 웹 역할 인스턴스 요금을 청구합니다. 서버 시간은 응용 프로그램이 배포된 다음에 사용되며 인스턴스가 실행되지 않고 중지된 상태인 경우에도 사용됩니다.

다음 단계에 따라 응용 프로그램을 중지 및 제거할 수 있습니다.

1.  Windows PowerShell 창에서, 이전 섹션에서 만든 서비스 배포를 다음 cmdlet을 사용하여 중지합니다.

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    서비스를 중지하려면 몇 분 정도 걸릴 수 있습니다. 서비스가 중지되면 서비스가 중지되었다는 메시지가 표시됩니다.

    ![서비스가 중지되었음을 나타내는 상태 메시지](./media/storage-nodejs-use-table-storage-cloud-service-app/node48.png)

2.  서비스를 삭제하려면 다음 cmdlet을 호출합니다.

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    메시지가 표시되면 **Y**를 입력하여 서비스를 삭제합니다.

    서비스를 삭제하려면 몇 분 정도 걸릴 수 있습니다. 서비스가 삭제되면 서비스가 삭제되었다는 메시지가 표시됩니다.

    ![서비스가 삭제되었음을 나타내는 상태 메시지](./media/storage-nodejs-use-table-storage-cloud-service-app/node49.png)


