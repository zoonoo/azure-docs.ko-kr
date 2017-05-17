---
title: "Node.js를 사용하여 Azure SQL Database에 연결 | Microsoft Docs"
description: "Azure SQL Database에 연결 및 쿼리하는 데 사용할 수 있는 Node.js 코드 샘플을 제시합니다."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/07/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 99c1ce93f30a54fcf0d76b1367c2f9545258c1f9
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Azure SQL Database: Node.js를 사용하여 데이터에 연결 및 쿼리

이 빠른 시작은 [Node.js](https://nodejs.org/en/)를 사용하여 Azure SQL Database에 연결한 후 Windows, Ubuntu Linux 및 Mac 플랫폼의 데이터베이스에서 Transact-SQL 문을 사용하여 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.

이 빠른 시작은 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.

- [DB 만들기 - 포털](sql-database-get-started-portal.md)
- [DB 만들기 - CLI](sql-database-get-started-cli.md)

## <a name="install-nodejs"></a>Node.js 설치 

이 섹션의 단계에서는 Node.js를 사용하여 개발하는 것에 익숙하고 Azure SQL Database 작업에 익숙하지 않다고 가정합니다. Node.js를 사용하여 개발하는 것이 처음인 경우 [SQL Server를 사용하여 앱 빌드](https://www.microsoft.com/en-us/sql-server/developer-get-started/)로 이동하고 **Node.js**를 선택한 다음 운영 체제를 선택합니다.

### <a name="mac-os"></a>**Mac OS**
**Node.js** 및 사용하기 쉬운 Mac OS X용 패키지 관리자인 **brew**를 설치하려면 다음 명령을 입력합니다.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux(Ubuntu)**
**Node.js** 및 Node.js용 패키지 관리자인 **npm**을 설치하려면 다음 명령을 입력합니다.

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
[Node.js 다운로드 페이지](https://nodejs.org/en/download/)를 방문하여 원하는 Windows 설치 관리자 옵션을 선택합니다.


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>Node.js용 Tedious SQL Server 드라이버 설치
Node.js용 권장 드라이버는 **[tedious](https://github.com/tediousjs/tedious)**입니다. Tedious는 Microsoft가 모든 플랫폼의 Node.js 응용 프로그램에 대해 기여하고 있는 오픈 소스 이니셔티브입니다. 이 자습서에서는 사용자 코드 및 설치할 `npm` 종속성을 포함할 빈 디렉터리가 필요합니다.

**tedious** 드라이버를 설치하려면 디렉터리 내에서 다음 명령을 실행합니다.

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>연결 정보 가져오기

Azure SQL Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차에는 정규화된 서버 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 **개요** 페이지에서 아래 이미지와 같이 정규화된 서버 이름을 검토합니다. 서버 이름 위로 마우스를 가져가면 **복사하려면 클릭** 옵션이 표시됩니다. 

   ![서버 이름](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Azure SQL Database 서버의 로그인 정보를 잊어버린 경우 SQL Database 서버 페이지로 이동하여 서버 관리자 이름을 확인하고 필요한 경우 암호를 다시 설정합니다.
    
## <a name="select-data"></a>데이터를 선택합니다.

범주별 상위 20개 제품에 대해 Azure SQL Database를 쿼리하려면 다음 코드를 사용합니다. 먼저 tedious 드라이버 라이브러리에서 드라이버 Connect 및 Request 클래스를 가져옵니다. 그런 다음 구성 개체를 만들고 **username**, **password**, **server** 및 **database** 변수를 AdventureWorksLT 샘플 데이터를 사용하여 데이터베이스를 만들 때 지정한 값으로 바꿉니다. 지정한 `config` 개체를 사용하여 `Connection` 개체를 만듭니다. 그런 다음 `connection` 개체의 `connect` 이벤트에 대한 콜백을 정의하여 `queryDatabase()` 함수를 실행합니다.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    
    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log("%s\t%s", column.metadata.colName, column.value);
        });
    });

    connection.execSql(request);
}
```

## <a name="insert-data-into-the-database"></a>데이터베이스에 데이터 삽입
`insertIntoDatabase()` 함수와 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 문을 사용하여 SalesLT.Product 테이블에 새 제품을 삽입하려면 다음 코드를 사용합니다. **username**, **password**, **server** 및 **database** 변수를 AdventureWorksLT 샘플 데이터를 사용하여 데이터베이스를 만들 때 지정한 값으로 바꿉니다. 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>데이터베이스에서 데이터 업데이트
`updateInDatabase()` 함수와 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 문을 사용하여 이전에 추가한 새 제품을 삭제하려면 다음 코드를 사용합니다. **username**, **password**, **server** 및 **database** 변수를 AdventureWorksLT 샘플 데이터를 사용하여 데이터베이스를 만들 때 지정한 값으로 바꿉니다. 이 샘플에서는 이전 예제에서 삽입한 제품 이름을 사용합니다.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>데이터베이스에서 데이터 삭제
데이터베이스에서 데이터를 삭제하려면 다음 코드를 사용합니다. **username**, **password**, **server** 및 **database** 변수를 AdventureWorksLT 샘플 데이터를 사용하여 데이터베이스를 만들 때 지정한 값으로 바꿉니다. 이번에는 `deleteFromDatabase()` 함수에 **DELETE 문**을 사용합니다. 이 샘플에서도 이전 예제에서 삽입한 제품 이름을 사용합니다.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>다음 단계
- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
- [SQL Server용 Microsoft Node.js 드라이버](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [SSMS를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)
- [Visual Studio Code를 사용하여 연결 및 쿼리](sql-database-connect-query-vscode.md).



