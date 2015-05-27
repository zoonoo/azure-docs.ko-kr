<properties 
	pageTitle="WebMatrix를 사용하여 Azure 앱 서비스에서 PHP-MySQL 웹앱 만들기 및 배포" 
	description="무료 WebMatrix IDE를 사용하여 MySQL에 데이터를 저장하는 Azure 앱 서비스의 PHP 웹앱을 만들고 배포하는 방법을 보여 주는 자습서입니다."
	tags="azure-portal" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="tomfitz"/>





# WebMatrix를 사용하여 Azure 앱 서비스에서 PHP-MySQL 웹앱 만들기 및 배포

이 자습서에서는 WebMatrix를 사용하여 PHP-MySQL 응용 프로그램을 개발하고 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹앱에 배포하는 방법을 보여 줍니다. WebMatrix는 웹 사이트 개발에 필요한 모든 기능이 포함된 Microsoft의 무료 웹 개발 도구입니다. WebMatrix는 PHP를 지원하며 PHP 개발에 필요한 IntelliSense를 포함하고 있습니다.

이 자습서의 내용은 컴퓨터에 [MySQL][install-mysql]이 설치되어 있어서 로컬로 응용 프로그램을 테스트할 수 있다는 것을 전제로 합니다. 하지만 MySQL을 설치하지 않은 상태로 자습서를 완료할 수 있습니다. 대신 Azure 앱 서비스 웹앱에 응용 프로그램을 직접 배포할 수 있습니다.

이 가이드를 완료하면 웹앱에서 실행되는 PHP-MySQL 웹 사이트가 완성됩니다.
 
다음 내용을 배웁니다.

* [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)을 사용하여 앱 서비스 웹앱 및 MySQL 데이터베이스를 만드는 방법. PHP는 웹 앱에서 기본적으로 사용하도록 설정되어 있으므로 PHP 코드를 실행하기 위해 특별한 조치를 취할 필요가 없습니다.
* WebMatrix를 사용하여 PHP 응용 프로그램을 개발하는 방법
* WebMatrix를 사용하여 응용 프로그램을 웹앱에 게시 및 다시 게시하는 방법
 
이 자습서의 지침에 따라 PHP에서 간단한 작업 목록 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 앱 서비스 웹앱에서 호스트됩니다. 아래에는 실행 중인 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure PHP 웹 사이트][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 시작 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

##필수 조건

1. 작업 목록 응용 프로그램 파일을 [다운로드][tasklist-mysql-download]합니다. 작업 목록 응용 프로그램은 작업 목록에서 항목을 추가, 완료 표시 및 삭제하는 데 사용할 수 있는 간단한 PHP 응용 프로그램입니다. 작업 목록 항목은 MySQL 데이터베이스에 저장됩니다. 응용 프로그램은 다음 파일로 구성됩니다.

	* **additem.php**: 목록에 항목을 추가합니다.
	* **createtable.php**: 응용 프로그램에 대한 MySQL 테이블을 만듭니다. 이 파일은 한 번만 호출됩니다.
	* **deleteitem.php**: 항목을 삭제합니다.
	* **getitems.php**: 데이터베이스의 모든 항목을 가져옵니다.
	* **index.php**: 작업을 표시하고 항목을 목록에 추가하는 데 필요한 양식을 제공합니다.
	* **markitemcomplete.php**: 완료할 항목의 상태를 변경합니다.
	* **taskmodel.php**: 데이터베이스의 항목에 대한 추가, 가져오기, 업데이트, 삭제 등의 기능을 포함하고 있습니다.

1. `tasklist`라는 로컬 MySQL 데이터베이스를 만듭니다. 이는 WebMatrix(자습서 아래에 설치되어 있어야 함)의 데이터베이스 작업 영역 또는 MySQL 명령 프롬프트에서 다음 명령으로 수행할 수 있습니다.

		mysql> create database tasklist;

	이 단계는 응용 프로그램을 로컬로 테스트하는 경우에만 필요합니다.

## 웹 앱 및 MySQL 데이터베이스 만들기

웹 앱 및 MySQL 데이터베이스를 만들려면 다음 단계를 따르세요.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 포털의 왼쪽 아래에서 **새로 만들기** 아이콘을 클릭합니다.

	![새 Azure 웹 사이트 만들기](./media/web-sites-php-mysql-use-webmatrix/new_website2.png)

3. **웹 + 모바일**, **웹앱 + MySQL**을 차례로 클릭합니다.

	![새 웹 사이트 사용자 지정 만들기](./media/web-sites-php-mysql-use-webmatrix/create_web_mysql.png)

4. 리소스 그룹의 올바른 이름을 입력합니다.

    ![리소스 그룹 이름 설정](./media/web-sites-php-mysql-use-webmatrix/set_group.png)

5. 새 웹 앱의 값을 입력합니다.

    ![웹앱 만들기](./media/web-sites-php-mysql-use-webmatrix/create_wa.png)

6. 약관에 대한 동의를 포함하여 새 데이터베이스의 값을 입력합니다.

	![새 MySQL 데이터베이스 만들기](./media/web-sites-php-mysql-use-webmatrix/create_db.png)

	웹 앱이 만들어지면 새 리소스 그룹이 보입니다.

## 원격 MySQL 연결 정보 가져오기

웹 앱에서 실행되는 MySQL 데이터베이스에 연결하려면 연결 정보가 필요합니다. MySQL 연결 정보를 가져오려면 다음 단계를 따르십시오.

1. 리소스 그룹에서 다음 데이터베이스를 클릭합니다.

	![데이터베이스 선택](./media/web-sites-php-mysql-use-webmatrix/select_database.png)

2. 데이터베이스 요약에서 **속성**을 선택합니다.

    ![속성 선택](./media/web-sites-php-mysql-use-webmatrix/select_properties.png)

2. `Database`, `Host`, `User Id` 및 `Password` 값을 기록해 둡니다.

    ![참고 속성](./media/web-sites-php-mysql-use-webmatrix/note-properties.png)

## WebMatrix에서 응용 프로그램 만들기

다음 몇 단계에서는 이전에 다운로드한 파일을 추가하고 몇 가지를 수정하여 작업 목록 응용 프로그램을 개발합니다. 하지만 사용자 자신의 기존 파일을 추가하거나 새 파일을 만들 수 있습니다.

1. [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/)를 시작합니다. 아직 설치하지 않은 경우 지금 설치합니다.
2. 이번에 처음으로 WebMatrix 3을 사용하는 경우 Azure에 로그인하라는 메시지가 표시됩니다. 또는 **로그인** 단추를 클릭하고 **계정 추가**를 선택할 수 있습니다. Microsoft 계정을 사용하여 **로그인**하도록 선택합니다.

	![계정 추가](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. Azure 계정을 등록한 경우 Microsoft 계정을 사용하여 로그인할 수 있습니다.

	![Azure에 로그인](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. 시작 화면에서 **새로 만들기** 단추를 클릭하고 **템플릿 갤러리**를 선택하여 템플릿 갤러리에서 새 사이트를 만듭니다.

	![템플릿 갤러리에서 새 사이트 만들기](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)

4. 사용할 수 있는 템플릿에서 **PHP**를 선택합니다.

	![템플릿의 사이트][site-from-template]

5. **Empty Site** 템플릿을 선택합니다. 사이트 이름을 지정하고 **다음**을 클릭합니다.

	![사이트 이름 지정][site-from-template-2]

	일부 기본값이 채워진 상태로 사이트가 WebMatrix에서 열립니다.

	다음 몇 단계에서는 이전에 다운로드한 파일을 추가하고 몇 가지를 수정하여 작업 목록 응용 프로그램을 개발합니다. 하지만 사용자 자신의 기존 파일을 추가하거나 새 파일을 만들 수 있습니다.

6. **기존 항목 추가**를 클릭하여 응용 프로그램 파일을 추가합니다.

	![WebMatrix - 기존 파일 추가][edit_addexisting]

	나타나는 대화 상자에서 이전에 다운로드한 파일로 이동하고 파일을 모두 선택한 후 열기를 클릭합니다. 메시지가 표시되면 `index.php` 파일을 바꾸도록 선택합니다.

7. 이제 로컬 MySQL 데이터베이스 연결 정보를 `taskmodel.php` 파일에 추가해야 합니다. `taskmodel.php` 파일을 두 번 클릭하여 열고 `connect` 함수에서 데이터베이스 연결 정보를 업데이트합니다. **참고**: 응용 프로그램을 로컬로 테스트하지 않고 대신 Azure 앱 서비스 웹앱에 직접 게시하려면 [응용 프로그램 게시](#Publish)로 바로 이동합니다.

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	`taskmodel.php` 파일을 저장합니다.

8. 응용 프로그램을 실행하려면 `items` 테이블을 만들어야 합니다. `createtable.php` 파일을 마우스 오른쪽 단추로 클릭하고 **브라우저에서 시작**을 선택합니다. 그러면 브라우저에서 `createtable.php`가 시작되고 `tasklist` 데이터베이스에 `items` 테이블을 만드는 코드가 실행됩니다.

	![WebMatrix - 브라우저에서 createtable.php 시작][edit_run]

9. 이제 응용 프로그램을 로컬로 테스트할 수 있습니다. `index.php` 파일을 마우스 오른쪽 단추로 클릭하고 **브라우저에서 시작**을 선택합니다. 항목을 추가하고, 완료 상태로 표시하고, 삭제하는 등 응용 프로그램을 테스트합니다.


## 응용 프로그램 게시

응용 프로그램을 앱 서비스 웹앱에 게시하려면 `taskmodel.php`의 데이터베이스 연결 정보를 이전에 [웹앱 및 MySQL 데이터베이스 만들기](#CreateWebsite) 섹션에서 확보한 연결 정보로 업데이트해야 합니다.

1. `taskmodel.php` 파일을 두 번 클릭하여 열고 `connect` 함수에서 데이터베이스 연결 정보를 업데이트합니다.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	`taskmodel.php` 파일을 저장합니다.

2. WebMatrix에서 **게시**를 클릭합니다.

	![WebMatrix - 게시][edit_publish]

3. **Microsoft Azure에서 기존 사이트 선택**을 클릭합니다.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site.png)

3. 앞에서 만든 앱 서비스 웹앱을 선택합니다.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. WebMatrix에서 Azure 앱 서비스 웹앱에 사이트를 게시할 때까지 계속해서 **계속**을 클릭합니다.

3. http://[your 웹 사이트 이름].azurewebsites.net/createtable.php로 이동하여 `items` 테이블을 만듭니다.

4. 마지막으로 http://[your 웹 사이트 이름].azurewebsites.net/index.php로 이동하여 응용 프로그램을 사용합니다.
	
##응용 프로그램 수정 및 다시 게시

이전에 다운로드한 사이트의 로컬 복사본을 편집하여 응용 프로그램을 쉽게 수정하고 다시 게시하거나, 원격 모드에서 직접 편집할 수 있습니다. 여기서는 간단히 `index.php` 파일의 제목을 변경하고 이를 라이브 사이트에 직접 저장하겠습니다.

1. WebMatrix에서 원격 탭을 클릭하고 **Open Remote View**를 선택합니다. 그러면 직접 편집할 수 있는 원격 사이트가 열립니다. ![WebMatrix - 원격 뷰 열기][OpenRemoteView]
 
2. `index.php` 파일을 두 번 클릭하여 엽니다. ![WebMatrix - index 파일 열기][Remote_editIndex]

3. **제목** 및 **h1** 태그에서 **My ToDo List**을 **My Task List**로 변경하고 파일을 저장합니다.


4. 저장이 완료되면 실행 단추를 클릭하여 라이브 사이트에서 변경 내용을 확인합니다. ![WebMatrix - 원격에서 사이트 시작][Remote_run]


## 다음 단계

* [WebMatrix 웹 사이트](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.




[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com














<!--HONumber=54-->