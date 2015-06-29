<properties
	urlDisplayName="How to connect to an Azure SQL database using SSMS"
	pageTitle="SSMS를 사용하여 Azure SQL 데이터베이스에 연결하는 방법" metaKeywords=""
	description="SSMS를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다."
	metaCanonical=""
	services="sql-database"
	documentationCenter=""
	title="How to connect to an Azure SQL database using SSMS"
	authors="sidneyh" solutions=""
	manager="jhubbard" editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="04/02/2015"
	ms.author="sidneyh" />

# SSMS를 사용하여 Azure SQL 데이터베이스에 연결하는 방법

다음은 SQL Server Management Studio를 사용하여 Microsoft Azure SQL 데이터베이스에 연결하는 절차입니다.

## 필수 조건
* Azure SQL 데이터베이스가 프로비전되고 실행 중이어야 합니다. 새 SQL 데이터베이스를 만들려면 [Microsoft Azure SQL 데이터베이스 시작](sql-database-get-started.md)을 참조하세요.
* SQL 데이터베이스의 관리자 이름 및 암호.
* SQL Server Management Studio 2014. 이 도구를 구하는 방법은 [SQL Express 다운로드](http://www.hanselman.com/blog/DownloadSQLServerExpress.aspx)를 참조하세요.

## SQL 데이터베이스의 인스턴스에 연결하려면
1. [Azure 관리 포털](https://portal.azure.com)에 로그인합니다.
2. **찾아보기** 단추를 클릭한 다음 **SQL 데이터베이스**(b)를 클릭합니다.

	![찾아보기, SQL 데이터베이스를 클릭합니다.][1]
3. **SQL 데이터베이스**가 선택된 상태에서(a), 서버에 있는 데이터베이스 중 연결하려는 이름을 클릭합니다(b).

	![데이터베이스 이름을 클릭합니다.][2]
4. 이름이 선택된 상태에서(a), 속성을 클릭합니다(b). 서버의 이름과(c) 관리자의 이름(d)을 복사합니다. 관리자 이름 및 암호는 SQL 데이터베이스를 만들 때 생성됩니다. 다음 단계로 진행하려면 암호가 있어야 합니다.

	![SQL Server, 설정, 속성을 클릭합니다.][3]
5. SQL Server Management Studio 2014를 엽니다.
6. 서버에 연결 대화 상자에서 **서버 이름** 상자(a)에 서버의 이름을 붙여넣습니다. 인증을 **SQL Server 인증**(b)으로 설정합니다. **로그인** 상자(c)에 서버 관리자 이름을 붙여넣고 암호(d)를 입력합니다. 그 다음 **옵션**(e)을 클릭합니다.

	![SSMS 로그인 대화 상자][4]
7. 연결 속성 탭에서 **데이터베이스에 연결** 상자를 **마스터**(또는 연결하려는 다른 데이터베이스)로 설정하고 **연결**을 클릭합니다.

	![마스터로 설정하고 연결을 클릭합니다.][5]

## 연결 문제 해결

문제가 있을 경우 [Azure SQL 데이터베이스에 대한 연결 문제 해결](https://support.microsoft.com/kb/2980233/)을 참조하세요. 발생 가능한 문제 및 답변 목록을 보려면 [Microsoft Azure SQL 데이터베이스 연결 문제 해결](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1)을 참조하세요.


## 다음 단계
Transact-SQL 문을 사용하여 데이터베이스를 만들거나 관리할 수 있습니다. [데이터베이스 만들기(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/dn268335.aspx) 및 [SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리하기](sql-database-manage-azure-ssms.md)를 참조하세요. 또한 Azure 저장소에 이벤트를 기록할 수도 있습니다. [SQL 데이터베이스 감사 시작](sql-database-auditing-get-started.md)을 참조하세요.

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/browse-vms.png
[2]: ./media/sql-database-connect-to-database/sql-databases.png
[3]: ./media/sql-database-connect-to-database/blades.png
[4]: ./media/sql-database-connect-to-database/ssms-connect-to-server.png
[5]: ./media/sql-database-connect-to-database/ssms-master.png
 

<!---HONumber=58_postMigration-->