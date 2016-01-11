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
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Windows에서 Node.js를 사용하여 SQL 데이터베이스에 연결


> [AZURE.SELECTOR]
- [C#](sql-database-develop-dotnet-simple.md)
- [PHP](sql-database-develop-php-simple-windows.md)
- [Python](sql-database-develop-python-simple-windows.md)
- [Ruby](sql-database-develop-ruby-simple-windows.md)
- [Java](sql-database-develop-java-simple-windows.md)
- [Node.js](sql-database-develop-nodejs-simple-windows.md)


이 항목에서는 Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Node.js 코드 샘플을 제시합니다. Node.js 프로그램은 Windows 클라이언트 컴퓨터에서 실행됩니다. 연결을 관리하는 데에는 msnodesql 드라이버가 사용됩니다.


## 필수 조건


클라이언트 개발 컴퓨터에 다음과 같은 소프트웨어가 있어야 합니다.


-  Node.js – [버전 0.8.9(32비트 버전)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/). 스크롤한 후 Windows x64 설치 관리자(64비트)가 아니라 32비트 x86용 Window 설치 관리자 다운로드를 클릭합니다.
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/), x86 또는 x64용 설치 관리자.
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2) - Express Edition은 Microsoft에서 무료로 제공합니다.
- SQL Server Native Client 11.0 - [SQL Server 2012 기능 팩](http://www.microsoft.com/download/details.aspx?id=29065)에 포함된 Microsoft SQL Server 2012 Native Client로 제공됩니다.


### 필요한 모듈 설치

요구 사항을 충족한 후 Node.js 버전 0.8.9에 있는지 확인합니다. 명령줄 터미널에서 다음 명령을 사용하여 확인할 수 있습니다. node -v. <br>**cmd.exe** 명령줄 창에서 프로젝트 디렉터리(예: C:\\NodeJSSQLProject)로 이동합니다. 아래 명령을 표시된 순서대로 입력합니다.

	npm init
	npm install msnodesql

node\_modules\\msnodesql 폴더로 이동한 다음 **msnodesql-0.2.1-v0.8-ia32** 실행 파일을 실행합니다. 설치 마법사에서 단계를 수행하고 완료되면 마침을 누릅니다. 이제 Node.js SQL Server 드라이버가 설치되었습니다. 다음 단계를 수행하여 연결 문자열을 가져오면 Node.js 응용 프로그램에서 Azure SQL DB에 연결할 수 있습니다.


### SQL 데이터베이스

샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md)를 참조하세요. 안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다.


## 1단계: 연결 정보 가져오기

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 2단계: 연결


- 다음 코드를 프로젝트 디렉터리에 있는 .js 파일에 복사합니다.


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});


- 이제 다음 명령을 입력하여 .js 파일을 실행합니다.


		node index.js


## 3단계: 쿼리 실행


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## 4단계: 행 삽입


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## 5단계: 트랜잭션 롤백


Azure SQL 데이터베이스에서는 **conn.beginTransactions** 메소드가 작동하지 않습니다. 대신 코드 샘플에 따라 SQL 데이터베이스에서 트랜잭션을 수행하십시오.


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });

	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## 6단계: 저장 프로시저

이 코드 샘플을 실행하려면 먼저 매개 변수가 입력되지 않은 프로시저를 가지고 있거나 만들어야 합니다. SQL Server Management Studio(SSMS.exe)와 같은 도구를 사용하여 저장된 프로시저를 만들 수 있습니다.


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");

	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});


## 다음 단계

자세한 내용은 [Node.js 개발자 센터](/develop/nodejs/)를 참조하세요.

<!---HONumber=AcomDC_1223_2015-->