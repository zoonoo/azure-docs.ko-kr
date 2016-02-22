<properties
	pageTitle="Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경"
	description="Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경에서는 SQL 데이터베이스 규모 확장 또는 축소 방법을 보여 줍니다. Azure SQL 데이터베이스의 가격 책정 계층 변경"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# SQL 데이터베이스의 서비스 계층 및 성능 수준(가격 책정 계층) 변경

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

이 문서에서는 [Azure 포털](https://portal.azure.com)을 사용하여 SQL 데이터베이스의 서비스 계층 및 성능 수준을 변경하는 방법을 보여 줍니다.

[SQL 데이터베이스 Web/Business 데이터베이스를 새 서비스 계층으로 업그레이드](sql-database-upgrade-new-service-tiers.md) 및 [Azure SQL 데이터베이스 서비스 계층 및 성능 수준](sql-database-service-tiers.md)의 정보를 사용하여 Azure SQL 데이터베이스에 대해 적절한 서비스 계층 및 성능 수준을 결정합니다.

> [AZURE.IMPORTANT] SQL 데이터베이스의 서비스 계층 및 성능 수준 변경은 온라인 작업입니다. 즉, 데이터베이스가 온라인 상태이 고 전체 작업 중에 가동 중지 시간 없이 사용할 수 있습니다.

- 데이터베이스를 다운그레이드하려면 데이터베이스가 대상 서비스 계층의 최대 허용 크기보다 작아야 합니다. 
- [표준 지역에서 복제](https://msdn.microsoft.com/library/azure/dn758204.aspx) 또는 [활성 지역 복제](https://msdn.microsoft.com/library/azure/dn741339.aspx)를 사용할 수 있는 데이터베이스를 업그레이드하는 경우에는 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 성능 계층으로 업그레이드해야 합니다.
- 프리미엄 서비스 계층에서 다운그레이드하는 경우는 먼저 모든 지역에서 복제 관계를 종료해야 합니다. [연속 복사 관계 종료](https://msdn.microsoft.com/library/azure/dn741323.aspx) 항목에 설명된 단계에 따라 주 데이터베이스와 활성 보조 데이터베이스 간의 복제 프로세스를 중지할 수 있습니다.
- 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. 다운그레이드하는 경우 지정 시간으로 복원하는 기능을 잃게 되거나 백업 보존 기간이 단축될 수 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 백업 및 복원](https://msdn.microsoft.com/library/azure/jj650016.aspx)을 참조하세요.
- 데이터베이스 가격 책정 계층을 변경하는 경우 최대 데이터베이스 크기는 변경되지 않습니다. 데이터베이스 최대 크기를 변경하려면 [Transact-SQL(T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 또는 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)을 사용하세요.
- 24시간 내에 최대 4개의 개별 데이터베이스 변경(서비스 계층 또는 성능 수준)을 수행할 수 있습니다.
- 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.


**이 문서를 완료하려면 다음이 필요합니다.**

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- Azure SQL 데이터베이스입니다. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.


## 데이터베이스의 서비스 계층 및 성능 수준 변경


규모 확장 또는 축소할 데이터베이스에 대한 SQL 데이터베이스 블레이드를 엽니다.

1.	[Azure 포털](https://portal.azure.com)로 이동합니다.
2.	**모두 찾아보기**를 클릭합니다.
3.	**SQL 데이터베이스**를 클릭합니다.
2.	변경할 데이터베이스를 클릭합니다.
3.	SQL 데이터베이스 블레이드에서 **모든 설정**을 클릭한 다음 **가격 책정 계층(배율 DTU)**을 클릭합니다.

    ![가격 책정 계층][1]


1.  새 계층을 선택하고 **선택**을 클릭합니다.

    **선택**을 클릭하면 데이터베이스 계층을 변경하기 위한 규모 요청을 제출합니다. 데이터베이스 크기에 따라 가져오기 작업의 규모를 설정하는 데 다소 시간이 걸릴 수 있습니다. 규모 작업의 세부 정보와 상태에 대한 알림을 클릭합니다.

    > [AZURE.NOTE] 데이터베이스 가격 책정 계층을 변경하는 경우 최대 데이터베이스 크기는 변경되지 않습니다. 데이터베이스 최대 크기를 변경하려면 [Transact-SQL(T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 또는 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)을 사용하세요.

    ![가격 책정 계층 선택][2]

3.	왼쪽 리본에서 **알림**을 클릭합니다.

    ![알림][3]

## 데이터베이스가 선택한 가격 책정 계층에 있는지 확인

   규모 설정 작업이 완료된 후 데이터베이스가 원하는 계층에 있는지 검사하고 확인합니다.

2.	**모두 찾아보기**를 클릭합니다.
3.	**SQL 데이터베이스**를 클릭합니다.
2.	업데이트된 데이터베이스를 클릭합니다.
3.	**가격 책정 계층**을 선택하고 올바른 계층으로 설정되었는지 확인합니다.

    ![새로운 가격][4]


## 다음 단계

- [Transact-SQL(T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 또는 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)을 사용하여 데이터베이스 최대 크기를 변경합니다.
- [규모 확장 및 감축](sql-database-elastic-scale-get-started.md)
- [SSMS를 사용하여 SQL 데이터베이스에 연결 및 쿼리하기](sql-database-connect-query-ssms.md)
- [Azure SQL 데이터베이스 내보내기](sql-database-export.md)

## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_0211_2016-->