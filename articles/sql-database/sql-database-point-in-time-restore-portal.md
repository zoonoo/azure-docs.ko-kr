<properties
	pageTitle="이전 시점으로 Azure SQL 데이터베이스 복원(Azure 포털) | Microsoft Azure"
	description="이전 시점으로 Azure SQL 데이터베이스를 복원합니다."
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


# Azure 포털을 사용하여 이전 시점으로 Azure SQL 데이터베이스 복원


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

이 문서에서는 Azure 포털을 사용하여 이전 시점으로 데이터베이스를 복원하는 방법을 보여 줍니다.

[**지정 시간 복원**](sql-database-point-in-time-restore.md)은 데이터베이스의 보존 기간 내에서 어떤 지점에 데이터베이스에 가져온 자동 백업에서 데이터베이스를 복원할 수 있도록 하는 셀프 서비스 기능입니다. 자동 백업 및 데이터베이스 보존 기간에 대해 자세히 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

## 이전 시점으로 데이터베이스를 복원할 데이터베이스를 선택합니다.

Azure 포털에서 Azure SQL 데이터베이스를 복원하려면 다음을 수행합니다.

1.	[Azure 포털](https://portal.azure.com)을 엽니다.
2.  화면 왼쪽에서 **찾아보기** > **SQL 데이터베이스**를 선택합니다.
3.  복원하려는 데이터베이스로 이동하고 선택합니다.
4.  데이터베이스의 블레이드 맨 위에서 **복원**을 선택합니다.

    ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  데이터베이스 이름, 지정 시간을 지정하고 확인을 클릭합니다.

    ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## 다음 단계

- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)
- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)



## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->