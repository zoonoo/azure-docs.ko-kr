<properties linkid="develop-php-website-with-sql-database-and-webmatrix" urlDisplayName="Web w/ SQL + WebMatrix" pageTitle="PHP website with SQL Database and WebMatrix - Azure" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in SQL Database." metaCanonical="" services="" documentationCenter="" title="Create and Deploy a PHP Website and SQL Database using WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />





# WebMatrix를 사용하여 PHP 웹 사이트 및 SQL 데이터베이스 만들기 및 배포

이 자습서에서는 WebMatrix를 사용하여, Azure SQL 데이터베이스를 사용하는 PHP 응용 프로그램을 개발하고 Azure 웹 사이트에 배포하는 방법을 설명합니다. WebMatrix는 웹 사이트 개발에 필요한 모든 기능이 포함된 Microsoft의 무료 웹 개발 도구입니다. WebMatrix는 PHP를 지원하며 PHP 개발에 필요한 IntelliSense를 포함하고 있습니다.

이 자습서의 내용은 컴퓨터에 [SQL Server Express][SQL Server Express]가 설치되어 있어서 로컬로 응용 프로그램을 테스트할 수 있다는 것을 전제로 합니다. 하지만 SQL Server Express를 설치하지 않은 상태로 자습서를 완료할 수 있습니다. 대신 응용 프로그램을 Azure 웹 사이트에 직접 배포할 수 있습니다.

이 가이드를 완료하면 Azure에서 실행하는 PHP-SQL 데이터베이스 웹 사이트가 완성됩니다.

다음 내용을 배웁니다.

-   관리 포털을 사용하여 Azure 웹 사이트 및 SQL 데이터베이스를 만드는 방법. PHP는 Azure 웹 사이트에서 기본적으로 사용하도록 설정되어 있으므로 PHP 코드를 실행하기 위해 특별한 조치를 취할 필요가 없습니다.
-   WebMatrix를 사용하여 PHP 응용 프로그램을 개발하는 방법
-   WebMatrix를 사용하여 응용 프로그램을 Azure에 게시 및 다시 게시하는 방법

이 자습서의 지침에 따라 PHP에서 간단한 작업 목록 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 Azure 웹 사이트에 호스트됩니다. 아래에는 실행 중인 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure PHP 웹 사이트][Azure PHP 웹 사이트]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## 필수 조건

1.  작업 목록 응용 프로그램 파일을 [다운로드][다운로드]합니다. 작업 목록 응용 프로그램은 작업 목록에서 항목을 추가, 완료 표시 및 삭제하는 데 사용할 수 있는 간단한 PHP 응용 프로그램입니다. 작업 목록 항목은 SQL 데이터베이스에 저장됩니다(로컬 테스트의 경우 SQL Server Express에 저장). 응용 프로그램은 다음 파일로 구성됩니다.

-   **index.php**: 작업을 표시하고 항목을 목록에 추가하는 데 필요한 양식을 제공합니다.
-   **additem.php**: 목록에 항목을 추가합니다.
-   **getitems.php**: 데이터베이스의 모든 항목을 가져옵니다.
-   **markitemcomplete.php**: 완료한 항목의 상태를 변경합니다.
-   **deleteitem.php**: 항목을 삭제합니다.
-   **taskmodel.php**: 데이터베이스의 항목에 대한 추가, 가져오기, 업데이트, 삭제 등의 기능을 포함하고 있습니다.
-   **createtable.php**: 응용 프로그램용 SQL 데이터베이스 테이블을 만듭니다. 이 파일은 한 번만 호출됩니다.

1.  `tasklist`라는 SQL Server 데이터베이스를 만듭니다. 이는 `sqlcmd` 명령 프롬프트에서 다음 명령으로 수행할 수 있습니다.

        >sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
        1> create database tasklist
        2> GO

    이 단계는 응용 프로그램을 로컬로 테스트하는 경우에만 필요합니다.

## 웹 사이트 및 SQL 데이터베이스 만들기

1.  [관리 포털][관리 포털]에 로그인합니다.
2.  포털의 왼쪽 아래에서 **+ 새로 만들기** 아이콘을 클릭합니다.

    ![새 Azure 웹 사이트 만들기][새 Azure 웹 사이트 만들기]

3.  **웹 사이트**를 클릭한 후 **사용자 지정 만들기**를 클릭합니다.

    ![새 웹 사이트 사용자 지정 만들기][새 웹 사이트 사용자 지정 만들기]

    **URL**에 대한 값을 입력하고 **데이터베이스** 드롭다운에서 **새 SQL 데이터베이스 만들기**를 선택한 후 **지역** 드롭다운에서 웹 사이트에 대한 데이터 센터를 선택합니다. 대화 상자 아래쪽의 화살표를 클릭합니다.

    ![웹 사이트 세부 정보 채우기][웹 사이트 세부 정보 채우기]

4.  데이터베이스의 **이름** 값을 입력하고 **새 SQL 데이터베이스 서버**를 선택합니다. 서버 로그인 이름과 암호를 입력하고 암호를 확인합니다. 새 SQL 데이터베이스 서버를 생성할 지역을 선택합니다.

    ![SQL 데이터베이스 설정 입력][SQL 데이터베이스 설정 입력]

    웹 사이트가 만들어지면 **웹 사이트 '[사이트 이름]'을(를) 만들었습니다.**라는 텍스트가 표시됩니다. 다음으로 데이터베이스 연결 정보를 가져옵니다.

5.  **LINKED RESOURCES**를 클릭하고 데이터베이스 이름을 클릭합니다.

    ![연결된 리소스][연결된 리소스]

6.  **연결 문자열 보기**를 클릭합니다.

    ![연결 문자열][연결 문자열]

나타나는 대화 상자의 **PHP** 섹션에서 `UID`, `PWD`, `Database` 및 `$serverName`의 값을 기록해 놓습니다. 이 정보는 이후에 사용할 예정입니다.

## WebMatrix 설치

[관리 포털][관리 포털]에서 WebMatrix를 설치할 수 있습니다.

1.  로그인한 후 웹 사이트의 빠른 시작 페이지로 이동하고 페이지 맨 아래에서 WebMatrix 아이콘을 클릭합니다.

    ![WebMatrix 설치][WebMatrix 설치]

    프롬프트에 따라 WebMatrix를 설치합니다.

2.  WebMatrix를 설치하면 사이트가 WebMatrix 프로젝트로 열립니다. 라이브 사이트를 직접 편집하거나 로컬 복사본을 다운로드하도록 선택할 수 있습니다. 이 자습서의 경우 'Edit local copy'를 선택합니다.

3.  사이트를 다운로드할지를 묻는 메시지가 나타나면 **Yes, install from the Template Gallery**를 선택합니다.

    ![웹 사이트 다운로드][웹 사이트 다운로드]

4.  사용할 수 있는 템플릿에서 **PHP**를 선택합니다.

    ![템플릿의 사이트][템플릿의 사이트]

5.  **Empty Site** 템플릿을 선택합니다. 사이트 이름을 지정하고 **다음**을 클릭합니다.

    ![사이트 이름 지정][사이트 이름 지정]

일부 기본값이 채워진 상태로 사이트가 WebMatrix에서 열립니다.

## 응용 프로그램 개발

다음 몇 단계에서는 이전에 다운로드한 파일을 추가하고 몇 가지를 수정하여 작업 목록 응용 프로그램을 개발합니다. 하지만 사용자 자신의 기존 파일을 추가하거나 새 파일을 만들 수 있습니다.

1.  사이트가 WebMatrix에서 열린 상태에서 **기존 항목 추가**를 클릭하여 응용 프로그램 파일을 추가합니다.

    ![WebMatrix - 기존 파일 추가][WebMatrix - 기존 파일 추가]

    나타나는 대화 상자에서 이전에 다운로드한 파일로 이동하고 파일을 모두 선택한 후 열기를 클릭합니다. 메시지가 표시되면 `index.php` 파일을 바꾸도록 선택합니다.

2.  다음으로, 로컬 SQL Server 데이터베이스 연결 정보를 `taskmodel.php` 파일에 추가해야 합니다. `taskmodel.php` 파일을 두 번 클릭하여 열고 `connect` 함수에서 데이터베이스 연결 정보를 업데이트합니다. (**참고**: 응용 프로그램을 로컬에서 테스트하지 않는 대신 Azure 웹 사이트에 직접 게시하려는 경우 [응용 프로그램 게시][응용 프로그램 게시]로 이동합니다.

        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "your user name";
        $pwd = "your password";
        $db = "tasklist";

    `taskmodel.php` 파일을 저장합니다.

3.  응용 프로그램이 실행할 수 있도록 `items` 테이블을 만들어야 합니다. `createtable.php` 파일을 마우스 오른쪽 단추로 클릭하고 **브라우저에서 시작**을 선택합니다. 그러면 브라우저에서 `createtable.php`가 시작되고 `tasklist` 데이터베이스에 `items` 테이블을 만드는 코드가 실행됩니다.

    ![WebMatrix - 브라우저에서 createtable.php 시작][WebMatrix - 브라우저에서 createtable.php 시작]

4.  이제 응용 프로그램을 로컬로 테스트할 수 있습니다. `index.php` 파일을 마우스 오른쪽 단추로 클릭하고 **브라우저에서 시작**을 선택합니다. 항목을 추가하고, 완료 상태로 표시하고, 삭제하는 등 응용 프로그램을 테스트합니다.

## <span id="Publish"></span></a>응용 프로그램 게시

응용 프로그램을 Azure 웹 사이트에 게시하기 전에 `taskmodel.php`의 데이터베이스 연결 정보를 이전에 [Azure 웹 사이트 및 SQL 데이터베이스 만들기][Azure 웹 사이트 및 SQL 데이터베이스 만들기] 섹션에서 확보한 연결 정보로 업데이트해야 합니다.

1.  `taskmodel.php` 파일을 두 번 클릭하여 열고 `connect` 함수에서 데이터베이스 연결 정보를 업데이트합니다.

        // DB connection info
        $host = "value of $serverName";
        $user = "value of UID";
        $pwd = "the SQL password you created when creating the website";
        $db = "value of Database";

    `taskmodel.php` 파일을 저장합니다.

2.  WebMatrix에서 **게시**를 클릭하고 **게시 미리 보기** 대화 상자에서 **계속**을 클릭합니다.

    ![WebMatrix - 게시][WebMatrix - 게시]

3.  http://[웹 사이트 이름].azurewebsites.net/createtable.php로 이동하여 `items` 테이블을 만듭니다.

4.  마지막으로 http://[웹 사이트 이름].azurewebsites.net/index.php로 이동하여 응용 프로그램을 시작합니다.

## 응용 프로그램 수정 및 다시 게시

이전에 다운로드한 사이트의 로컬 복사본을 편집하여 응용 프로그램을 쉽게 수정하고 다시 게시하거나, 원격 모드에서 직접 편집할 수 있습니다. 여기서는 간단히 `index.php` 파일의 제목을 변경하고 이를 라이브 사이트에 직접 저장하겠습니다.

1.  WebMatrix에서 원격 탭을 클릭하고 **Open Remote View**를 선택합니다. 그러면 직접 편집할 수 있는 원격 사이트가 열립니다.
     ![WebMatrix - 원격 뷰 열기][WebMatrix - 원격 뷰 열기]

2.  `index.php` 파일을 두 번 클릭하여 엽니다.
    ![WebMatrix - 인덱스 파일 열기][WebMatrix - 인덱스 파일 열기]

3.  **제목** 및 **h1** 태그에서 **My ToDo List**을 **My Task List**로 변경하고 파일을 저장합니다.

4.  저장이 완료되면 실행 단추를 클릭하여 라이브 사이트에서 변경 내용을 봅니다.
    ![WebMatrix - 원격으로 사이트 실행][WebMatrix - 원격으로 사이트 실행]

## 다음 단계

WebMatrix에서 웹 사이트를 만들어 Azure에 배포하는 방법을 학습했습니다. WebMatrix에 대한 자세한 내용은 다음 리소스를 참조하세요.

-   [Azure용 WebMatrix(영문)][Azure용 WebMatrix(영문)]

-   [WebMatrix 웹 사이트][WebMatrix 웹 사이트]

  [SQL Server Express]: http://www.microsoft.com/ko-KR/download/details.aspx?id=29062
  [Azure PHP 웹 사이트]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
  [다운로드]: http://go.microsoft.com/fwlink/?LinkId=252504
  [관리 포털]: https://manage.windowsazure.com
  [새 Azure 웹 사이트 만들기]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
  [새 웹 사이트 사용자 지정 만들기]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
  [웹 사이트 세부 정보 채우기]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
  [SQL 데이터베이스 설정 입력]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png
  [연결된 리소스]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
  [연결 문자열]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png
  [WebMatrix 설치]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
  [웹 사이트 다운로드]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
  [템플릿의 사이트]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
  [사이트 이름 지정]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
  [WebMatrix - 기존 파일 추가]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
  [응용 프로그램 게시]: #Publish
  [WebMatrix - 브라우저에서 createtable.php 시작]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
  [Azure 웹 사이트 및 SQL 데이터베이스 만들기]: #CreateWebsite
  [WebMatrix - 게시]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
  [WebMatrix - 원격 뷰 열기]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
  [WebMatrix - 인덱스 파일 열기]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
  [WebMatrix - 원격으로 사이트 실행]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png
  [Azure용 WebMatrix(영문)]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [WebMatrix 웹 사이트]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
