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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure 포털을 사용하여 지역 중복 백업에서 Azure SQL 데이터베이스 지역 복원


> [AZURE.SELECTOR]
- [개요](sql-database-geo-restore.md)
- [Azure 포털](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

이 문서에서는 Azure 포털을 사용하여 지역 중복을 사용하는 새 서버에 데이터베이스를 복원하는 방법을 보여 줍니다.

## 복원할 데이터베이스를 선택합니다.

Azure 포털에서 Azure SQL 데이터베이스를 복원하려면 다음을 수행합니다.

1.	[Azure 포털](https://portal.azure.com)을 엽니다.
2.  화면 왼쪽에서 **새로 만들기** > **데이터 및 저장소** > **SQL 데이터베이스**를 선택합니다.
3.  원본으로 **백업**을 선택한 다음 복구에 사용할 지역 중복 백업을 선택합니다.

    ![Azure SQL 데이터베이스 복원](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  데이터베이스 이름, 데이터베이스를 복원하려는 서버를 지정한 다음 만들기를 클릭합니다.

## 다음 단계

- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)


## 추가 리소스

- [지역 복원](sql-database-geo-restore.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0615_2016-->