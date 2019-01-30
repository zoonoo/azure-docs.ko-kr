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
ms.date: 11/28/2018
ms.openlocfilehash: b768b50af7ad6736e5cc3c885e6ac5016976f48a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958545"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>빠른 시작: PHP를 사용하여 Azure SQL Database 쿼리

이 문서에서는 [PHP](http://php.net/manual/en/intro-whatis.php)를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 보여 줍니다. 그런 다음, 데이터를 쿼리하는 T-SQL 문을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 샘플을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 사용 중인 컴퓨터의 공용 IP 주소에 대한 [서버 수준 방화벽 규칙](sql-database-get-started-portal-firewall.md)

- 운영 체제에 설치되는 PHP 관련 소프트웨어

    - **MacOS** - PHP, ODBC 드라이버, PHP Driver for SQL Server를 차례로 설치합니다. [1, 2 및 3단계](/sql/connect/php/installation-tutorial-linux-mac)를 참조하세요.

    - **Linux** - PHP, ODBC 드라이버, PHP Driver for SQL Server를 차례로 설치합니다. [1, 2 및 3단계](/sql/connect/php/installation-tutorial-linux-mac)를 참조하세요.

    - **Windows** - IIS Express용 PHP와 Chocolatey를 설치한 다음, ODBC 드라이버와 SQLCMD를 설치합니다. [1.2 및 1.3 단계](https://www.microsoft.com/sql-server/developer-get-started/php/windows/) 참조

## <a name="get-database-connection"></a>데이터베이스 연결 설정

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

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
