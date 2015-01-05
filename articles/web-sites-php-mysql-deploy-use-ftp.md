<properties urlDisplayName="Web w/ MySQL + FTP" pageTitle="MySQL 및 FTP를 사용한 PHP 웹 사이트 - Azure 자습서" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use FTP deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Website and Deploy Using FTP" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />


#PHP-MySQL Azure 웹 사이트 만들기 및 FTP를 사용하여 배포

이 자습서에서는 PHP-MySQL Azure 웹 사이트를 만들고 FTP를 사용하여 배포하는 방법을 설명합니다. 이 자습서의 내용은 컴퓨터에 [PHP][install-php], [MySQL][install-mysql], 웹 서버 및 FTP 클라이언트가 설치되어 있다는 것을 전제로 합니다. 이 자습서의 지침은 Windows, Mac 및 Linux를 포함하여 모든 운영 체제에 적용될 수 있습니다. 이 가이드를 완료하면 Azure에서 실행하는 PHP/MySQL 웹 사이트가 완성됩니다.
 
다음 내용을 배웁니다.

* Azure 관리 포털을 사용하여 Azure 웹 사이트 및 MySQL 데이터베이스를 만드는 방법. PHP는 Azure 웹 사이트에서 기본적으로 사용하도록 설정되어 있으므로 PHP 코드를 실행하기 위해 특별한 조치를 취할 필요가 없습니다.
* FTP를 사용하여 응용 프로그램을 Azure에 게시하는 방법
 
이 자습서의 지침에 따라 PHP에서 간단한 등록 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 Azure 웹 사이트에 호스트됩니다. 아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Azure 웹 사이트 만들기 및 FTP 게시 설정

Azure 웹 사이트 및 MySQL 데이터베이스를 만들려면 다음 단계를 따르세요.

1. [Azure 관리 포털][management-portal]에 로그인합니다.
2. 포털의 왼쪽 아래에서 **+ 새로 만들기** 아이콘을 클릭합니다.

	![Create New Azure Web Site][new-website]

3. **웹 사이트**를 클릭한 후 **사용자 지정 만들기**를 클릭합니다.

	![Custom Create a new Web Site][custom-create]
	
	**URL**에 대한 값을 입력하고 **데이터베이스** 드롭다운에서 **새 MySQL 데이터베이스 만들기**를 선택한 후 **지역** 드롭다운에서 웹 사이트에 대한 데이터 센터를 선택합니다. 대화 상자 아래쪽의 화살표를 클릭합니다.

	![Fill in Web Site details][website-details]

4. 데이터베이스의 **이름**에 값을 입력하고 **지역** 드롭다운에서 데이터베이스에 대한 데이터 센터를 선택한 후 약관에 동의함을 나타내는 확인란을 선택합니다. 대화 상자 맨 아래에 있는 확인 표시를 클릭합니다.

	![Create new MySQL database][new-mysql-db]

	웹 사이트가 만들어지면 **'[SITENAME]' 웹 사이트 만들기가 완료되었습니다.**라는 텍스트가 표시됩니다. 이제 FTP 게시를 사용하도록 설정할 수 있습니다.

5. 웹 사이트 목록에 표시된 웹 사이트 이름을 클릭하여 웹 사이트의 **퀵 스타트** 대시보드를 엽니다.

	![Open web site dashboard][go-to-dashboard]


6. **퀵 스타트** 페이지 맨 아래에 있는 **배포 자격 증명 재설정**을 클릭합니다. 

	![Reset deployment credentials][reset-deployment-credentials]

7. FTP 게시를 사용하도록 설정하려면 사용자 이름 및 암호를 지정해야 합니다. 만든 사용자 이름 및 암호를 기록해 둡니다.

	![Create publishing credentials][portal-git-username-password]

##로컬에서 응용 프로그램 빌드 및 테스트

등록 응용 프로그램은 이름과 메일 주소를 지정하여 이벤트에 등록하는 데 사용할 수 있는 간단한 PHP 응용 프로그램입니다. 이전 등록자에 대한 정보가 테이블에 표시되어 있습니다. 등록 정보는 MySQL 데이터베이스에 저장되어 있습니다. 응용 프로그램은 다음 두 파일로 구성되어 있습니다.

* **index.php**: 등록 양식 및 등록자 정보가 포함된 테이블을 표시합니다.
* **createtable.php**: 응용 프로그램용 MySQL 테이블을 만듭니다. 이 파일은 한 번만 사용됩니다.

응용 프로그램을 빌드하여 로컬에서 실행하려면 아래 단계를 따릅니다. 이러한 단계는 로컬 컴퓨터에 PHP, MySQL 및 웹 서버가 설정되어 있으며 [PDO extension for MySQL][pdo-mysql]이 사용되도록 설정되어 있다는 것을 전제로 합니다.

1. `registration`이라는 MySQL 데이터베이스를 만듭니다. 이는 MySQL 명령 프롬프트에서 다음 명령으로 수행할 수 있습니다.

		mysql> create database registration;

2. 웹 서버의 루트 디렉터리에서 'registration'이라는 폴더를 만들고 이 폴더 내에 'createtable.php' 및 'index.php'라는 두 파일을 만듭니다.

3. 텍스트 편집기 또는 IDE에서 'createtable.php' 파일을 열고 아래 코드를 추가합니다. 이 코드는 'registration' 데이터베이스에 'registration_tbl' 테이블을 만드는 데 사용됩니다.

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
						id INT NOT NULL AUTO_INCREMENT, 
						PRIMARY KEY(id),
						name VARCHAR(30),
						email VARCHAR(30),
						date DATE)";
			$conn->query($sql);
		}
		catch(Exception $e){
			die(print_r($e));
		}
		echo "<h3>Table created.</h3>";
		?>

	> [WACOM.NOTE] 
	> 다시 <code>$user</code> 및 <code>$pwd</code>의 값을 로컬 MySQL 사용자 이름 및 암호로 업데이트해야 합니다.

4. 웹 브라우저를 열고 [http://localhost/registration/createtable.php][localhost-createtable]로 이동합니다. 그러면 데이터베이스에 'registration_tbl' 테이블이 만들어집니다.

5. 텍스트 편집기 또는 IDE에서 **index.php** 파일을 열고 페이지의 기본 HTML 및 CSS 코드를 추가합니다(PHP 코드는 이후 단계에서 추가 예정).

		<html>
		<head>
		<Title>Registration Form</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php

		?>
		</body>
		</html>

6. PHP 태그 내에서 add PHP code for connecting to the database.

		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [WACOM.NOTE]
	> 다시 <code>$user</code> 및 <code>$pwd</code>의 값을 로컬 MySQL 사용자 이름 및 암호로 업데이트해야 합니다.

7. 데이터베이스 연결 코드 다음에 등록 정보를 데이터베이스에 삽입하는 데 필요한 코드를 추가합니다.

		if(!empty($_POST)) {
		try {
			$name = $_POST['name'];
			$email = $_POST['email'];
			$date = date("Y-m-d");
			// Insert data
			$sql_insert = "INSERT INTO registration_tbl (name, email, date) 
						   VALUES (?,?,?)";
			$stmt = $conn->prepare($sql_insert);
			$stmt->bindValue(1, $name);
			$stmt->bindValue(2, $email);
			$stmt->bindValue(3, $date);
			$stmt->execute();
		}
		catch(Exception $e) {
			die(var_dump($e));
		}
		echo "<h3>Your're registered!</h3>";
		}

8. 마지막으로 위 코드 다음에 데이터베이스에서 데이터 검색에 필요한 코드를 추가합니다.

		$sql_select = "SELECT * FROM registration_tbl";
		$stmt = $conn->query($sql_select);
		$registrants = $stmt->fetchAll(); 
		if(count($registrants) > 0) {
			echo "<h2>People who are registered:</h2>";
			echo "<table>";
			echo "<tr><th>Name</th>";
			echo "<th>Email</th>";
			echo "<th>Date</th></tr>";
			foreach($registrants as $registrant) {
				echo "<tr><td>".$registrant['name']."</td>";
				echo "<td>".$registrant['email']."</td>";
				echo "<td>".$registrant['date']."</td></tr>";
		    }
		 	echo "</table>";
		} else {
			echo "<h3>No one is currently registered.</h3>";
		}

이제 [http://localhost/registration/index.php][localhost-index]로 이동하여 응용 프로그램을 테스트할 수 있습니다.

##MySQL 및 FTP 연결 정보 가져오기

Azure 웹 사이트에서 실행되는 MySQL 데이터베이스에 연결하려면 연결 정보가 필요합니다. MySQL 연결 정보를 가져오려면 다음 단계를 따르세요.

1. 웹 사이트의 대시보드에서 페이지의 오른쪽에 있는 **연결 문자열 보기** 링크를 클릭합니다.

	![Get database connection information][connection-string-info]
	
2. `Database`, `Data Source`, `User Id` 및 `Password`의 값을 기록해 둡니다.

3. 웹 사이트의 대시보드에서 페이지의 오른쪽 맨 아래에 있는 **게시 프로필 다운로드** 링크를 클릭합니다.

	![Download publish profile][download-publish-profile]

4. XML 편집기에서 '.publishsettings' 파일을 엽니다. 

3. 다음과 유사한 `publishMethod="FTP"`가 있는 `<publishProfile >` 요소를 찾습니다.

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
`publishUrl`, `userName` 및 `userPWD` 특성을 기록해 놓습니다.

##응용 프로그램 게시

응용 프로그램을 로컬에서 테스트한 후 FTP를 사용하여 Azure 웹 사이트에 게시할 수 있습니다. 하지만 먼저 응용 프로그램의 데이터베이스 연결 정보를 업데이트해야 합니다. 이전에 **MySQL 및 FTP 연결 정보 가져오기** 섹션에서 가져온 데이터베이스 연결 정보를 사용하여 `createdatabase.php` 및 `index.php` 파일 **둘 다**에서 다음 정보를 적합한 값으로 업데이트합니다.

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

이제 FTP를 사용하여 응용 프로그램을 게시할 준비가 완료되었습니다.

1. 원하는 FTP 클라이언트를 엽니다.

2. 앞서 기록한 'publishUrl' 특성의 호스트 이름 부분을 FTP 클라이언트에 입력합니다.

3. FTP 클라이언트를 변경하지 않고 앞서 기록한 'userName' 및 'userPWD' 특성을 입력합니다.

4. 연결을 설정합니다.

연결된 후 필요에 따라 파일을 업로드 및 다운로드할 수 있습니다. 루트 디렉터리 '/site/wwwroot'에 파일을 업로드하고 있는지 확인하세요.

`index.php` 및 `createtable.php`를 모두 업로드한 후 **http://[사이트 이름].azurewebsites.net/createtable.php**로 이동하여 응용 프로그램용 MySQL 테이블을 만든 다음 **http://[사이트 이름].azurewebsites.net/index.php**로 이동하여 응용 프로그램을 사용하기 시작합니다.
 

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg
[custom-create]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png
[reset-deployment-credentials]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png
[portal-git-username-password]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png


[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[download-publish-profile]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png

<!--HONumber=35.1-->
