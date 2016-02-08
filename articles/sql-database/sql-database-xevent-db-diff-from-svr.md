<properties 
	pageTitle="SQL 데이터베이스의 확장 이벤트 | Microsoft Azure" 
	description="Azure SQL 데이터베이스의 확장 이벤트(XEvent)와 Microsoft SQL Server의 이벤트 세션 간 차이점에 대해 설명합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/13/2015" 
	ms.author="genemi"/>


# SQL 데이터베이스의 확장 이벤트


이 항목은 Azure SQL 데이터베이스의 확장 이벤트 구현과 Mirosoft SQL Server의 확장 이벤트 구현의 작은 차이점에 대해 설명합니다.


- SQL 데이터베이스 V12는 2015년 후반기 확장 이벤트 기능을 추가했습니다.
- SQL Server는 2008년 이후 확장 이벤트를 추가해 왔습니다.
- SQL 데이터베이스의 확장 이벤트 기능 집합은 SQL Server 기능의 견고한 하위 집합입니다. 


*XEvent*는 블로그 및 기타 비공식 위치에서 '확장 이벤트'를 가리키는 비공식적 별명입니다.


> [AZURE.NOTE] 2015 년 10월을 기준으로 확장 이벤트 세션 기능은 Azure SQL 데이터베이스에서 미리 보기 수준으로 활성화됩니다. GA(일반 공급) 날짜는 아직 정해지지 않았습니다.
> 
> Azure [서비스 업데이트](https://azure.microsoft.com/updates/?service=sql-database)페이지에 GA에 대한 공지 사항이 게시됩니다.


## 필수 조건


이 항목은 다음에 대한 어느 정도의 지식이 있는 것으로 가정합니다.


- [Azure SQL 데이터베이스 서비스](https://azure.microsoft.com/services/sql-database/).


- Microsoft SQL Server의 [확장 이벤트](http://msdn.microsoft.com/library/bb630282.aspx).
 - 이 설명서에서 확장 이벤트에 대한 내용의 많은 부분이 SQL Server와 SQL 데이터베이스에 모두 적용됩니다.


이벤트 파일을 [대상](#AzureXEventsTargets)으로 선택할 경우 다음 항목에 대한 사전 지식이 있으면 도움이 됩니다.


- [Azure 저장소 서비스](https://azure.microsoft.com/services/storage/)


- PowerShell
 - [Azure 저장소와 함께 Azure PowerShell 사용](storage-powershell-guide-full.md) - PowerShell 및 Azure 저장소 서비스에 대한 포괄적 정보를 제공합니다.


## 코드 샘플


관련 항목에서 두 가지 코드 샘플을 제공합니다.


- [SQL 데이터베이스의 확장 이벤트에 대한 링 버퍼 대상 코드](sql-database-xevent-code-ring-buffer.md)
 - 짧고 간단한 Transact-SQL 스크립트.
 - 코드 샘플 항목에서는 링 버퍼 대상을 마칠 때 alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` 문을 실행하여 리소스를 해제해야 함을 강조합니다. 나중에 `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`(으)로 링 버퍼의 다른 인스턴스를 추가할 수 있습니다.


- [SQL 데이터베이스의 확장 이벤트에 대한 이벤트 파일 대상 코드](sql-database-xevent-code-event-file.md)
 - 1단계는 Azure 저장소 컨테이너를 만드는 PowerShell입니다.
 - 2단계는 Azure 저장소 컨테이너를 사용하는 Transact-SQL입니다.


## Transact-SQL 차이점


- SQL Server에서 [CREATE EVENT SESSION](http://msdn.microsoft.com/library/bb677289.aspx) 명령을 사용하는 경우 **ON SERVER** 절을 사용합니다. 하지만 SQL 데이터베이스에서는 대신 **ON DATABASE** 절을 사용합니다.


- **ON DATABASE** 절은 [ALTER EVENT SESSION](http://msdn.microsoft.com/library/bb630368.aspx) 및 [DROP EVENT SESSION](http://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL 명령에도 적용됩니다.


- **CREATE EVENT SESSION** 또는 **ALTER EVENT SESSION** 문에 **STARTUP\_STATE = ON**의 이벤트 세션 옵션을 포함하는 것이 가장 좋습니다.
 - **= ON** 값은 장애 조치(failover)로 인해 논리적 데이터베이스를 재구성한 다음 자동 재시작을 지원합니다.


## 새 카탈로그 뷰


확장 이벤트 기능은 여러 [카탈로그 뷰](http://msdn.microsoft.com/library/ms174365.aspx)에서 지원합니다. 카탈로그 뷰를 통해 현재 데이터베이스에서 사용자가 만든 이벤트 세션의 *메타데이터 또는 정의*를 확인할 수 있습니다. 뷰는 활성 이벤트 세션의 인스턴스에 대한 정보를 반환하지 않습니다.


| 카탈로그 뷰의 <br/>이름 | 설명 |
| :-- | :-- |
| **sys.database\_event\_session\_actions** | 이벤트 세션의 각 이벤트에 있는 각 작업에 대한 행을 반환합니다. |
| **sys.database\_event\_session\_events** | 이벤트 세션의 각 이벤트에 대한 행을 반환합니다. |
| **sys.database\_event\_session\_fields** | 이벤트 및 대상에 명시적으로 설정된 사용자 지정 가능한 각 열에 대한 행을 반환합니다. |
| **sys.database\_event\_session\_targets** | 이벤트 세션의 각 이벤트 대상에 대한 행을 반환합니다. |
| **sys.database\_event\_sessions** | SQL 데이터베이스의 각 이벤트 세션에 대한 행을 반환합니다. |


Microsoft SQL Server에서 유사한 카탈로그 뷰의 이름에는 *.database\_*가 아닌 *.server\_*가 포함됩니다. 이름 패턴은 **sys.server\_event\_%**와 같습니다.


## 새로운 [DMV](http://msdn.microsoft.com/library/ms188754.aspx)(동적 관리 뷰)


Azure SQL 데이터베이스에는 확장 이벤트를 지원하는 [DMV(동적 관리 뷰)](http://msdn.microsoft.com/library/bb677293.aspx)가 있습니다. DMV를 통해 *활성* 이벤트 세션을 확인할 수 있습니다.


| DMV의 이름 | 설명 |
| :-- | :-- |
| **sys.dm\_xe\_database\_session\_event\_actions** | 이벤트 세션 작업에 대한 정보를 반환합니다. |
| **sys.dm\_xe\_database\_session\_events** | 세션 이벤트에 대한 정보를 반환합니다. |
| **sys.dm\_xe\_database\_session\_object\_columns** | 세션에 바인딩되는 개체에 대한 구성 값을 보여줍니다. |
| **sys.dm\_xe\_database\_session\_targets** | 세션 작업에 대한 정보를 반환합니다. |
| **sys.dm\_xe\_database\_sessions** | 현재 데이터베이스로 범위가 한정된 각 이벤트 세션에 대한 행을 반환합니다. |


Microsoft SQL Server에서 유사한 카탈로그 뷰는 다음과 같이 이름에 *\_database* 부분이 없습니다.


- **sys.dm\_xe\_database\_sessions**가 아닌 <br/>**sys.dm\_xe\_sessions**


### 둘 다에 공통적인 DMV


확장 이벤트에는 Azure SQL 데이터베이스와 Microsoft SQL Server에 공통적인 추가 DMV가 있습니다.


- **sys.dm\_xe\_map\_values**
- **sys.dm\_xe\_object\_columns**
- **sys.dm\_xe\_objects**
- **sys.dm\_xe\_packages**



 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## 사용 가능한 확장 이벤트, 동작, 대상 찾기


간단한 SQL **SELECT**를 실행하여 사용 가능한 이벤트, 작업, 대상의 목록을 가져올 수 있습니다.


```
SELECT
		o.object_type,
		p.name         AS [package_name],
		o.name         AS [db_object_name],
		o.description  AS [db_obj_description]
	FROM
		           sys.dm_xe_objects  AS o
		INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
	WHERE
		o.object_type in
			(
			'action',  'event',  'target'
			)
	ORDER BY
		o.object_type,
		p.name,
		o.name;
```



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## SQL 데이터베이스 이벤트 세션의 대상


SQL 데이터베이스에서 이벤트 세션의 결과를 캡처할 수 있는 대상은 다음과 같습니다.


- [링 버퍼 대상](http://msdn.microsoft.com/library/ff878182.aspx) - 이벤트 데이터를 메모리에 잠시 보관합니다.
- [이벤트 카운터 대상](http://msdn.microsoft.com/library/ff878025.aspx) - 확장 이벤트 세션 동안 발생하는 모든 이벤트의 수를 계산합니다.
- [이벤트 파일 대상](http://msdn.microsoft.com/library/ff878115.aspx) - Azure 저장소 컨테이너에 전체 버퍼를 기록합니다.


[ETW(Windows 이벤트 추적)](http://msdn.microsoft.com/library/ms751538.aspx) API는 SQL 데이터베이스의 확장 이벤트에서 사용할 수 없습니다.


## 제한


SQL 데이터베이스의 클라우드 환경에 적합한 몇 가지 보안 관련 차이점이 있습니다.


- 확장 이벤트는 단일 테넌트 격리 모델에서 찾을 수 있습니다. 한 데이터베이스의 이벤트 세션은 다른 데이터베이스의 데이터 또는 이벤트에 액세스할 수 없습니다.

- **마스터** 데이터베이스의 컨텍스트에서 **CREATE EVENT SESSION** 문을 실행할 수 없습니다.


## 권한 모델


**CREATE EVENT SESSION** 문을 실행하려면 데이터베이스에 **제어** 권한이 있어야 합니다. 데이터베이스 소유자(dbo)는 **제어** 권한이 있습니다.


### 저장소 컨테이너 권한


Azure 저장소 컨테이너에 대해 만드는 SAS 토큰은 권한에 대해 **rwl**을 지정해야 합니다. 그러면 다음과 같은 권한이 제공됩니다.


- 읽기
- 쓰기
- 나열


## 성능 고려 사항


확장 이벤트를 집중적으로 사용할 경우 전체 시스템에 안정적인 메모리보다 더 많은 활성 메모리가 누적되는 시나리오가 있습니다. 따라서 Azure SQL 데이터베이스 시스템은 이벤트 세션이 누적할 수 있는 활성 메모리의 양에 대한 한도를 동적으로 설정 및 조정합니다. 많은 요소가 동적 계산에 활용됩니다.


메모리 최대 한도가 적용되었다는 오류 메시지가 표시될 경우 다음과 같은 방법으로 해결할 수 있습니다.


- 실행하는 동시 이벤트 세션 수를 줄입니다.


- 이벤트 세션에 대한 **CREATE** 및 **ALTER** 문을 통해 **MAX\_MEMORY** 절에 지정하는 메모리의 양을 줄입니다.


### 네트워크 대기 시간


Azure 저장소 BLOB에 데이터를 유지하는 동안 **이벤트 파일** 대상에서 네트워크 지연 또는 오류가 발생할 수 있습니다. 네트워크 통신이 완료될 때까지 기다리는 동안 SQL 데이터베이스의 다른 이벤트가 지연될 수 있습니다. 이 지연으로 인해 워크로드가 느려질 수 있습니다.

- 이러한 성능 위험을 줄이려면 이벤트 세션 정의에서 **EVENT\_RETENTION\_MODE** 옵션을 **NO\_EVENT\_LOSS**로 설정하지 마십시오.


## 관련 링크


- [Azure 저장소와 함께 Azure PowerShell 사용](storage-powershell-guide-full.md)
- [Azure 저장소 Cmdlet](http://msdn.microsoft.com/library/dn806401.aspx)


- [Azure 저장소와 함께 Azure PowerShell 사용](storage-powershell-guide-full.md) - PowerShell 및 Azure 저장소 서비스에 대한 포괄적 정보를 제공합니다.
- [.NET에서 Blob 저장소를 사용하는 방법](storage-dotnet-how-to-use-blobs.md)


- [CREATE CREDENTIAL(Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION(Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)


- [Microsoft SQL Server의 확장 이벤트에 대한 Jonathan Kehayias의 블로그](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


확장 이벤트에 대한 다른 코드 샘플 항목은 다음 링크에서 사용할 수 있습니다. 하지만 어느 샘플이든 Azure SQL 데이터베이스와 Microsoft SQL Server 중 무엇을 대상으로 하는지 늘 확인해야 합니다. 그런 다음 샘플을 실행하기 위해 약간의 변경이 필요한지 결정할 수 있습니다.


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->

<!---HONumber=AcomDC_0128_2016-->