<properties
	pageTitle="삭제된 Azure SQL 데이터베이스 복원(Azure 포털) | Microsoft Azure"
	description="삭제된 Azure SQL 데이터베이스를 복원합니다(Azure 포털)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure 포털을 사용하여 삭제된 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [개요](sql-database-restore-deleted-database.md)
- [Azure 포털](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## 복원할 데이터베이스를 선택합니다. 

Azure 포털에서 Azure SQL 데이터베이스를 복원하려면 다음을 수행합니다.

1.	[Azure 포털](https://portal.azure.com)을 엽니다.
2.  화면 왼쪽에서 **찾아보기** > **SQL 서버**를 선택합니다.
3.  복원하려는 삭제된 데이터베이스를 사용하여 서버로 이동하고 서버를 선택합니다.
4.  서버 블레이드의 **작업** 섹션으로 스크롤하고 **삭제된 데이터베이스**를 선택합니다. ![Azure SQL 데이터베이스 복원](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  복원할 삭제된 데이터베이스를 선택합니다.
6.  데이터베이스 이름을 지정하고 확인을 클릭합니다.

    ![Azure SQL 데이터베이스 복원](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## 다음 단계

- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)
- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)
- [PowerShell을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-powershell.md)
- [REST API를 사용하여 삭제된 데이터베이스 복원](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)

## 추가 리소스

- [지정 시간 복원](sql-database-point-in-time-restore.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->