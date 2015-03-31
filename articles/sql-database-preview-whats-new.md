<properties 
	pageTitle="SQL 데이터베이스 V12의 새로운 기능" 
	description="2014년 12월 이상에 추가된 Azure SQL 데이터베이스의 최신 향상된 기능을 설명합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# SQL 데이터베이스 V12의 새로운 기능


<!--
GeneMi , 2015-March-05 Thursday 20:06pm
Remove 'Azure' from topic title, append 'V12' (per J.G.).
-->



Azure SQL 데이터베이스 버전 V12가 2015년 1월에 GA(일반 공급)되었습니다. 이 주요 업데이트를 통해 SQL 데이터베이스는 이제 Microsoft SQL Server 엔진과 거의 완벽한 호환성을 제공합니다. 또한 고객에게 더욱 뛰어난 성능을 제공합니다. 이러한 기능 향상은 SQL Server 응용 프로그램을 SQL 데이터베이스로 간단하게 마이그레이션하는 데 도움이 되며 데이터베이스 작업이 많은 고객에게 유용합니다.


일부 지역은 여전히 미리 보기 상태이며 아직 GA되지 않았습니다. [GA-per-region 테이블](../sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable)에서 해당 지역을 확인해 보세요. 해당 지역에 GA될 때까지 V12는 프로덕션 데이터베이스가 아니라 테스트 데이터베이스에 가장 적합합니다.


**이 새로운 세대의 Microsoft Azure를 활용하려면 SQL 데이터베이스에 [등록](https://portal.azure.com)하세요. 먼저 Microsoft Azure를 구독해야 합니다. [무료 Azure 평가판](http://azure.microsoft.com/pricing/free-trial)을 등록하고 [가격](http://azure.microsoft.com/pricing/details/sql-database) 정보를 검토할 수 있습니다.**


[최신 SQL 데이터베이스 업데이트 V12(미리 보기) 계획 및 준비](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)에서 V12 계획 및 V11 데이터베이스 업그레이드 경로를 시작합니다.


### 주요 사항


Azure SQL 데이터베이스 V12의 주요 사항은 다음과 같습니다.


- **보안**은 [포함된 데이터베이스 사용자](../sql-database-preview-whats-new/#UsersInContainedDatabases)의 새로운 기능입니다. 두 가지 다른 기능은 [행 수준 보안](../sql-database-preview-whats-new/#RowLevelSecurity)과 [동적 데이터 마스킹](../sql-database-preview-whats-new/#DynamicDataMasking)입니다. 이러한 기능은 여전히 미리 보기 상태이며 아직 GA되지 않았습니다.


- 대규모 데이터베이스의 **관리가 용이**하므로 병렬 쿼리(프리미엄에만 해당), [테이블 분할](http://msdn.microsoft.com/library/ms187802.aspx), [온라인 인덱싱](http://msdn.microsoft.com/library/ms188388.aspx), 2GB 크기 제한이 제거된 안전한 대규모 인덱스 다시 작성 및 [alter database](http://msdn.microsoft.com/library/bb522682.aspx) 문의 추가 옵션을 사용하여 더 많은 작업 지원


- **주요 프로그래밍 기능을 지원**하므로 [CLR 통합](http://msdn.microsoft.com/library/ms189524.aspx), T-SQL [창 함수](http://msdn.microsoft.com/library/ms189461.aspx), [XML 인덱스](http://msdn.microsoft.com/library/bb934097.aspx) 및 데이터 [변경 내용 추적](http://msdn.microsoft.com/library/bb933875.aspx)을 사용하여 더욱 강력한 응용 프로그램 디자인 적용


- 데이터 마트 및 소규모 분석 작업에 대해 메모리 내 [columnstore](http://msdn.microsoft.com/library/gg492153.aspx) 쿼리(프리미엄에만 해당)를 지원하는 **혁신적인 성능** 제공


- 확장된 DMV[데이터베이스 관리 뷰](http://msdn.microsoft.com/library/ms188754.aspx) 집합에서 새 테이블 뷰를 100개 이상 표시하여 **모니터링 및 문제 해결** 향상


- **표준 계층의 새로운 S3 성능 수준:** 표준과 프리미엄 간에 가격 유연성 증가. S3은 표준 계층에서 사용 가능한 모든 기능과 추가 DTU(데이터베이스 처리량 단위)를 제공합니다.


## 1. 설명한 새로운 V12 기능 향상의 세부 정보


이 섹션에서는 각 범주의 새로운 기능에 이름을 지정하고 설명합니다.


### 1.1 범주: 확장된 데이터베이스 관리


| 기능 | 설명 |
| :--- | :--- |
| . | ***2014년 12월:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>포함된 데이터베이스 사용자 | 이제 마스터 데이터베이스에 해당 로그인이 없어도 포함된 데이터베이스에서 사용자를 만들 수 있습니다. 따라서 데이터베이스를 다른 서버로 이동하기가 훨씬 수월해졌습니다. 클라이언트 응용 프로그램의 연결 코드는 포함된 데이터베이스 사용자를 사용하는지 여부에 상관없이 동일합니다.<br/><br/>이 기능은 더욱 보장된 서비스 수준 계약을 활용하려는 고객에게 필요할 수 있습니다.<br/><br/>일반적으로 고객은 이 기능을 사용하는 것이 좋습니다. 그러나 일부 고객은 기존 *login+user* 쌍이 더 적합한 특정 시나리오가 있을 수 있습니다.<br/><br/>자세한 내용은 다음을 참조하세요.<br/>- [Azure SQL 데이터베이스 보안 지침 및 제한 사항](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [포함된 데이터베이스](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| 테이블 분할 | [테이블 분할](http://msdn.microsoft.com/library/ms190787.aspx)에 대한 이전 제한이 제거되었습니다. |
| 대규모 트랜잭션 | V12에서는 더 이상 단일 트랜잭션에서 최대 2GB의 데이터 수정으로 제한되지 않습니다. <br/><br/> 한 가지 이점은 대규모 인덱스를 다시 작성할 때 더 이상 2GB 트랜잭션 크기 제한으로 제한되지 않는다는 것입니다. 일반적인 내용은 [Azure SQL 데이터베이스 리소스 제한](http://msdn.microsoft.com/library/azure/dn338081.aspx)을 참조하세요. |
| 온라인 인덱스 작성 및 다시 작성 | V12 이전에는 Azure SQL 데이터베이스가 일반적으로 ALTER INDEX 문의 (ONLINE=ON) 절을 지원했지만 BLOB 유형 열의 인덱스에는 지원되지 않았습니다. 이제 V12에서는 BLOB 열의 인덱스에 대해서도 (ONLINE=ON)을 지원합니다.<br/><br/> ONLINE 기능을 사용하면 인덱스가 다시 작성되는 동안에도 쿼리에서 인덱스를 활용할 수 있습니다. |
| CHECKPOINT 지원 | V12에서는 데이터베이스에 대해 T-SQL CHECKPOINT 문을 실행할 수 있습니다. |
| ALTER TABLE 향상 | 테이블을 사용 가능한 상태로 유지하면서 많은 alter 열 작업을 수행할 수 있습니다. 자세한 내용은 [ALTER TABLE(Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx)을 참조하세요. |
| TRUNCATE TABLE 향상 | 특정 파티션의 잘림을 허용합니다. 자세한 내용은 [TRUNCATE TABLE(Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx)을 참조하세요. |
| ALTER DATABASE의 추가 옵션 | V12에서는 ALTER DATABASE 문에서 사용할 수 있는 추가 옵션을 지원합니다. <br/><br/> 자세한 내용은 [ALTER DATABASE(Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) 또는 [Azure SQL 데이터베이스 Transact-SQL 참조](http://msdn.microsoft.com/library/azure/ee336281.aspx)를 참조하세요. |
| 추가 DBCC 명령 | 이제 여러 추가 DBCC 명령이 V12에서 제공됩니다. 자세한 내용은 [Azure SQL 데이터베이스 Transact-SQL 참조](http://msdn.microsoft.com/library/azure/ee336281.aspx)를 참조하세요. |


### 1.2 범주: 프로그래밍 및 응용 프로그램 지원


| 기능 | 설명 |
| :--- | :--- |
| . | ***2015년 2월:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> 동적 데이터 마스킹 미리 보기 | 쿼리에서 행 집합이 생성될 때 설정된 데이터 마스킹 정책이 데이터 일부분을 'X'로 바꿔 중요한 데이터를 가리는 방식으로 보호할 수 있습니다. 마스킹 작업이 완료되면 수정된 행 집합이 클라이언트에 전송됩니다.<br/><br/>신용 카드 번호의 마지막 몇 자리를 제외한 모든 숫자를 마스킹하는 경우를 예로 들 수 있습니다.<br/><br/>**참고:** 이 기능은 미리 보기 상태이며 프로덕션 환경에서 사용할 수 있는 일반 공급 기능은 아직 발표되지 않았습니다.<br/><br/>자세한 내용은 [Azure SQL 데이터베이스 동적 데이터 마스킹 시작](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/)을 참조하세요. |
| . | ***2015년 1월:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> RLS(행 수준 보안) 미리 보기 | **주의:** V12가 GA(일반 공급) 상태인 지역에서도 RLS 기능은 현재 *preview* 상태로만 제공됩니다. RLS는 GA 상태가 될 때까지는 업무상 중요한 프로덕션 데이터베이스에서 사용하기에 적합하지 않습니다.<br/><br/>T-SQL의 새로운 CREATE SECURITY POLICY 문을 통해 RLS를 구현할 수 있습니다. RLS를 사용하는 경우 데이터베이스 서버는 행 집합이 호출자에게 반환되기 전에 일부 데이터 행을 필터링하는 조건을 추가합니다.<br/><br/>관련 업계에서는 RLS를 세분화된 액세스 제어라고도 합니다.<br/><br/>코드 예제 및 기타 정보는 [행 수준 보안 미리 보기](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx)를 참조하세요. |
| . | ***2014년 12월:*** |
| T-SQL 쿼리의 창 함수 | ANSI 창 함수는 [OVER 절](http://msdn.microsoft.com/library/ms189461.aspx)을 통해 액세스합니다. <br/><br/> Itzik Ben-Gan은 창 함수 및 OVER 절에 대해 자세히 설명하는 뛰어난 [블로그 게시물](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1)을 작성했습니다. |
| .NET CLR 통합 | .NET Framework의 CLR(공용 언어 런타임)이 V12에 통합되었습니다. <br/><br/> 완전히 이진 코드로 컴파일된 SAFE 어셈블리만 지원됩니다. 자세한 내용은 [CLR 통합 프로그래밍 모델 제한](http://msdn.microsoft.com/library/ms403273.aspx)을 참조하세요. <br/><br/> 다음 항목에서 이 기능에 대한 자세한 내용을 확인할 수 있습니다. <br/> * [SQL Server CLR 통합 소개](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY(Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| 데이터 변경 내용 추적 | 이제 데이터베이스 또는 테이블 수준에서 데이터 변경 내용 추적을 구성할 수 있습니다. <br/><br/> 변경 내용 추적에 대한 자세한 내용은 [변경 내용 추적 정보(SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx)를 참조하세요. |
| XML 인덱스 | V12에서는 T-SQL 문 CREATE XML INDEX 및 CREATE SELECTIVE XML INDEX를 사용할 수 있습니다. <br/><br/> XML 인덱스에 대한 자세한 내용은 다음 위치에서 확인할 수 있습니다. <br/> * [CREATE XML INDEX(Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> * [선택적 XML 인덱스 만들기, 변경 및 삭제](http://msdn.microsoft.com/library/jj670109.aspx) |
| 힙으로 테이블 만들기 | V12에서는 클러스터된 인덱스가 없는 테이블을 만들 수 있습니다. <br/><br/> 이 기능은 쿼리 결과에서 테이블을 만드는 T-SQL SELECT...INTO 문을 지원하므로 특히 유용합니다. |
| 응용 프로그램 역할 | 보안 및 사용 권한 제어를 위해 V12 미리 보기에서는 [응용 프로그램 역할](http://msdn.microsoft.com/library/ms190998.aspx)에 대해 GRANT - DENY - REMOVE 문을 실행할 수 있습니다. |


### 1.3 범주: 고객 통찰력 향상


| 기능 | 설명 |
| :--- | :--- |
| . | ***2014년 12월:*** |
| DMV(동적 관리 뷰) | 여러 DMV가 V12에 추가되었습니다. 자세한 내용은 [Azure SQL 데이터베이스 Transact-SQL 참조](http://msdn.microsoft.com/library/azure/ee336281.aspx)를 참조하세요. |
| 변경 내용 추적 | V12는 변경 내용 추적을 완벽히 지원합니다. <br/><br/> 이 기능에 대한 자세한 내용은 [변경 내용 추적 사용 및 사용 안 함(SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx)을 참조하세요. |


### 1.4 프리미엄 서비스 계층의 성능 향상


이러한 성능 향상은 프리미엄 서비스 계층 내의 P2 및 P3 수준에 적용됩니다.


| 기능 | 설명 |
| :--- | :--- |
| . | ***2014년 12월:*** |
| 쿼리에 대한 병렬 처리 | V12에서는 병렬 처리를 활용할 수 있는 쿼리에 대해 더 높은 병렬 처리 수준을 제공합니다. |
| 보다 짧은 I/O 대기 시간 | V12에서는 입력/출력 작업의 대기 시간이 크게 단축되었습니다. |
| IOPS 증가 | V12에서는 더 많은 IOPS(초당 입/출력)를 처리할 수 있습니다. |
| 기록 속도 | V12에서는 초당 더 많은 데이터 변경 내용을 기록할 수 있습니다. |


### 1.5 기능 향상 요약


V12에서는 SQL 데이터베이스와 SQL Server 제품 간의 기능 호환성이 향상되었습니다. V12에서는 많이 사용하는 기능과 프로그래밍 기능 지원에 중점을 둡니다. 따라서 보다 효율적으로 즐겁게 개발을 진행할 수 있습니다.  이제 SQL 데이터베이스 응용 프로그램을 클라우드로 쉽게 이동할 수 있습니다.


또한 프리미엄 계층에서 V12는 성능을 훨씬 향상시킵니다. 일부 응용 프로그램은 쿼리 속도가 훨씬 향상됩니다. 작업이 많은 응용 프로그램은 강력한 처리량이 안정적으로 나타납니다.


## <a name="V12AzureSqlDbPreviewGaTable"></a>2. 지역별 V12 GA(일반 공급) 상태


Azure SQL 데이터베이스의 V12 버전은 2014년 12월에 아래 표에 나와 있는 지역에서 미리 보기 및 테스트용으로만 출시되었습니다. 지역에 따라 각기 다른 날짜에 미리 보기에서 완전한 GA(일반 공급) 릴리스로 상태가 변경될 예정입니다. 다음 표에는 각 지역의 현재 V12 릴리스 상태가 나와 있습니다.


> [AZURE.NOTE]
> 미리 보기 기간 동안에는 [가격](http://azure.microsoft.com/pricing/details/sql-database/) 할인 혜택이 적용됩니다. 2015년 3월 31부터는 모든 지역에서 원래 GA 수준의 가격이 적용됩니다.


| Azure 지역 | 현재 릴리스<br/>V12 상태 | 프로모션 날짜<br/>GA |
| :--- | :--- | :--- |
| 미국 중남부 | GA(일반 공급) | 2015년 2월 9일 |
| 미국 중부 | GA(일반 공급) | 2015년 2월 9일 |
| 미국 중북부 | GA(일반 공급) | 2015년 2월 9일 |
| 미국 서부 | GA(일반 공급) | 2015년 2월 9일 |
| 미국 동부 | GA(일반 공급) | 2015년 2월 9일 |
| 미국 동부 2 | GA(일반 공급) | 2015년 2월 9일 |
| 동아시아 | GA(일반 공급) | 2015년 2월 24일 |
| 동남아시아 | GA(일반 공급) | 2015년 2월 24일 |
| 일본 서부 | GA(일반 공급) | 2015년 2월 24일 |
| 일본 동부 | GA(일반 공급) | 2015년 2월 24일 |
| 북유럽 | GA(일반 공급) | 2015년 1월 29일 |
| 서유럽 | GA(일반 공급) | 2015년 1월 29일 |
| 브라질 남부 | 사용할 수 없음 | 2015년 3/4분기 예정 |
| 오스트레일리아 동부 | 미리 보기 | 2015년 2/4분기 예정 |
| 오스트레일리아 남동부 | 미리 보기 | 2015년 2/4분기 예정 |


GA 상태에 도달한 지역에서는 모든 새 구독과 후속 데이터베이스에서 V12 서비스 아키텍처를 사용하므로 최신 기능에 액세스할 수 있습니다. 현재 문서에는 V12에서 제공하는 새로운 기능이 나와 있습니다.


GA 상태에서 V12 이전 서버 및 데이터베이스를 소유한 고객은 이러한 새 기능을 프로덕션에 사용하기 위해 데이터베이스를 V12 서비스 아키텍처로 업그레이드(이동)할 수 있습니다. V12 데이터베이스는 기본, 표준 또는 프리미엄 [가격 계정](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) 중 하나를 사용해야 합니다.


## 3. 진행 방법


[최신 Azure SQL 데이터베이스 업데이트 미리 보기로 업그레이드 계획 및 준비](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)에서 Azure SQL 데이터베이스 V11에서 V12로 데이터베이스를 업그레이드하는 방법을 알아볼 수 있습니다.


V12와 같은 버전 번호는 다음 Transact-SQL 문에서 반환되는 값을 나타냅니다.<br/>
**SELECT @@version;**


- 11.0.9228.18*(V11)*
- 12.0.2000.8*(또는 좀 더 높은 버전, V12)*


*할인:* V12 미리 보기 기간 동안 V12를 할인 가격으로 사용할 수 있습니다. 새로운 S3 수준은 표준 계층에서 사용 가능한 모든 기능과 100 DTU(데이터베이스 처리량 단위)를 $0.2016/시간으로 제공합니다. V12 미리 보기 중에는 가격이 $0.1008/시간까지 할인됩니다. 자세한 내용은 [SQL 데이터베이스 가격](http://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.


## 4. V12 미리 보기에 대한 주의 사항


Azure SQL 데이터베이스 업데이트 V12(미리 보기)로의 업그레이드 및 업그레이드 후 작업과 관련하여 다음 주의 사항을 참조하세요.


### 4.1 V12용 미리 보기 포털


다음 두 Azure 관리 포털 중 첫 번째만 V12 데이터베이스를 지원합니다.


- [http://portal.azure.com/](http://portal.azure.com/)
 - 이 새 포털은 미리 보기 상태이며, 아직 GA(일반 공급)되지 않았습니다.<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - 이 이전 포털은 V12를 지원하도록 업데이트되지 않습니다.


고객은 VS2013(Visual Studio 2013)을 사용하여 Azure SQL 데이터베이스에 연결하는 것이 좋습니다. VS2013은 다음과 같은 작업에 사용될 수 있습니다.


- T-SQL 문 실행
- 스키마 디자인
- 온라인 또는 오프라인으로 데이터베이스 개발


대신 완전한 기능을 갖춘 무료 버전의 VS2013인 [Visual Studio Community 2013](https://www.visualstudio.com/ko-kr/news/vs2013-community-vs.aspx/)에 연결할 수 있습니다.


이전 Azure 관리 포털의 데이터베이스 페이지에서 **Visual Studio에서 열기**를 클릭하여 Azure SQL 데이터베이스에 연결할 컴퓨터에서 VS2013을 시작할 수 있습니다.


또는 SSMS(SQL Server Management Studio) 2014 [CU6](http://support.microsoft.com/kb/3031047/)을 사용하여 Azure SQL 데이터베이스에 연결할 수 있습니다. 자세한 내용은 블로그 게시물<br/>[Azure SQL 데이터베이스용 클라이언트 도구 업데이트](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/)를 참조하세요.


### 4.2 V12 미리 보기로의 업그레이드 *during* 주의 사항


> [AZURE.NOTE]
> V12로 업그레이드하는 동안에도 V12 이전 버전 데이터베이스 *remains available*하여 데이터에 액세스할 수 있습니다.


- 50GB보다 큰 데이터베이스의 경우 V12로 업그레이드하려면 최대 24시간이 걸릴 수 있습니다.
- Azure SQL 데이터베이스 V12 서버로 데이터베이스를 업그레이드하는 중에는 업그레이드가 완료될 때까지 서버에서 다음 작업을 수행할 수 없습니다.
 - 새 데이터베이스 만들기
 - 서버에 데이터베이스 복사
 - 삭제된 데이터베이스 복원
 - 특정 시점으로 데이터베이스 복원
 - 지역에서 복제
- V12로의 업그레이드가 완료되는 시점부터 시스템이 DNS(Domain Name System) 항목을 V12 데이터베이스로 업데이트할 때까지 몇 분이 걸립니다. DNS 업데이트가 수행되고 나면 클라이언트 응용 프로그램이 데이터베이스에 연결할 수 있습니다.
- Web 및 Business 서비스 가격 계층은 V12에서 지원되지 않습니다. 또한 이후 어떤 버전에서도 지원되지 않습니다.


### 4.3 V12 미리 보기로의 업그레이드 *after* 주의 사항


- V12로 바로 업그레이드된 데이터베이스는 다시 이전 버전으로 되돌릴 수 없습니다.
- Azure SQL 데이터베이스 V12 가격 50% 할인 혜택은 2015년 3월 31일(화요일)에 만료될 때까지 모든 지역에서 적용됩니다. V12가 미리 보기 및 GA 상태인 지역에도 할인이 적용됩니다.


### 4.4 V12로 업그레이드한 *after*의 내보내기 및 가져오기


[Azure 웹 포털](http://portal.azure.com/)을 사용하여 V12 데이터베이스를 내보내거나 가져올 수 있습니다. 또한 다음 중에서 원하는 도구를 사용할 수도 있습니다.


- SSMS(SQL Server Management Studio) 2014
- Visual Studio 2013
- 데이터 계층 응용 프로그램 프레임워크(DacFX)


그러나 이러한 도구를 사용하려면 먼저 해당 도구가 새 V12 기능을 지원하도록 최신 업데이트를 설치해야 합니다.


- [SQL Server Management Studio 2014 누적 업데이트 6](http://support2.microsoft.com/kb/3031047)
- [Visual Studio 2013의 SQL Server 데이터베이스 2015년 2월 업데이트](https://msdn.microsoft.com/data/hh297027)
- [Azure SQL 데이터베이스 V12의 2015년 2월 데이터 계층 응용 프로그램 프레임워크(DacFx)](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] 위 도구 링크는 2015년 3월 2일부터 업데이트되었습니다. 이러한 도구의 이 새 업데이트를 사용하는 것이 좋습니다.


[2. 지역별 V12 GA(일반 공급) 상태]:#V12AzureSqlDbPreviewGaTable


<!-- EndOfFile -->


<!--HONumber=47-->
