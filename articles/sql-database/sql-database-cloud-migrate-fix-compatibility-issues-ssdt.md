---
title: SQL 데이터베이스로 마이그레이션하기 전에 SQL Server 데이터베이스 호환성 문제 해결 | Microsoft Docs
description: Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 호환성, SQL Azure 마이그레이션 마법사, SSDT
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab

---
# Visual Studio용 SQL Server Data Tools를 사용하여 Azure SQL 데이터베이스로 SQL Server 데이터베이스 마이그레이션
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [업그레이드 관리자](http://www.microsoft.com/download/details.aspx?id=48119)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

이 문서에서는 Azure SQL 데이터베이스로 마이그레이션하기 전에 Visual Studio용 SQL Server 데이터 도구를 사용하여 SQL Server 데이터베이스 호환성 문제를 감지하고 해결하는 방법을 알아봅니다.

## Visual Studio용 SQL Server Data Tools 사용
Visual Studio용 SSDT("SQL Server Data Tools")를 사용하여 데이터베이스 스키마를 Visual Studio 데이터베이스 프로젝트로 가져온 다음 분석할 수 있습니다. 분석하려면 대한 대상 플랫폼을 SQL 데이터베이스 V12로 프로젝트로 지정하고 프로젝트를 빌드합니다. 빌드에 성공한 경우 데이터베이스가 호환됩니다. 빌드에 실패하는 경우 SSDT(또는 이 항목에서 설명한 다른 도구 중 하나)에서 오류를 해결할 수 있습니다. 프로젝트를 성공적으로 빌드되면 원본 데이터베이스의 복사본으로 다시 게시됩니다. SSDT의 데이터 비교 기능을 사용하여 원본 데이터베이스의 데이터를 Azure SQL V12 호환 가능 데이터베이스로 복사할 수 있습니다. 그런 다음 이 업데이트된 데이터베이스를 마이그레이션할 수 있습니다. 이 옵션을 사용하려면 [최신 버전의 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)를 다운로드합니다.

  ![VSSSDT 마이그레이션 다이어그램](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> 스키마만 마이그레이션해야 할 경우 Visual Studio에서 Azure SQL 데이터베이스로 직접 스키마를 게시할 수 있습니다. 데이터베이스 스키마가 마이그레이션 마법사 하나로 처리될 수 있는 것보다 많은 변경 내용이 필요한 경우 이 메서드를 사용합니다.
> 
> 

## Visual Studio용 SQL Server Data Tools를 사용하여 호환성 문제 검색
1. Visual Studio에서 **SQL Server 개체 탐색기**를 엽니다. **SQL Server 추가**를 사용하여 마이그레이션할 데이터베이스가 포함된 SQL Server 인스턴스에 연결합니다. 개체 탐색기에서 데이터베이스를 찾아 마우스 오른쪽 단추로 클릭하고 **새 프로젝트 만들기...**를 선택합니다.
   
   ![새 프로젝트](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)
2. 가져오기 설정을 **응용 프로그램 범위 개체만 가져오기**로 구성합니다. 다음의 참조된 로그인, 사용 권한 및 데이터베이스 설정을 가져오는 옵션의 선택을 취소합니다.
   
   ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)
3. **시작**을 클릭하여 데이터베이스를 가져온 다음 데이터베이스의 각 개체에 대한 T-SQL 스크립트 파일이 포함된 프로젝트를 만듭니다. 스크립트 파일은 프로젝트 내의 폴더에서 중첩됩니다.
   
   ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)
4. Visual Studio 솔루션 탐색기에서 데이터베이스 프로젝트를 마우스 오른쪽 단추로 클릭하고 속성을 선택합니다. **프로젝트 설정** 페이지에서 대상 플랫폼을 Microsoft Azure SQL Database V12로 구성합니다.
   
   ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)
5. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드**를 선택하여 프로젝트를 빌드합니다.
   
   ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)
6. **오류 목록**은 비호환성 각각을 표시합니다. 이 경우 사용자 이름은 NT AUTHORITY\\NETWORK SERVICE는 호환되지 않습니다. 호환되지 않으므로 주석으로 처리하거나 제거합니다.(데이터베이스 솔루션에서 이 로그인 및 역할을 제거에 미치는 영향을 짚어봄)
   
   ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)

## Visual Studio용 SQL Server Data Tools를 사용하여 호환성 문제 해결
1. 스크립트를 두 번 클릭하여 쿼리 창에서 스크립트를 열고 주석 처리한 다음 실행합니다. ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)
2. 오류가 남지 않을 때까지 비호환성을 포함하는 각 스크립트에 이 프로세스를 반복합니다. ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
3. 데이터베이스에 오류가 없으면 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. 그러면 원본 데이터베이스의 복사본이 빌드되어 게시됩니다. 적어도 처음에는 복사본을 사용하는 것이 좋습니다.
   
   * 원본 SQL Server 버전(SQL Server 2014 이전)에 따라 게시하기 전에 배포가 가능하도록 프로젝트의 대상 플랫폼을 다시 설정해야 할 수 있습니다.
   * 기존 SQL Server 데이터베이스를 마이그레이션하는 경우 최신 SQL Server 버전으로 데이터베이스를 마이그레이션할 때까지는 원본 SQL Server에서 지원되지 않는 기능을 프로젝트에 가져오지 마세요.
     
     ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
     
     ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
4. SQL Server 개체 탐색기에서 원본 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **데이터 비교**를 클릭합니다. 프로젝트를 원본 데이터베이스와 비교하면 마법사에서 변경한 내용을 쉽게 파악할 수 있습니다. 데이터베이스의 Azure SQL V12 버전을 선택하고 **마침**을 클릭합니다.
   
   ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)
   
   ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)
5. 검색된 차이점을 검토한 다음 **업데이트 대상**을 클릭하여 원본 데이터베이스에서 Azure SQL V12 데이터베이스로 데이터를 마이그레이션합니다.
   
   ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)
6. 배포 방법을 선택합니다. [호환되는 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.

## 다음 단계
* [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)

## 추가 리소스
* [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
* [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0831_2016-->