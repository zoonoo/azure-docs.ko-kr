

## 새 데이터베이스 사용자 db\_owner 권한 부여

다음 단계를 사용하여 기존 데이터베이스 사용자 db\_owner 권한 부여

이 단계는 사용자가 SSMS의 개체 탐색기에 있는 SQL 데이터베이스에 연결되어 있으며 서버 수준 보안 주체 관리자 권한 또는 사용자 권한을 부여할 수 있는 권한이 있는 사용자 계정으로 SQL 데이터베이스 논리 서버에 연결되어 있다고 가정합니다.

1. 개체 탐색기에서 데이터베이스 노드를 확장하고 dbo 사용 권한을 부여하려는 사용자로 데이터베이스를 선택합니다.

     ![SQL Server Management Studio: SQL 데이터베이스 서버에 연결](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. 선택한 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **쿼리**를 클릭합니다.

     ![SQL Server Management Studio: SQL 데이터베이스 서버에 연결](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. 쿼리 창에서 다음 Transact-SQL 문을 편집 및 사용하여 지정된 사용자에게 dbo 사용 권한을 부여합니다.

    ```ALTER ROLE db_owner ADD MEMBER user1;
    ```

     ![SQL Server Management Studio: SQL 데이터베이스 서버에 연결](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)

<!---HONumber=AcomDC_0629_2016-->