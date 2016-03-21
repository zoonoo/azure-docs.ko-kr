<properties
   pageTitle="SQL 데이터 웨어하우스 | Microsoft Azure에 연결"
   description="솔루션 개발을 위해 SQL 데이터 웨어하우스에 연결하기에 대한 팁."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL 데이터 웨어하우스에 연결
SQL 데이터 웨어하우스에 연결하려면 인증 목적으로 보안 자격 증명을 전달해야 합니다. 연결을 설정할 때 특정 연결 설정이 쿼리 세션을 설정하는 일부로 구성된다는 사실도 확인하게 될 것입니다.

이 문서에서는 SQL 데이터 웨어하우스에 연결하는 다음과 같은 측면에 대해 자세히 설명합니다.

- 인증
- 연결 설정
- 세션 및 요청 식별자


## 인증
SQL 데이터 웨어하우스에 연결하려면 다음 정보를 제공해야 합니다.

- 정규화된 서버 이름
- SQL 인증 지정
- 사용자 이름
- 암호
- 기본 데이터베이스(옵션)

참고로 사용자는 SQL 인증을 사용하여 인증해야 합니다. 현재 현재할 수 있는 인증은 지원되지 않습니다.

기본적으로 사용자의 연결은 사용자의 사용자 데이터베이스가 아닌 마스터 데이터베이스에 연결합니다. 사용자의 사용자 데이터베이스에 연결하려면 다음과 같은 두 가지 작업 중 하나를 수행하도록 선택할 수 있습니다.

1. SSDT의 SQL Server 개체 탐색기에서 서버를 등록할 때 또는 응용 프로그램 연결 문자열에 기본 데이터베이스를 지정합니다. 예를 들어 ODBC 연결에 대해 InitialCatalog 매개 변수를 포함합니다.
2. SSDT에서 세션을 만들기 전에 먼저 사용자 데이터베이스를 강조 표시합니다.

> [AZURE.NOTE] SSDT를 사용하여 SQL 데이터 웨어하우스에 연결하는 지침은 [연결 및 쿼리][] 시작 문서를 다시 참조하세요.

연결을 위한 데이터베이스 변경의 경우 Transact-SQL 문 **USE<your DB>**이 지원되지 않는다는 데 유의해야 합니다.

## 응용 프로그램 연결 프로토콜
다음 프로토콜 중 하나를 사용하여 SQL 데이터 웨어하우스에 연결할 수 있습니다.

- ADO.NET
- ODBC
- PHP
- JDBC

### 샘플 ADO.NET 연결 문자열

```
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### 샘플 ODBC 연결 문자열

```
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### 샘플 PHP 연결 문자열

```
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### 샘플 JDBC 연결 문자열

```
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## 연결 설정
SQL 데이터 웨어하우스는 연결 및 개체 생성 중에 몇 가지 설정을 표준화합니다. 이는 재정의할 수 없습니다.

| 데이터베이스 설정 | 값 |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | 켜기 |
| QUOTED\_IDENTIFIERS | 켜기 |
| NO\_COUNT | 끄기 |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| 데이터베이스 데이터 정렬 | SQL\_Latin1\_General\_CP1\_CI\_AS |

## 세션 및 요청
연결이 설정되고 세션이 설정되면 SQL 데이터 웨어하우스에 대한 쿼리를 작성하고 제출할 준비가 완료된 것입니다.

각 쿼리는 하나 이상의 요청 식별자로 표시됩니다. 해당 연결에 대해 제출된 모든 쿼리는 단일 세션의 일부이므로 단일 세션 ID로 표시됩니다.

그러나 SQL 데이터 웨어하우스는 분산된 MPP 시스템이므로 세션과 요청 식별자 모두 SQL Server와 조금 다르게 표시됩니다.

세션과 요청은 해당 식별자에 의해 논리적으로 표시됩니다.

| 식별자 | 예제 값 |
| :--------- | :------------ |
| 세션 ID | SID123456 |
| 요청 ID | QID123456 |

참고로 세션 ID 앞에는 세션 ID의 축약형인 SID가 추가되며 요청 앞에는 쿼리 ID의 축약형인 QID가 추가됩니다.

쿼리 성능을 모니터링할 때 쿼리를 식별하기 위해 이 정보가 필요합니다. [Azure 포털] 및 동적 관리 보기 중 하나를 사용하여 쿼리 성능을 모니터링할 수 있습니다.

다음 함수를 사용하여 현재 사용 중인 세션을 식별하려면:

```
SELECT SESSION_ID()
;
```

실행 중이거나 데이터 웨어하우스에 대해 최근 실행한 모든 쿼리를 보려면 아래와 같은 쿼리를 사용할 수 있습니다.

```
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;
```

참고로 이 쿼리는 사용자의 솔루션에 통합할 수 있도록 하나의 보기에 캡슐화되었습니다. 그러나 결과를 보려면 보기를 생성하고 실행해야 합니다.

## 다음 단계
연결된 후 사용자의 테이블 디자인을 시작할 수 있습니다. 자세한 내용은 [테이블 디자인]을 참조하세요.

<!--Image references-->

<!--Azure.com references-->
[연결 및 쿼리]: ./sql-data-warehouse-get-started-connect.md
[테이블 디자인]: ./sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!--Other references-->

<!---HONumber=AcomDC_0309_2016-->