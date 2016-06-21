<properties
	pageTitle="Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경"
	description="Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경에서는 SQL 데이터베이스 규모 확장 또는 축소 방법을 보여 줍니다. Azure SQL 데이터베이스의 가격 책정 계층 변경"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# SQL 데이터베이스의 서비스 계층 및 성능 수준(가격 책정 계층) 변경


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


서비스 계층 및 성능 수준은 SQL 데이터베이스에 사용할 수 있는 기능 및 리소스를 설명하고 응용 프로그램이 변경될 필요에 따라 업데이트될 수 있습니다. 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요.

서비스 계층 및/또는 데이터베이스의 성능 수준을 변경하는 작업은 새 성능 수준에서 원본 데이터베이스의 복제본을 만들고 연결을 복제본으로 전환합니다. 이 프로세스 중에 데이터가 손실되지는 않지만 복제본으로 전환할 경우 잠깐 동안 데이터베이스에 대한 연결이 비활성화되므로 비행의 일부 트랜잭션이 롤백될 수 있습니다. 하지만 평균 4초 이내 또 사례의 99% 이상에서 30초 미만으로 이 창이 달라집니다. 현재 연결에서 비행의 트랜잭션 숫자가 있는 경우에 특히 이 창을 더 이상 유지될 수 없습니다.

전체 확장 프로세스 기간은 변경 전후 데이터베이스의 크기 및 서비스 계층에 따라 달라집니다. 예를 들어 표준 서비스 계층 내에서 변경되고 있는 250GB 데이터베이스는 6시간 내에 완료되어야 합니다. 프리미엄 서비스 계층 내의 성능 수준을 변경하고 있는 동일한 크기의 데이터베이스의 경우 3시간 이내에 완료되어야 합니다.


[SQL 데이터베이스 Web/Business 데이터베이스를 새 서비스 계층으로 업그레이드](sql-database-upgrade-server-portal.md) 및 [Azure SQL 데이터베이스 서비스 계층 및 성능 수준](sql-database-service-tiers.md)의 정보를 사용하여 Azure SQL 데이터베이스에 대해 적절한 서비스 계층 및 성능 수준을 결정합니다.

- 데이터베이스를 다운그레이드하려면 데이터베이스가 대상 서비스 계층의 최대 허용 크기보다 작아야 합니다. 
- [지역에서 복제](sql-database-geo-replication-overview.md)를 사용할 수 있는 데이터베이스를 업그레이드하는 경우에는 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 성능 계층으로 업그레이드해야 합니다.
- 서비스 계층에서 다운그레이드하는 경우는 먼저 모든 지역에서 복제 관계를 종료해야 합니다. 
- 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. 다운그레이드하는 경우 지정 시간으로 복원하는 기능을 잃게 되거나 백업 보존 기간이 단축될 수 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 백업 및 복원](sql-database-business-continuity.md)을 참조하세요.
- 데이터베이스 가격 책정 계층을 변경하는 경우 최대 데이터베이스 크기는 변경되지 않습니다. 데이터베이스 최대 크기를 변경하려면 [Transact-SQL(T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 또는 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)을 사용하세요.
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

<!---HONumber=AcomDC_0608_2016-->