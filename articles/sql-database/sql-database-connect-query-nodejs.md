---
title: Node.js를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: Node.js를 사용하여 Azure SQL Database에 연결하고 T-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법입니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: c6e518f644e8fc43db58ef481312f44b2071a0dc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194307"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>빠른 시작: Node.js를 사용하여 Azure SQL Database 쿼리

이 문서에서는 [Node.js](https://nodejs.org)를 사용하여 Azure SQL에 연결하는 방법을 보여 줍니다. 그런 다음, 데이터를 쿼리하는 T-SQL 문을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 샘플을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 운영 체제용 Node.js 관련 소프트웨어:

  - **MacOS**: Homebrew와 Node.js를 설치한 다음, ODBC 드라이버와 SQLCMD를 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/node/mac/) 참조
  
  - **Ubuntu**: Node.js를 설치한 다음, ODBC 드라이버와 SQLCMD를 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/) 참조
  
  - **Windows**: Chocolatey와 Node.js를 설치한 다음, ODBC 드라이버와 SQLCMD를 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/node/windows/) 참조

## <a name="get-database-connection"></a>데이터베이스 연결 설정

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> 이 자습서를 수행하는 컴퓨터의 공용 IP 주소에 대한 방화벽 규칙이 있어야 합니다. 다른 컴퓨터에 있거나 다른 공용 IP 주소가 있으면 [Azure Portal을 사용하여 서버 수준 방화벽 규칙을 만듭니다](sql-database-get-started-portal-firewall.md).

## <a name="create-the-project"></a>프로젝트 만들기

명령 프롬프트를 열고 *sqltest*라는 폴더를 만듭니다. 생성한 폴더로 이동하여 다음 명령을 실행합니다.

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>코드를 추가하여 데이터베이스 쿼리

1. 원하는 텍스트 편집기에서 *sqltest.js* 파일을 새로 만듭니다.

1. 해당 내용을 다음 코드로 바꿉니다. 그런 다음, 서버, 데이터베이스, 사용자 및 암호에 적절한 값을 추가합니다.

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
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
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        var request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
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

> [!NOTE]
> 이 코드 예제는 Azure SQL의 **AdventureWorksLT** 샘플 데이터베이스를 사용합니다.

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 프로그램을 실행합니다.

    ```bash
    node sqltest.js
    ```

1. 상위 20개 행이 반환되는지 확인한 다음, 애플리케이션 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [SQL Server용 Microsoft Node.js 드라이버](/sql/connect/node-js/node-js-driver-for-sql-server)

- [.NET Core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md) 또는 [SSMS](sql-database-connect-query-ssms.md)(Windows만 해당)를 사용하여 Windows/Linux/macOS에서 연결하고 쿼리 실행

- [명령줄을 사용하여 Windows/Linux/macOS에서 .NET Core 시작](/dotnet/core/tutorials/using-with-xplat-cli)

- [.NET](sql-database-design-first-database-csharp.md) 또는 [SSMS](sql-database-design-first-database.md)를 사용하여 첫 번째 Azure SQL Database 디자인
