<properties 
	pageTitle="SQL 데이터베이스 Web 또는 Business 데이터베이스를 새 서비스 계층으로 업그레이드" 
	description="Azure SQL 데이터베이스 Web 또는 Business 데이터베이스를 새 Azure SQL 서비스 기본, 표준 및 프리미엄 서비스 계층/성능 수준으로 업그레이드합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/08/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# SQL 데이터베이스 Web 또는 Business 데이터베이스를 새 서비스 계층으로 업그레이드

Azure SQL [Web 및 Business 데이터베이스는 더 이상 제공되지 않을](sql-database-web-business-sunset-faq.md) 예정입니다. 따라서 이제 기존 Web 또는 Business 데이터베이스를 [기본, 표준, 프리미엄 또는 탄력적 서비스 계층](sql-database-service-tiers.md)으로 업그레이드해야 합니다.


> [AZURE.IMPORTANT] Web 또는 Business 데이터베이스를 새로운 서비스 계층으로 업그레이드해도 데이터베이스가 오프라인으로 설정되지는 않습니다. 데이터베이스는 온라인 및 업그레이드 작업을 통해 사용할 수 있는 상태로 유지됩니다.


업그레이드를 지원하기 위해 SQL 데이터베이스 서비스는 각 데이터베이스에 적절한 서비스 계층 및 성능 수준(가격 책정 계층)을 권장합니다. 서비스는 각 데이터베이스의 기존 사용량을 분석하여 기존 데이터베이스의 워크로드를 실행하는 데 가장 적합한 계층을 권장합니다.

각 데이터베이스에 대한 권장 가격 계층은 웹 또는 비즈니스 데이터베이스 서비스 계층을 변경하는 과정 중에 또는 SQL 데이터베이스 V12로 업그레이드하는 동안 제공됩니다.

특정 환경에 따라 서비스는 데이터베이스의 일부 또는 전부를 [탄력적인 데이터베이스 풀](sql-database-elastic-pool.md)로 업그레이드하는 것을 권장할 수 있습니다.

사용 중지된 데이터베이스에 대한 권장된 서비스 계층을 보려면 [Azure 미리 보기 포털](https://portal.azure.com) 또는 PowerShell을 사용할 수 있습니다. 단계별 지침은 다음을 참조하세요.

- [SQL 데이터베이스 V12로 업그레이드(Azure 미리 보기 포털)](sql-database-v12-upgrade.md)
- [SQL 데이터베이스 V12로 업그레이드 준비(PowerShell)](sql-database-upgrade-server.md)


여기서 기억해야 할 사항은, SQL 데이터베이스에서 반드시 특정 서비스 계층이나 서비스 수준을 사용해야 하는 것은 아니라는 점입니다. 따라서 데이터베이스의 요구 사항이 변경되면 사용 가능한 서비스 계층과 성능 수준 간에 쉽게 변경이 가능합니다. 실제로 기본, 표준 및 Premium SQL 데이터베이스에서는 시간 단위로 요금이 청구되며 24시간 동안 각 데이터베이스를 4번 확장 및 축소할 수 있습니다. 즉, 응용 프로그램의 요구 사항과 변화하는 워크로드에 따라 데이터베이스의 성능 요구 사항, 기능 집합 및 비용 효율성을 최대화하도록 서비스 계층과 성능 수준을 조정할 수 있습니다. 또한 데이터베이스의 서비스 계층과 성능 수준을 평가하고 변경하는 작업(확장 및 축소)은 예약된 유지 관리 및 성능 튜닝 루틴의 일부분으로 포함해야 하는 지속적인 프로세스입니다.
 
Web/Business와 새 서비스 계층 간의 차이점에 대한 자세한 내용 및 추가 마이그레이션 세부 정보는 [Web 및 Business 데이터베이스 마이그레이션 지침 설명서](http://download.microsoft.com/download/3/C/9/3C9FF3D8-E702-4F5D-A38C-5EFA1DBA73E6/Azure_SQL_Database_Migration_Cookbook.pdf)를 다운로드합니다.



## 개요

<p> Azure Web 및 Business SQL 데이터베이스는 데이터베이스에 예약된 리소스 용량 없이 공유 다중 테넌트 환경에서 실행됩니다. 이 공유 리소스 환경 내에 있는 다른 데이터베이스의 작업이 성능에 영향을 줄 수 있습니다. 지정된 시점의 리소스 가용성은 시스템에서 동시에 실행되는 다른 작업에 따라 달라집니다. 이로 인해 데이터베이스 응용 프로그램 성능이 매우 가변적이며 예측할 수 없습니다. 이와 같은 예측할 수 없는 성능은 관리하기가 어려우므로 더 예측 가능한 성능을 원한다는 고객 의견이 많았습니다.

이 의견을 수렴하여 Azure SQL 데이터베이스 서비스는 예측 가능한 성능과 비즈니스 지속성 및 보안을 위한 여러 새로운 기능을 제공하는 새 데이터베이스 서비스 계층[(기본, 표준 및 프리미엄)](sql-database-service-tiers.md)을 도입했습니다. 이러한 새 서비스 계층은 환경에서 실행 중인 다른 고객 작업에 관계없이 데이터베이스 작업에 지정된 수준의 리소스를 제공하도록 설계되었습니다. 이로 인해 예측 가능한 성능 동작이 발생합니다.

이러한 변경과 더불어 현재 Web 및 Business(W/B) 데이터베이스에 가장 적합한 새 서비스 계층을 평가하고 결정하는 방법 및 실제 업그레이드 프로세스에 대한 질문이 제기되었습니다.

궁극적으로는 기능, 성능 및 비용 간의 균형을 최적 상태로 유지하는 동시에 응용 프로그램의 요구 사항과 업무상의 요구를 완벽하게 충족하는 서비스 계층 및 성능 수준 조합을 선택하는 것이 가장 좋습니다.

이 문서에서는 Web 또는 Business 데이터베이스를 새 서비스 계층/성능 수준 중 하나로 업그레이드하는 안내식 방법을 제공합니다.


## Web 및 Business 데이터베이스 업그레이드

Web 또는 Business 데이터베이스를 새로운 서비스 계층/성능 수준으로 업그레이드해도 데이터베이스가 오프라인으로 설정되지는 않습니다. 즉, 업그레이드 작업 중 데이터베이스가 계속 작동합니다. 실제로 새 성능 수준으로 전환할 때 아주 짧은 시간(보통 몇 초) 동안 데이터베이스 연결이 일시적으로 끊어질 수 있습니다. 응용 프로그램에 연결 종료에 대한 일시적인 오류 처리 기능이 있을 경우 업그레이드 종료 시의 연결 끊김을 방지할 수 있습니다.

Web 또는 Business 데이터베이스를 새 서비스 계층으로 업그레이드하려면 다음 단계를 수행해야 합니다.


1. 기능을 기준으로 서비스 계층 결정
2. 과거 리소스 사용량을 적합한 기준으로 성능 수준 결정
3. Web 또는 Business 데이터베이스의 기존 성능이 더 높은 수준인 프리미엄에 해당하는 이유 확인
4. 더 낮은 성능 수준에 맞게 워크로드 튜닝
5. *새 서비스 계층/성능 수준으로 업그레이드*
6. 새 서비스 계층/성능 수준으로의 업그레이드 모니터링
7. 업그레이드 후 데이터베이스 모니터링



## 1. 기능을 기준으로 서비스 계층 결정

기본, 표준 및 프리미엄 서비스 계층에서는 각기 다른 기능 집합을 제공하므로 적합한 계층을 선택하는 첫 단계에서는 응용 프로그램과 업무에 필요한 최소 기능 수준을 제공하는 서비스 계층을 확인해야 합니다.

예를 들어 백업을 유지해야 하는 기간이나 [표준 또는 활성 지역 복제](sql-database-business-continuity.md) 기능이 필요한지 여부, 필요한 전체 최대 데이터베이스 크기 등을 고려한 다음 이러한 요구 사항에 따라 최소 서비스 계층 항목을 선택합니다.

'기본' 계층은 주로 작업량이 적은 초소형 데이터베이스에 사용됩니다. 따라서 업그레이드 시에는 대개 필요한 최소 기능 수준을 기준으로 하여 '표준' 또는 '프리미엄' 계층부터 시작해야 합니다.

새 서비스 계층의 대략적인 기능 및 성능 수준과 각 계층을 비교한 내용이 다음 표에 나와 있습니다.

[AZURE.INCLUDE [SQL DB 서비스 계층 테이블](../../includes/sql-database-service-tiers-table.md)]

**서비스 계층 및 성능 수준 비교 내용을 확인할 수 있는 추가 리소스:**

| 문서 | 설명 |
|:--|:--|
|[Azure SQL 데이터베이스 서비스 계층 및 성능 수준](sql-database-service-tiers.md)| 각 서비스 계층의 개요, 메트릭 및 기능 및 관리 포털과 DVM을 사용하여 데이터베이스 사용량을 모니터링하는 방법을 설명합니다. |
|[Azure SQL 데이터베이스 비즈니스 연속성](sql-database-business-continuity.md)|무중단 업무 방식과 각 서비스 계층에서 제공되는 재해 복구 기능(특정 시점 복원, 지역에서 복원, 지역에서 복제)에 대해 자세히 설명합니다.|
|[SQL 데이터베이스 가격](http://azure.microsoft.com/pricing/details/sql-database/)|각 서비스 계층 및 성능 수준의 자세한 가격 정보를 제공합니다.|

<br>

데이터베이스 요구 사항을 충족하는 적합한 서비스 계층을 선택한 후 다음 단계에서는 실제 데이터베이스 작업에 적합한 성능 수준을 선택합니다.



## 2\. 과거 리소스 사용량을 적합한 기준으로 성능 수준 결정

Azure SQL 데이터베이스 서비스는 관리 포털과 시스템 뷰를 표시하여 기존 Web 또는 Business 데이터베이스와 비슷한 권장 새 서비스 계층 및 성능 수준 정보를 제공합니다.

Web 및 Business 데이터베이스에는 보장된 DTU/리소스 한도가 연결되어 있지 않으므로 S2 성능 수준 데이터베이스에서 사용할 수 있는 리소스 양에 따라 백분율 값을 정규화합니다. 특정 간격의 데이터베이스 평균 DTU 사용률은 해당 간격의 CPU, IO 및 로그 사용 중 가장 높은 백분율 값으로 계산할 수 있습니다.


Azure 미리 보기 포털에서 DTU 백분율 사용 방법에 대한 간략한 개요를 파악한 다음 시스템 뷰를 통해 세부 정보를 확인합니다.

Azure SQL 데이터베이스 V12로 서버를 업그레이드할 때 Web 또는 Business 데이터베이스에 대한 권장 서비스 계층을 보려면 Azure 미리 보기 포털을 사용할 수도 있습니다.

### Azure 미리 보기 포털에서 권장 서비스 계층 보는 방법
Azure 포털은 SQL 데이터베이스 V12로 서버를 업그레이드하는 과정 중에 Web 또는 Business 데이터베이스에 적합한 서비스 계층을 권장합니다. 권장 사항은 데이터베이스의 리소스 사용 기록 분석을 기반으로 합니다.

**새 관리 포털**

1. [Azure 미리 보기 포털](https://portal.azure.com)에 로그온하여 Web 또는 Business 데이터베이스를 포함하는 서버를 찾습니다.
2. 서버 블레이드에서 **최신 업데이트** 부분을 클릭합니다.
3. **이 서버 업그레이드**를 클릭합니다.

이제 **이 서버 업그레이드** 블레이드에 권장 서비스 계층과 함께 서버의 Web 또는 Business 데이터베이스 목록이 표시됩니다.



### 관리 포털에서 DTU 사용량을 확인하는 방법
관리 포털에서는 기존 Web 또는 Business 데이터베이스의 DTU 사용량을 확인할 수 있습니다. 최신 Azure 포털에서 DTU 정보가 제공됩니다.


**관리 포털**

1. [관리 포털](https://manage.windowsazure.com)에 로그온하여 기존 Web 또는 Business 데이터베이스로 이동합니다.
2. **모니터** 탭을 클릭합니다.
3. **메트릭 추가**를 클릭합니다.
4. **DTU 백분율**을 선택하고 아래쪽의 확인 표시를 클릭하여 백분율을 확인합니다.

확인 후 표에 **DTU 백분율** 데이터가 표시되어야 합니다. 이 백분율은 S2(표준) 수준 데이터베이스의 DTU(50)와 비교한 백분율입니다.

또한 이 데이터는 5분마다 측정된 샘플의 평균이므로 샘플 간의 일시적인 작업량 증가는 이 메트릭에 반영되지 않을 수도 있습니다.

![DTU 백분율 데이터][2]

위 예제의 데이터는 약 10개 DTU에 해당하는 평균 사용량(50개의 19.23%) 및 최대 28개 DTU의 최대 DTU 백분율(55.83% x 50)을 나타냅니다. 
이 데이터가 일반적인 워크로드를 나타낸다고 가정할 때 초기 업그레이드로는 표준(S1)을 선택하면 적합합니다. 표준(S0)도 평균 사용량인 10개 DTU를 제공하지만 이 계층을 선택하는 경우 데이터베이스가 평균 100% 용량으로 실행되어야 하므로 적합한 계획이라고 할 수 없습니다. S1도 평균 사용량에 적합한 계획이기는 하지만 최대 사용량에 도달하는 경우도 고려해야 합니다. 야간 유지 관리 프로세스 시에만 작업량이 증가하여 고객의 데이터베이스 사용에는 영향이 없다면 해당 시간 동안에는 성능 수준이 낮아져도 됩니다. 그러나 최대 사용량에 도달하는 시기를 알 수 없다면 DTU 백분율 사용량을 더 자세히 분석해야 할 수 있습니다.

데이터베이스 리소스 사용량의 세부 정보를 확인하려는 경우 제공된 시스템 뷰를 사용하면 됩니다.


### 시스템 뷰


현재 데이터베이스가 있는 논리 서버의 master 데이터베이스 [sys.resource_stats](http://msdn.microsoft.com/library/azure/dn269979.aspx) 뷰를 통해 Web 및 Business 데이터베이스 리소스 사용량 데이터에 액세스합니다. 리소스 사용 데이터를 성능 수준 한도의 백분율로 표시합니다. 이 보기는 5분 간격으로 최대 최근 14일간의 데이터를 제공합니다.

> [AZURE.NOTE] 이제 리소스 사용량 데이터를 더 세분화하여 보기 위해(15초마다) Web 및 Business 데이터베이스에서 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 뷰를 사용할 수 있습니다. sys.dm_db_resource_stats만 한 시간동안의 기록 데이터만 유지할 수 있기 때문에 이 DMV를 매 시간 쿼리하여 추가 분석을 위해 해당 데이터를 저장할 수 있습니다.

마스터 데이터베이스에서 다음 쿼리를 실행하여 데이터베이스의 평균 DTU 사용을 검색합니다.

 
                   
     SELECT start_time, end_time
	 ,(SELECT Max(v)
         FROM (VALUES (avg_cpu_percent)
                    , (avg_physical_data_read_percent)
                    , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.resource_stats
    WHERE database_name = '<your db name>'
    ORDER BY end_time DESC;

Web 및 Business 계층에 대해 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 및 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)으로 반환된 데이터는 표준 S2 성능 계층 면에서 백분율을 나타냅니다. 예를 들어, Web 또는 Business 데이터베이스에 대해 실행 시 값이 70%를 반환하는 경우 S2 계층 제한의 70%를 나타냅니다. 또한 Web 및 Business의 경우, 백분율은 100%를 초과하는 숫자를 나타낼 수 있으며, 이는 S2 계층 제한에 기반합니다.

S2 데이터베이스 수준의 DTU 사용 정보를 통해 새 계층 데이터베이스 측면에서 Web 및 Business 데이터베이스의 현재 사용을 정규화하고 더 적합한 위치를 확인할 수 있습니다. 예를 들어 평균 DTU 사용률 값이 80%이면 데이터베이스가 S2 성능에서 데이터베이스 한도의 80% 비율로 DTU를 사용하고 있음을 나타냅니다. **sys.resource_stats** 뷰에 100%보다 큰 값이 표시되는 경우 S2보다 높은 성능 계층이 필요한 것입니다. 예를 들어 최대 DTU 백분율 값이 300%로 표시된다고 가정해 보겠습니다. 이 경우 S2에서 사용 가능한 것보다 리소스를 3배 더 사용하고 있음을 나타냅니다. 적합한 시작 크기를 결정하려면 S2에서 사용 가능한 DTU(50개 DTU)를 다음 상위 크기인 S3/P1 = 100개 DTU(S2의 200%) 및 P2 = 200개 DTU(S2의 400%)와 비교합니다. 최대 백분율이 S2의 300%이므로 P2에서 시작하여 다시 테스트하는 것이 좋습니다.

DTU 사용률과 작업에 필요한 최대 버전에 따라 데이터베이스 작업에 가장 적합한 서비스 계층 및 성능 수준을 결정할 수 있습니다. 관련 수치는 다양한 [성능 수준](sql-database-service-tiers.md)의 상대적인 DTU 기능 및 DTU 백분율을 통해 표시됩니다. 다음은 Web/Business 리소스 사용률과 동등한 새 계층 서비스 수준 간의 매핑을 제공하는 표입니다.

![리소스 사용][4]

> **참고:**
> 다양한 성능 수준 간의 상대 DTU 수치는 [Azure SQL 데이터베이스 벤치마크](http://msdn.microsoft.com/library/azure/dn741327.aspx) 작업을 기반으로 합니다. 데이터베이스 작업이 벤치마크와 다를 가능성이 크므로 위 계산은 새 계층에서 Web/Business 데이터베이스의 초기 맞춤에 대한 지침으로만 사용해야 합니다. 데이터베이스를 새 계층으로 이동한 후에는 이전 섹션에 설명된 프로세스를 사용하여 작업 요구에 맞는 올바른 서비스 계층을 검증/미세 조정하세요.
> 
> 제안된 새 버전 계층/성능 수준은 최근 14일간의 데이터베이스 활동을 고려하지만 이 데이터는 5분간의 평균 리소스 사용 데이터 샘플을 기반으로 합니다. 이에 따라 기간이 5분보다 짧은 단기 활동 급증은 누락될 수 있습니다. 따라서 이 지침은 데이터베이스를 업그레이드할 시작점으로만 사용해야 합니다. 데이터베이스를 제안된 계층으로 업그레이드한 후에는 추가 모니터링, 테스트 및 유효성 검사가 필요하며, 필요에 따라 데이터베이스를 다른 계층/성능 수준으로 상향/하향 이동할 수 있습니다.

다음은 Web/Business 계층 데이터베이스에 대한 계산을 수행하고 이러한 각 5분 데이터 샘플 간격 동안 작업에 적합한 버전을 제안하는 마스터 데이터베이스에 대한 쿼리입니다.

> **참고:** 이 쿼리는 Web/Business 데이터베이스에만 유용하며 새 계층의 데이터베이스에 대해서는 올바른 결과를 제공하지 **않습니다**.

    WITH DTU_mapping AS
    ( SELECT *
        FROM ( VALUES (1, 10,'Basic'), (2, 20,'S0'), (3, 40,'S1'), (4, 100, 'S2')
                    , (5, 200, 'S3/P1'), (6, 1600,'Premium')
           ) AS t(id, percent_of_S2, target_edition)
    ), rc as
    ( SELECT start_time, end_time
           , (SELECT Max(v)
                FROM (VALUES (avg_cpu_percent)
                       		, (avg_physical_data_read_percent)
                       		, (avg_log_write_percent)
                   ) AS value(v)) as [avg_DTU_percent]
        FROM sys.resource_stats	
       WHERE database_name = 'WebDB'
    )
    SELECT rc.*
         , (SELECT TOP(1) t.target_edition
              FROM DTU_mapping AS t
             WHERE t.percent_of_S2 > CAST(1.2*rc.avg_DTU_percent as int)
             ORDER BY t.percent_of_S2) as target_edition
    FROM rc;

**샘플 결과:**

![샘플 결과](media/sql-database-upgrade-new-service-tiers/sample_result.png)

시간 경과에 따른 평균 DTU 사용률 추세를 그래프로 확인할 수 있습니다. 다음은 대부분의 시간 동안 S2 수준 내에 있고 일부 최대 활동이 P1 데이터베이스 수준까지 증가하는 데이터베이스에 대한 예제 그래프입니다. 시간 경과에 따른 DTU 사용은 'Basic' 한도에서 'P1' 한도까지 가변적입니다. 이 데이터베이스를 새 계층에 완전히 맞추려면 'P1' 성능 수준인 프리미엄 서비스 계층 데이터베이스가 필요합니다. 반면, P1 수준으로의 이러한 급증이 드물게 발생하는 경우 S2 수준 데이터베이스를 사용해도 됩니다.

![DPU 사용량](media/sql-database-upgrade-new-service-tiers/DTU_usage.png)

**메모리가 성능에 미치는 영향:** 메모리는 DTU 등급에 기여하는 리소스 차원 중 하나이지만 SQL 데이터베이스는 사용 가능한 모든 메모리를 데이터베이스 작업에 사용하도록 설계되었습니다. 이런 이유로 메모리 사용은 위 쿼리의 평균 DTU 사용에 포함되지 않습니다. 반면, 하위 성능 수준으로 다운사이징하는 경우 데이터베이스에 사용 가능한 메모리가 감소합니다. 이로 인해 높은 IO 사용이 사용되는 DTU에 영향을 줄 수 있습니다. 따라서 하위 성능 수준으로 다운사이징하는 경우 IO 백분율에 충분한 위쪽 공간이 있는지 확인합니다. 이를 모니터링하려면 위에서 언급한 [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV를 사용합니다.



## 3. Web 또는 Business 데이터베이스의 기존 성능이 더 높은 수준인 프리미엄에 해당하는 이유 확인

Web 및 Business 데이터베이스에서는 개별 데이터베이스에 대해 일정량의 리소스 용량이 예약되지 않습니다. 또한 고객이 Web 또는 Business 데이터베이스에 대해 성능을 확장 또는 축소할 수 있는 메커니즘도 없습니다. 따라서 Web 및 Business 데이터베이스 성능은 매우 느린 수준에서 매우 빠른 수준까지 다양합니다. 이처럼 다양한 성능 범위는 리소스를 공유하는 다중 테넌트 환경 내의 다른 데이터베이스가 특정 시점에 사용하는 리소스의 전반적인 수준에 따라 *달라집니다*.

물론 Azure SQL 데이터베이스 서비스의 목표는 모든 Web 및 Business 데이터베이스가 최대한 100%에 가깝게 실행되도록 유지하는 것입니다. 그리고 Web 및 Business 데이터베이스의 평균 성능 수준도 프리미엄 수준 이상으로 유지되어 왔습니다. 따라서 기존 데이터베이스의 성능 수준이 최신 수준에서는 프리미엄에 해당할 수도 있습니다. 그러나 이로 인해 업그레이드 대상 서비스 계층을 평가할 때 Web 및 Business 데이터베이스를 새로운 서비스 계층/성능 수준에 비교하는 과정에서 기대치가 비현실적으로 높아지게 되었습니다.

Web/Business 서비스 계층과 기본/표준/프리미엄 서비스 계층 간의 차이를 더욱 명확하게 파악하기 위해 아래 그림을 살펴보겠습니다. 여기서는 리소스를 공유하는 Web/Business 모델과 기본/표준/프리미엄 서비스 계층 모델에서 각각 SQL 데이터베이스 9개를 실행한다고 가정하겠습니다. Web/Business 모델에서는 이 공유 리소스 풀에 포함된 다른 데이터베이스에 대한의 영향을 명확하게 확인할 수 있습니다. 특정 데이터베이스에서 리소스를 많이 사용하는 작업을 실행하면 풀의 다른 모든 데이터베이스가 영향을 받으며, 사용 가능한 리소스가 감소하기 시작합니다. 반면 기본/표준/프리미엄 서비스 계층에서는 일정량의 리소스가 각 데이터베이스에 대해 미리 ***할당***되므로 공유 환경 내의 다른 데이터베이스는 작업량이 많은 인접 데이터베이스의 문제로부터 격리되며 해당 데이터베이스에 대해 선택한 성능 수준에 따라 작동합니다.

![새 서비스 계층의 예측 가능한 성능][3]

전체 DTU 백분율이 매우 높으면 DTU를 구성하는 세부 메트릭을 확인해야 합니다. 구체적으로는 데이터베이스 로그 I/O 및 메모리 사용량을 더 자세하게 파악해야 합니다. 그러면 DTU 사용량을 최적화하여 낮출 수 있는 영역을 찾아낼 수도 있습니다.


## 4. 더 낮은 성능 수준에 맞게 데이터베이스 워크로드 튜닝
데이터베이스의 기존 리소스 사용량 분석 결과 원하는 수준보다 더 고가의 성능 수준으로 업그레이드해야 하는 경우에는 성능을 추가로 튜닝하면 도움이 되는 영역을 파악할 수 있습니다.

현재 알고 있는 응용 프로그램 세부 정보에 따라 리소스 사용량이 일반적인 워크로드에 비해 너무 높다고 생각되면 성능 튜닝을 통해 응용 프로그램을 개선할 수도 있습니다.

실제로 모든 데이터베이스에서는 성능 튜닝을 추가로 수행하면 도움이 됩니다.

인덱스나 실행 계획 분석 등의 일반적인 튜닝 유지 관리 작업을 수행해야 할 뿐 아니라, Azure SQL 데이터베이스만을 대상으로 하여 데이터 액세스 루틴을 최적화해야 합니다.

| 문서 | 설명 |
|:--|:--|
| [Azure SQL 데이터베이스 성능 지침](http://msdn.microsoft.com/library/dn369873.aspx) | '응용 프로그램 튜닝' 섹션에서는 Azure SQL 데이터베이스의 성능 튜닝을 위한 세부 권장 사항 및 기술에 대해 설명합니다.|
|[성능 모니터링 및 튜닝 도구](https://msdn.microsoft.com/library/ms179428.aspx)| SQL Server에서 이벤트를 모니터링하고 실제 데이터베이스 디자인을 튜닝하는 데 사용할 수 있는 도구의 링크와 설명을 제공합니다.|
|[성능 모니터링 및 튜닝](https://msdn.microsoft.com/library/ms189081.aspx)|MSDN의 SQL Server 2014 성능 튜닝 섹션입니다.|
| [SQL Server 2008의 성능 문제 해결](https://msdn.microsoft.com/library/dd672789.aspx) | 이전 버전 관련 백서이지만 메모리 및 CPU 병목 현상 문제 해결 관련 정보를 비롯하여 일반적인 성능 문제를 해결하는 데 활용할 수 있는 지침을 제공합니다.|
|[Azure SQL 데이터베이스에서 쿼리 문제 해결 및 최적화](http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-azure-sql-database.aspx)|이전 버전 관련 항목이지만 동적 관리 뷰 및 문제 해결용으로 이러한 뷰를 사용하는 방법에 대한 유용한 정보를 제공합니다.|



## 5. 새 서비스 계층/성능 수준으로 업그레이드
Web 또는 Business 데이터베이스에 해당하는 서비스 계층 및 성능 수준을 결정한 후에는 여러 가지 방법을 통해 데이터베이스를 새 계층으로 업그레이드할 수 있습니다.

| 관리 도구 | 데이터베이스의 서비스 계층 및 성능 수준을 변경하는 방법|
| :---| :---|
| [Azure 관리 포털](https://manage.windowsazure.com) | 데이터베이스 대시보드 페이지에서 **확장** 탭을 클릭합니다. |
| [Azure PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) | [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) cmdlet을 사용합니다. |
| [REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [데이터베이스 생성 또는 업데이트](https://msdn.microsoft.com/library/azure/mt163685.aspx) 명령을 사용합니다.|
| [Transact-SQL](http://msdn.microsoft.com/library/azure/bb510741.aspx) | [ALTER DATABASE(Transact-SQL)](http://msdn.microsoft.com/library/azure/ms174269.aspx) 문을 사용합니다. |

자세한 내용은 [데이터베이스 서비스 계층 및 성능 수준 변경](sql-database-scale-up.md)을 참조하세요.


## 6. 새 서비스 계층/성능 수준으로의 업그레이드 모니터링
Azure SQL 데이터베이스는 현재 데이터베이스가 있는 논리 서버의 master 데이터베이스 내 sys.dm\_operation\_status 동적 관리 뷰에서 데이터베이스에 대해 수행하는 CREATE, ALTER, DROP 등의 관리 작업에 대한 정보를 제공합니다. 관련 정보는 [sys.dm _operation _status 설명서를 참조하세요](http://msdn.microsoft.com/library/azure/dn270022.aspx). 데이터베이스 업그레이드 작업 진행 상태를 결정하려면 작업 상태 DMV를 사용합니다. 다음 샘플 쿼리는 데이터베이스에서 수행된 모든 관리 작업을 보여 줍니다.

    SELECT o.operation, o.state_desc, o.percent_complete
    , o.error_code, o.error_desc, o.error_severity, o.error_state
    , o.start_time, o.last_modify_time
    FROM sys.dm_operation_status AS o
    WHERE o.resource_type_desc = 'DATABASE'
    and o.major_resource_id = '<database_name>'
    ORDER BY o.last_modify_time DESC;

관리 포털을 업그레이드에 사용한 경우 포털 내에서 작업에 대한 알림을 확인할 수 있습니다.

## 7\. 업그레이드 후 데이터베이스 모니터링
Web/Business 데이터베이스를 새 계층으로 업그레이드한 후 데이터베이스를 적극적으로 모니터링하여 원하는 성능으로 응용 프로그램이 실행되고 있는지 확인하고 필요에 따라 사용을 최적화하는 것이 좋습니다. 데이터베이스 모니터링을 위해 다음과 같은 추가 단계가 권장됩니다.


**리소스 사용량 데이터:** 기본, 표준 및 프리미엄 데이터베이스의 경우 사용자 데이터베이스에서 [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx)라는 새 DMV를 통해 보다 세부적인 리소스 사용량 데이터를 사용할 수 있습니다. 이 DMV는 지난 1시간 동안의 작업에 대해 15초 간격으로 거의 실시간 리소스 사용량 정보를 제공합니다. 특정 간격의 DTU 사용률은 CPU, IO 및 로그 차원의 최대 사용률로 계산됩니다. 다음은 지난 1시간 동안의 평균 DTU 사용률을 계산하는 쿼리입니다.

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

 
추가 [설명서](http://msdn.microsoft.com/library/dn800981.aspx)에는 이 DMV를 사용하는 방법의 자세한 내용이 포함되어 있습니다. [Azure SQL 데이터베이스 성능 지침](http://msdn.microsoft.com/library/azure/dn369873.aspx)에서는 응용 프로그램을 모니터링하고 튜닝하는 방법을 설명합니다.


- **경고:** 업그레이드된 데이터베이스의 DTU 사용량이 지정된 높은 수준에 도달하면 알리도록 Azure 관리 포털에서 '경고'를 설정합니다. 데이터베이스 경고는 DTU, CPU, IO 및 로그와 같은 다양한 성능 메트릭에 대해 Azure 관리 포털에서 설정할 수 있습니다. 

	예를 들어 평균 DTU 백분율 값이 최근 5분 동안 75%를 초과할 경우 "DTU 백분율"에 대해 메일 경고를 설정할 수 있습니다. 경고 알림을 구성하는 방법에 대한 내용은 [경고 알림 받기](insights-receive-alert-notifications.md)를 참조하세요.


- **예약된 성능 수준 업그레이드/다운그레이드:** 응용 프로그램을 사용하는 특정 상황에서 특정 일/주의 특정 시간에만 더 높은 성능이 필요한 경우에는 [Azure 자동화](https://azure.microsoft.com/documentation/services/automation/)를 사용하여 계획된 작업으로 데이터베이스를 상위/하위 성능 수준으로 업사이징/다운사이징할 수 있습니다.

	예를 들어 주별 배치/유지 관리 작업 기간 동안 데이터베이스를 상위 성능 수준으로 업그레이드하고 작업이 완료된 후 다운사이징합니다. 이러한 종류의 예약은 데이터 로드 작업, 인덱스 다시 만들기 등과 같은 리소스를 많이 사용하는 작업에도 유용합니다. Azure SQL 데이터베이스 요금 청구 모델은 서비스 계층/성능 수준의 시간별 사용량에 기반합니다. 이러한 유연성을 통해 예약 또는 계획된 업그레이드를 보다 비용 효율적으로 계획할 수 있습니다.



## 요약
Azure SQL 데이터베이스 서비스는 Web/Business 데이터베이스 작업을 평가하고 업그레이드할 서비스 계층을 결정하는 원격 분석 데이터 및 도구를 제공합니다. 업그레이드 프로세스는 매우 간단하며, 데이터베이스를 오프라인으로 전환하지 않고도 데이터 손실 없이 수행할 수 있습니다. 업그레이드된 데이터베이스는 새 서비스 계층이 제공하는 예측 가능한 성능과 추가 기능을 활용할 수 있습니다.




<!--Image references-->
[2]: ./media/sql-database-upgrade-new-service-tiers/portal-dtus.JPG
[3]: ./media/sql-database-upgrade-new-service-tiers/web-business-noisy-neighbor.png
[4]: ./media/sql-database-upgrade-new-service-tiers/resource_consumption.png

 

<!----HONumber=Oct15_HO3-->