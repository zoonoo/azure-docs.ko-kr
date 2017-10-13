---
title: "Node.js를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs"
description: "이 항목에서는 Node.js를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법을 보여 줍니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: d2765d4606bf6f926def15788ab23efd2a000ad4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-nodejs-to-query-an-azure-sql-database"></a>Node.js를 사용하여 Azure SQL Database 쿼리

이 빠른 시작 자습서에서는 [Node.js](https://nodejs.org/en/)를 통해 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작 자습서를 완료하려면 다음 항목이 있어야 합니다.

- Azure SQL 데이터베이스입니다. 이 빠른 시작에서는 다음과 같은 빠른 시작 중 하나에서 만든 리소스를 사용합니다. 

   - [DB 만들기 - 포털](sql-database-get-started-portal.md)
   - [DB 만들기 - CLI](sql-database-get-started-cli.md)
   - [DB 만들기 - PowerShell](sql-database-get-started-powershell.md)

- 이 빠른 시작 자습서에서 사용하는 컴퓨터의 공용 IP 주소에 대한 [서버 수준 방화벽 규칙](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)
- 운영 체제에 맞게 설치된 Node.js 및 관련 소프트웨어
    - **MacOS**: Homebrew와 Node.js를 설치한 다음 ODBC 드라이버와 SQLCMD를 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/node/mac/) 참조
    - **Ubuntu**: Node.js를 설치한 다음 ODBC 드라이버와 SQLCMD를 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/) 참조
    - **Windows**: Chocolatey와 Node.js를 설치한 다음 ODBC 드라이버와 SQLCMD를 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/node/windows/) 참조

## <a name="sql-server-connection-information"></a>SQL 서버 연결 정보

Azure SQL Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차에는 정규화된 서버 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 **개요** 페이지에서 다음 이미지와 같이 정규화된 서버 이름을 검토합니다. 서버 이름 위로 마우스를 가져가면 **복사하려면 클릭** 옵션이 표시됩니다. 

   ![서버 이름](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Azure SQL Database 서버의 로그인 정보를 잊어버린 경우 SQL Database 서버 페이지로 이동하여 서버 관리자 이름을 확인하고 필요한 경우 암호를 다시 설정합니다.

> [!IMPORTANT]
> 이 자습서를 수행하는 컴퓨터의 공용 IP 주소에 대한 방화벽 규칙이 있어야 합니다. 다른 컴퓨터에 있거나 다른 공용 IP 주소가 있으면 [Azure Portal을 사용하여 서버 수준 방화벽 규칙을 만듭니다](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="create-a-nodejs-project"></a>Node.js 프로젝트 만들기

명령 프롬프트를 열고 *sqltest*라는 폴더를 만듭니다. 생성한 폴더로 이동하여 다음 명령을 실행합니다.

    
    npm init -y
    npm install tedious
    npm install async
    

## <a name="insert-code-to-query-sql-database"></a>SQL 데이터베이스 쿼리 코드 삽입

1. 개발 환경 또는 원하는 텍스트 편집기에서 **sqltest.js** 파일 새로 만듭니다.

2. 내용을 다음 코드로 바꾸고, 서버, 데이터베이스, 사용자 및 암호에 대해 적절한 값을 추가합니다.

   ```js
   var Connection = require('tedious').Connection;
   var Request = require('tedious').Request;

   // Create connection to database
   var config = 
      {
        userName: 'someuser', // update me
        password: 'somepassword', // update me
        server: 'edmacasqlserver.database.windows.net', // update me
        options: 
           {
              database: 'somedb' //update me
              , encrypt: true
           }
      }
   var connection = new Connection(config);

   // Attempt to connect and execute queries if connection goes through
   connection.on('connect', function(err) 
      {
        if (err) 
          {
             console.log(err)
          }
       else
          {
              queryDatabase()
          }
      }
    );

   function queryDatabase()
      { console.log('Reading rows from the Table...');

          // Read all rows from table
        request = new Request(
             "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
                function(err, rowCount, rows) 
                   {
                       console.log(rowCount + ' row(s) returned');
                       process.exit();
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

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```js
   node sqltest.js
   ```

2. 상위 20개 행이 반환되는지 확인한 다음 응용 프로그램 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [SQL Server용 Microsoft Node.js Driver](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)에 대해 알아봅니다.
- Windows/Linux/macOS에서 [.NET Core를 사용하여 Azure SQL Database를 연결 및 쿼리하는 방법](sql-database-connect-query-dotnet-core.md)을 알아봅니다.  
- [명령줄을 사용하여 Windows/Linux/macOS에서 .NET Core를 시작하는 방법](/dotnet/core/tutorials/using-with-xplat-cli)을 알아봅니다.
- [SSMS를 사용하여 첫 번째 Azure SQL Database를 설계하는 방법](sql-database-design-first-database.md) 또는 [.NET을 사용하여 첫 번째 Azure SQL Database를 설계하는 방법](sql-database-design-first-database-csharp.md)을 알아봅니다.
- [SSMS를 사용하여 연결 및 쿼리하는 방법](sql-database-connect-query-ssms.md)을 알아봅니다.
- [Visual Studio Code를 사용하여 연결 및 쿼리하는 방법](sql-database-connect-query-vscode.md)을 알아봅니다.


