<properties
	pageTitle="Azure 웹 앱 서비스에서 PHP-MySQL 웹 앱 만들기 및 Git를 사용하여 배포"
	description="MySQL에 데이터를 저장하는 PHP 웹앱을 만들고 Git를 사용하여 Azure에 배포하는 방법을 보여 주는 자습서입니다."
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor="mollybos"
	tags="mysql"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="08/03/2015"
	ms.author="tomfitz"/>

#Azure 웹 앱 서비스에서 PHP-MySQL 웹 앱 만들기 및 Git를 사용하여 배포

이 자습서에서는 PHP-MySQL 웹앱을 만들고 Git를 사용하여 [앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포하는 방법을 보여 줍니다. 컴퓨터에 설치된 [PHP][install-php], MySQL 명령줄 도구([MySQL][install-mysql]의 일부), 웹 서버 및 [Git][install-git]를 사용합니다. 이 자습서의 지침은 Windows, Mac 및 Linux를 포함하여 모든 운영 체제에 적용될 수 있습니다. 이 가이드를 완료하면 Azure에서 실행하는 PHP/MySQL 웹 앱이 완성됩니다.

다음 내용을 배웁니다.

* [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)을 사용하여 웹앱 및 MySQL 데이터베이스를 만드는 방법. PHP는 [앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)에서 기본적으로 사용하도록 설정되므로 PHP 코드를 실행하기 위해 특별한 조치를 취할 필요가 없습니다.
* Git를 사용하여 응용 프로그램을 Azure에 게시 및 다시 게시하는 방법

이 자습서의 지침에 따라 PHP에서 간단한 등록 웹 앱을 빌드할 수 있습니다. 응용 프로그램은 Azure 웹 앱에 호스트됩니다. 아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure PHP 웹 사이트][running-app]

##개발 환경 설정

이 자습서의 내용은 컴퓨터에 [PHP][install-php], MySQL 명령줄 도구([MySQL][install-mysql]의 일부), 웹 서버 및 [Git][install-git]가 설치되어 있다는 것을 전제로 합니다.

> [AZURE.NOTE]Windows에서 이 자습서의 내용을 수행하는 경우 <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">PHP용 Azure SDK</a>를 설치하여 PHP용 컴퓨터를 설정하고 자동으로 IIS(Windows에서 기본 제공되는 웹 서버)를 구성할 수 있습니다.

##<a id="create-web-site-and-set-up-git"></a>웹앱 만들기 및 Git 게시 설정

웹 앱 및 MySQL 데이터베이스를 만들려면 다음 단계를 따르세요.

1. [Azure 포털][management-portal]에 로그인합니다.
2. **새로 만들기** 아이콘을 클릭합니다.

3. **웹 + 모바일**, **Azure 마켓플레이스**를 차례로 클릭합니다.

4. **웹앱**, **웹앱 + MySQL**을 클릭합니다. 그런 다음에 **만들기**를 클릭합니다.

4. 리소스 그룹의 올바른 이름을 입력합니다.

    ![리소스 그룹 이름 설정][resource-group]

5. 새 웹 앱의 값을 입력합니다.

    ![웹앱 만들기][new-web-app]

6. 약관에 대한 동의를 포함하여 새 데이터베이스의 값을 입력합니다.

	![새 MySQL 데이터베이스 만들기][new-mysql-db]

7. 웹 앱이 만들어지면 새 리소스 그룹이 보입니다. 웹 앱의 이름을 클릭하여 해당 설정을 구성합니다.

7. **연속 배포 설정**을 클릭합니다.

	![Git 게시 설정][setup-publishing]

8. 원본에 대한 **로컬 Git 리포지토리**를 선택합니다.

    ![Git 리포지토리 설정][setup-repository]


9. Git 게시를 사용하도록 설정하려면 사용자 이름 및 암호를 지정해야 합니다. 만든 사용자 이름 및 암호를 기록해 둡니다. 이전에 Git 리포지토리를 설정한 경우 이 단계를 건너뜁니다.

	![게시 자격 증명 만들기][credentials]


##원격 MySQL 연결 정보 가져오기

웹 앱에서 실행되는 MySQL 데이터베이스에 연결하려면 연결 정보가 필요합니다. MySQL 연결 정보를 가져오려면 다음 단계를 따르십시오.

1. 리소스 그룹에서 다음 데이터베이스를 클릭합니다.

	![데이터베이스 선택][select-database]

2. 데이터베이스 요약에서 **속성**을 선택합니다.

    ![속성 선택][select-properties]

2. `Database`, `Host`, `User Id` 및 `Password` 값을 기록해 둡니다.

    ![참고 속성][note-properties]

##로컬에서 앱 빌드 및 테스트

웹 앱을 만들었으므로 로컬에서 응용 프로그램을 개발하여 테스트 후 배포할 수 있습니다.

등록 응용 프로그램은 이름과 전자 메일 주소를 지정하여 이벤트에 등록하는 데 사용할 수 있는 간단한 PHP 응용 프로그램입니다. 이전 등록자에 대한 정보가 테이블에 표시되어 있습니다. 등록 정보는 MySQL 데이터베이스에 저장되어 있습니다. 응용 프로그램은 다음 한 파일로 구성되어 있습니다(아래에서 사용할 수 있는 코드 복사/붙여넣기).

* **index.php**: 등록 양식 및 등록자 정보가 포함된 테이블을 표시합니다.

응용 프로그램을 빌드하여 로컬에서 실행하려면 아래 단계를 따릅니다. 이러한 단계는 로컬 컴퓨터에 PHP, MySQL 명령줄 도구(MySQL의 일부) 및 웹 서버가 설정되어 있으며 [PDO Extension for MySQL][pdo-mysql]이 사용하도록 설정되어 있다는 것을 전제로 합니다.

1. 이전에 검색한 `Data Source`, `User Id`, `Password` 및 `Database` 값을 사용하여 원격 MySQL 서버에 연결합니다.

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. MySQL 명령 프롬프트가 나타납니다.

		mysql>

3. 다음 `CREATE TABLE` 명령을 붙여 넣어 데이터베이스에 `registration_tbl` 테이블을 만듭니다.

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. 웹 서버의 루트 디렉터리에서 `registration`이라는 폴더를 만들고 이 폴더 내에 `index.php`라는 파일을 만듭니다.

5. 텍스트 편집기 또는 IDE에서 **index.php** 파일을 열고 다음 코드를 추가하여 `//TODO:` 주석으로 표시된 필수 변경을 완료합니다.


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
			// DB connection info
			//TODO: Update the values for $host, $user, $pwd, and $db
			//using the values you retrieved earlier from the portal.
			$host = "value of Data Source";
			$user = "value of User Id";
			$pwd = "value of Password";
			$db = "value of Database";
			// Connect to database.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insert registration info
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
			// Retrieve data
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
		?>
		</body>
		</html>

이제 ****http://localhost/registration/index.php**(으)로 이동하여 응용 프로그램을 테스트할 수 있습니다.


##응용 프로그램 게시

앱을 로컬에서 테스트한 후 Git를 사용하여 웹 앱에 게시할 수 있습니다. 로컬 Git 리포지토리를 초기화하고 응용 프로그램을 게시하겠습니다.

> [AZURE.NOTE]이들은 앞선 웹 앱 만들기 및 Git 게시 설정 섹션의 끝에서 포털에 표시된 단계와 동일합니다.

1. (옵션) Git 원격 리포지토리 URL을 잊어버렸거나 제대로 보관하지 못한 경우 포털의 웹 앱 속성으로 이동합니다.

1. GitBash를 열거나 Git가 `PATH`에 있는 경우 터미널을 열고 응용 프로그램의 루트 디렉터리로 이동한 후 다음 명령을 실행합니다.

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다.

	![Git를 통해 최초로 Azure에 푸시][git-initial-push]

2. **http://[site 이름].azurewebsites.net/index.php**로 이동하여 응용 프로그램 사용을 시작합니다(이 정보는 계정 대시보드에 저장됨).

	![Azure PHP 웹 사이트][running-app]

앱을 게시한 후 변경을 시작하고 Git를 사용하여 변경 내용을 게시할 수 있습니다.

##앱의 변경 내용 게시

앱에 변경 내용을 게시하려면 다음 단계를 따르세요.

1. 앱을 로컬에서 변경합니다.
2. GitBash를 열거나 Git가 `PATH`에 있는 경우 터미널을 열고 앱의 루트 디렉터리로 이동한 후 다음 명령을 실행합니다.

		git add .
		git commit -m "comment describing changes"
		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다.

	![Git를 통해 사이트 변경 내용을 Azure에 푸시][git-change-push]

3. **http://[site 이름].azurewebsites.net/index.php**로 이동하여 앱과 변경한 내용을 확인합니다.

	![Azure PHP 웹 사이트][running-app]

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx
 

<!---HONumber=August15_HO6-->