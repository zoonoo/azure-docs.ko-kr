## Azure Portal에 로그인
<a id="log-in-to-the-azure-portal" class="xliff"></a>

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## Azure Portal을 사용하여 빈 SQL Database 만들기
<a id="create-a-blank-sql-database-using-the-azure-portal" class="xliff"></a>

Azure SQL Database는 일련의 정의된 [계산 및 저장소 리소스](../articles/sql-database/sql-database-service-tiers.md)를 사용하여 만들어집니다. 데이터베이스는 [Azure 리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md) 및 [Azure SQL Database 논리 서버](../articles/sql-database/sql-database-features.md)에서 만들어집니다. 

빈 SQL Database를 만들려면 다음 단계를 수행합니다. 

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. **새로 만들기** 페이지에서 **데이터베이스**를 선택하고 **데이터베이스** 페이지에서 **SQL Database**를 선택합니다. 

   ![빈 데이터베이스 만들기](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

3. 위의 이미지에 표시된 대로 다음과 같은 정보를 사용하여 SQL Database 형식을 작성합니다.   

   | 설정 | 제안 값 | 설명 |
   | --------| --------------- | ----------- | 
   | **데이터베이스 이름** | mySampleDatabase | 유효한 데이터베이스 이름은 [데이터베이스 식별자](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)를 참조하세요. | 
   | **구독** | 사용자의 구독  | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
   | **리소스 그룹** | myResourceGroup | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
   | **원본 선택** | 빈 데이터베이스 | 빈 데이터베이스를 만들어야 함을 나타냅니다. |
   ||||

4. **서버**를 클릭하여 새 데이터베이스에 새 서버를 만들고 구성합니다. 다음 정보로 **새 서버 양식**을 작성합니다. 

   | 설정 | 제안 값 | 설명 |
   | --------| --------------- | ----------- | 
   | **서버 이름** | 전역적으로 고유한 이름 | 유효한 서버 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. | 
   | **서버 관리자 로그인** | 모든 유효한 이름 | 유효한 로그인 이름은 [데이터베이스 식별자](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)를 참조하세요.|
   | **암호** | 유효한 암호 | 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지 범주의 문자를 포함해야 합니다. |
   | **위치** | 모든 유효한 위치 | 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |
   ||||

   ![create database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

5. **선택**을 클릭합니다.

6. **가격 책정 계층**을 클릭하고 새 데이터베이스의 서비스 계층 및 성능 수준을 지정합니다. 이 자습서에서는 **20 DTU** 및 **250**GB의 저장소를 선택합니다.

   ![create database-s1](../articles/sql-database/media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. **Apply**를 클릭합니다.  

8. 빈 데이터베이스에 대한 **데이터 정렬**을 선택합니다(이 자습서의 경우 기본값 사용). 데이터 정렬에 대한 자세한 내용은 [데이터 정렬](https://docs.microsoft.com/sql/t-sql/statements/collations)을 참조하세요.

9. **만들기**를 클릭하여 데이터베이스를 프로비전합니다. 프로비전을 완료하는 데 약 1분 30초가 걸립니다. 

10. 도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.

   ![알림](../articles/sql-database/media/sql-database-get-started-portal/notification.png)

## Azure Portal을 사용하여 서버 수준 방화벽 규칙 만들기
<a id="create-a-server-level-firewall-rule-using-the-azure-portal" class="xliff"></a>

SQL Database 서비스는 서버 수준 방화벽을 만듭니다. 처음에는 방화벽이 외부 도구와 응용 프로그램이 서버 또는 서버의 모든 데이터베이스에 연결하지 못하도록 합니다. 연결은 방화벽 규칙을 만들어 특정 IP 주소를 연 후 허용됩니다. 다음 단계에 따라 클라이언트의 IP 주소에 대한 [SQL Database 서버 수준 방화벽 규칙](../articles/sql-database/sql-database-firewall-configure.md)을 만들고 IP 주소에만 SQL Database 방화벽을 통해 외부 연결을 사용하도록 설정합니다. 


> [!NOTE]
> Azure SQL Database는 포트 1433을 통해 통신합니다. 네트워크의 방화벽이 1433 포트를 통한 아웃바운드 트래픽을 허용한 후에만 SQL Database에 연결할 수 있습니다.


1. 배포가 완료되면 왼쪽 메뉴에서 **SQL Database**를 클릭한 다음 **SQL Database** 페이지에서 **mySampleDatabase**를 클릭합니다. 데이터베이스에 대한 개요 페이지가 열리고 이 페이지에 정규화된 서버 이름(예: **mynewserver20170313.database.windows.net**)이 표시되며 추가 구성을 위한 옵션도 제공됩니다. 나중에 사용하기 위해 이 정규화된 서버 이름을 복사합니다.

   > [!IMPORTANT]
   > 이후 빠른 시작에서 서버 및 해당 데이터베이스에 연결하려면 이 정규화된 서버 이름이 필요합니다.
   > 

   ![서버 이름](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 

2. 이전 이미지에 표시된 대로 도구 모음에서 **서버 방화벽 설정**을 클릭합니다. SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다. 

   ![서버 방화벽 규칙](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png) 


3. 도구 모음에서 **클라이언트 IP 추가**를 클릭하여 현재 IP 주소를 새 방화벽 규칙에 추가합니다. 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

4. **Save**를 클릭합니다. 논리 서버의 1433 포트를 여는 현재 IP 주소에 서버 수준 방화벽 규칙이 생성됩니다.

   ![set server firewall rule](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. **확인**을 클릭한 후 **방화벽 설정** 페이지를 닫습니다.

이제 SQL Server Management Studio(SSMS)와 같은 도구를 사용하여 Azure SQL Database 서버와 데이터베이스에 연결할 수 있습니다. 이 IP 주소를 통해 연결되며 이전에 만든 서버 관리자 계정을 사용합니다.


> [!IMPORTANT]
> SQL Database 방화벽을 통한 액세스는 기본적으로 모든 Azure 서비스에 대해 사용됩니다. 이 페이지에서 **끄기**를 클릭하여 모든 Azure 서비스에 대해 사용하지 않도록 설정합니다.


## Azure Portal을 사용하여 연결 문자열 값 가져오기
<a id="get-connection-string-values-using-the-azure-portal" class="xliff"></a>

Azure Portal에 있는 Azure SQL Database 서버의 정규화된 서버 이름을 가져옵니다. 정규화된 서버 이름을 사용하여 SQL Server Management Studio를 사용하는 서버에 연결합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 

3. 데이터베이스의 경우 Azure Portal의 **Essentials** 창에서 **서버 이름**을 찾고 복사합니다.

   ![연결 정보](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 
