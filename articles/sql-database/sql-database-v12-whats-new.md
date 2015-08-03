<properties 
	pageTitle="SQL 데이터베이스 V12의 새로운 기능 | Microsoft Azure" 
	description="클라우드에서 Azure SQL 데이터베이스를 사용하는 비즈니스 시스템이 이제 V12 버전으로 업그레이드하면 이익이 되는지 이유를 설명합니다." 
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
	ms.topic="get-started-article" 
	ms.date="07/21/2015" 
	ms.author="genemi"/>


# SQL 데이터베이스 V12의 새로운 기능


이 항목에서는 Azure SQL 데이터베이스의 새 V12 버전이 V11 버전에 비해 더 많은 이점을 설명합니다.


V12에 기능은 추가 계속됩니다. Azure에 대한 서비스 업데이트 웹 페이지를 방문하고 해당 필터를 사용해 보세요.


- [SQL 데이터베이스 서비스](http://azure.microsoft.com/updates/?service=sql-database)에 대해 필터링되었습니다.
- SQL 데이터베이스 기능의 [GA(General Availability) 공지](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)에 대해 필터링되었습니다.


## 향상된 SQL Server와의 응용 프로그램 호환성


SQL 데이터베이스 V12에 대한 주요 목표는 Microsoft SQL Server 2014와 호환성을 개선하는 것입니다. 다른 영역 중 V12는 프로그래밍의 중요한 영역에서 SQL server와의 패리티를 실현합니다. 예를 들어 다음과 같습니다.


- [CLR(Common Language Runtime) 어셈블리](http://msdn.microsoft.com/library/ms189524.aspx)
- [OVER](http://msdn.microsoft.com/library/ms189461.aspx)를 포함한 [Window 함수](https://msdn.microsoft.com/library/bb934097.aspx) 
- [XML 인덱스](https://msdn.microsoft.com/library/bb934097.aspx) 및 [선택적 XML 인덱스](http://msdn.microsoft.com/library/jj670104.aspx)
- [변경 내용 추적](http://msdn.microsoft.com/library/bb933875.aspx)
- [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx)
- [전체 텍스트 검색](http://msdn.microsoft.com/library/ms142571.aspx)


SQL 데이터베이스에서 아직 지원되지 않는 작은 집합의 기능은 [여기](http://msdn.microsoft.com/library/azure/ee336281.aspx)를 참조하세요.


## 자세한 프리미엄 성능, 새로운 성능 수준


V12에서 추가 비용 없이 25% 씩 모든 프리미엄 성능 수준에 할당된 데이터베이스 처리량 단위(DTU)가 증가되었습니다. 다음과 같은 새로운 기능으로 보다 큰 성능 향상을 수행할 수 있습니다.


- 메모리 내 [columnstore 인덱스](http://msdn.microsoft.com/library/gg492153.aspx)에 대한 지원.
- [TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx)과 관련된 기능이 향상된 [행으로 테이블 분할](http://msdn.microsoft.com/library/ms187802.aspx)
- 동적 관리 뷰[(DMV)](http://msdn.microsoft.com/library/ms188754.aspx)의 가용성 및 확장 이벤트[(XEvents)](https://msdn.microsoft.com/library/bb630282.aspx)로 모니터링을 돕고 성능을 튜닝합니다.


## 클라우드 SaaS 공급 업체에 대한 향상된 지원


V12에서만 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)의 공용 미리 보기 및 새 표준 성능 수준 S3을 릴리스했습니다. 클라우드 SaaS 공급 업체를 위해 특별히 설계된 솔루션입니다. 탄력적 데이터베이스 풀로 다음을 수행할 수 있습니다.


- 많은 수의 데이터베이스에 대한 비용을 절감하는 데이터베이스 간에 DTU를 공유합니다.
- [탄력적 데이터베이스 작업](sql-database-elastic-jobs-overview.md)을 실행하여 대규모로 데이터베이스를 관리합니다.


## 향상된 보안 기능


보안은 클라우드에서 비즈니스를 실행하는 사용자에 대한 주요 관심사입니다. V12에 출시된 최신 보안 기능에는 다음이 포함됩니다.


- [행 수준 보안](http://msdn.microsoft.com/library/dn765131.aspx)(RLS)
- [동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)
- [포함된 데이터베이스](http://msdn.microsoft.com/library/azure/ff394108.aspx)
- GRANT, DENY, REVOKE로 관리되는 [응용 프로그램 역할](http://msdn.microsoft.com/library/ms190998.aspx)
- [투명한 데이터 암호화](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)(TDE)


## 복구가 필요한 경우 증가되는 비즈니스 연속성


V12는 대폭 개선된 복구 지점 목표(RPO) 및 예상된 복구 시간 (ERTs)를 제공합니다.


| 비즈니스 연속성 기능 | 이전 버전 | V12 |
| :-- | :-- | :-- |
| 지역 복원 | • RPO < 24시간.<br/>• ERT < 12시간. | • RPO < 1시간.<br/>• ERT < 12시간. |
| 표준 지역 복제 | • RPO < 30분.<br/>• ERT < 2 시간. | • RPO < 5초.<br/>• ERT < 30초. |
| 활성 지역 복제 | • RPO < 5분.<br/>• ERT < 1시간. | • RPO < 5초.<br/>• ERT < 30초. |


자세한 내용은 [SQL 데이터베이스 비즈니스 연속성](https://msdn.microsoft.com/library/azure/hh852669.aspx)을 참조하세요.


## 지금 업그레이드하는 더 많은 이유


지금 고객이 V11에서 Azure SQL 데이터베이스 V12로 업그레이드해야 하는 수 많은 이유가 있습니다.


- SQL 데이터베이스 V12는 V11를 넘어서는 다수의 기능이 있습니다.
- 새 기능을 V12에 계속 추가하지만 V11에는 새로운 기능이 추가되지 않습니다.
- Microsoft SQL Server용으로 릴리스되기 전에 SQL 데이터베이스 V12에서 대부분의 새로운 기능리 릴리스됩니다.


## 게이트웨이는 V12에서 재시도 논리를 더 이상 제공하지 않습니다.


V12 버전 이전의 Azure SQL 데이터베이스에는 데이터베이스와 사용자의 클라이언트 프로그램 사이의 모든 상호 작용을 버퍼하기 위해 프록시로 동작하는 게이트웨이가 있었습니다. 이 게이트웨이는 일부 *일시적인 오류*에 자동화된 *다시 시도 논리*를 제공했습니다.

 V12에서는 이 게이트웨이를 제거했습니다. 이제 사용자의 프로그램은 일시적인 오류를 더욱 완벽하게 처리해야 합니다. 자세한 내용은 다음을 참조하세요.


- [SQL 데이터베이스 연결: 링크, 모범 사례, 설계 지침](sql-database-connect-central-recommendations.md#gatewaynoretry)


## V12를 이미 사용 중입니까?


SQL 데이터베이스 서비스의 이전 버전에서 실행되는 데이터베이스나 논리 서버가 있는지 알 수 있는 쉬운 방법 중 하나는 다음을 수행해 보면 됩니다.


1. [Azure Preview 포털](http://portal.azure.com/)로 이동합니다.
2. **찾아보기**를 클릭합니다.
3. **SQL Server**를 클릭합니다.
4. 서버 또는 데이터베이스 옆의 아이콘이 다음을 표시합니다.
 - ![V12 서버에 대한 아이콘](./media/sql-database-v12-whats-new/v12_icon.png) **V12 논리 서버**
 - ![이전 버전의 서버에 대한 아이콘](./media/sql-database-v12-whats-new/earlier_icon.png) **이전 버전의 논리 서버**


버전을 식별하기 위한 다른 기술은 데이터베이스에서 `SELECT @@version;` 문을 실행하고 다음과 유사한 결과를 봅니다.


- **12**.0.2000.10 &nbsp; *버전 V12)*
- **11**.0.9228.18 &nbsp; *(버전 V11)*


V12 논리 서버에만 V12 데이터베이스를 호스팅할 수 있습니다. V12 서버는 V12 데이터베이스만 호스팅할 수 있습니다.


아직 V12에서 실행하지 않는 경우, [SQL 데이터베이스 V12로 업그레이드](sql-database-v12-upgrade.md)의 단계에 따라 논리 서버를 업그레이드할 수 있습니다.


## <a name="V12AzureSqlDbPreviewGaTable"></a>미리 보기 영역


V12는 2014년 12월에 릴리스되지만 [미리 보기](http://azure.microsoft.com/support/legal/preview-supplemental-terms/) 상태에서만 릴리스됩니다. 2015년 4월까지 V12는 거의 모든 지역에서 GA(General Availability)로 승격되었습니다.


V12는 다음 지역에서 미리 보기로 제공됩니다.


| Azure 지역 | V12의 현재 <br/>릴리스 상태 | GA 프로모션 날짜,<br/> 예정 |
| :--- | :--- | :--- |
| 오스트레일리아 동부 | **미리 보기** | 2015년 3분기, 예정 |
| 오스트레일리아 남동부 | **미리 보기** | 2015년 3분기, 예정 |

 

<!---HONumber=July15_HO4-->