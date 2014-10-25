<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="nodejs" title="How to Access Azure SQL Database from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# Node.js에서 Azure SQL 데이터베이스에 액세스하는 방법

이 가이드에서는 Microsoft Driver for Node.JS for SQL Server를 사용하는 Azure SQL 데이터베이스 액세스의 기본 사항을 보여 줍니다. **SQL 데이터베이스 만들기** 및 **SQL 데이터베이스에 연결** 시나리오를 다룹니다. 이 가이드에서는 [Azure 관리 포털][]에서 SQL 데이터베이스를 만듭니다.

## 목차

-   [개념][]
-   [방법: 환경 설정][]
-   [방법: SQL 데이터베이스 만들기][]
-   [방법: SQL 데이터베이스 연결 정보 가져오기][]
-   [방법: SQL 데이터베이스 인스턴스에 연결][]
-   [Azure 배포 고려 사항][]
-   [다음 단계][]

## <span id="Concepts"></span></a>개념

### Azure SQL 데이터베이스 정의

Azure SQL 데이터베이스는 Azure에 관계형 데이터베이스 관리 시스템을 제공하며 SQL Server 기술을 기반으로 합니다. SQL 데이터베이스를 사용하여 쉽게 클라우드에 관계형 데이터베이스 솔루션을 프로비전하고 배포할 수 있으며 데이터 보호와 자동 복구가 기본 제공되는 혜택을 갖춘 엔터프라이즈급 가용성, 확장성, 보안을 제공하는 분산 데이터 센터를 활용할 수 있습니다.

## Microsoft Driver for Node.JS for SQL Server 정의

Microsoft Driver for Node.JS for SQL Server에서는 개발자가 Node.js 응용 프로그램에서 Microsoft SQL Server 또는 Azure SQL 데이터베이스에 저장된 데이터에 액세스할 수 있습니다. 이 드라이버는 현재 미리 보기 릴리스로만 제공됩니다. 추가 기능이 완료되면 프로젝트로 통합될 것입니다. 드라이버에 대한 자세한 내용은 Microsoft Driver for Node.JS for SQL Server 프로젝트의 [Github 페이지][]와 관련 [Wiki][]를 참조하십시오.

<div class="dev-callout">
<b>참고</b>
<p>Microsoft Driver for Node.JS for SQL Server는 현재 미리 보기 릴리스로 제공되며 Microsoft Windows 및 Azure 운영 체제에서만 사용 가능한 런타임 구성 요소를 기반으로 합니다.</p>
</div>

## <span id="Setup"></span></a>방법: 환경 설정

### SQL Server Native Client 설치

Microsoft SQL Server Driver for Node.js는 SQL Server Native Client를 기반으로 합니다. 네이티브 클라이언트는 Azure에 응용 프로그램이 배포되면 자동으로 사용할 수 있으나 로컬 개발 환경에 표시되지 않을 수 있습니다. SQL Server Native Client는 [Microsoft SQL Server 2012 기능 팩][] 다운로드 페이지에서 설치할 수 있습니다.

<div class="dev-callout">
<b>참고</b>
<p>SQL Server Native Client는 Microsoft Windows 운영 체제에서만 사용할 수 있습니다. 이 드라이버는 기본적으로 Azure에서만 사용할 수 있으며 Microsoft Windows 이외의 운영 체제에서 개발하는 경우 로컬에서 이 문서의 정보를 사용하여 응용 프로그램을 테스트할 수 없습니다.</p>
</div>

### Node.js 설치

Node.js는 [][]<http://nodejs.org/#download></a>에서 설치할 수 있습니다. 설치 패키지를 사용자 운영 체제에서 사용할 수 없는 경우 소스에서 Node.js를 빌드할 수 있습니다.

## <span id="CreateServer"></span></a>방법: SQL 데이터베이스 만들기

다음 단계에 따라 Azure SQL 데이터베이스를 만듭니다.

1.  [관리 포털][Azure 관리 포털]에 로그인합니다.
2.  포털의 왼쪽 아래에서 **+ 새로 만들기** 아이콘을 클릭합니다.

    ![새 Azure 웹 사이트 만들기][]

3.  **SQL 데이터베이스**를 클릭하고 **사용자 지정 만들기**를 클릭합니다.

    ![새 SQL 데이터베이스 사용자 지정 만들기][]

4.  데이터베이스 **이름**에 값을 입력하고 **버전**(WEB 또는 BUSINESS), 데이터베이스의 **최대 크기**, **정렬** 및 **새 SQL 데이터베이스 서버**를 선택합니다. 대화 상자 아래쪽의 화살표를 클릭합니다. 이전에 SQL 데이터베이스를 만든 경우 **서버 선택** 드롭다운에서 기존 서버를 선택할 수 있습니다.

    ![SQL 데이터베이스 설정 입력][]

5.  관리자 이름 및 암호를 입력하고 암호를 확인한 후 새 SQL 데이터베이스를 만들 지역을 선택하고 `Allow Azure Services to access the server` 상자를 선택합니다.

    ![새 SQL 데이터베이스 서버 만들기][]

서버 및 데이터베이스 정보를 보려면 Azure 관리 포털에서 **SQL 데이터베이스**를 클릭합니다. 그런 다음 **데이터베이스** 또는 **서버**를 클릭하여 관련 정보를 볼 수 있습니다.

![서버 및 데이터베이스 정보 보기][]

## <span id="ConnectionInfo"></span></a>방법: SQL 데이터베이스 연결 정보 가져오기

SQL 데이터베이스 연결 정보를 가져오려면 포털에서 **SQL 데이터베이스**를 클릭하고 데이터베이스 이름을 클릭합니다.

![데이터베이스 정보 보기][]

그런 다음 **연결 문자열 표시**를 클릭합니다.

![연결 문자열 표시][]

결과 창의 ODBC 섹션에서 연결 문자열 값을 기록해 둡니다. 이 값은 노드 응용 프로그램에서 SQL 데이터베이스에 연결할 때 사용할 연결 문자열입니다. 암호는 SQL 데이터베이스를 만들 때 사용한 암호입니다.

## <span id="Connect"></span></a>방법: SQL 데이터베이스 인스턴스에 연결

### node-sqlserver 설치

Microsoft Driver for Node.JS for SQL Server는 node-sqlserver 네이티브 모듈로 사용할 수 있습니다. 이 모듈의 이진 버전은 [다운로드 센터][]에서 제공합니다. 이진 버전을 사용하려면 다음 단계를 수행하십시오.

1.  응용 프로그램의 **node\_modules** 디렉터리로 이진 보관 파일을 추출합니다.
2.  보관 파일에서 추출된 **node-sqlserver-install.cmd** 파일을 실행합니다. **node\_modules**에 **node-sqlserver** 하위 디렉터리가 만들어지고 드라이버 파일이 새 디렉터리 구조로 이동됩니다.
3.  **node-sqlserver-install.cmd** 파일은 더 이상 필요하지 않으므로 삭제합니다.

<div class="dev-callout">
<b>참고</b>
<p>npm 유틸리티를 사용하여 node-sqlserver 모듈을 설치할 수도 있습니다. 그러나 이렇게 하면 시스템에 이진 버전의 모듈을 구축하는 node-gyp가 호출됩니다.</p>
</div>

### 연결 문자열 지정

node-sqlserver를 사용하려면 응용 프로그램에서 요청하고 연결 문자열을 지정해야 합니다. 연결 문자열은 이 문서의 [방법: SQL 데이터베이스 연결 정보 가져오기][] 섹션에 반환된 ODBC 값이어야 합니다. 코드는 다음과 유사합니다.

    var sql = require('node-sqlserver');
    var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

### 데이터베이스 쿼리

쿼리는 **query** 메서드를 사용하는 Transact-SQL 문을 지정하여 수행할 수 있습니다. 다음 코드는 HTTP 서버를 만들고 사용자가 웹 페이지를 볼 때 **Test** 테이블에서 **ID**, **Column1** 및 **Column2** 행의 데이터를 반환합니다.

    var http = require('http')
    var port = process.env.port||3000;
    http.createServer(function (req, res) {
        sql.query(conn_str, "SELECT * FROM TestTable", function (err, results) {
            if (err) {
                res.writeHead(500, { 'Content-Type': 'text/plain' });
                res.write("Got error :-( " + err);
                res.end("");
                return;
            }
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            for (var i = 0; i < results.length; i++) {
                res.write("ID: " + results[i].ID + " Column1: " + results[i].Column1 + " Column2: " + results[i].Column2);
            }
            res.end("; Done.");
        });
    }).listen(port);

위의 예제는 결과 컬렉션의 모든 행을 한 번에 반환하는 방법을 설명하지만 이벤트를 구독할 수 있는 문 개체를 반환할 수도 있습니다. 이렇게 하면 개별 행과 열이 반환될 때 이러한 행과 열을 수신할 수 있습니다. 다음 예제에서는 그 방법을 보여 줍니다.

    var stmt = sql.query(conn_str, "SELECT * FROM TestTable");
    stmt.on('meta', function (meta) { console.log("We've received the metadata"); });
    stmt.on('row', function (idx) { console.log("We've started receiving a row"); });
    stmt.on('column', function (idx, data, more) { console.log(idx + ":" + data);});
    stmt.on('done', function () { console.log("All done!"); });
    stmt.on('error', function (err) { console.log("We had an error: " + err); });

## <span id="Deploy"></span></a>Azure 배포 고려 사항

<div class="dev-callout">
<b>참고</b>
<p>다음 정보는 Microsoft Driver for Node.JS for SQL Server 미리 보기 릴리스를 기반으로 합니다. 이 섹션의 정보 중 Azure에서의 node-sqlserver 모듈 사용에 대한 정보가 최신이 아닐 수도 있습니다. 최신 정보는 Github의 <a href="https://github.com/WindowsAzure/node-sqlserver">Microsoft Driver for Node.JS for SQL Server 프로젝트 페이지</a>(영문)를 참조하십시오.</p>
</div>

Azure는 런타임 시 node-sqlserver 모듈을 동적으로 설치하지 않으므로 응용 프로그램 배포에 이진 버전의 모듈이 포함되었는지 확인해야 합니다. 다음 디렉터리 구조가 존재하는지 확인하여 배포에 이진 버전의 모듈이 포함되었고 아래 설명된 파일이 포함되었는지 확인할 수 있습니다.

    application directory
        node_modules
            node-sqlserver
                lib

**node-sqlserver** 디렉터리에는 **package.json** 파일이 있어야 합니다. **lib** 디렉터리에는 node-sqlserver 모듈의 컴파일 형식인 **sql.js** 및 **sqlserver.node** 파일이 있어야 합니다.

Azure에 Node.js 응용 프로그램을 배포하는 작업에 대한 자세한 내용은 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포][](영문) 및 [Node.js 클라우드 서비스][](영문)를 참조하십시오.

## <span id="NextSteps"></span></a>다음 단계

-   [Microsoft Driver for Node.JS for SQL Server 소개(영문)][]
-   [Github.com의 Microsoft Driver for Node.js for SQL Server(영문)][Github 페이지]

  [Azure 관리 포털]: https://manage.windowsazure.com
  [개념]: #Concepts
  [방법: 환경 설정]: #Setup
  [방법: SQL 데이터베이스 만들기]: #CreateServer
  [방법: SQL 데이터베이스 연결 정보 가져오기]: #ConnectionInfo
  [방법: SQL 데이터베이스 인스턴스에 연결]: #Connect
  [Azure 배포 고려 사항]: #Deploy
  [다음 단계]: #NextSteps
  [Github 페이지]: https://github.com/WindowsAzure/node-sqlserver
  [Wiki]: https://github.com/WindowsAzure/node-sqlserver/wiki
  [Microsoft SQL Server 2012 기능 팩]: http://www.microsoft.com/ko-KR/download/details.aspx?id=29065
  []: http://nodejs.org/#download
  [새 Azure 웹 사이트 만들기]: ./media/sql-database-nodejs-how-to-use/new_website.jpg
  [새 SQL 데이터베이스 사용자 지정 만들기]: ./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg
  [SQL 데이터베이스 설정 입력]: ./media/sql-database-nodejs-how-to-use/new-sql-db.png
  [새 SQL 데이터베이스 서버 만들기]: ./media/sql-database-nodejs-how-to-use/db-server-settings.png
  [서버 및 데이터베이스 정보 보기]: ./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png
  [데이터베이스 정보 보기]: ./media/sql-database-nodejs-how-to-use/go-to-db-info.png
  [연결 문자열 표시]: ./media/sql-database-nodejs-how-to-use/show-connection-string.png
  [다운로드 센터]: http://www.microsoft.com/ko-KR/download/details.aspx?id=29995
  [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포]: /ko-KR/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js 클라우드 서비스]: /ko-KR/develop/nodejs/tutorials/getting-started/
  [Microsoft Driver for Node.JS for SQL Server 소개(영문)]: http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx
