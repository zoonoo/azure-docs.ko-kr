<properties
	pageTitle="BACPAC 파일을 가져와 새 Azure SQL 데이터베이스 만들기 | Microsoft Azure"
	description="기존 BACPAC 파일을 가져와 새 Azure SQL 데이터베이스를 만듭니다."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# BACPAC 파일을 가져와 새 Azure SQL 데이터베이스 만들기


**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

이 문서에서는 [Azure 포털](https://portal.azure.com)을 사용하여 BACPAC 파일에서 Azure SQL 데이터베이스를 만드는 방법에 대한 지침을 제공합니다.

BACPAC은 데이터베이스 스키마 및 데이터를 포함하는 .bacpac 파일입니다. 데이터베이스는 Azure 저장소 Blob 컨테이너에서 가져온 BACPAC으로 만들어집니다. Azure 저장소에 .bacpac 파일이 없는 경우 [Azure SQL 데이터베이스의 BACPAC 만들기 및 내보내기](sql-database-export.md)의 단계에 따라 새로 만들 수 있습니다.


> [AZURE.NOTE] Azure SQL 데이터베이스는 모든 사용자 데이터베이스에 대해 복원할 수 있는 백업을 자동으로 만들고 유지 관리합니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.


.bacpac에서 SQL 데이터베이스를 가져오려면 다음이 필요합니다.

- Azure 구독. 
- Azure SQL 데이터베이스 V12 서버. V12 서버가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.
- [Azure 저장소 계정(표준)](../storage/storage-create-storage-account.md) Blob 컨테이너에서 가져오려는 데이터베이스의 .bacpac 파일

***중요*** Azure Blob 저장소에서 BACPAC를 가져올 때 표준 저장소를 사용합니다. 프리미엄 저장소에서 BACPAC 가져오기는 지원되지 않습니다.


## 데이터베이스가 포함될 서버 선택

SQL Server 블레이드를 엽니다.

1.	[Azure 포털](https://portal.azure.com)로 이동합니다.
2.	**SQL Server**를 클릭합니다.
3.	데이터베이스를 복원할 서버를 클릭합니다.
4.	SQL Server 블레이드에서 **데이터베이스 가져오기**를 클릭하여 **데이터베이스 가져오기** 블레이드를 엽니다.

    ![데이터베이스 가져오기][1]

1.  **저장소**를 클릭하고 저장소 계정, Blob 컨테이너 및 .bacpac 파일을 선택한 다음 **확인**을 클릭합니다.

    ![저장소 옵션 구성][2]

1.  새 데이터베이스에 대한 가격 책정 계층을 선택하고 **선택**을 클릭합니다. 탄력적 풀에 직접 데이터베이스 가져오기는 지원되지 않지만 먼저 단일 데이터베이스로 가져온 다음 데이터베이스를 풀로 이동할 수 있습니다.

    ![가격 책정 계층 선택][3]

1.  BACPAC 파일에서 만들 데이터베이스에 대한 **데이터베이스 이름**을 입력합니다.
2.  인증 유형을 선택한 후 서버에 대한 인증 정보를 제공합니다. 
3.  **만들기**를 클릭하여 BACPAC에서 데이터베이스를 만듭니다.

    ![데이터베이스 만들기][4]

**만들기**를 클릭하면 데이터베이스 가져오기 요청이 서비스에 제출됩니다. 데이터베이스 크기에 따라 가져오기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

## 가져오기 작업의 진행률 모니터링

1.	**SQL Server**를 클릭합니다.
2.	복원할 서버를 클릭합니다.
3.	SQL Server 블레이드의 작업 영역에서 **가져오기/내보내기 기록**을 클릭합니다.

    ![가져오기 내보내기 기록][5] ![가져오기 내보내기 기록][6]





## 데이터베이스가 서버에서 라이브 상태인지 확인

1.	**SQL 데이터베이스**를 클릭하고 새 데이터베이스가 **온라인** 상태인지 확인합니다.



## 다음 단계

- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)



## 추가 리소스

- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

<!---HONumber=AcomDC_0518_2016-->