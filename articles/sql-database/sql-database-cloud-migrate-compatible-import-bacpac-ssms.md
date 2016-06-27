<properties
   pageTitle="SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션 | Microsoft Azure"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 배포, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 데이터베이스 내보내기, 마이그레이션 마법사"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# SSMS를 사용하여 BACPAC에서 SQL 데이터베이스로 가져오기

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure 포털](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

이 문서에서는 SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사를 사용하여 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일에서 SQL 데이터베이스로 가져오는 방법을 보여 줍니다.

> [AZURE.NOTE] 아래 단계는 사용자가 이미 Azure SQL 논리 인스턴스를 프로비전하고 연결 정보를 알고 있는 경우를 가정합니다.

1. 최신 버전의 SQL Server Management Studio가 있는지 확인합니다. 새로운 버전의 Management Studio는 매월 업데이트되어 Azure 포털의 업데이트와 동기화 상태를 유지합니다.

	 > [AZURE.IMPORTANT] Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).

2. Azure SQL 데이터베이스 서버에 연결하고 **Databases** 폴더를 마우스 오른쪽 단추로 클릭하고 **데이터 계층 응용 프로그램 가져오기...**를 클릭합니다.

    ![데이터 계층 응용 프로그램 메뉴 항목 가져오기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.	가져오기 마법사에서 로컬 디스크에서 BACPAC 파일을 가져오거나 BACPAC 파일을 업로드한 Azure 저장소 계정 및 컨테이너를 선택하여 Azure SQL 데이터베이스에 새 데이터베이스를 만듭니다.

    ![설정 가져오기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

    ***중요:*** Azure Blob 저장소에서 BACPAC를 가져올 때 표준 저장소를 사용합니다. 프리미엄 저장소에서 BACPAC 가져오기는 지원되지 않습니다.

4.	Azure SQL DB에서 데이터베이스에 대해 **새 데이터베이스 이름**을 입력하고 **Microsoft Azure SQL 데이터베이스 버전**(서비스 계층), **최대 데이터베이스 크기**, **서비스 목표**(성능 수준)를 입력합니다.

    ![데이터베이스 설정](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.	**다음**을 클릭한 다음 **마침**을 클릭하여 BACPAC 파일을 Azure SQL 데이터베이스 서버의 새 데이터베이스로 가져옵니다.

6. 개체 탐색기를 사용하여 Azure SQL 데이터베이스 서버에서 마이그레이션된 데이터베이스에 연결합니다.

6.	Azure 포털을 사용하여 데이터베이스와 해당 속성을 봅니다.

## 다음 단계

- [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)

## 추가 리소스

- [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
- [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->