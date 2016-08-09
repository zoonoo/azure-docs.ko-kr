### 필수 조건
- Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
- [Azure SQL 데이터베이스](../articles/sql-database/sql-database-get-started.md)는 서버 이름, 데이터베이스 이름 및 사용자 이름/암호를 비롯한 해당 연결 정보를 포함합니다. 이 정보는 SQL 데이터베이스 연결 문자열에 포함됩니다.
  
	Server=tcp:*yoursqlservername*.database.windows.net,1433;Initial Catalog=*yourqldbname*;Persist Security Info=False;User ID={your\_username};Password={your\_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

	[Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database)에 대해 자세히 알아봅니다.

> [AZURE.NOTE] Azure SQL 데이터베이스를 만들 때 SQL에 포함된 샘플 데이터베이스도 만들 수 있습니다.



논리 앱에서 Azure SQL 데이터베이스를 사용하기 전에 SQL 데이터베이스에 연결합니다. Azure 포털의 논리 앱 내에서 이 작업을 쉽게 수행할 수 있습니다.

다음 단계를 사용하여 Azure SQL 데이터베이스에 연결합니다.

1. 논리 앱을 만듭니다. 논리 앱 디자이너에서 트리거를 추가하고 동작을 추가합니다. 드롭다운 목록에서 **Microsoft 관리되는 API 표시**를 선택한 다음 검색 상자에 "sql"을 입력합니다. 다음 동작 중 하나를 선택합니다.

	![SQL Azure 연결 만들기 단계](./media/connectors-create-api-sqlazure/sql-actions.png)

2. 이전에 SQL 데이터베이스에 대한 연결을 만들지 않은 경우 연결 세부 정보를 지정하라는 메시지가 표시됩니다.

	![SQL Azure 연결 만들기 단계](./media/connectors-create-api-sqlazure/connection-details.png)

3. SQL 데이터베이스 세부 정보를 입력합니다. 별표가 있는 속성은 필수 사항입니다.

	| 속성 | 세부 정보 |
|---|---|
| 게이트웨이를 통해 연결 | 선택 취소된 상태로 둡니다. 온-프레미스 SQL Server에 연결할 때 사용됩니다. |
| 연결 이름 * | 연결의 이름을 입력합니다. | 
| SQL Server 이름 * | 서버 이름을 입력합니다(예: *servername.database. windows.net*). 서버 이름은 Azure 포털의 SQL 데이터베이스 속성에 표시되고 연결 문자열에도 표시됩니다. | 
| SQL 데이터베이스 이름 * | SQL 데이터베이스에 지정한 이름을 입력합니다. 이 이름은 연결 문자열의 SQL 데이터베이스 속성, Initial Catalog=*yoursqldbname*에 표시됩니다. | 
| 사용자 이름 * | SQL 데이터베이스를 만들 때 만든 사용자 이름을 입력합니다. 이 이름은 Azure 포털의 SQL 데이터베이스 속성에 표시됩니다. | 
| 암호 * | SQL 데이터베이스를 만들 때 만든 암호를 입력합니다. | 

	이러한 자격 증명을 사용하여 SQL 데이터에 연결하도록 논리 앱에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다. 완료되면 연결 정보가 다음과 비슷하게 표시됩니다.

	![SQL Azure 연결 만들기 단계](./media/connectors-create-api-sqlazure/sample-connection.png)

4. **만들기**를 선택합니다.

5. 연결이 만들어졌는지 확인합니다. 이제 논리 앱의 다른 단계를 진행합니다.

	![SQL Azure 연결 만들기 단계](./media/connectors-create-api-sqlazure/table.png)

<!---HONumber=AcomDC_0727_2016-->