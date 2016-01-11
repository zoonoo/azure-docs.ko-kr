<properties
	pageTitle="Ubuntu Linux상의 Tedious와 함께 Node.js를 사용하여 SQL 데이터베이스에 연결"
	description="Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Node.js 코드 샘플을 제시합니다. 이 샘플에서는 연결하는 데 Tedious 드라이버를 사용합니다."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Ubuntu Linux상의 Tedious와 함께 Node.js를 사용하여 SQL 데이터베이스에 연결


> [AZURE.SELECTOR]
- [Node.js](sql-database-develop-nodejs-simple-mac.md)
- [Python](sql-database-develop-python-simple-mac-osx.md)
- [Ruby](sql-database-develop-ruby-simple-mac-osx.md)


이 항목에서는 Mac OS X에서 실행 되는 Node.js 코드 샘플을 제공 합니다. 이 샘플은 Tedious 드라이버를 사용 하여 Azure SQL 데이터베이스에 연결합니다.


## 필수 조건


**노드**가 이미 컴퓨터에 설치되지 않았다면 노드를 설치합니다.


OSX 10.10 Yosemite에 node.js를 설치 하려면 깔끔하고 간단하게 설치 하는 미리 컴파일된 이전 패키지를 다운로드할 수 있습니다. [nodejs.org로 이동](http://nodejs.org/)하고 설치 단추를 클릭하여 최신 패키지를 다운로드합니다.

.dmg에서 받은 패키지를 설치 마법사를 따라 설치하여 **노드** 및 **npm**을 설치합니다. npm은 노드 패키지 관리자로, node.js에 대한 추가 패키지의 설치를 용이하게 합니다.


컴퓨터에 **노드** 및 **npm**이 구성된 후, Node.js 프로젝트를 만들려는 디렉터리로 이동하고 다음 명령을 입력합니다.


	npm init
	npm install tedious


**npm init** 명령으로 노드 프로젝트를 만듭니다. 프로젝트를 만들 때 기본값을 유지하려면 프로젝트 생성이 완료될 때까지 Enter 키를 누릅니다. 이제 프로젝트 디렉터리에서 **package.json** 파일을 볼 수 있습니다.

### SQL 데이터베이스

샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md)를 참조하세요. 안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.

## 1단계: 연결 정보 가져오기

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 2단계: 연결

[새 연결](http://pekim.github.io/tedious/api-connection.html) 기능을 사용하여 SQL 데이터베이스에 연결할 수 있습니다.

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## 3단계: 쿼리 실행


모든 SQL 문은 [new Request()](http://pekim.github.io/tedious/api-request.html) 함수를 사용하여 실행됩니다. Select 문과 같이 행을 반환하는 문의 경우, [request.on()](http://pekim.github.io/tedious/api-request.html) 함수를 사용하여 행을 가져올 수 있습니다. 행이 없다면 [request.on()](http://pekim.github.io/tedious/api-request.html) 함수가 빈 목록을 반환합니다.


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});


	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);}
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});

		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## 4단계: 행 삽입

이 예제에서는 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) 문을 안전하게 실행하고, [SQL 삽입](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 취약성으로부터 응용 프로그램을 보호하는 매개 변수를 전달하며, 자동으로 생성된 [기본 키](https://msdn.microsoft.com/library/ms179610.aspx) 값을 검색하는 방법을 보여 줍니다.


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});


	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);}
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}


## 다음 단계

자세한 내용은 [Node.js 개발자 센터](/develop/nodejs/)를 참조하세요.

<!---HONumber=AcomDC_1223_2015-->