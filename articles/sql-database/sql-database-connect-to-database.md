<properties
	pageTitle="SSMS를 사용하여 Azure SQL 데이터베이스에 연결하는 방법" 
	description="SSMS를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다."
	services="sql-database"
	documentationCenter=""
	authors="sidneyh" 
	manager="jhubbard" 
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/15/2015"
	ms.author="sidneyh" />

# SQL Server Management Studio로 연결

SQL Server Management Studio(SSMS)를 설치하고 SSMS를 사용하여 연결하여 SQL 데이터베이스를 쿼리하는 단계입니다.

## 필수 조건
* [Microsoft Azure SQL 데이터베이스 시작](sql-database-get-started.md)에서 설명한 대로 SQL 데이터베이스 AdventureWorks 샘플 데이터베이스.

## SSMS(SQL Server Management Studio) 설치 및 시작
1. [SQL Server 2014 Express](http://www.microsoft.com/download/details.aspx?id=42299)에 대한 다운로드 페이지로 이동하고 **다운로드**를 클릭하고 MgmtStudio 다운로드의 32비트 버전(x86) 또는 64비트 버전(x64) 중 하나를 선택합니다.

	![MgtmtStudio32BIT 또는 MgmtStudio64BIT][1]
2.	기본 설정을 사용하여 SSMS를 설치할 때 나타나는 프롬프트를 따릅니다.
3.	다운로드 된 후 사용자 PC에서 SQL Server 2014 Management Studio를 검색한 다음 SSMS를 시작합니다.


## SQL 데이터베이스 연결
1. SSMS를 엽니다.
2. **서버에 연결** 창의 **서버 이름** 상자에 *&lt;servername>*.**database.windows.net** 형식으로 서버 이름을 입력합니다.
3. **인증** 드롭다운에서 **SQL Server 인증**을 선택합니다.
4. SQL 데이터베이스 서버를 만들 때 지정한 **로그인** 및 **암호**를 입력합니다.

	![서버에 연결 대화 상자][2]
5. **옵션** 단추를 클릭합니다.
6. **데이터베이스에 연결** 상자에 **AdventureWorks**를 입력하고 **연결**을 클릭합니다.

	![데이터베이스에 연결][3]

### 연결에 실패하는 경우
만든 논리 서버의 방화벽에서 로컬 컴퓨터의 연결을 허용하는지 확인합니다. [방법: 방화벽 설정 구성(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/azure/jj553530.aspx)을 참조하세요.

## 예제 쿼리 실행

1. **개체 탐색기**에서 **AdventureWorks** 데이터베이스로 이동합니다.
2. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다.

	![새 쿼리][4]
3. 새로 열린 쿼리 창에서 다음 코드를 복사하여 붙여넣습니다.

		SELECT 
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. 그런 다음 **실행** 단추를 클릭합니다. 성공 시 다음과 같이 표시됩니다. ![성공][5]


## 다음 단계
Transact-SQL 문을 사용하여 데이터베이스를 만들거나 관리할 수 있습니다. [데이터베이스 만들기(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/dn268335.aspx) 및 [SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리하기](sql-database-manage-azure-ssms.md)를 참조하세요. 또한 Azure 저장소에 이벤트를 기록할 수도 있습니다. [SQL 데이터베이스 감사 시작](sql-database-auditing-get-started.md)을 참조하세요.

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
 

<!---HONumber=August15_HO6-->