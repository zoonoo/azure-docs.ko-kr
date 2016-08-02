<properties
	pageTitle="SQL 데이터베이스 자습서: 보안 시작"
	description="데이터베이스에 액세스하고 관리하기 위해 사용자 계정을 만드는 방법에 대해 알아봅니다."
	keywords=""
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
	ms.date="07/19/2016"
	ms.author="carlrab"/>

# SQL 데이터베이스 자습서: Azure 포털을 사용하여 데이터베이스에 액세스하고 관리하도록 SQL 데이터베이스 사용자 계정 만들기


> [AZURE.SELECTOR]
- [시작 자습서](sql-database-get-started-security.md)
- [액세스 권한 부여](sql-database-manage-logins.md)

이 자습서에서는 Azure 포털을 다음에 사용하는 방법에 대해 알아봅니다.

- 서버 수준 보안 주체 로그인을 사용하여 SQL 데이터베이스에 로그인
- SQL 데이터베이스 사용자 계정 만들기
- 사용자 데이터베이스 내에 SQL 데이터베이스 사용자 계정 dbo 권한 부여
- 서버 수준의 보안 주체가 아닌 사용자 계정으로 SQL 데이터베이스에 연결

[AZURE.INCLUDE [로그인](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [SQL 데이터베이스 논리 서버 만들기](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [SQL 데이터베이스 데이터베이스 만들기](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [SQL 데이터베이스 데이터베이스 만들기](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [SQL 데이터베이스 데이터베이스 만들기](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## 다음 단계
이제 이 SQL 데이터베이스 자습서를 완료하고 사용자 계정을 만들고 사용자 계정 dbo 권한을 부여했으므로 [SQL 데이터베이스 보안](sql-database-manage-logins.md)에 대해 자세히 알아볼 준비가 되었습니다.

<!---HONumber=AcomDC_0720_2016-->