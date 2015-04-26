<properties 
	pageTitle="SQL 데이터베이스 및 Git를 사용한 PHP 웹 사이트 - Azure 자습서" 
	description="SQL 데이터베이스에 데이터를 저장하는 PHP 웹 사이트를 만들고 Git를 사용하여 Azure에 배포하는 방법을 보여 주는 자습서입니다." 
	services="web-sites, sql-database" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="tomfitz"/>

#SQL 데이터베이스를 사용하여 PHP 웹 사이트 만들기 및 Git를 사용하여 배포

이 자습서에서는 Azure SQL 데이터베이스를 사용하여 PHP Azure 웹 사이트를 만들고 Git를 사용하여 배포하는 방법을 설명합니다. 이 자습서는 컴퓨터에 [PHP][install-php], [SQL Server Express][install-SQLExpress], [PHP용 SQL Server를 위한 Microsoft 드라이버][install-drivers], 웹 서버 및 [Git][install-git]가 설치되어 있다고 가정합니다. 이 가이드를 완료하면 Azure에서 실행하는 PHP-SQL 데이터베이스 웹 사이트가 완성됩니다.

> [AZURE.NOTE]
> PHP, SQL Server Express, PHP용 SQL Server를 위한 Microsoft 드라이버 및 IIS(인터넷 정보 서비스)는 <a href="http://www.microsoft.com/web/downloads/platform.aspx">Microsoft 웹 플랫폼 설치 관리자</a>를 사용하여 설치하고 구성할 수 있습니다.

다음 내용을 배웁니다.

* Azure 관리 포털을 사용하여 Azure 웹 사이트 및 SQL 데이터베이스를 만드는 방법. PHP는 Azure 웹 사이트에서 기본적으로 사용하도록 설정되어 있으므로 PHP 코드를 실행하기 위해 특별한 조치를 취할 필요가 없습니다.
* Git를 사용하여 응용 프로그램을 Azure에 게시 및 다시 게시하는 방법
 
이 자습서의 지침에 따라 PHP에서 간단한 등록 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 Azure 웹 사이트에 호스트됩니다. 아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure PHP Web Site][running-app]

> [AZURE.NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">MSDN 구독자 혜택을 활성화</a>하거나 <a href="http://azure.microsoft.com/pricing/free-trial/">무료 평가판을 등록</a>할 수 있습니다.
> 
> 계정을 등록하기 전에 Azure 웹 사이트를 시작하려면 <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>으로 이동합니다. Azure 웹 사이트에서는 무료로 단기 ASP.NET 시작 사이트를 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

##Azure 웹 사이트 만들기 및 Git 게시 설정

Azure 웹 사이트 및 SQL 데이터베이스를 만들려면 다음 단계를 따르세요.

1. [Azure 관리 포털][management-portal]에 로그인합니다.
2. 포털의 왼쪽 아래에서 **새로 만들기** 아이콘을 클릭합니다.
![Create New Azure Web Site][new-website]

3. **웹 사이트**를 클릭한 후 **사용자 지정 만들기**를 클릭합니다.

	![Custom Create a new Web Site][custom-create]

	**URL** 값을 입력하고 **데이터베이스** 드롭다운에서 **새 SQL 데이터베이스 만들기**를 선택한 후 **소스 제어에서 게시**를 선택합니다. 대화 상자 아래쪽의 화살표를 클릭합니다.

	![Fill in web site details][website-details-sqlazure]

4. 데이터베이스의 **이름** 값을 입력하고 **새 SQL 데이터베이스 서버**를 선택한 후 로그인 자격 증명을 제공하고 지역을 선택합니다. 대화 상자 아래쪽의 화살표를 클릭합니다.

	![Fill in SQL Database settings][database-settings]

5. 소스 코드에 대해 **로컬 Git 리포지토리**를 선택합니다.

	![where is your source code][where-is-code]

	이전에 Git 리포지토리를 설정하지 않았으면 사용자 이름과 암호를 제공해야 합니다.

6. 웹 사이트가 만들어지면 사이트의 대시보드를 열고 **배포 보기**를 선택합니다.

	![Web site dashboard][go-to-dashboard]

9. 응용 프로그램 파일을 리포지토리에 푸시하는 지침이 표시됩니다. 이러한 지침은 나중에 필요하므로 기록해 둡니다.

	![Git instructions][git-instructions]

##SQL 데이터베이스 연결 정보 가져오기

Azure 웹 사이트에서 실행되는 SQL 데이터베이스 인스턴스에 연결하려면 연결 정보가 필요합니다. SQL 데이터베이스 연결 정보를 가져오려면 다음 단계를 따르세요.

1. Azure 관리 포털에서 **연결된 리소스**를 클릭한 후 데이터베이스 이름을 클릭합니다.

	![Linked Resources][linked-resources]

2. **연결 문자열 보기**를 클릭합니다.

	![Connection string][connection-string]
	
3. 나타나는 대화 상자의 **PHP** 섹션에서  `SERVER`, `DATABASE` 및  `USERNAME`의 값을 기록해 놓습니다.

##로컬에서 응용 프로그램 빌드 및 테스트

등록 응용 프로그램은 이름과 메일 주소를 지정하여 이벤트에 등록하는 데 사용할 수 있는 간단한 PHP 응용 프로그램입니다. 이전 등록자에 대한 정보가 테이블에 표시되어 있습니다. 등록 정보는 SQL 데이터베이스 인스턴스에 저장되어 있습니다. 응용 프로그램은 다음과 같은 두 파일로 구성되어 있습니다(아래에서 사용할 수 있는 코드 복사/붙여넣기).

* **index.php**: 등록 양식 및 등록자 정보가 포함된 테이블을 표시합니다.
* **createtable.php**: 응용 프로그램용 SQL 데이터베이스 테이블을 만듭니다. 이 파일은 한 번만 사용됩니다.

응용 프로그램을 로컬에서 실행하려면 아래 단계를 따릅니다. 이러한 단계는 로컬 컴퓨터에 PHP, SQL Server Express 및 웹 서버가 설정되어 있으며 [SQL Server용 PDO 확장][pdo-sqlsrv]이 사용하도록 설정되어 있다는 것을 전제로 합니다.

1.  `registration`이라는 SQL Server 데이터베이스를 만듭니다. 이는  `sqlcmd` 명령 프롬프트에서 다음 명령으로 수행할 수 있습니다.

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. 웹 서버의 루트 디렉터리에서  `registration`이라는 폴더를 만들고 이 폴더 내에  `createtable.php` 및  `index.php`라는 두 파일을 만듭니다.

3. 텍스트 편집기 또는 IDE에서  `createtable.php` 파일을 열고 아래 코드를 추가합니다. 이 코드는  `registration` 데이터베이스에  `registration_tbl` 테이블을 만드는 데 사용됩니다.

		<?php
		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
			id INT NOT NULL IDENTITY(1,1) 
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

	이제 <code>$user</code> 및 <code>$pwd</code> 값을 로컬 SQL Server 사용자 이름 및 암호로 업데이트해야 합니다.

4. 웹 브라우저를 열고 **http://localhost/registration/createtable.php**으로 이동합니다. 그러면 데이터베이스에  `registration_tbl` 테이블이 만들어집니다.

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

6. PHP 태그 내에서 데이터베이스 연결에 필요한 PHP 코드를 추가합니다.

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

    다시 <code>$user</code> 및 <code>$pwd</code> 값을 로컬 MySQL 사용자 이름 및 암호로 업데이트해야 합니다.

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

이제 **http://localhost/registration/index.php**로 이동하여 응용 프로그램을 테스트할 수 있습니다.

##응용 프로그램 게시

응용 프로그램을 로컬에서 테스트한 후 Git를 사용하여 Azure 웹 사이트에 게시할 수 있습니다. 하지만 먼저 응용 프로그램의 데이터베이스 연결 정보를 업데이트해야 합니다. 이전에 **SQL 데이터베이스 연결 정보 가져오기** 섹션에서 가져온 데이터베이스 연결 정보를 사용하여  `createdatabase.php` 및  `index.php` 파일 **둘 다**에서 다음 정보를 적합한 값으로 업데이트합니다.

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [AZURE.NOTE]
> 그런 다음 <code>$host</code>에서 SERVER 값 앞에 <code>tcp:</code>를 붙여야 하며 <code>$user</code> 값은 USERNAME, '@' 및 서버 ID가 연결되어 형성됩니다. 서버 ID는 SERVER 값의 처음 10개 문자입니다.


이제 Git 게시를 설정하고 응용 프로그램을 게시할 수 있습니다.

> [AZURE.NOTE]
> 이러한 단계는 앞서 "Azure 웹 사이트 만들기 및 Git 게시 설정" 섹션의 끝에서 설명한 단계와 동일합니다.


1. GitBash를 열거나 Git가  `PATH`에 있는 경우 터미널을 열고 응용 프로그램의 루트 디렉터리로 이동한 후 다음 명령을 실행합니다.

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다.

2. **http://[사이트 이름].azurewebsites.net/createtable.php**로 이동하여 응용 프로그램용 MySQL 테이블을 만듭니다.
3. **http://[사이트 이름].azurewebsites.net/index.php**로 이동하여 응용 프로그램을 사용합니다.

응용 프로그램을 게시한 후 변경을 시작하고 Git를 사용하여 변경 내용을 게시할 수 있습니다. 

##응용 프로그램의 변경 내용 게시

응용 프로그램에 변경 내용을 게시하려면 다음 단계를 따르세요.

1. 응용 프로그램을 로컬에서 변경합니다.
2. GitBash를 열거나 Git가  `PATH`에 있는 경우 터미널을 열고 응용 프로그램의 루트 디렉터리로 이동한 후 다음 명령을 실행합니다.

		git add .
		git commit -m "comment describing changes"
		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다.

3. **http://[사이트 이름].azurewebsites.net/index.php**로 이동하여 변경 내용을 확인합니다.

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/ko-kr/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/ko-kr/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
[running-app]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
[new-website]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/createphpgitsite.png
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/setupdb.png
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/viewdeploy.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/gitsettings.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/setupgit.png



<!--HONumber=42-->
