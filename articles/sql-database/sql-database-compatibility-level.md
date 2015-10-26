<properties 
	pageTitle="SQL 데이터베이스의 호환성 수준 | Microsoft Azure" 
	description="Azure SQL 데이터베이스에 대한 호환성 수준 및 영향을 받은 기능을 설정하는 방법을 설명합니다."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="genemi"/>


# SQL 데이터베이스의 호환성 수준


Azure SQL 데이터베이스에 대한 이 항목에서는 Transact-SQL 문**ALTER DATABASE**를 사용하여 실행할 수 있는 기능 선택을 설명합니다. 호환성 수준 개념은 모든 업그레이드 위험을 줄이기 위한 것입니다.


호환성 수준에 의해 활성화되거나 비활성화된 대부분의 기능은 쿼리 최적화 프로그램의 일부입니다. Microsoft는 다음과 같은 경우 호환성 수준에 따라 새 쿼리 최적화 기능을 활성화합니다.


- 업그레이드된 컨텍스트가 이전 기능의 의미 체계를 변경합니다.
- 쿼리 최적화가 일반적이지 않지만 올바른 SQL 쿼리의 성능을 저하시킬 수 있습니다.


버전을 Azure SQL 데이터베이스 서버 또는 데이터베이스로 업그레이드한 후 쿼리가 제대로 수행되지 않으면 호환성 수준을 감소시키는 옵션이 있습니다. 설정을 낮추면 쿼리에 바람직하지 않은 최적화 향상 집합을 포함하여 좁은 최적화 향상 집합이 비활성화될 수 있습니다.


## 호환성 수준이 작동하는 방법


Microsoft SQL Server에는 수 년 동안 설정 가능한 호환성 수준이 있었습니다. 이제 버전 V12부터 Azure SQL 데이터베이스도 호환성 수준을 채택합니다. 일부 새 기능의 활성화는 각 SQL 데이터베이스 내의 호환성 수준 설정에 의해 제어됩니다. 가능한 설정은 다음과 같습니다.


- 110: 가장 낮은 가능한 설정 및 따라서 대부분의 새로운 기능을 제외하는 설정입니다.
- 120: SQL 데이터베이스 V11에 거의 일치합니다(`SELECT @@version;`이 **11.**0.0.0을 반환한다는 것을 의미함).
 - V11 버전을 SQL 데이터베이스의 ' SAWA V2'라고도 합니다.
 - 이 120은 Microsoft SQL Server 2014에서 가장 높은 값입니다.
- 130: V12에 거의 일치합니다(`SELECT @@version;`이 **12.**0.0.0을 반환한다는 것을 의미함).
 - 이 130은 Microsoft SQL Server 2016에서 가장 높은 값입니다.


예를 들어 설정 120은 데이터베이스가 수준 120에서 활성화되는 기능 *그리고* 110 같은 더 낮은 설정에서 활성화되는 기능의 부분집합에 액세스할 수 있음을 의미합니다. 120으로 설정한 경우 데이터베이스는 수준 130 기능에 액세스할 수 없습니다.


## 호환성 수준 읽기 또는 설정


### 호환성 수준 읽기


```
SELECT d.name, d.compatibility_level
	FROM sys.databases AS d
	WHERE d.name = db_name();
```


### 호환성 수준 설정


```
ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL = 130;
```


자세한 내용은 다음을 참조하세요.


- [ALTER TABLE 호환성 수준(Transact-SQL)](http://msdn.microsoft.com/library/bb510680.aspx)
- [sys.databases(Transact-SQL)](http://msdn.microsoft.com/library/ms178534.aspx)


## 메모리 내 테이블과 columnstore 인덱스


수준 120 및 130에서 활성화되는 대부분의 호환성 수준 기능은 *메모리 내*에 있는 테이블 및 인덱스와 관계가 있습니다. 따라서 중요한 항목은 다음을 포함합니다.


- 메모리 최적화된 테이블
- Columnstore 인덱스


메모리 내 항목이 관련된 경우 호환성 수준 130은 다음과 같은 이점을 제공합니다.


- *배치* 모드에서 더 많은 쿼리를 처리하는 쿼리 최적화 프로그램의 기능이 활성화됩니다.
 - 배치 모드는 많은 행이 관련된 경우 *행* 모드보다 더 빠릅니다.
- **SORT** 연산자를 추가합니다.
- 창 집계를 추가합니다.


메모리 내 테이블 및 Columnstore 인덱스에 대한 자세한 내용은 다음을 참조하세요.


- [메모리 최적화된 테이블](http://msdn.microsoft.com/library/dn133165.aspx)
- [CREATE CLUSTERED COLUMNSTORE INDEX(Transact-SQL)](http://msdn.microsoft.com/library/dn511016.aspx)
- [Columnstore Indexes Described](http://msdn.microsoft.com/library/gg492088.aspx)


## 최소 수준 130이 필요한 일반 기능


카디널리티 평가기(CE)는 테이블의 근사값 행 수에 관한 통계를 생성하고 저장합니다. 쿼리 최적화 프로그램의 많은 부분은 카디널리티 정보를 사용합니다.


CE에서 사용하는 알고리즘이 향상되었습니다. 향상된 기능은 변경을 야기하며, 변경 내용은 일반적이지 않은 경우로 돌아가게 할 수 있습니다.


| 130은 <br/>필요한<br/> 최소 수준 | 쿼리 영역, <br/>일반 | <br/>쿼리 계획<br/> 개선 세부 정보 |
| :-- | :-- | :-- |
| 130 | 카디널리티 평가기(CE) | 수준 120에서 이전의 CE에 비해 카디널리티 평가기(CE)로 구체화합니다.<br/><br/>쿼리 계획에서 다음을 참조할 수 있습니다. **CardinalityEstimationModelVersion="130"**<br/><br/>**Trace flag** [**9481**](http://www.sqlservergeeks.com/sql-server-2014-trace-flags-9481/)을 켜면 데이터베이스가 수준 130에 있는 경우 수준 120의 CE를 사용할 수 있습니다.<br/><br/>**추적 플래그** [**4199**](http://support.microsoft.com/kb/974006), 데이터베이스가 호환성 수준 130에 있는 경우 쿼리 최적화 프로그램에 대해 핫픽스를 선택하기 위해 끌 수 있습니다. 플래그는 수준 130이 미리 보기에서 완전히 사라지고 일반 가용성(GA)에 대해 해제된 후 구현된 핫픽스에만 적용됩니다. 자세한 내용 참조:<br/><br/>● [DBCC TRACEON](http://msdn.microsoft.com/library/ms187329.aspx) |
| 130 | 메모리 내 테이블에 대한 병렬 쿼리 계획 | 쿼리에서 여러 스레드를 사용할 수 있으며 메모리 내 테이블과 반대인 경우(**MEMORY\_OPTIMIZED = YES** 절을 사용하여 만든 테이블을 의미함) 병렬로 실행할 수 있습니다. 병렬 처리를 사용하면 쿼리가 더 빨리 실행할 수 있습니다.<br/><br/>이 향상된 기능은 일반 Transact-SQL 및 사용자 저장 절차에 대해 지원됩니다. 그러나 DLL로 컴파일된 원시 저장된 절차에 대해서는 지원되지 않습니다. |


## 최소 수준 130이 필요한 Columnstore 인덱스 기능


호환성 수준 130에서 쿼리 최적화 프로그램 향상으로 새 쿼리 계획이 가능합니다. Columnstore 인덱스를 포함하는 변경된 계획의 경우 변경내용은 일반적으로 다음 중 하나를 포함합니다.


- 추가 하위 작업을 위해 데이터를 복사해야 하는 상황에서 감소.
- 정렬 등과 같은 작업을 위해 *행* 처리에서 ***배치*** 처리로 변경.


대부분의 경우 계획을 변경하면 다음을 포함하게 되어 쿼리의 성능이 향상됩니다.


- Columnstore 인덱스에 병렬 삽입.
 - 수준 130은 비클러스터형 인덱스의 병렬 탐색을 제공하지 않습니다.
- **tempdb** 데이터베이스 사용 증가.


| 130은 <br/>필요한<br/> 최소 수준 | 쿼리 영역, <br/>Columnstore 인덱스 | <br/>쿼리 계획<br/> 개선 세부 정보 |
| :-- | :-- | :-- |
| 130 | 함수 쿼리 | 다음과 같은 경우 배치 모드로 전환하면 성능이 향상됩니다.<br/><br/>• 정렬이 포함된 경우.<br/><br/>• *여러* 고유 함수를 사용하여 집계하는 경우<br/>(다음 목록에서 서로 다른 두 글머리표 각각에서 함수 한 개):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT** *or* **COUNT\_BIG**<br/>&nbsp;&nbsp;&nbsp;▫ **AVG** *or* **SUM**<br/>&nbsp;&nbsp;&nbsp;▫ **CHECKSUM\_AGG**<br/>&nbsp;&nbsp;&nbsp;▫ **STDEV** *or* **STDEVP**<br/><br/>• 창 집계 함수<br/>([MSDN에서는 ](http://msdn.microsoft.com/library/ms189461.aspx) 여기서 설명, 그리고 [Kathi Kellenberger](http://www.bidn.com/blogs/KathiKellenberger/sql-server/4397/what-is-a-window-aggregate-function)의 경우 여기서 설명):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT**, **COUNT\_BIG**, **SUM**, **AVG**, **MIN**, **MAX**, **CLR**<br/><br/>• 창 [사용자 정의](http://msdn.microsoft.com/library/ms131057.aspx) 집계:<br/>&nbsp;&nbsp;&nbsp;▫ [**CHECKSUM\_AGG**](http://msdn.microsoft.com/library/ms188920.aspx), [**STDEV**](http://msdn.microsoft.com/library/ms190474.aspx), [**STDEVP**](http://msdn.microsoft.com/library/ms176080.aspx), [**VAR**](http://msdn.microsoft.com/library/ms186290.aspx), [**VARP**](http://msdn.microsoft.com/library/ms188735.aspx), [**GROUPING**](http://msdn.microsoft.com/library/ms178544.aspx)<br/><br/>• 창 집계 분석 함수:<br/>&nbsp;&nbsp;&nbsp;▫ [**LAG**](http://msdn.microsoft.com/library/hh231256.aspx), [**LEAD**](http://msdn.microsoft.com/library/hh213125.aspx), [**FIRST\_VALUE**](http://msdn.microsoft.com/library/hh213018.aspx), [**LAST\_VALUE**](http://msdn.microsoft.com/library/hh231517.aspx), [**PERCENTILE\_CONT**](http://msdn.microsoft.com/library/hh231473.aspx), [**PERCENTILE\_DISC**](http://msdn.microsoft.com/library/hh231327.aspx), [**CUME\_DIST**](http://msdn.microsoft.com/library/hh231078.aspx), [**PERCENT\_RANK**](http://msdn.microsoft.com/library/hh213573.aspx) |
| 130 | 단일 스레드 직렬 쿼리 계획 | 단일 스레드에 대해 실행한 쿼리는 배치 모드에서 실행할 수 있습니다. 따라서 쿼리가 더 빠르게 수행될 수 있습니다.<br/><br/>쿼리 계획을 단일 스레드로 디자인하거나 쿼리를 **MAXDOP 1**에서 실행할 수 있습니다. |
| 130 | 병렬 삽입 | 쿼리 계획은 일부 삽입을 병렬로 수행할 수 있습니다.< b r /<br/>이 항목의 뒤에 나오는 [예제](#ExampleQueryParallelCciByCompatLevel)가 이 병렬 처리를 보여줍니다. |
| 130 | 세미 조인 금지 | 이제 이 연산자는 배치 모드에서 실행할 수 있습니다. |


## 최소 수준 120이 필요한 일반 기능


| 120은 <br/>필요한<br/> 최소 수준 | 쿼리 영역, <br/>일반 | <br/>쿼리 계획<br/> 개선 세부 정보 |
| :-- | :-- | :-- |
| 120 | [MEMORY\_OPTIMIZED 테이블 변경](http://msdn.microsoft.com/library/dn269114.aspx) | **MEMORY\_OPTIMIZED = YES**인 테이블에 대해 Transact-SQL **ALTER TABLE** 연산을 수행할 수 있습니다.<br/><br/>데이터베이스 응용 프로그램은 계속 실행할 수 있지만 테이블에 액세스하는 연산은 **ALTER TABLE**가 완료될 때까지 차단됩니다. |
| 120 | [메모리 최적화 개체에 대한 저장소 만들기 및 관리](http://msdn.microsoft.com/library/dn133174.aspx) | 메모리 내 OLTP 엔진은 SQL Server에 통합됩니다. 이 기능을 이용하여 **MEMORY\_OPTIMIZED** 테이블과 기존의 디스크 기반 테이블을 모두 동일한 데이터베이스에 둘 수 있습니다. |
| 120 | [메모리 내 OLTP에 대한 Transact-SQL 지원](http://msdn.microsoft.com/library/dn133180.aspx) | 메모리 내 온라인 트랜잭션 처리(OLTP)를 지원하기 위해 몇몇 Transact-SQL 명령이 향상되었습니다.<br/><br/>한 예로 [CREATE PROCEDURE](http://msdn.microsoft.com/library/ms187926.aspx) 명령에 대한 새 **NATIVE\_COMPILATION** 키워드 가 있습니다. |
| 120 | 카디널리티 평가기(CE) | 수준 110에서 이전의 CE에 비해 카디널리티 평가기(CE)가 구체화되었습니다.<br/><br/>쿼리 계획에서 다음을 볼 수 있습니다. **CardinalityEstimationModelVersion="120"**<br/><br/>**추적 플래그 4199**가 호환성 수준 값과 상호작용하는 방법에 대한 자세한 내용은 [KB 974006](http://support.microsoft.com/kb/974006)을 참조하세요.|


## 최소 수준 120이 필요한 Columnstore 인덱스 기능


이 섹션에서는 호환성 수준 120 이상에서 활성화되는 [columnstore 인덱싱의 기능](http://msdn.microsoft.com/library/dn934994.aspx)을 설명합니다.


| 120은 최소 <br/>호환성<br/> 수준 | Columnstore 인덱스 <br/>기능 이름 | Columnstore 인덱스 <br/>기능 설명 |
| :-- | :-- | :-- |
| 120 | 스냅숏 격리(SI) 수준 및 <br/><br/>읽기 커밋된 스냅숏 격리(RCSI) 수준. | 쿼리 계획이 Columnstore 인덱스를 포함하는 경우 SI 및 RCSI는 과도한 잠금이 필요 없이 부분적으로 완료된 트랜잭션에서 나온 데이터가 쿼리결과에 포함되지 않도록 합니다. |
| 120 | 인덱스 조각 모음 | 삭제된 행은 인덱스를 명시적으로 다시 빌드하지 않고 제거됩니다.<br/><br/>**ALTER INDEX ... REORGANIZE**는 테이블과 인덱스가 온라인으로 계속 작동할 수 있는 동안 Columnstore 인덱스에서 삭제된 행을 제거합니다. |
| 120 | AlwaysOn [읽을 수 있는 보조 복제](http://msdn.microsoft.com/library/ff878253.aspx)에서 액세스 가능 | 분석 쿼리를 AlwaysOn 보조 복제에 오프로드하여 연산 분석을 위한 성능을 개선할 수 있습니다. |
| 120 | 푸시 다운 집계, <br/>집계 함수의 테이블 탐색 단계 중 | 이후 단계에 복사해야 하는 데이터가 감소하도록 쿼리 계획에 이전의 중간 계산을 완료하여 성능을 개선합니다.<br/><br/>**MIN**, **MAX**, **SUM**, **COUNT**, **AVG**에 적용됩니다.<br/><br/>문자열에는 적용되지 않지만 데이터 형식이 8바이트 이하인 경우에만 적용됩니다. |
| 120 | 문자열 기반 **WHERE** 절의 푸시 다운 | 조건자 푸시 다운 최적화는 &#x5b;var&#x5d;char or n&#x5b;var&#x5d;char 형식의 문자열 데이터를 비교하는 쿼리의 속도를 향상시킬 수 있습니다. 이 최적화는:<br/><br/>• 비트맵 필터를 사용하는 **LIKE**를 포함하여 일반적인 비교 연산자에 적용됩니다.<br/><br/>• 문자열 조건자 한 개가 있는 경우에만 작동합니다.<br/><br/>• 제품이 지원하는 모든 조합에 대해 작동합니다.<br/><br/>비트맵 필터에 대한 자세한 내용은: [쿼리 실행 비트비트맵 필터 소개](http://blogs.msdn.com/b/sqlqueryprocessing/archive/2006/10/27/query-execution-bitmaps.aspx) 블로그 게시물을 참조하세요. |



<a id="ExampleQueryParallelCciByCompatLevel" name="ExampleQueryParallelCciByCompatLevel"></a>


&nbsp;


## 호환성 수준별 쿼리 계획 변경 예제


Transact-SQL **INSERT...SELECT** 문 한 개에 대해, 이 섹션은 호환성 수준 120과 130 사이의 쿼리 계획 변경 내용을 표시합니다.


#### 원본 테이블 스키마


다음 테이블에는 최소 300,000개의 행이 있습니다. 고급 쿼리 계획은 데이터가 너무 적어서 병렬 처리가 의미가 없는 경우 병렬 처리를 방해하지 않습니다.


```
CREATE TABLE [dbo].[ccitestoriginal](
	[FinanceKey] [int] NOT NULL,
	[DateKey] [int] NOT NULL,
	[OrganizationKey] [int] NOT NULL,
	[DepartmentGroupKey] [int] NOT NULL,
	[ScenarioKey] [int] NOT NULL,
	[AccountKey] [int] NOT NULL,
	[Amount] [float] NOT NULL,
	[Date] [datetime] NULL
) ON [PRIMARY];
GO

CREATE CLUSTERED COLUMNSTORE INDEX [cci_ccitestoriginal]
	ON [dbo].[ccitestoriginal]
	WITH (DROP_EXISTING = OFF) ON [PRIMARY];
GO
```


#### 쿼리 계획을 생성하는 스크립트


뒤에 나오는 Transact-SQL 스크립트에 이 단계를 적용합니다.


1. **Ssms.exe**에서 사용자의 데이터베이스에 연결합니다.
2. Transact-SQL 스크립트를 **Ssms.exe** 쿼리 창에 붙여넣습니다.
3. **120**이 **ALTER DATABASE** 문의 값이 되도록 스크립트를 편집합니다.
4. 스크립트에서 **INSERT INTO** 문 *앞*에 있는 부분만 실행합니다.
5. 메뉴 옵션 활성화: **쿼리** > **실제 실행 계획 포함(Ctrl+M)**.
6. **INSERT INTO** 문만 실행합니다.<br/><br/>
7. 메뉴 옵션: **쿼리** > **실제 실행 계획 포함(Ctrl+M)**을 비활성화합니다.
8. 끝으로 스크립트에서 **INSERT INTO** 문의 *뒤*에 있는 부분만 실행합니다 .
9. 참고로 **실행 계획** 탭의 **120**에 대한 쿼리 계획은 **결과** 탭과 유사합니다.<br/>-- -- -- -- -- --
10. 스크립트를 **130**이 **ALTER DATABASE**에 대한 값이 되도록 편집합니다.
11. 이전 단계에서 설명한 대로 스크립트를 다시 실행합니다.
12. 참고로 **130**에 대한 쿼리는 다르며 병렬 처리를 포함합니다.


&nbsp;


```
go
SET NOCOUNT ON;
go

ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL =
	120
	--130
;
go

DROP TABLE ccitest_into_work;
go

SELECT *
	INTO ccitest_into_work  -- Create an empty copy of the table.
	FROM ccitestoriginal
	WHERE 1=2;
go

CREATE CLUSTERED COLUMNSTORE INDEX ccitest_into_work_cci
	ON ccitest_into_work;
go

SET NOCOUNT OFF;
go
	
	-- Run this INSERT statement alone!
	--
	-- First run all the preceding Transact-SQL statements.
	-- Then run this one INSERT statement alone.
	-- Then run all remaining Transact-SQL statements.

INSERT INTO ccitest_into_work WITH (TABLOCK)
	SELECT TOP 300000 *
		FROM ccitestoriginal;
go

SET NOCOUNT ON;
go

DROP TABLE ccitest_into_work;
go
```


#### 두 계획의 표시



<br/>**120:** 호환성 수준이 **120**인 경우 쿼리 계획이 여기에 있습니다.


![수준 120에서 계획][1-Plan-at-level-120]


<br/>**130:** 호환성 수준이 **130**인 경우 쿼리 계획이 여기에 있습니다.


**130** 계획은 **120** 계획에 없는 *병렬 처리*를 포함합니다 .


이 계획의 표시는 **Ssms.exe**에서 더 넓습니다. 여기서 더 나은 표시를 위해 스크린숏이 두 부분으로 분할됩니다. 두 번째 부분은 첫 번째 부분을 계속합니다.


![수준 130에서 계획][2-Plan-at-level-130]


## 데이터베이스의 기본 호환성 수준


Azure SQL 데이터베이스 서버를 버전 V11에서 V12와 같이 업그레이드 하는 각 데이터베이스의 호환성 수준은 변경되지 않고 남아 있습니다. 업그레이드 후 **ALTER DATABASE** 문을 사용하여 지정된 데이터베이스에서 호환성 수준을 증가시킬 수 있습니다.


새로 만든 모든 데이터베이스는 SQL 데이터베이스가 허용하는 최대 호환성 수준을 갖습니다.



<!-- Image references. -->

[1-Plan-at-level-120]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b-120.png

[2-Plan-at-level-130]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b12-130.png

<!---HONumber=Oct15_HO3-->