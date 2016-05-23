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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure 포털을 사용하여 삭제된 Azure SQL 데이터베이스 복원


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

이 문서에서는 삭제된 Azure SQL 데이터베이스를 복원하는 방법을 보여 줍니다.

데이터베이스가 삭제된 경우, Azure SQL 데이터베이스를 사용하면 삭제된 데이터베이스를 지정된 삭제 시점으로 복원할 수 있습니다. Azure SQL 데이터베이스는 데이터베이스 보존 기간 동안 삭제된 데이터베이스의 백업을 저장합니다.

삭제된 데이터베이스의 보존 기간은 해당 데이터베이스가 존재했던 서비스 계층 또는 데이터베이스의 존재 일 수 중 더 작은 일 수에 의해 결정됩니다. 데이터베이스 보존에 대해 자세히 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 읽어 보시기 바랍니다.

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
- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)



## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->