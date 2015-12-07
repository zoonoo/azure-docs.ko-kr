<properties 
	pageTitle="Windows에서 Node.js를 사용하여 SQL 데이터베이스에 연결" 
	description="Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Node.js 코드 샘플을 제시합니다. 샘플은 Windows 클라이언트 컴퓨터에서 실행됩니다."
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
	ms.date="11/19/2015"
	ms.author="meetb"/>


# Windows에서 Node.js를 사용하여 SQL 데이터베이스에 연결


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Node.js 코드 샘플을 제시합니다. Node.js 프로그램은 Windows 클라이언트 컴퓨터에서 실행됩니다. 연결을 관리하는 데에는 msnodesql 드라이버가 사용됩니다.


## 요구 사항


클라이언트 개발 컴퓨터에 다음과 같은 소프트웨어가 있어야 합니다.


-  [Node.js](https://nodejs.org/en/download/) -Windows Installer를 클릭하고 적절한 msi 설치 관리자를 다운로드합니다. 다운로드를 완료하면 msi를 실행하여 Node.js를 설치합니다. 


### 필요한 모듈 설치

컴퓨터에 **노드**가 구성되면 cmd.exe를 열고 Node.js 프로젝트를 만들려는 디렉터리로 이동한 후 다음 명령을 입력합니다.


	npm init
	npm install tedious


**npm init** 명령으로 노드 프로젝트를 만듭니다. 프로젝트를 만들 때 기본값을 유지하려면 프로젝트 생성이 완료될 때까지 Enter 키를 누릅니다. 이제 프로젝트 디렉터리에서 **package.json** 파일을 볼 수 있습니다.


### AdventureWorks 데이터베이스 만들기


이 항목의 코드 예제는 **AdventureWorks** 테스트 데이터베이스를 사용합니다. 이미 가지고 있지 않다면 [SQL 데이터베이스 시작](sql-database-get-started.md)을 참조하세요. 안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 예제는 **AdventureWorks 스키마**에서만 작동합니다.


## SQL 데이터베이스 연결

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


## SQL SELECT 실행


모든 SQL 문은 [new Request()](http://pekim.github.io/tedious/api-request.html) 함수를 사용하여 실행됩니다. Select 문과 같이 행을 반환하는 문의 경우, [request.on()](http://pekim.github.io/tedious/api-request.html) 함수를 사용하여 행을 가져올 수 있습니다. 행이 없다면 [request.on()](http://pekim.github.io/tedious/api-request.html) 함수가 빈 목록을 반환합니다.


	var Connection = require('tedious').Connection;
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
	
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
	
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


## 행을 삽입하고 매개 변수를 적용한 다음 생성된 기본 키를 검색합니다.


SQL 데이터베이스에서 [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 속성 및 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 개체를 사용하여 [기본 키 값](https://msdn.microsoft.com/library/ms179610.aspx)을 자동으로 생성할 수 있습니다. 이 예제에서는 insert 문을 실행하고, SQL 삽입을 방지하는 매개 변수를 안전하게 전달하며, 자동으로 생성된 기본 키 값을 검색하는 방법을 보여줍니다.


이 섹션의 코드 샘플은 SQL INSERT 문에 매개 변수를 적용합니다. 생성되는 기본 키 값은 프로그램에서 가져옵니다.


	var Connection = require('tedious').Connection;
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
	
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
	
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

 

<!---HONumber=AcomDC_1125_2015-->