
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## 새 Azure SQL 데이터베이스 서버 수준 방화벽 만들기

Azure 포털에서 다음 단계를 사용하여 (클라이언트 컴퓨터의)개별 IP 주소 또는 전체 IP 주소 범위에서 SQL 데이터베이스 논리 서버에 연결을 허용하는 서버 수준 방화벽 규칙을 만듭니다.

1. 연결되어 있지 않은 경우 [Azure 포털](http://portal.azure.com)에 연결합니다.
2. 기본 블레이드에서 **SQL Server**를 클릭합니다.

  	![새 서버 방화벽](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)

2. SQL Server 블레이드에서 방화벽 규칙을 만들 SQL 데이터베이스 서버를 클릭합니다.

 	![새 서버 방화벽](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)
           
3. 서버의 속성을 검토합니다.

 	![새 서버 방화벽](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)
      
4. 설정 블레이드에서 **방화벽**을 클릭합니다.

 	![새 서버 방화벽](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-4.png)
    
5. **클라이언트 IP 추가**를 클릭하여 Azure에서 클라이언트 IP 주소에 규칙을 생성하도록 합니다.

      ![새 서버 방화벽](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)

6. 필요에 따라 방화벽 주소를 편집하는 데 추가된 IP 주소를 클릭하여 IP 주소의 범위에 대한 액세스를 허용합니다.

      ![새 서버 방화벽](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)
    
7. **저장**을 클릭하여 서버 수준 방화벽 규칙을 만듭니다.

     ![새 서버 방화벽](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)

	>[AZURE.IMPORTANT] 클라이언트 IP 주소가 수시로 변경될 수 있으면 새 방화벽 규칙을 만들 때까지 서버에 액세스하지 못할 수 있습니다. [Bing](http://www.bing.com/search?q=my%20ip%20address)을 사용하여 IP 주소를 확인한 후 단일 IP 주소 또는 IP 주소 범위를 추가할 수 있습니다. 자세한 내용은 [방화벽 설정 관리](sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal)를 참조하세요.

<!---HONumber=AcomDC_0427_2016-->