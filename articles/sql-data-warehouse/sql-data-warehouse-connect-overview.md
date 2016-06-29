<properties
   pageTitle="Azure SQL 데이터 웨어하우스에 연결 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스에 연결에 대한 연결 개요"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/20/2016"
   ms.author="sonyama;barbkess"/>

# Azure SQL 데이터 웨어하우스에 연결

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-connect-overview.md)
- [인증](sql-data-warehouse-authentication.md)
- [드라이버](sql-data-warehouse-connection-strings.md)

Azure SQL 데이터 웨어하우스에 연결의 개요.

## 포털에서 연결 문자열 검색

SQL 데이터 웨어하우스는 Azure SQL Server와 연결됩니다. 연결하려면 서버의 정규화된 이름(**servername**.database.windows.net*)이 필요합니다.

정규화된 서버 이름을 찾으려면:

1. [Azure 포털][]로 이동합니다.
2. **SQL 데이터베이스**를 클릭하고 연결하려는 데이터베이스를 클릭합니다. 이 예제에서는 AdventureWorksDW 샘플 데이터베이스를 사용합니다.
3. 전체 서버 이름을 찾습니다.

    ![전체 서버 이름][1]

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

하지만 SQL 데이터 웨어하우스는 MPP(Massively Parallel Processing) 시스템으로 배포되므로 SQL Server에 비해 세션과 요청 식별자 모두 약간 다르게 노출됩니다.

세션과 요청은 해당 식별자에 의해 논리적으로 표시됩니다.

| 식별자 | 예제 값 |
| :--------- | :------------ |
| 세션 ID | SID123456 |
| 요청 ID | QID123456 |

참고로 세션 ID 앞에는 세션 ID의 축약형인 SID가 추가되며 요청 앞에는 쿼리 ID의 축약형인 QID가 추가됩니다.

쿼리 성능을 모니터링할 때 쿼리를 식별하기 위해 이 정보가 필요합니다. [Azure 포털] 및 동적 관리 보기 중 하나를 사용하여 쿼리 성능을 모니터링할 수 있습니다.

이 쿼리는 현재 세션을 식별합니다.

```sql
SELECT SESSION_ID()
;
```

실행 중이거나 데이터 웨어하우스에 대해 최근 실행한 모든 쿼리를 보려면 다음 예제를 사용할 수 있습니다. 뷰를 만든 다음 뷰를 실행합니다.

```sql
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

SELECT * FROM dbo.vSessionRequests;
```

## 다음 단계

Visual Studio 및 다른 응용 프로그램으로 데이터 웨어하우스 쿼리를 시작하려면 [Visual Studio를 사용하여 쿼리][]를 참조하세요.


<!--Arcticles-->

[Visual Studio를 사용하여 쿼리]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Azure 포털]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0622_2016-->