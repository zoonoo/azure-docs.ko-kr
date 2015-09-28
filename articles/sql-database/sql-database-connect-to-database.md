<properties
	urlDisplayName="How to connect to an Azure SQL database using SQL Server Management Studio (SSMS)"
	pageTitle="SSMS를 사용하여 Azure SQL 데이터베이스에 연결하는 방법 | Microsoft Azure"
	metaKeywords=""
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
	ms.date="09/14/2015"
	ms.author="sidneyh" />

# SQL Server Management Studio(SSMS)로 연결

SSMS(SQL Server Management Studio)를 사용하여 SQL 데이터베이스를 연결하고 쿼리하려면 다음 단계를 따르세요.

## 필수 조건

* SSMS(SQL Server Management Studio) - 최신 버전의 SSMS를 다운로드하려면 [SQL Server Management Studio 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)를 참조하세요.
* [Microsoft Azure SQL 데이터베이스 시작](sql-database-get-started.md)에 설명된 AdventureWorks 샘플 데이터베이스


## 정규화된 SQL Azure 서버 이름 가져오기

데이터베이스에 연결하려면 연결하려는 데이터베이스를 포함하는 서버의 전체 이름(****servername**.database.windows.net*)이 필요합니다.

1. [Azure Preview 포털](https://portal.azure.com)로 이동합니다.
2. 연결하려는 데이터베이스를 찾습니다.
3. 전체 서버 이름을 찾습니다.

    ![정규화된 서버 이름][6]

    아래 3단계에서 정규화된 서버 이름을 사용합니다.



## SQL 데이터베이스 연결

1. SSMS를 엽니다.
2. **연결** > **데이터베이스 엔진...**을 클릭합니다.

    ![연결 > 데이터베이스 엔진][7]

2. **서버에 연결** 대화 상자의 **서버 이름** 상자에 *&lt;servername>*.**database.windows.net** 형식으로 서버 이름을 입력합니다.
3. **인증** 목록에서 **SQL Server 인증**을 선택합니다.
4. SQL 데이터베이스 서버를 만들 때 지정한 **로그인** 및 **암호**를 입력하고 **연결**을 클릭합니다.

	![서버에 연결 대화 상자][2]



### 연결에 실패하는 경우
만든 논리 서버의 방화벽에서 로컬 컴퓨터의 연결을 허용하는지 확인합니다. 자세한 내용은 [방법: SQL 데이터베이스에 방화벽 설정 구성](sql-database-configure-firewall-settings.md)을 참조하세요.

## 예제 쿼리 실행

1. **개체 탐색기**에서 **AdventureWorks** 데이터베이스로 이동합니다.
2. 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 선택합니다.

	![새 쿼리][4]

3. 쿼리 창에서 다음 코드를 복사하여 붙여넣습니다.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. **실행** 단추를 클릭합니다. 다음 스크린샷에서는 성공적인 쿼리를 보여 줍니다.

	![성공][5]




## 다음 단계
Transact-SQL 문을 사용하여 데이터베이스를 만들거나 관리할 수 있습니다. 자세한 내용은 [데이터베이스 만들기(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/dn268335.aspx) 및 [SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리](sql-database-manage-azure-ssms.md)를 참조하세요. 또한 Azure 저장소에 이벤트를 기록할 수도 있습니다. 자세한 내용은 [SQL 데이터베이스 감사 시작](sql-database-auditing-get-started.md)을 참조하세요.

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
[6]: ./media/sql-database-connect-to-database/server-name.png
[7]: ./media/sql-database-connect-to-database/connect-dbengine.png

<!---HONumber=Sept15_HO3-->