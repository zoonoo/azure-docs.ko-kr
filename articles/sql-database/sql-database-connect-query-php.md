---
title: PHP를 사용하여 Azure SQL 데이터베이스 쿼리 | Microsoft Docs
description: PHP를 사용하여 Azure SQL 데이터베이스에 연결하고 T-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법입니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: 4a53d733b30c06cb48fe2a57f7be232d914dc79c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448942"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>빠른 시작: PHP를 사용하여 Azure SQL Database 쿼리

이 문서에서는 [PHP](https://php.net/manual/en/intro-whatis.php)를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 보여 줍니다. 그런 다음, 데이터를 쿼리하는 T-SQL 문을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 샘플을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

- Azure SQL 데이터베이스입니다. 다음 빠른 시작 중 하나를 사용하여 Azure SQL Database에서 데이터베이스를 만들고 구성할 수 있습니다.

  || 단일 데이터베이스 | Managed Instance |
  |:--- |:--- |:---|
  | 생성| [포털](sql-database-single-database-get-started.md) | [포털](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 구성 | [서버 수준 IP 방화벽 규칙](sql-database-server-level-firewall-rule.md)| [VM에서 연결](sql-database-managed-instance-configure-vm.md)|
  |||[사이트에서 연결](sql-database-managed-instance-configure-p2s.md)
  |데이터 로드|Adventure Works(빠른 시작마다 로드됨)|[Wide World Importers 복원](sql-database-managed-instance-get-started-restore.md)
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](sql-database-import.md) 파일에서 Adventure Works 복원 또는 가져오기|
  |||

  > [!IMPORTANT]
  > 이 문서의 스크립트는 Adventure Works 데이터베이스를 사용하도록 작성되었습니다. 관리되는 인스턴스의 경우 Adventure Works 데이터베이스를 인스턴스 데이터베이스로 가져오거나 이 문서의 스크립트를 수정하여 Wide World Importors 데이터베이스를 사용해야 합니다.

- 운영 체제에 설치되는 PHP 관련 소프트웨어

  - **MacOS** - PHP, ODBC 드라이버, PHP Driver for SQL Server를 차례로 설치합니다. [1, 2 및 3단계](/sql/connect/php/installation-tutorial-linux-mac)를 참조하세요.

  - **Linux** - PHP, ODBC 드라이버, PHP Driver for SQL Server를 차례로 설치합니다. [1, 2 및 3단계](/sql/connect/php/installation-tutorial-linux-mac)를 참조하세요.

  - **Windows** - IIS Express용 PHP와 Chocolatey를 설치한 다음, ODBC 드라이버와 SQLCMD를 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/php/windows/) 참조

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL 관리되는 인스턴스** 페이지로 이동합니다.

3. **개요** 페이지에서 단일 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 관리되는 인스턴스에 대한 **호스트** 옆에 있는 정규화된 서버 이름을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

## <a name="add-code-to-query-database"></a>코드를 추가하여 데이터베이스 쿼리

1. 원하는 텍스트 편집기에서 *sqltest.php* 파일을 새로 만듭니다.  

1. 해당 내용을 다음 코드로 바꿉니다. 그런 다음, 서버, 데이터베이스, 사용자 및 암호에 적절한 값을 추가합니다.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 앱을 실행합니다.

   ```bash
   php sqltest.php
   ```

1. 상위 20개 행이 반환되는지 확인하고, 앱 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)

- [SQL Server용 Microsoft PHP 드라이버](https://github.com/Microsoft/msphpsql/)

- [문제 보고 또는 질문(영문)](https://github.com/Microsoft/msphpsql/issues)

- [다시 시도 논리 예제: PHP로 SQL에 탄력적으로 연결](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
