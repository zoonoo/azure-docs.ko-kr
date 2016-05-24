<properties
	pageTitle="SQL 데이터베이스 자습서: SQL 데이터베이스 만들기 | Microsoft Azure"
	description="SQL 데이터베이스 논리 서버, 서버 방화벽 규칙, SQL 데이터베이스, 샘플 데이터를 설정하고 클라이언트 도구로 연결하며 사용자 및 데이터베이스 방화벽 규칙을 구성하는 방법을 알아봅니다."
	keywords="SQL 데이터베이스 자습서, SQL 데이터베이스 만들기"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/14/2016"
	ms.author="carlrab"/>

# SQL 데이터베이스 자습서: Azure 포털을 사용하여 빠르게 SQL 데이터베이스 만들기

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

이 자습서에서는 Azure 포털을 다음에 사용하는 방법에 대해 알아봅니다.

- SQL 데이터베이스 논리 서버를 만들어서 SQL 데이터베이스 호스팅
- 샘플 데이터 또는 SQL 데이터베이스 백업의 데이터로 데이터가 없는 SQL 데이터베이스를 만듭니다.
- 단일 IP 주소 또는 IP 주소의 범위에 서버 수준 방화벽 규칙을 만듭니다.

이러한 링크를 사용하여 [C#](sql-database-get-started-csharp.md) 또는 [PowerShell](sql-database-get-started-powershell.md)을 사용하는 이러한 동일한 태스크를 수행합니다.

[AZURE.INCLUDE [로그인](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [SQL 데이터베이스 논리 서버 만들기](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [SQL 데이터베이스 데이터베이스 만들기](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [SQL 데이터베이스 데이터베이스 만들기](../../includes/sql-database-create-new-server-firewall-portal.md)]

## 다음 단계
이제 이 SQL 데이터베이스 자습서를 완료하고 일부 샘플 데이터가 포함된 데이터베이스를 만들었으므로 원하는 도구를 사용하여 탐색할 수 있습니다.

- Transact-SQL 및 SQL Server Management Studio가 익숙한 경우 [SSMS으로 SQL 데이터베이스 연결 및 쿼리](sql-database-connect-query-ssms.md) 방법에 대해 알아보세요.

- Excel을 알고 있는 경우 [Excel로 SQL 데이터베이스에 연결](sql-database-connect-excel.md)하는 방법에 대해 알아보세요.

- 코딩을 시작할 준비가 되었다면 [SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md)에서 프로그래밍 언어를 선택합니다.

- 온-프레미스 SQL 서버 데이터베이스를 Azure로 이동하려면 [Azure SQL 데이터베이스로 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)에서 자세히 알아보세요.


## 자세한 정보

[SQL 데이터베이스 정의](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0518_2016-->