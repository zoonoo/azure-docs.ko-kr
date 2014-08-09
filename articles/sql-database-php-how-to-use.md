<properties linkid="develop-php-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (PHP) - Azure feature guides" metaKeywords="Azure SQL Database PHP, SQL Database PHP" description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

PHP에서 Azure SQL 데이터베이스에 액세스하는 방법
================================================

이 가이드에서는 PHP에서 Azure SQL 데이터베이스를 사용하는 방법의 기본 사항을 보여 줍니다. 샘플은 PHP로 작성되었습니다. **SQL 데이터베이스 만들기** 및 **SQL 데이터베이스에 연결** 시나리오를 다룹니다. 이 가이드에서는 [관리 포털](https://manage.windowsazure.com)에서 SQL 데이터베이스를 만듭니다. 프로덕션 포털에서 이러한 작업을 수행하는 방법에 대한 자세한 내용은 [PHP 및 SQL 데이터베이스 시작](http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx)(영문)을 참조하십시오. 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하십시오.

Azure SQL 데이터베이스 정의
---------------------------

Azure SQL 데이터베이스는 Azure에 관계형 데이터베이스 관리 시스템을 제공하며 SQL Server 기술을 기반으로 합니다. SQL 데이터베이스를 사용하여 쉽게 클라우드에 관계형 데이터베이스 솔루션을 프로비전하고 배포할 수 있으며 데이터 보호와 자동 복구가 기본 제공되는 혜택을 갖춘 엔터프라이즈급 가용성, 확장성, 보안을 제공하는 분산 데이터 센터를 활용할 수 있습니다.

목차
----

-   [개념](#Concepts)
-   [방법: 환경 설정](#Setup)
-   [방법: SQL 데이터베이스 만들기](#CreateServer)
-   [방법: SQL 데이터베이스 연결 정보 가져오기](#ConnectionInfo)
-   [방법: SQL 데이터베이스 인스턴스에 연결](#Connect)
-   [다음 단계](#NextSteps)

개념
----

Azure SQL 데이터베이스는 SQL Server 기술을 기반으로 하므로 PHP에서 SQL 데이터베이스에 액세스하는 것은 PHP에서 SQL Server에 액세스하는 것과 매우 유사합니다. 로컬에서 SQL Server를 사용하여 응용 프로그램을 개발한 후 연결 문자열만 변경하여 SQL 데이터베이스에 연결할 수 있습니다. 그러나 응용 프로그램에 영향을 줄 수 있는 SQL 데이터베이스와 SQL Server 간의 몇 가지 차이점이 있습니다. 자세한 내용은 [지침 및 제한 사항(SQL 데이터베이스)](http://msdn.microsoft.com/ko-kr/library/windowsazure/ff394102.aspx)을 참조하십시오.

PHP에서 SQL 데이터베이스에 액세스하는 권장 방법은 [Microsoft Drivers for PHP for SQL Server](http://www.microsoft.com/download/en/details.aspx?id=20098)(영문)를 사용하는 것입니다. (이 문서의 예제에서는 이러한 드라이버를 사용합니다.) Microsoft Drivers for PHP for SQL Server는 Windows에서만 작동합니다.

방법: 환경 설정
---------------

개발 환경을 설치하는 권장 방법은 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=253447)를 사용하는 것입니다. 웹 플랫폼 설치 관리자를 통해 웹 개발 플랫폼의 요소를 선택하여 자동으로 설치 및 구성할 수 있습니다. 웹 플랫폼 설치 관리자를 다운로드하고 WebMatrix, PHP for WebMatrix 및 SQL Server Express를 설치하도록 선택하면 전체 개발 환경이 설치됩니다.

또는 다음과 같이 환경을 수동으로 설치할 수 있습니다.

-   PHP 설치 및 IIS 구성: <http://php.net/manual/en/install.windows.iis7.php>(영문)
-   SQL Server Express 다운로드 및 설치: [http://www.microsoft.com/ko-kr/download/details.aspx?id=29062](http://www.microsoft.com/en-us/download/details.aspx?id=29062)
-   Microsoft Drivers for PHP for SQL Server 다운로드 및 설치: <http://php.net/manual/en/sqlsrv.requirements.php>(영문)

방법: SQL 데이터베이스 만들기
-----------------------------

다음 단계에 따라 Azure SQL 데이터베이스를 만듭니다.

1.  [관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2.  포털의 왼쪽 아래에서 **새로 만들기**를 클릭합니다.

    ![새 Azure 웹 사이트 만들기](./media/sql-database-php-how-to-use-sql-database/plus-new.png)

3.  **데이터 서비스**, **SQL 데이터베이스**, **사용자 지정 만들기**를 차례로 클릭합니다.

    ![새 SQL 데이터베이스 사용자 지정 만들기](./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png)

4.  데이터베이스 **이름**에 값을 입력하고 **버전**(WEB 또는 BUSINESS), 데이터베이스의 **최대 크기**, **정렬** 및 **새 SQL 데이터베이스 서버**를 선택합니다. 대화 상자 아래쪽의 화살표를 클릭합니다. 이전에 SQL 데이터베이스를 만든 경우 **서버 선택** 드롭다운에서 기존 서버를 선택할 수 있습니다.

    ![SQL 데이터베이스 설정 입력](./media/sql-database-php-how-to-use-sql-database/new-sql-db.png)

5.  관리자 이름 및 암호를 입력하고, 암호를 확인하고, 새 SQL 데이터베이스 만들 지역을 선택하고, `Azure 서비스가 서버에 액세스할 수 있도록 허용합니다.` 상자를 선택합니다.

    ![새 SQL 데이터베이스 서버 만들기](./media/sql-database-php-how-to-use-sql-database/db-server-settings.png)

서버 및 데이터베이스 정보를 보려면 관리 포털에서 **SQL 데이터베이스**를 클릭합니다. 그런 다음 **데이터베이스** 또는 **서버**를 클릭하여 관련 정보를 볼 수 있습니다.

![서버 및 데이터베이스 정보 보기](./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png)

방법: SQL 데이터베이스 연결 정보 가져오기
-----------------------------------------

SQL 데이터베이스 연결 정보를 가져오려면 포털에서 **SQL 데이터베이스**를 클릭하고 데이터베이스 이름을 클릭합니다.

![데이터베이스 정보 보기](./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png)

그런 다음 **View SQL Database connection strings for ADO.NET, ODBC, PHP, and JDBC**를 클릭합니다.

![연결 문자열 표시](./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png)

결과 창의 PHP 섹션에서 **서버**, **데이터베이스** 및 **사용자 이름** 값을 기록해 둡니다. 암호는 SQL 데이터베이스를 만들 때 사용한 암호입니다.

방법: SQL 데이터베이스 인스턴스에 연결
--------------------------------------

다음 예제에서는 **SQLSRV** 및 **PDO\_SQLSRV** 확장을 사용하여 `testdb`라는 SQL 데이터베이스에 연결하는 방법을 보여 줍니다. 앞의 섹션에서 얻은 정보가 필요합니다. `SERVER_ID`를 10자로 된 서버 ID(앞의 섹션에서 얻은 서버 값의 처음 10자)로 바꾸고, 올바른 값(사용자 이름 및 암호)을 `$user` 및 `$pwd` 변수에 할당합니다.

##### SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    $conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

    if($conn === false){
        die(print_r(sqlsrv_errors()));
    }

##### PDO\_SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    try {
        $conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
        $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
    }
    catch(Exception $e) {
        die(print_r($e));
    }

다음 단계
---------

앞에서 언급했듯이 SQL 데이터베이스 사용은 SQL Server 사용과 매우 유사합니다. 앞에 표시된 것처럼 SQL 데이터베이스에 대한 연결을 설정하고 나면 **SQLSRV** 또는 **PDO\_SQLSRV** API를 사용하여 데이터를 삽입, 검색, 업데이트 및 삭제할 수 있습니다. **SQLSRV** 및 **PDO\_SQLSRV** API에 대한 자세한 내용은 [Microsoft Drivers for PHP for SQL Server 설명서](http://msdn.microsoft.com/ko-kr/library/dd638075(SQL.10).aspx)를 참조하십시오. 그러나 응용 프로그램에 영향을 줄 수 있는 SQL 데이터베이스와 SQL Server 간의 몇 가지 차이점이 있습니다. 자세한 내용은 [지침 및 제한 사항(SQL 데이터베이스)](http://msdn.microsoft.com/ko-kr/library/windowsazure/ff394102.aspx)을 참조하십시오.

Azure에서 PHP로 SQL 데이터베이스를 사용하는 방법을 보여 주는 샘플은 https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure 에서 제공합니다.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/ko-kr/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/en-us/download/details.aspx?id=29062
[preview-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png

