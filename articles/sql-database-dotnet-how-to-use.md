<properties urlDisplayName="SQL Database" pageTitle="SQL 데이터베이스를 사용하는 방법(.NET) - Azure 기능 가이드" metaKeywords="SQL Azure 시작, SQL Azure 시작, SQL Azure 데이터베이스 연결, SQL Azure ADO.NET, SQL Azure ODBC, SQL Azure EntityClient" description="SQL 데이터베이스를 시작합니다. SQL 데이터베이스 인스턴스를 만들고 ADO.NET, ODBC 및 EntityClient 공급자를 사용하여 이 인스턴스에 연결하는 방법에 대해 알아봅니다." metaCanonical="" services="sql-database" documentationCenter=".NET" title="How to use Azure SQL Database in .NET applications" authors="jeffreyg" solutions="" manager="jeffreyg" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/31/2015" ms.author="jeffreyg" />







# .NET 응용 프로그램에서 Azure SQL 데이터베이스를 사용하는 방법

이 가이드에서는 Azure SQL 데이터베이스에서 논리 서버 및 데이터베이스 인스턴스를 만든 다음
ADO.NET, ODBC, EntityClient 공급자 등의 .NET Framework 데이터 공급자 기술을 사용하여 데이터베이스에 연결하는 방법을
보여 줍니다.


<h2><a name="Whatis"></a>SQL 데이터베이스 정의</h2>

SQL 데이터베이스는 Azure에 관계형 데이터베이스 관리 시스템을 제공하며 SQL Server 기술을 기반으로 합니다. SQL 데이터베이스 인스턴스를 사용하여 쉽게 클라우드에 관계형 데이터베이스 솔루션을 프로비전하고 배포할 수 있으며 데이터 보호와 자동 복구가 기본 제공되는 혜택을 갖춘 엔터프라이즈급 가용성, 확장성, 보안을 제공하는 분산 데이터 센터를 활용할 수 있습니다.

## 목차

- [Azure에 로그인](#PreReq1)
- [SQL 데이터베이스 만들기 및 구성](#PreReq2)
- [SQL 데이터베이스에 연결](#connect-db) 
- [ADO.NET을 사용하여 연결](#using-sql-server)
- [ODBC를 사용하여 연결](#using-ODBC)
- [EntityClient 공급자를 사용하여 연결](#using-entity)
- [다음 단계](#next-steps)

<h2><a name="PreReq1"></a>Azure에 로그인</h2>

SQL 데이터베이스를 통해 Azure에서 관계형 데이터 저장소, 액세스 및 관리 서비스를 사용할 수 있습니다. 그러려면 Azure 구독이 필요합니다.

1. 웹 브라우저를 열고 [http://www.windowsazure.com](http://www.windowsazure.com)으로 이동합니다. 무료 계정으로 시작하려면 오른쪽 위에 있는 무료 평가판을 클릭하고 안내되는 단계를 따르세요

2. 이제 계정이 만들어집니다. 시작할 준비가 되었습니다.


<h2><a name="PreReq2"></a>SQL 데이터베이스 만들기 및 구성</h2>

다음으로, 데이터베이스 및 서버를 만들고 구성합니다. Azure 관리 포털에서 수정된 워크플로를 통해 데이터베이스를 처음 만든 후에 서버를 프로비전합니다. 

<h3 name="createsrvr">데이터베이스 인스턴스 및 논리 서버 만들기</h3>

1. [Azure 관리 포털][]에 로그인합니다.

2. 페이지 아래쪽에서 **+새로 만들기**를 클릭합니다.

3. **데이터 서비스**를 클릭합니다.

4. **SQL 데이터베이스**를 클릭합니다.

5. **사용자 지정 만들기**를 클릭합니다. 

6. 이름에 데이터베이스 이름을 입력합니다.

7. 버전, 최대 크기 및 데이터 정렬을 선택합니다. 이 가이드에서는 기본값을 사용할 수 있습니다. 

	SQL 데이터베이스에는 Basic, Standard 및 Premium의 세 가지 데이터베이스 버전이 있습니다.

	MAXSIZE는 데이터베이스를 처음 만들 때 지정되며
나중에 ALTER DATABASE를 사용하여 변경할 수 있습니다. MAXSIZE는
데이터베이스 크기를 제한하는 기능을 제공합니다.

	Azure에서 만들어진 SQL 데이터베이스별로
해당 데이터베이스의 복제본이 실제로 3개가 있습니다. 이 복제본은 고가용성을 보장하기 위한 것입니다.
장애 조치(Failover)가 투명하며 서비스의 일부로 제공됩니다. [서비스 수준
계약][]은 SQL 데이터베이스에 대해 99.9% 작동 시간을 제공합니다.

8. 서버에서 **새 SQL 데이터베이스 서버**를 선택합니다. 

9. 화살표를 클릭하여 다음 페이지로 이동합니다.

10. 서버 설정에서 SQL Server 인증 로그인 이름을 입력합니다.

	SQL 데이터베이스는 암호화된 연결을 통해 SQL 인증을 사용합니다. sysadmin 고정 서버 역할에 할당된 새 SQL Server 인증 로그인은 사용자가 제공한 이름을 사용하여 만들어집니다. 

	로그인은 메일 주소, Windows 사용자 계정 또는 Windows Live ID일 수 없습니다. SQL 데이터베이스에서는 클레임과 Windows 인증이 모두 지원되지 않습니다.

11. 대문자 및 소문자 값과 숫자 또는 기호를 조합하여 8자 이상의 강력한 암호를 입력합니다.

12. 지역을 선택합니다. 지역은 서버의 지리적 위치를 결정합니다. 지역은 쉽게 바꿀 수 없으므로 이 서버에 맞는 지역을 선택하세요. 사용자에게 가장 가까운 위치를 선택합니다. Azure 응용 프로그램 및 데이터베이스를 동일한 지역에 유지하면 발신용 대역폭 비용과 데이터 대기 시간이 절약됩니다.

13. SQL 데이터베이스 관리 포털, 저장소 서비스 및 Azure의 기타 서비스를 사용하여 이 데이터베이스에 연결할 수 있도록 **Azure 서비스가 서버에 액세스할 수 있도록 허용합니다.** 옵션을 선택된 상태로 유지해야 합니다. 

14. 완료하면 페이지 맨 아래에 있는 확인 표시를 클릭합니다.

서버 이름을 지정하지 않았다는 점에 유의하세요. SQL 데이터베이스는 서버 이름을 자동 생성하여 중복 DNS 항목이 없도록 합니다. 서버 이름은 10자로 된 영숫자 문자열입니다. SQL 데이터베이스 서버의 이름은 변경할 수 없습니다.

데이터베이스가 만들어진 후에는 데이터베이스를 클릭하여 대시보드를 엽니다. 대시보드에는 복사하여 응용 프로그램 코드에서 사용할 수 있는 연결 문자열이 나옵니다. 또한 Management Studio 또는 기타 관리 도구에서 데이터베이스에 연결하는 경우 지정해야 하는 관리 URL도 표시됩니다.


![image](./media/sql-database-dotnet-how-to-use/SQLDbDashboard.PNG)


다음 단계에서는 네트워크에서 실행 중인 응용 프로그램의 연결이 허용된 액세스가 되도록 방화벽을 구성합니다.

<h3 name="configFWLogical">논리 서버용 방화벽 구성</h3>

1. **SQL 데이터베이스**를 클릭하고 페이지 맨 위에서 **서버**를 클릭한 후 방금 만든 서버를 클릭합니다.

	![Image2](./media/sql-database-dotnet-how-to-use/SQLDBFirewall.PNG)

2. **구성**을 클릭합니다. 

3. 현재 클라이언트 IP 주소를 복사합니다. 네트워크에서 연결하는 경우 이 주소는 라우터 또는 프록시 서버가 수신 대기하는 IP 주소입니다. SQL 데이터베이스는 이 장치의 연결 요청을 허용하는 방화벽 규칙을 만들 수 있도록 현재 연결에 사용되는 IP 주소를 검색합니다. 

4. 시작 IP 주소 및 끝 IP 주소에 IP 주소를 붙여넣어 서버 액세스가 허용되는 범위 주소를 설정합니다. 나중에, 범위가 너무 좁다는 연결 오류가 발생할 경우 이 규칙을 편집하여 범위를 넓힐 수 있습니다.

	클라이언트 컴퓨터가 동적으로 할당된 IP 주소를 사용하는 경우 네트워크의 컴퓨터에 할당된 IP 주소를 포함하기에 충분히 넓은 범위를 지정해야 합니다. 좁은 범위로 시작한 후 필요한 경우에만 확장합니다.

5. 방화벽 규칙의 이름(예: 컴퓨터 또는 회사의 이름)을 입력합니다.

6. 규칙 옆에 있는 확인 표시를 클릭하여 규칙을 저장합니다.

	![Image3](./media/sql-database-dotnet-how-to-use/SQLDBIPRange.PNG)

7. 페이지 맨 아래에서 **저장**을 클릭하여 단계를 완료합니다. **저장** 단추가 나타나지 않으면 브라우저 페이지를 새로 고칩니다.

이제 데이터베이스 인스턴스, 논리 서버, IP 주소의 인바운드 연결을 허용하는 방화벽 규칙 및 관리자 로그인이 있습니다. 이제 프로그래밍 방식으로 데이터베이스에 연결할 준비가 되었습니다.


<h2><a name="Connect-DB"></a>SQL 데이터베이스에 연결</h2>

이 섹션에서는 다양한 .NET Framework 데이터 공급자를 사용하여 SQL 데이터베이스 인스턴스에 연결하는 방법을 보여 줍니다.

Visual Studio를 사용하려고 하며 구성에 Azure 웹 응용 프로그램이 프런트 엔드로 포함되어 있지 않은 경우 개발 컴퓨터에 추가 도구 또는 SDK를 설치할 필요가 없습니다. 응용 프로그램 개발을 시작하면 됩니다.

SQL Server 작업 시 사용할 수 있는 것과 동일한 Visual Studio의 모든 디자이너 도구를 사용하여 SQL 데이터베이스 작업을 할 수 있습니다. 서버 탐색기를 통해
데이터베이스 개체를 볼 수 있습니다(편집할 수 없음). Visual Studio 엔터티 데이터 모델 디자이너가 제대로 작동하며, 이를 사용하여 Entity Framework 작업을 위해 SQL 데이터베이스에 대한 모델을 만들 수 있습니다.

### <a name="using-sql-server"></a>.NET Framework Data Provider for SQL Server 사용

**System.Data.SqlClient** 네임스페이스는 .NET Framework Data
Provider for SQL Server입니다.

표준 연결 문자열은 다음과 같습니다.

    Server=tcp:.database.windows.net;
    Database=;
    User ID=@;
    Password=;
    Trusted_Connection=False;
    Encrypt=True;

다음 코드 샘플에서 볼 수 있는 것처럼 **SQLConnectionStringBuilder** 클래스를 사용하여 연결 문자열을 작성할 수 있습니다.

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder();
    csBuilder.DataSource = xxxxxxxxxx.database.windows.net;
    csBuilder.InitialCatalog = testDB;
    csBuilder.Encrypt = true;
    csBuilder.TrustServerCertificate = false;
    csBuilder.UserID = MyAdmin;
    csBuilder.Password = pass@word1;

연결 문자열의 요소가 사전에 알려진 경우 요소를 구성 파일에 저장했다가 런타임에 검색하여 연결 문자열을 생성할 수 있습니다. 다음은 구성 파일의 샘플 연결 문자열입니다.

    <connectionStrings>
      <add name="ConnectionString" 
           connectionString ="Server=tcp:xxxxxxxxxx.database.windows.net;Database=testDB;User ID=MyAdmin@xxxxxxxxxx;Password=pass@word1;Trusted_Connection=False;Encrypt=True;" />
    </connectionStrings>

구성 파일에서 연결 문자열을 검색하려면 **ConfigurationManager** 클래스를 사용합니다.

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder(ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString);
    After you have built your connection string, you can use the SQLConnection class to connect the SQL Database server:
    SqlConnection conn = new SqlConnection(csBuilder.ToString());
    conn.Open();

### <a name="using-ODBC"></a>.NET Framework Data Provider for ODBC 사용

**System.Data.Odbc** 네임스페이스는 .NET Framework Data Provider for ODBC입니다. 다음은 샘플 ODBC 연결 문자열입니다.

    Driver={SQL Server Native Client 10.0};
    Server=tcp:.database.windows.net;
    Database=;
    Uid=@;
    Pwd=;
    Encrypt=yes;

**OdbcConnection** 클래스는 데이터 원본에 대한 열려 있는 연결을 나타냅니다. 다음은 연결을 여는 방법에 대한 코드 샘플입니다.

    string cs = "Driver={SQL Server Native Client 10.0};" +
                "Server=tcp:xxxxxxxxxx.database.windows.net;" +
                "Database=testDB;"+
                "Uid=MyAdmin@xxxxxxxxxx;" +
                "Pwd=pass@word1;"+
                "Encrypt=yes;";

    OdbcConnection conn = new OdbcConnection(cs);
    conn.Open();

런타임에 연결 문자열을 작성하려는 경우 **OdbcConnectionStringBuilder** 클래스를 사용할 수 있습니다.

### <a name="using-entity"></a>EntityClient 공급자 사용

**System.Data.EntityClient** 네임스페이스는 Entity Framework용 .NET Framework 데이터 공급자입니다.

Entity Framework를 통해 개발자는 관계형 저장소 스키마에 대해 직접 프로그래밍하는 대신 개념적 응용 프로그램 모델에 대해 프로그래밍하여 데이터 액세스 응용 프로그램을 만들 수 있습니다. Entity Framework는 기본 데이터 공급자 및 관계형 데이터베이스에 **EntityConnection**을 제공하여 저장소별 ADO.NET 데이터 공급자를 기반으로 구축됩니다.

**EntityConnection** 개체를 생성하려면 필요한 모델 및 매핑이 포함된 메타데이터 집합뿐 아니라 저장소별 데이터 공급자 이름 및 연결 문자열도 참조해야 합니다.   **EntityConnection**가 생성된 후에는 개념적 모델에서 생성된 클래스를 통해 엔터티에 액세스할 수 있습니다.

다음은 연결 문자열 샘플입니다.

    metadata=res://*/SchoolModel.csdl|res://*/SchoolModel.ssdl|res://*/SchoolModel.msl;provider=System.Data.SqlClient;provider connection string="Data Source=xxxxxxxxxx.database.windows.net;Initial Catalog=School;Persist Security Info=True;User ID=MyAdmin;Password=***********"

자세한 내용은 [Entity Framework용 EntityClient 공급자
][](영문)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SQL 데이터베이스 연결의 기본 사항을 알아봤습니다. 이제 다음 리소스에서 SQL 데이터베이스에 대해 자세히 알아봅니다.

-   [개발: 방법 항목(SQL 데이터베이스)][]
-   [SQL 데이터베이스][]


  [SQL 데이터베이스 정의]: #WhatIs
  [Azure에 로그인]: #PreReq1
  [SQL 데이터베이스 만들기 및 구성]: #PreReq2
  [SQL 데이터베이스에 연결]: #connect-db
  [ADO.NET을 사용하여 연결]: #using-sql-server
  [ODBC를 사용하여 연결]: #using-ODBC
  [EntityClient 공급자를 사용하여 연결]: #using-entity
  [다음 단계]: #next-steps
  [Azure 무료 평가판]: {localLink:2187} "무료 평가판"
  [Azure 관리 포털]: http://manage.windowsazure.com
  [SQL 데이터베이스 서버를 만드는 방법]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-server.aspx
  [SQL 데이터베이스용 관리 포털]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg442309.aspx
  [SQL 데이터베이스 방화벽]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-firewall.aspx
  [도구 및 유틸리티 지원(SQL 데이터베이스)]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee621784.aspx
  [Azure에 SQL 데이터베이스를 만드는 방법]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-database.aspx
  [서비스 수준 계약]: {localLink:1132} "SLA"
  [Entity Framework용 EntityClient 공급자]: http://msdn.microsoft.com/ko-kr/library/bb738561.aspx
  [개발: 방법 항목(SQL 데이터베이스)]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee621787.aspx
  [SQL 데이터베이스]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee336279.aspx

<!--HONumber=35.2-->
