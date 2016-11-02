<properties
	pageTitle="Azure 포털을 사용하여 Azure SQL 데이터베이스 V12로 업그레이드 | Microsoft Azure"
	description="Web 및 Business 데이터베이스를 업그레이드하는 방법을 포함하여 Azure SQL 데이터베이스 V12로 업그레이드하는 방법을 설명하며 Azure 포털을 사용하여 V11 서버 업그레이드를 통해 데이터베이스를 탄력적 데이터베이스 풀로 직접 마이그레이션하는 방법을 설명합니다."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="08/08/2016"
	ms.author="sstein"/>


# Azure 포털을 사용하여 Azure SQL 데이터베이스 V12로 업그레이드


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL 데이터베이스 V12가 최신 버전이므로 기존 서버를 SQL 데이터베이스 V12로 업그레이드하는 것이 좋습니다. SQL 데이터베이스 V12에는 [이전 버전에 비해 다양한 이점](sql-database-v12-whats-new.md)이 있습니다.

- SQL Server와의 호환성 증가
- 향상된 프리미엄 성능과 새로운 성능 수준
- [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)

이 문서에서는 기존 SQL 데이터베이스 V11 서버 및 데이터베이스를 SQL 데이터베이스 V12로 업그레이드하는 방법에 대해 지침을 제공합니다.

V12로 업그레이드하는 과정에서 Web/Business 데이터베이스를 새로운 서비스 계층으로 업데이트하게 됩니다. 따라서 Web/Business 데이터베이스 업그레이드에 대한 지침도 포함되어 있습니다.

또한 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)로 마이그레이션하는 것이 단일 데이터베이스에 대한 개별 성능 수준(가격 책정 계층)으로 업그레이드하는 것보다 비용 면에서 효율적일 수 있습니다. 개별 데이터베이스의 성능 수준을 개별적으로 관리하지 않고 풀의 성능 설정만 관리하면 되므로, 풀은 데이터베이스 관리를 간소화합니다. 여러 서버에 데이터베이스가 있는 경우에는 이 데이터베이스를 동일한 서버로 이동해서 풀로 유용하게 관리하는 것이 좋습니다. [PowerShell을 사용하여 V11 서버에서 탄력적 데이터베이스 풀로 직접 데이터베이스를 자동 마이그레이션](sql-database-upgrade-server-powershell.md)하는 작업을 손쉽게 수행할 수 있습니다. 포털을 사용하여 V11 데이터베이스를 풀로 마이그레이션할 수도 있지만, 포털에 이미 V12 서버가 있어야 풀을 만들 수 있습니다. [풀을 활용할 수 있는 데이터베이스](sql-database-elastic-pool-guidance.md)가 이미 있는 경우에는 이 문서 뒷부분에서 제공되는 서버 업그레이드 후 풀 만들기 지침을 참조하세요.

데이터베이스는 업그레이드 작업 동안 온라인 상태로 유지되며 계속 작동합니다. 실제로 새 성능 수준으로 전환할 때 아주 짧은 시간(보통 90초 정도이지만 최대 5분일 수도 있음) 동안 데이터베이스 연결이 일시적으로 끊어질 수 있습니다. 응용 프로그램에 [연결 종료에 대한 일시적인 오류 처리](sql-database-connectivity-issues.md) 기능이 있을 경우 업그레이드 종료 시의 연결 끊김을 방지할 수 있습니다.

SQL 데이터베이스 V12로 업그레이드하고 난 후에는 취소할 수 없습니다. 업그레이드 후에는 서버를 V11로 되돌릴 수 없습니다.

V12로 업그레이드한 후에는 서비스가 새 서버의 워크로드를 평가할 때까지 일정 시간 동안 [서비스 계층 권장 사항](sql-database-service-tier-advisor.md) 및 [탄력적 풀 성능 권장 사항](sql-database-elastic-pool-guidance.md)을 사용할 수 없습니다. V11 서버 권장 사항 기록은 유지되지 않으므로 V12 서버에 적용되지 않습니다.

## 업그레이드 준비

- **모든 Web 및 Business 데이터베이스 업그레이드**: 아래의 [모든 Web 및 Business 데이터베이스 업그레이드](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) 섹션을 참조하거나 [탄력적 데이터베이스 풀 모니터링 및 관리(PowerShell)](sql-database-elastic-pool-manage-powershell.md)를 수행하세요.
- **지역에서 복제 검토 및 일시 중단**: Azure SQL 데이터베이스가 지역에서 복제가 가능하도록 구성된 경우 데이터베이스의 현재 구성을 문서에 기록해 놓고 [지역에서 복제를 중지](sql-database-geo-replication-portal.md#remove-secondary-database)해야 합니다. 업그레이드가 완료된 후 지역에서 복제 데이터베이스를 다시 구성하세요.
- **Azure VM에 클라이언트가 있는 경우 열어야 하는 포트**: Azure VM(가상 컴퓨터)에서 클라이언트가 실행되면서 클라이언트 프로그램이 SQL 데이터베이스 V12에 연결하는 경우에는 VM에서 11000-11999 및 14000-14999 범위의 포트를 열어야 합니다. 자세한 내용은 [SQL 데이터베이스 V12용 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.



## 업그레이드 시작

1. [Azure 포털](https://portal.azure.com/)에서 **찾아보기** > **SQL Server**를 선택한 후 업그레이드할 v2.0 서버를 선택하여 업그레이드하려는 서버로 이동합니다.
2. **최신 SQL 데이터베이스 업데이트**를 선택한 다음 **이 서버 업그레이드**를 선택합니다.

      ![서버 업그레이드][1]

3. 서버를 최신 SQL 데이터베이스 업데이트로 업그레이드하는 작업은 영구적이며 되돌릴 수 없습니다. 업그레이드를 확인하려면 서버의 이름을 입력하고 **확인**을 클릭합니다.

## 모든 Web 및 Business 데이터베이스 업그레이드

서버에 We 또는 Business 데이터베이스가 있는 경우 데이터베이스를 업그레이드해야 합니다. SQL 데이터베이스 V12로 업그레이드하는 과정에서 모든 Web 및 Business 데이터베이스를 새 서비스 계층으로 업데이트하게 됩니다.

업그레이드를 지원하기 위해 SQL 데이터베이스 서비스는 각 데이터베이스에 적절한 서비스 계층 및 성능 수준(가격 책정 계층)을 권장합니다. 서비스는 데이터베이스의 기존 사용량을 분석하여 기존 데이터베이스의 워크로드를 실행하는 데 가장 적합한 계층을 권장합니다.

3. **이 서버 업그레이드** 블레이드에서 검토할 각 데이터베이스를 선택하고 업그레이드할 권장 가격 책정 계층을 선택합니다. 언제든지 사용 가능한 가격 책정 계층을 찾아보고 환경에 가장 적합한 계층을 선택할 수 있습니다.


     ![데이터베이스][2]


7. 권장 계층을 클릭하면 **가격 책정 계층 선택** 블레이드가 표시됩니다. 여기서 계층을 선택한 다음 **선택** 단추를 클릭하여 해당 계층으로 변경할 수 있습니다. 각 Web 또는 Business 데이터베이스에 대한 새 계층 선택

    여기서 기억해야 할 사항은, SQL 데이터베이스에서 반드시 특정 서비스 계층이나 서비스 수준을 사용해야 하는 것은 아니라는 점입니다. 따라서 데이터베이스의 요구 사항이 변경되면 사용 가능한 서비스 계층과 성능 수준 간에 쉽게 변경이 가능합니다. 실제로 기본, 표준 및 Premium SQL 데이터베이스에서는 시간 단위로 요금이 청구되며 24시간 동안 각 데이터베이스를 4번 확장 및 축소할 수 있습니다.

    ![동영상 추천 기능][6]


서버의 모든 데이터베이스가 적격이면 업그레이드를 시작할 수 있음

## 업그레이드 확인

3. 서버에 있는 모든 데이터베이스의 업그레이드가 가능해지면 **서버 이름을 입력**하여 업그레이드를 수행할 것임을 확인한 후 **확인**을 클릭합니다.

    ![업그레이드 확인][3]


4. 업그레이드가 시작되고, 진행 중 알림이 표시됩니다. 업그레이드 프로세스가 시작됩니다. 특정 데이터베이스의 세부 정보에 따라 V12로 업그레이드하는 데 시간이 어느 정도 걸릴 수 있습니다. 이 시간 동안 서버의 모든 데이터베이스가 온라인 상태로 유지되지만 서버 및 데이터베이스 관리 작업이 제한됩니다.

    ![업그레이드 진행 중][4]

    실제로 새 성능 수준으로 전환할 때 아주 짧은 시간(보통 몇 초) 동안 데이터베이스 연결이 일시적으로 끊어질 수 있습니다. 응용 프로그램에 연결 종료에 대한 일시적인 오류 처리 기능(재시도 논리)이 있을 경우 업그레이드 종료 시의 연결 끊김을 방지할 수 있습니다.

5. 업그레이드 작업이 완료된 후에는 **최신 업데이트** 블레이드에 **사용 가능**이 표시됩니다.

    ![V12 사용 가능][5]

## 탄력적 데이터베이스 풀로 데이터베이스 이동

[Azure 포털](https://portal.azure.com/)에서 V12 서버로 이동하고 **풀 추가**를 클릭합니다.

또는

**여기를 클릭하여 이 서버에 권장되는 탄력적 데이터베이스 풀을 확인하세요.**라는 메시지가 나타나면 이 메시지를 클릭하여 서버의 데이터베이스에 최적화된 풀을 쉽게 만들 수 있습니다. 자세한 내용은 [탄력적 데이터베이스 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요.

![서버에 풀 추가][7]

[탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool.md) 문서의 지침을 따라 풀 만들기를 완료합니다.

## SQL 데이터베이스 V12로 업그레이드한 후 데이터베이스 모니터링

>[AZURE.IMPORTANT] 새 v12 기능을 활용하려면 최신 버전의 SSMS(SQL Server Management Studio)로 업그레이드합니다. [SQL Server Management Studio를 다운로드합니다](https://msdn.microsoft.com/library/mt238290.aspx).

업그레이드한 후 데이터베이스를 적극적으로 모니터링하여 원하는 성능으로 응용 프로그램이 실행되고 있는지 확인하고 필요에 따라 설정을 최적화합니다.

개별 데이터베이스를 모니터링하는 것 외에, [Azure 포털을 사용하여 탄력적 데이터베이스 풀 모니터링, 관리 및 크기 조정](sql-database-elastic-pool-manage-portal.md)하거나 [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools)을 사용하여 탄력적 데이터베이스 풀을 모니터링할 수도 있습니다.


**리소스 소비 데이터:** 기본, 표준 및 프리미엄 데이터베이스의 경우 사용자 데이터베이스에서 [sys.dm_ db_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV를 통해 리소스 소비 데이터를 사용할 수 있습니다. 이 DMV는 지난 1시간 동안의 작업에 대해 15초 간격으로 거의 실시간 리소스 사용량 정보를 제공합니다. 특정 간격의 DTU 사용률은 CPU, IO 및 로그 차원의 최대 사용률로 계산됩니다. 다음은 지난 1시간 동안의 평균 DTU 사용률을 계산하는 쿼리입니다.

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

추가 모니터링 정보:

- [단일 데이터베이스의 Azure SQL 데이터베이스 성능 지침](http://msdn.microsoft.com/library/azure/dn369873.aspx)
- [탄력적 데이터베이스 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)
- [동적 관리 뷰를 사용하여 Azure SQL 데이터베이스 모니터링](sql-database-monitoring-with-dmvs.md)




**경고:** 업그레이드된 데이터베이스의 DTU 사용량이 지정된 높은 수준에 도달하면 알리도록 Azure 포털에서 '경고'를 설정합니다. 데이터베이스 경고는 DTU, CPU, IO 및 로그와 같은 다양한 성능 메트릭에 대해 Azure 포털에서 설정할 수 있습니다. 데이터베이스로 이동한 후 **설정** 블레이드에서 **경고 규칙**을 선택하면 됩니다.

예를 들어 평균 DTU 백분율 값이 최근 5분 동안 75%를 초과할 경우 "DTU 백분율"에 대해 메일 경고를 설정할 수 있습니다. 경고 알림을 구성하는 방법에 대한 내용은 [경고 알림 받기](../azure-portal/insights-receive-alert-notifications.md)를 참조하세요.





## 다음 단계

- [풀 권장 사항을 확인하고 풀을 만듭니다](sql-database-elastic-pool-create-portal.md).
- [데이터베이스의 서비스 계층 및 성능 수준 변경](sql-database-scale-up.md)



## 관련 링크

- [SQL 데이터베이스 V12의 새로운 기능](sql-database-v12-whats-new.md)
- [SQL 데이터베이스 V12로 업그레이드 계획 및 준비](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png

<!---HONumber=AcomDC_0810_2016-->