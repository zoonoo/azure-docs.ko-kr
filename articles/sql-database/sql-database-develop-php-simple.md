---
title: "Windows에서 PHP를 사용하여 SQL Database에 연결 | Microsoft Docs"
description: "Windows 클라이언트에서 Azure SQL 데이터베이스에 연결하는 샘플 PHP 프로그램을 제시하고, 클라이언트에 필요한 필수 소프트웨어 구성 요소의 링크를 제공합니다."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/13/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 94fa09526683582bc017213d0ad9455f31cb22ae
ms.openlocfilehash: fba66e9d41daa2df34fbb3ffd8c92e664eaa560e
ms.lasthandoff: 02/21/2017


---

# <a name="connect-to-sql-database-by-using-php"></a>PHP를 사용하여 SQL 데이터베이스에 연결
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

이 항목에서는 PHP를 사용하여 Azure SQL Database를 연결 및 쿼리하는 방법을 보여 줍니다. Windows 또는 Linux에서 이 샘플을 실행할 수 있습니다. 


## <a name="step-1-create-a-sql-database"></a>1단계: SQL 데이터베이스 만들기
샘플 데이터베이스를 만드는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md) 를 참조하세요.  안내에 따라 **AdventureWorks 데이터베이스 템플릿**을 만드는 것이 중요합니다. 아래 표시된 샘플은 **AdventureWorks 스키마**에서만 작동합니다. 데이터베이스를 만들었으면 [시작 페이지](sql-database-get-started.md)에 설명된 대로 방화벽 규칙을 사용하여 IP 주소에 액세스할 수 있도록 설정해야 합니다.

## <a name="step-2-configure-development-environment"></a>2단계: 개발 환경 구성

### <a name="linux-ubuntu"></a>**Linux(Ubuntu)**
터미널을 열고 Python 스크립트를 만들려는 디렉터리로 이동합니다. 다음 명령을 입력하여 **Linux용 Microsoft ODBC 드라이버** **pdo_sqlsrv** 및 **sqlsrv**를 설치합니다. SQL Server용 Microsoft PHP Driver는 Linux의 Microsoft ODBC 드라이버를 사용하여 SQL Database에 연결합니다.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql unixodbc-dev gcc g++ build-essential
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- [웹 플랫폼 설치 관리자에서](https://www.microsoft.com/web/downloads/platform.aspx?lang=)PHP 7.1.1(x64) 설치 
- [Microsoft ODBC 드라이버 13.1](https://www.microsoft.com/download/details.aspx?id=53339)을 설치합니다. 
- [SQL Server용 Microsoft PHP 드라이버](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows)용 스레드가 아닌 안전 dll을 다운로드하고 PHP\v7.x\ext 폴더에 이진 파일을 추가합니다.
- 그런 다음 dll에 대한 참조를 추가하여 php.ini(C:\Program Files\PHP\v7.1\php.ini) 파일을 편집합니다. 예:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

이 시점에서 dll을 PHP와 함께 등록해야 합니다.

## <a name="step-3-run-sample-code"></a>3단계: 샘플 코드 실행
**sql_sample.php**라는 파일을 만들고 그 안에 다음 코드를 붙여 넣습니다. 다음을 사용하여 명령줄에서 이 코드를 실행할 수 있습니다.

```
php sql_sample.php
```

### <a name="connect-to-your-sql-database"></a>SQL 데이터베이스 연결
SQL Database에 연결하려면 [pyodbc.connect](http://php.net/manual/en/function.sqlsrv-connect.php) 함수가 사용됩니다.

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
    );
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
?>
```

### <a name="execute-an-sql-select-statement"></a>SQL SELECT 문 실행
[sqlsrv_query](http://php.net/manual/en/function.sqlsrv-query.php) 함수를 사용하여 SQL Database에 대한 쿼리에서 결과 집합을 검색할 수 있습니다. 

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";  
$getProducts = sqlsrv_query($conn, $tsql);  
if ($getProducts == FALSE)  
    die(FormatErrors(sqlsrv_errors()));  
$productCount = 0;  
while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))  
{  
    echo($row['CompanyName']);  
    echo("<br/>");  
    $productCount++;  
}  
sqlsrv_free_stmt($getProducts);  
sqlsrv_close($conn);    
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>행을 삽입하고, 매개 변수를 전달하며, 생성된 기본 키를 검색합니다.
SQL Database에서 [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 속성 및 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 개체를 사용하여 [기본 키](https://msdn.microsoft.com/library/ms179610.aspx) 값을 자동으로 생성할 수 있습니다. 


```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";  
//Insert query  
$insertReview = sqlsrv_query($conn, $tsql);  
if($insertReview == FALSE)  
    die(FormatErrors( sqlsrv_errors()));  
echo "Product Key inserted is :";  
while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))  
{     
    echo($row['ProductID']);  
}  
sqlsrv_free_stmt($insertReview);  
sqlsrv_close($conn);  
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```


## <a name="next-steps"></a>다음 단계
* [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md)
* [SQL Server용 Microsoft PHP Driver](https://github.com/Microsoft/msphpsql/)
* [문제/질문을 보관](https://github.com/Microsoft/msphpsql/issues)합니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 모든 [SQL 데이터베이스의 기능](https://azure.microsoft.com/services/sql-database/)

