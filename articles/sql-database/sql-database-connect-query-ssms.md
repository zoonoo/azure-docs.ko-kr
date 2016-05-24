<properties
	pageTitle="SQL 데이터베이스 연결 - SQL Server Management Studio | Microsoft Azure"
	description="SQL Server Management Studio(SSMS)를 사용하여 SQL 데이터베이스에 연결하는 방법을 알아봅니다. 그런 다음 Transact-SQL(T-SQL)를 사용하여 샘플 쿼리를 실행합니다."
	metaCanonical=""
	keywords="SQL 데이터베이스에 연결, SQL Server Management Studio"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/09/2016"
	ms.author="sstein;carlrab" />

# SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 실행합니다.

> [AZURE.SELECTOR]
- [SSMS](sql-database-connect-query-ssms.md)
- [C#](sql-database-connect-query.md)
- [Excel](sql-database-connect-excel.md)

이 문서에서는 최신 버전의 SQL Server Management Studio(SSMS)를 사용하여 Azure SQL 데이터베이스 서버에 연결하고 Transact-SQL(T-SQL) 문을 통해 간단한 쿼리를 수행하는 방법을 보여 줍니다.

[AZURE.INCLUDE [로그인](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [SSMS 설치](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [SSMS 연결](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

방화벽 규칙에 대한 자세한 내용은 [방화벽 설정 구성: 방법(Azure SQL Database)](sql-database-configure-firewall-settings.md)을 참조하세요.

## 예제 쿼리 실행

논리적 서버에 연결한 후 데이터베이스에 연결하고 샘플 쿼리를 실행할 수 있습니다.

1. **개체 탐색기**에서 **AdventureWorks** 샘플 데이터베이스와 같은 권한이 있는 서버의 데이터베이스로 이동합니다.
2. 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 선택합니다.

	![새 쿼리 SQL 데이터베이스 서버에 연결: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. 쿼리 창에서 다음 코드를 복사하여 붙여넣습니다.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. **실행** 단추를 클릭합니다. 다음 스크린샷에서는 성공적인 쿼리를 보여 줍니다.

	![성공 SQL 데이터베이스 서버에 연결: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## 다음 단계

T-SQL 문을 사용하여 SQL Server를 통해 할 수 있는 것과 동일한 방식으로 Azure에서 데이터베이스를 만들고 관리할 수 있습니다. SQL server에서 T-SQL을 사용하는 것에 익숙하다면 차이점 요약에 대해 [Azure SQL 데이터베이스 Transact-SQL 정보](sql-database-transact-sql-information.md)를 참조하세요.

T-SQL을 처음 접하는 경우 [자습서: Transact-SQL 문 쓰기](https://msdn.microsoft.com/library/ms365303.aspx) 및 [Transact-SQL 참조(데이터베이스 엔진)](https://msdn.microsoft.com/library/bb510741.aspx)를 참조하세요.

데이터베이스 사용자 및 데이터베이스 사용자 관리자 만들기를 시작하려면 [Azure SQL 데이터베이스 보안 시작](sql-database-get-started-security.md)을 참조하세요.

<!---HONumber=AcomDC_0518_2016-->