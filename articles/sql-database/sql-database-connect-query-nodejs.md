---
title: Node.js를 사용하여 데이터베이스 쿼리
description: Node.js를 사용하여 Azure SQL 데이터베이스에 연결하고 T-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법입니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768597"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>빠른 시작: Node.js를 사용하여 Azure SQL 데이터베이스 쿼리

이 빠른 시작에서는 Node.js를 사용하여 Azure SQL 데이터베이스에 연결하고 T-SQL 문을 사용하여 데이터를 쿼리합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL 데이터베이스](sql-database-single-database-get-started.md)
- [Node.js](https://nodejs.org) 관련 소프트웨어

  # <a name="macos"></a>[macOS](#tab/macos)

  Homebrew 및 Node.js를 설치한 다음, **macOS에서 SQL Server를 사용하여 Node.js 앱 만들기**의 **1.2** 및 [1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/) 단계를 사용하여 ODBC 드라이버와 SQLCMD를 설치합니다.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Node.js를 설치한 다음, **Ubuntu에서 SQL Server를 사용하여 Node.js 앱 만들기**의 **1.2** 및 [1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/) 단계를 사용하여 ODBC 드라이버와 SQLCMD를 설치합니다.

  # <a name="windows"></a>[Windows](#tab/windows)

  Chocolatey 및 Node.js를 설치한 다음, **Windows에서 SQL Server를 사용하여 Node.js 앱 만들기**의 **1.2** 및 [1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/) 단계를 사용하여 ODBC 드라이버와 SQLCMD를 설치합니다.

  ---

> [!IMPORTANT]
> 이 문서의 스크립트는 **Adventure Works** 데이터베이스를 사용하도록 작성되었습니다.

> [!NOTE]
> 선택적으로 Azure SQL 관리형 인스턴스를 사용하도록 선택할 수 있습니다.
>
> 만들고 구성하려면 [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) 또는 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)를 사용한 다음, [온-사이트](sql-database-managed-instance-configure-p2s.md) 또는 [VM](sql-database-managed-instance-configure-vm.md) 연결을 설정합니다.
>
> 데이터를 로드하려면 [Adventure Works](sql-database-import.md) 파일을 사용하여 [BACPAC로 복원](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 또는 [Wide World Importers 데이터베이스 복원](sql-database-managed-instance-get-started-restore.md)을 참조하세요.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL 관리형 인스턴스** 페이지로 이동합니다.

3. **개요** 페이지에서 단일 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 관리되는 인스턴스에 대한 **호스트** 옆에 있는 정규화된 서버 이름을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다. 

## <a name="create-the-project"></a>프로젝트 만들기

명령 프롬프트를 열고 *sqltest*라는 폴더를 만듭니다. 생성한 폴더를 열고 다음 명령을 실행합니다.

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>코드를 추가하여 데이터베이스 쿼리

1. 원하는 텍스트 편집기에서 *sqltest.js* 파일을 새로 만듭니다.

1. 해당 내용을 다음 코드로 바꿉니다. 그런 다음, 서버, 데이터베이스, 사용자 및 암호에 적절한 값을 추가합니다.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
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

- [.NET](sql-database-design-first-database-csharp.md) 또는 [SSMS](sql-database-design-first-database.md)를 사용하여 첫 번째 Azure SQL 데이터베이스 디자인
