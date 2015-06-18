<properties 
	pageTitle="SQL 데이터베이스 V12의 새로운 기능" 
	description="2015년 5월까지 Azure SQL 데이터베이스 V12에 추가된 최신 기능을 설명합니다." 
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
	ms.date="05/15/2015" 
	ms.author="genemi"/>


# SQL 데이터베이스 V12의 새로운 기능


Azure SQL 데이터베이스 버전 V12([일부 지역에서는 미리 보기](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable))는 Microsoft SQL Server 엔진과 거의 완벽하게 호환됩니다. 향상된 최신 기능은 SQL Server 응용 프로그램을 SQL 데이터베이스로 간단하게 마이그레이션하는 데 도움이 되며 시스템에서 대량의 데이터베이스 작업을 안정적으로 처리하도록 지원합니다.


이 새로운 세대의 Microsoft Azure를 [시작](sql-database-get-started.md)하려면 SQL 데이터베이스에 [등록](https://portal.azure.com)하세요. 먼저 Microsoft Azure를 구독해야 합니다. [무료 Azure 평가판](http://azure.microsoft.com/pricing/free-trial)을 등록하고 [가격](http://azure.microsoft.com/pricing/details/sql-database) 정보를 검토할 수 있습니다.


[SQL 데이터베이스 V12로 업그레이드 계획 및 준비](sql-database-v12-plan-prepare-upgrade.md)에서 V12 계획 및 이전 버전의 데이터베이스를 V12로 업그레이드하는 경로를 시작합니다.


> [AZURE.TIP]Azure SQL 데이터베이스의 새로운 기능에 대한 정보의 기본 웹 페이지는 [http://azure.microsoft.com/updates/](http://azure.microsoft.com/updates/)의 "서비스 업데이트" 웹 페이지입니다. 서비스 업데이트 웹 페이지의 **서비스** 컨트롤에서 **SQL 데이터베이스**를 선택합니다.


## 주요 사항


- **Azure 미리 보기 포털**은 버전 V12 또는 필요에 따라 이전 버전에서 SQL 데이터베이스용 데이터베이스 및 서버를 만드는 데 [사용할 수 있습니다](http://portal.azure.com/). Azure 미리 보기 포털에서 SQL 데이터베이스 데이터베이스를 지정한 후 해당 데이터베이스를 호스팅할 SQL 데이터베이스 서버를 지정합니다.
 - 2015년 4월 기준으로 [이전 Azure 포털](http://manage.windowsazure.com/)도 계속 지원되고 있습니다.


- Azure 미리 보기 포털을 사용하여 새 데이터베이스를 만들 때 SQL 데이터베이스 서버의 **버전을 선택**합니다. 기본값은 V12이지만 더 이전 버전의 SQL 데이터베이스 서버를 선택할 수도 있습니다.


- **보안**은 [포함된 데이터베이스 사용자](sql-database-v12-whats-new.md#UsersInContainedDatabases)의 새로운 기능입니다. 다른 두 가지 기능은 [행 수준 보안](sql-database-v12-whats-new.md#RowLevelSecurity)과 [동적 데이터 마스킹](sql-database-v12-whats-new.md#DynamicDataMasking)입니다. 이들 기능은 여전히 미리 보기 상태이며 아직 GA되지 않았습니다.


- 대규모 데이터베이스의 **관리가 용이**하므로 병렬 쿼리(프리미엄에만 해당), [테이블 분할](http://msdn.microsoft.com/library/ms187802.aspx), [온라인 인덱싱](http://msdn.microsoft.com/library/ms188388.aspx), 2GB 크기 제한이 제거된 안전한 대규모 인덱스 다시 작성 및 [ALTER DATABASE](http://msdn.microsoft.com/library/bb522682.aspx) 명령의 추가 옵션을 사용하여 더 많은 작업 지원


- **주요 프로그래밍 기능을 지원**하므로 [CLR 통합](http://msdn.microsoft.com/library/ms189524.aspx), Transact-SQL [창 함수](http://msdn.microsoft.com/library/ms189461.aspx), [XML 인덱스](http://msdn.microsoft.com/library/bb934097.aspx) 및 데이터 [변경 내용 추적](http://msdn.microsoft.com/library/bb933875.aspx)을 사용하여 더욱 강력한 응용 프로그램 디자인 적용


- 데이터 마트 및 소규모 분석 작업에 대해 메모리 내 [columnstore 인덱스](http://msdn.microsoft.com/library/gg492153.aspx) 쿼리(프리미엄에만 해당)를 지원하는 **혁신적인 성능** 제공


- 확장된 [DMV](http://msdn.microsoft.com/library/ms188754.aspx)(데이터베이스 관리 뷰) 집합에서 새 테이블 뷰를 100개 이상 표시하여 **모니터링 및 문제 해결** 향상


- **표준 계층의 새로운 S3 성능 수준:** 표준과 프리미엄 간에 [가격](sql-database-upgrade-new-service-tiers.md) 유연성 증가. S3은 표준 계층에서 사용 가능한 모든 기능과 추가 DTU(데이터베이스 처리량 단위)를 제공합니다.


## V12 기능 향상: 확장된 데이터베이스 관리


| 기능 | 설명 |
| :--- | :--- |
| . | ***2015년 5월 1일:*** |
| Powershell을 사용하여 이전 버전에서 V12로 업그레이드 | 새로운 Powershell cmdlet을 사용하여 V11 또는 기타 V12 이전 버전에서 Azure SQL 데이터베이스 V12로의 업그레이드를 시작, 취소 또는 모니터링할 수 있습니다.<br/><br/>이러한 cmdlet에 대한 참조 설명서는 다음을 참조하세요.<br/>* [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)<br/>* [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)<br/>* [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx) |
| . | ***2015년 4월:*** |
| TDE | TDE(투명한 데이터 암호화) (미리 보기)는 데이터베이스, 연결된 백업 및 저장된 트랜잭션 로그 파일에 대한 실시간 암호화 및 암호 해독을 수행하여 악의적인 활동으로부터 보호합니다. TDE는 응용 프로그램에 대한 변경이 필요하지 않습니다.<br/><br/>자세한 내용은 다음을 참조하세요.<br/>- [Azure SQL 데이터베이스를 사용하여 투명한 데이터 암호화](http://msdn.microsoft.com/library/dn948096.aspx) - 단계별 지침<br/>- [TDE(투명한 데이터 암호화)](http://msdn.microsoft.com/library/bb934049.aspx) - 일반 설명 |
| Transact-SQL 인증서, 키 및 암호화 기능 | 이제 Azure SQL 데이터베이스 V12에서 인증서, 대칭 키 및 비대칭 키를 만들 수 있습니다. 이제 대부분의 암호화 함수가 지원됩니다.<br/><br/>자세한 내용은 다음을 참조하세요.<br/>- [CREATE CERTIFICATE(Transact-SQL)](http://msdn.microsoft.com/library/ms187798.aspx)<br/>- [CREATE SYMMETRIC KEY(Transact-SQL)](http://msdn.microsoft.com/library/ms188357.aspx)<br/>- [CREATE ASYMMETRIC KEY(Transact-SQL)](http://msdn.microsoft.com/library/ms174430.aspx)<br/>- [암호화 함수(Transact-SQL)](http://msdn.microsoft.com/library/ms173744.aspx) |
| SQL 데이터베이스의 탄력적 풀 | 수천 개의 데이터베이스로 규모를 확장하면 단순한 작업이 복잡해지고, 이는 폭발적인 증가의 단점입니다. SaaS 개발자는 비즈니스 성장에 따라 스키마 변경 및 기타 관리 작업을 처리하기 위해 복잡한 논리를 작성하는 데 많은 시간을 소비합니다.<br/><br/>[탄력적 데이터베이스](sql-database-elastic-pool.md)을 사용하면 작업이 훨씬 쉬워집니다. 스크립트를 작성하여 작업으로 제출하면 나머지 작업은 Azure SQL 데이터베이스에서 수행합니다. |
| 가격 책정 계층 권장 사항 및 STA | 데이터베이스를 Web 또는 Business 버전에서 새로운 기본, 표준 또는 프리미엄 서비스 계층 중 하나로 이동할 때 이제 Azure 미리 보기 포털에서 직접 가격 책정 계층 권장 사항을 가져올 수 있습니다.<br/><br/>Azure SQL 데이터베이스 서비스는 기존 데이터베이스에 대한 성능, 기능 요구 사항을 분석합니다. [STA(서비스 계층 관리자)](sql-database-service-tier-advisor.md)는 성능 데이터를 읽어보고 데이터베이스에 가장 적합한 서비스 계층을 추천합니다. |
| DMV 사용 권한 | 이전에는 여러 DMV(동적 관리 뷰)를 실행하려면 VIEW SERVER STATE 권한이 필요했습니다. 이제 Azure SQL 데이터베이스 V12에서는 대부분의 경우, 기본 또는 표준 서비스 계층에 있는 데이터베이스에서 SQL 데이터베이스 관리자 계정으로 DMV를 실행할 수 있습니다.<br/><br/>프리미엄 계층에서는 데이터베이스에서 VIEW DATABASE STATE 권한을 사용하여 DMV에 대한 액세스 권한을 추가 사용자에게 부여할 수 있습니다.<br/><br/>자세한 내용은 해당하는 개별 [DMV](http://msdn.microsoft.com/library/ms188754.aspx)에 대한 사용 권한 섹션을 참조하세요. |
| DBCC SQLPERF | 이제 [DBCC SQLPERF](http://msdn.microsoft.com/library/ms189768.aspx) 명령을 Azure SQL 데이터베이스 V12에서 사용할 수 있습니다.  *재설정 대기* 및 *래치 통계* 옵션은 SQL 데이터베이스 V12에서 지원되지 않습니다. |
| . | ***2014년 12월:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>데이터베이스에 포함된 사용자 | 이제 마스터 데이터베이스에 해당 로그인이 없어도 데이터베이스에서 사용자를 만들 수 있습니다. 이런 사용자를 *포함된 사용자*라고 합니다. 따라서 데이터베이스를 다른 서버로 이동하기가 훨씬 수월해졌습니다. 클라이언트 응용 프로그램의 연결 코드는 포함된 데이터베이스 사용자를 사용하는지 여부에 상관없이 동일합니다.<br/><br/>이 기능은 더욱 보장된 서비스 수준 계약을 활용하는 뛰어난 방법입니다.<br/><br/>일반적으로 이 기능을 사용하는 것이 좋습니다. 그러나 기존 *로그인+사용자* 쌍이 더 적합한 특정 시나리오가 있을 수 있습니다.<br/><br/>자세한 내용은 다음을 참조하세요.<br/>- [Azure SQL 데이터베이스 보안 지침 및 제한 사항](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [포함된 데이터베이스](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| 테이블 분할 | [테이블 분할](http://msdn.microsoft.com/library/ms190787.aspx)에 대한 이전 제한이 제거되었습니다. |
| 대규모 트랜잭션 | V12에서는 더 이상 단일 트랜잭션에서 최대 2GB의 데이터 수정으로 제한되지 않습니다. <br/><br/> 한 가지 이점은 대규모 인덱스를 다시 작성할 때 더 이상 2GB 트랜잭션 크기 제한으로 제한되지 않는다는 것입니다. 일반적인 내용은 [Azure SQL 데이터베이스 리소스 제한](http://msdn.microsoft.com/library/azure/dn338081.aspx)을 참조하세요. |
| 온라인 인덱스 작성 및 다시 작성 | V12 이전에는 Azure SQL 데이터베이스가 일반적으로 [ALTER INDEX](http://msdn.microsoft.com/library/ms188388.aspx) 명령의 (ONLINE=ON) 절을 지원했지만 BLOB 유형 열의 인덱스에는 지원되지 않았습니다. 이제 V12에서는 BLOB 열의 인덱스에 대해서도 (ONLINE=ON)을 지원합니다.<br/><br/> ONLINE 기능을 사용하면 인덱스가 다시 작성되는 동안에도 쿼리에서 인덱스를 활용할 수 있습니다. |
| CHECKPOINT 지원 | V12에서는 데이터베이스에 대해 Transact-SQL [CHECKPOINT](http://msdn.microsoft.com/library/ms188748.aspx) 명령을 실행할 수 있습니다. |
| ALTER TABLE 향상 | 테이블을 사용 가능한 상태로 유지하면서 많은 alter 열 작업을 수행할 수 있습니다. 자세한 내용은 [ALTER TABLE(Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx)을 참조하세요. |
| TRUNCATE TABLE 향상 | 특정 파티션의 잘림을 허용합니다. 자세한 내용은 [TRUNCATE TABLE(Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx)을 참조하세요. |
| ALTER DATABASE의 추가 옵션 | V12에서는 [ALTER DATABASE](http://msdn.microsoft.com/library/ms174269.aspx) 명령에서 사용할 수 있는 추가 옵션을 지원합니다. <br/><br/> 자세한 내용은 [Azure SQL 데이터베이스 Transact-SQL 참조](http://msdn.microsoft.com/library/azure/ee336281.aspx)를 참조하세요. |
| 추가 DBCC 명령 | 이제 여러 추가 [DBCC](http://msdn.microsoft.com/library/ms188796.aspx) 명령이 V12에서 제공됩니다. 자세한 내용은 [Azure SQL 데이터베이스 Transact-SQL 참조](http://msdn.microsoft.com/library/azure/ee336281.aspx)를 참조하세요. |


## 프로그래밍 및 응용 프로그램 지원


| 기능 | 설명 |
| :--- | :--- |
| . | ***2015년 5월 1일:*** |
| 인덱스 크기 증가 | SQL 데이터베이스 V12에서는 최대 32개의 열을 단일 복합 인덱스 키로 결합할 수 있습니다. 허용 가능한 최대 인덱스 값 크기의 경우, 클러스터형 인덱스는 900바이트, 비클러스터형 인덱스는 1700바이트입니다.<br/><br/>버전 V11을 비롯하여 SQL 데이터베이스 V12 이전의 모든 버전은 16개 열과 900바이트로 제한되어 있습니다. |
| . | ***2015년 4월:*** |
| 전체 텍스트 검색 미리 보기 | [FTS(전체 텍스트 검색)](http://msdn.microsoft.com/library/ms142571.aspx)를 사용하면 LIKE 연산자 보다 더 강력한 방법으로 문자 기반 열을 쿼리할 수 있습니다. 예:<br/><br/>- FREETEXT: 단어가 정확하게 일치하지 않아도 검색 구문의 *의미*와 일치하는 구문을 검색합니다.<br/>- CONTAINS: 서로 긴밀한 *물리적 근접성*이 있는 두 검색 용어가 포함된 셀 값을 검색합니다.<br/><br/>**참고:** 이 기능은 미리 보기 상태이며 프로덕션 환경에서 사용할 수 있는 일반 공급 기능은 아직 발표되지 않았습니다. FTS 미리 보기 기능은 Microsoft SQL Server의 FTS 범위 중 일부만 지원됩니다. |
| . | ***2015년 2월:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> 동적 데이터 마스킹 미리 보기 | 쿼리에서 행 집합이 생성될 때 설정된 데이터 마스킹 정책이 데이터 일부분을 'X'로 바꿔 중요한 데이터를 가리는 방식으로 보호할 수 있습니다. 마스킹 작업이 완료되면 수정된 행 집합이 클라이언트에 전송됩니다.<br/><br/>신용 카드 번호의 마지막 몇 자리를 제외한 모든 숫자를 마스킹하는 경우를 예로 들 수 있습니다.<br/><br/>**참고:** 이 기능은 미리 보기 상태이며 프로덕션 환경에서 사용할 수 있는 일반 공급 기능은 아직 발표되지 않았습니다.<br/><br/>자세한 내용은 [Azure SQL 데이터베이스 동적 데이터 마스킹 시작](sql-database-dynamic-data-masking-get-started.md)을 참조하세요. |
| . | ***2015년 1월:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> RLS(행 수준 보안) 미리 보기 | Transact-SQL의 새로운 [CREATE SECURITY POLICY](http://msdn.microsoft.com/library/dn765135.aspx) 명령을 통해 RLS를 구현할 수 있습니다. RLS를 사용하는 경우 데이터베이스 서버는 행 집합이 호출자에게 반환되기 전에 일부 데이터 행을 필터링하는 조건을 추가합니다.<br/><br/>관련 업계에서는 RLS를 세분화된 액세스 제어라고도 합니다.<br/><br/>**참고:** 이 기능은 미리 보기 상태이며 프로덕션 환경에서 사용할 수 있는 일반 공급 기능은 아직 발표되지 않았습니다.<br/><br/>코드 예제 및 기타 정보는 [행 수준 보안 미리 보기](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx)를 참조하세요. |
| . | ***2014년 12월:*** |
| Transact-SQL 쿼리의 창 함수 | ANSI 창 함수는 [OVER 절](http://msdn.microsoft.com/library/ms189461.aspx)을 통해 액세스합니다. <br/><br/> Itzik Ben-Gan은 창 함수 및 OVER 절에 대해 자세히 설명하는 뛰어난 [블로그 게시물](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1)을 작성했습니다. |
| .NET CLR 통합 | .NET Framework의 CLR(공용 언어 런타임)이 V12에 통합되었습니다. <br/><br/> 완전히 이진 코드로 컴파일된 SAFE 어셈블리만 지원됩니다. 자세한 내용은 [CLR 통합 프로그래밍 모델 제한](http://msdn.microsoft.com/library/ms403273.aspx)을 참조하세요. <br/><br/> 다음 항목에서 이 기능에 대한 자세한 내용을 확인할 수 있습니다. <br/> * [SQL Server CLR 통합 소개](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY(Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx) |
| 데이터 변경 내용 추적 | 이제 데이터베이스 또는 테이블 수준에서 데이터 변경 내용 추적을 구성할 수 있습니다. <br/><br/> 변경 내용 추적에 대한 자세한 내용은 [변경 내용 추적 정보(SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx)를 참조하세요. |
| XML 인덱스 | V12에서는 Transact-SQL 명령 [CREATE XML INDEX](http://msdn.microsoft.com/library/bb934097.aspx) 및 [CREATE SELECTIVE XML INDEX](http://msdn.microsoft.com/library/jj670104.aspx)를 사용할 수 있습니다. |
| 힙으로 테이블 만들기 | V12에서는 클러스터된 인덱스가 없는 테이블을 만들 수 있습니다. <br/><br/> 이 기능은 쿼리 결과에서 테이블을 만드는 Transact-SQL [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx) 명령을 지원하는 데 특히 유용합니다. |
| 응용 프로그램 역할 | 보안 및 사용 권한 제어를 위해 V12에서는 [응용 프로그램 역할](http://msdn.microsoft.com/library/ms190998.aspx)에 대해 [GRANT](http://msdn.microsoft.com/library/ms187965.aspx) - [DENY](http://msdn.microsoft.com/library/ms188338.aspx) - [REVOKE](http://msdn.microsoft.com/library/ms187728.aspx) 명령을 실행할 수 있습니다. |


## 고객 통찰력 향상


| 기능 | 설명 |
| :--- | :--- |
| . | ***2014년 12월:*** |
| DMV(동적 관리 뷰) | 여러 DMV가 V12에 추가되었습니다. 자세한 내용은 [Azure SQL 데이터베이스 Transact-SQL 참조](http://msdn.microsoft.com/library/azure/ee336281.aspx)를 참조하세요. |
| 변경 내용 추적 | V12는 변경 내용 추적을 완벽히 지원합니다. <br/><br/> 이 기능에 대한 자세한 내용은 [변경 내용 추적 사용 및 사용 안 함(SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx)을 참조하세요. |
| Columnstore 인덱스 | 인덱싱된 열에 동일한 값이 반복적으로 포함된 경우 columnstore 인덱스를 사용하면 데이터 웨어하우스에 대한 시스템 성능이 향상됩니다.  [columnstore 인덱스](http://msdn.microsoft.com/library/gg492088.aspx)는 중복 값을 압축하여 쿼리하는 동안 액세스해야 하는 실제 행 수를 줄입니다. |


## 프리미엄 서비스 계층의 성능 향상


이러한 성능 향상은 프리미엄 서비스 계층 내의 P2 및 P3 수준에 적용됩니다.


| 기능 | 설명 |
| :--- | :--- |
| . | ***2014년 12월:*** |
| 쿼리에 대한 병렬 처리 | V12에서는 병렬 처리를 활용할 수 있는 쿼리에 대해 더 높은 병렬 처리 수준을 제공합니다. |
| 보다 짧은 I/O 대기 시간 | V12에서는 입력/출력 작업의 대기 시간이 크게 단축되었습니다. |
| IOPS 증가 | V12에서는 더 많은 IOPS(초당 입/출력)를 처리할 수 있습니다. |
| 기록 속도 | V12에서는 초당 더 많은 데이터 변경 내용을 기록할 수 있습니다. |


## 2015년 8월 1일부터 API 기본 버전으로 V12 사용  


| 기능 | 설명 |
| :--- | :--- |
| . | ***2015년 8월:*** |
| REST 또는 PowerShell을 사용하여 서버 만들기 | 서버 버전을 지정하지 않고 서버를 만들면 기본 버전이 V11에서 V12로 변경됩니다.<br/><br/>[Azure 미리 보기 포털](http://portal.azure.com)에 맞춰 조정됩니다. |
| Transact-SQL, [REST](http://msdn.microsoft.com/library/azure/gg715283.aspx) 또는 [PowerShell](http://msdn.microsoft.com/library/azure/dn806332.aspx)을 사용하여 데이터베이스 만들기 | V11 서버에서 버전 또는 서비스 목표를 지정하지 않고 새 데이터베이스를 만들면 Web 및 Business 대신 [S0](http://azure.microsoft.com/pricing/details/sql-database/)이 기본 서비스 목표로 지정됩니다. Azure 미리 보기 포털에서 V12 서버에 맞춰 조정됩니다. |


## 기능 향상 요약


V12에서는 SQL 데이터베이스와 SQL Server 제품 간의 기능 호환성이 향상되었습니다. V12에서는 많이 사용하는 기능과 프로그래밍 기능 지원에 중점을 둡니다. 따라서 보다 효율적으로 즐겁게 개발을 진행할 수 있습니다. 이제 SQL 데이터베이스 응용 프로그램을 클라우드로 쉽게 이동할 수 있습니다.


또한 프리미엄 계층에서 V12는 성능을 훨씬 향상시킵니다. 일부 응용 프로그램은 쿼리 속도가 훨씬 향상됩니다. 작업이 많은 응용 프로그램은 강력한 처리량이 안정적으로 나타납니다.


## <a name="V12AzureSqlDbPreviewGaTable"></a>지역별 V12 GA(일반 공급) 상태


> [AZURE.NOTE]
> [Pricing](http://azure.microsoft.com/pricing/details/sql-database/) 미리 보기 기간 동안에는 할인 혜택이 적용됩니다. 2015년 3월 31일 화요일부터는 모든 지역에서 원래 GA 수준의 가격이 적용됩니다.


| Azure 지역 | V12의 현재 <br/>릴리스 상태 | GA<br/>프로모션 날짜 |
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
| 브라질 남부 | GA(일반 공급) | 2015년 4월 21일 |
| 오스트레일리아 동부 | 미리 보기 | 2015년 2/4분기 예정 |
| 오스트레일리아 남동부 | 미리 보기 | 2015년 2/4분기 예정 |


GA 상태에 도달한 지역에서는 모든 새 구독과 후속 데이터베이스에서 V12 서비스 아키텍처를 사용하므로 최신 기능에 액세스할 수 있습니다. 현재 문서에는 V12에서 제공하는 새로운 기능이 나와 있습니다.


아직 GA 상태가 아닌 미리 보기 상태에 있는 지역의 경우, [V12 데이터베이스를 사용하기 위한 옵션을 활성화](sql-database-v12-sign-up.md)해야 합니다.


GA 상태에서 V12 이전 서버와 데이터베이스가 있는 경우는 데이터베이스를 V12 서비스 아키텍처로 업그레이드(또는 이동)할 수 있습니다. 그러면 프로덕션 환경을 위한 새로운 기능을 사용할 수 있습니다. V12 데이터베이스는 기본, 표준 또는 프리미엄 [가격 책정 계층](sql-database-upgrade-new-service-tiers.md) 중 하나를 사용해야 합니다.


## 진행 방법


[Azure SQL 데이터베이스 V12로 업그레이드 계획 및 준비](sql-database-v12-plan-prepare-upgrade.md)에서, Azure SQL 데이터베이스 V11에서 V12로 데이터베이스를 업그레이드하는 방법을 알아볼 수 있습니다.


V12와 같은 버전 번호는 다음 Transact-SQL 문에서 반환되는 값을 나타냅니다.<br/> **SELECT @@version;**


- 11.0.9228.18*(V11)*
- 12.0.2000.8*(또는 좀 더 높은 버전, V12)*


V12에 대한 최신 가격 정보는 [SQL 데이터베이스 가격](http://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.


## V12 업그레이드에 대한 주의 사항


V11 데이터베이스를 V12로 업그레이드하는 중과 업그레이드한 후의 제한 사항에 대해 알아야 할 중요한 사항이 있습니다. 자세한 내용은 *SQL 데이터베이스 V12로 업그레이드 계획 및 준비* 항목의 [중간점](sql-database-v12-plan-prepare-upgrade.md#limitations)에 대한 이 링크에서 읽어볼 수 있습니다.


## 기타 최신 뉴스 소스


Azure SQL 데이터베이스 V12에 대한 이 *새로운 기능* 항목은 2015년 4월 30일 후에 마무리되어 더 이상 업데이트되지 않을 예정입니다. 새로운 기능 및 기타 공지에 대한 정보는 다음 공지 링크로 전환되고 있습니다.


- **서비스 업데이트** 웹 페이지의 Azure SQL 데이터베이스에 대한 [공지](http://azure.microsoft.com/updates/?service=sql-database)
 - 서비스 컨트롤이 *SQL 데이터베이스*로 설정된 경우는 해당 웹 페이지에 있는 **RSS** 아이콘을 클릭할 수 있습니다.
- Twitter 팔로우: **@SQLTechCenter**


[V12 general availability (GA) status per region]: #V12AzureSqlDbPreviewGaTable

<!---HONumber=58--> 