<properties
	pageTitle="지역 중복 백업에서 Azure SQL 데이터베이스 복원(Azure 포털) | Microsoft Azure"
	description="지역 중복 백업에서 Azure SQL 데이터베이스 지역 복원(Azure 포털)"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure 포털을 사용하여 지역 중복 백업에서 Azure SQL 데이터베이스 지역 복원


> [AZURE.SELECTOR]
- [개요](sql-database-recovery-using-backups.md)
- [지리적 복원: PowerShell](sql-database-geo-restore-powershell.md)

이 문서에서는 Azure 포털을 사용하여 지리적 복원을 사용하는 새 서버에 데이터베이스를 복원하는 방법을 보여 줍니다.

## 복원할 데이터베이스를 선택합니다.

Azure 포털에서 Azure SQL 데이터베이스를 복원하려면 다음을 수행합니다.

1.	[Azure 포털](https://portal.azure.com)을 엽니다.
2.  화면 왼쪽에서 **새로 만들기** > **데이터 및 저장소** > **SQL 데이터베이스**를 선택합니다.
3.  원본으로 **백업**을 선택한 다음 복구에 사용할 지역 중복 백업을 선택합니다.

    ![Azure SQL 데이터베이스 복원](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  데이터베이스 이름, 데이터베이스를 복원하려는 서버를 지정한 다음 만들기를 클릭합니다.


## 다음 단계

- 비즈니스 연속성을 대략적으로 이해하려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
- Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
- 비즈니스 연속성 설계 및 복구 시나리오에 대해 알아보려면 [연속성 시나리오](sql-database-business-continuity-scenarios.md)를 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.
- 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)를 참조하세요.

<!---HONumber=AcomDC_0629_2016-->