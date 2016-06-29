<properties
   pageTitle="SqlPackage.exe를 사용하여 SQL 데이터베이스 호환성 확인 | Microsoft Azure"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, SQL 데이터베이스 호환성, SqlPackage"
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

# SqlPackage.exe를 사용하여 SQL 데이터베이스 호환성 확인

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [업그레이드 관리자](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

이 문서에서는 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령 프롬프트 유틸리티를 사용하여 SQL Server 데이터베이스가 SQL 데이터베이스로 마이그레이션하는 데 호환되는지 확인하는 방법을 배웁니다.

## SqlPackage.exe 사용

1. 명령 프롬프트를 열고 최신 버전의 sqlpackage.exe가 포함된 디렉터리를 변경합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다. [최신 버전의 Visual Studio용 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)를 다운로드하여 SqlPackage 유틸리티의 최신 버전을 가져옵니다.
2. 사용자 환경에 대해 다음 인수를 사용하여 다음 SqlPackage 명령을 실행하세요.

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	| 인수 | 설명 |
	|---|---|
	| < server_name > | 원본 서버 이름 |
	| < database_name > | 원본 데이터베이스 이름 |
	| < target_file > | 파일 이름 및 BACPAC 파일의 위치 |
	| < schema_name.table_name > | 데이터가 대상 파일에 대해 출력되는 테이블 |
	| < output_file > | 오류가 발생할 경우 오류가 있는 출력 파일의 파일 이름 및 위치 |

	/p:TableName 인수를 사용하는 이유는 모든 테이블의 데이터를 내보내지 않고 Azure SQL DB V12로 내보내기에 대한 데이터 호환성만 테스트하기 때문입니다. 하지만 sqlpackage.exe의 내보내기 인수는 테이블 없는 추출을 지원하지 않으므로 하나의 작은 테이블을 지정해야 합니다. < output_file >에 오류 보고서가 포함됩니다. "> 2 > & 1" 문자열은 지정된 출력 파일에 명령을 실행한 결과로 생성되는 표준 출력 및 표준 오류를 모두 파이프합니다.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 출력 파일을 열고 호환성 오류(있는 경우)를 검토합니다.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## 다음 단계

- [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx) [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)
- [데이터베이스 마이그레이션 호환성 문제 해결](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [호환되는 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## 추가 리소스

- [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
- [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->