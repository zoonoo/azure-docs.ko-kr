<properties
   pageTitle="SQL 데이터 웨어하우스용 드라이버 | Microsoft Azure"
   description="SQL 데이터 웨어하우스용 드라이버 및 연결 문자열"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="sonyama;barbkess"/>


# Azure SQL 데이터 웨어하우스용 드라이버

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-connect-overview.md)
- [인증](sql-data-warehouse-authentication.md)
- [드라이버](sql-data-warehouse-connection-strings.md)


다음 응용 프로그램 프로토콜 중 하나를 사용하여 SQL 데이터 웨어하우스에 연결할 수 있습니다.

- [ADO.NET][]
- [ODBC][]
- [PHP][]
- [JDBC][]

다음은 각 프로토콜에 대한 연결 문제열의 몇 가지 예입니다. 또한 Azure 포털을 사용하여 연결 문자열을 설정할 수 있습니다. Azure 포털에서 데이터베이스로 이동한 다음 *필수*에서 *데이터베이스 연결 문자열 표시*를 클릭합니다.

## 샘플 ADO.NET 연결 문자열

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## 샘플 ODBC 연결 문자열

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## 샘플 PHP 연결 문자열

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## 샘플 JDBC 연결 문자열

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## 다음 단계

Visual Studio 및 다른 응용 프로그램으로 데이터 웨어하우스 쿼리를 시작하려면 [Visual Studio를 사용하여 쿼리][]를 참조하세요.

<!--Image references-->

<!--Azure.com references-->
 [Visual Studio를 사용하여 쿼리]: ./sql-data-warehouse-query-visual-studio.md
 
<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->

<!---HONumber=AcomDC_0720_2016-->