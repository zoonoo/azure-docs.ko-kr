<properties linkid="develop-php-website-with-mysql-and-git" urlDisplayName="Web w/ MySQL + Git" pageTitle="PHP website with MySQL and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# PHP-MySQL Azure 웹 사이트 만들기 및 Git를 사용하여 배포

이 자습서에서는 PHP-MySQL Azure 웹 사이트를 만들고 Git를 사용하여 배포하는 방법을 설명합니다. 컴퓨터에 설치된 [PHP][PHP], MySQL 명령줄 도구([MySQL][install-mysql]의 일부), 웹 서버 및 [Git][Git]를 사용할 예정입니다. 이 자습서의 지침은 Windows, Mac 및 Linux를 포함하여 모든 운영 체제에 적용될 수 있습니다. 이 가이드를 완료하면 Azure에서 실행하는 PHP/MySQL 웹 사이트가 완성됩니다.

다음 내용을 배웁니다.

-   Azure 관리 포털을 사용하여 Azure 웹 사이트 및 MySQL 데이터베이스를 만드는 방법. PHP는 Azure 웹 사이트에서 기본적으로 사용하도록 설정되어 있으므로 PHP 코드를 실행하기 위해 특별한 조치를 취할 필요가 없습니다.
-   Git를 사용하여 응용 프로그램을 Azure에 게시 및 다시 게시하는 방법

이 자습서의 지침에 따라 PHP에서 간단한 등록 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 Azure 웹 사이트에 호스트됩니다. 아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure PHP 웹 사이트][Azure PHP 웹 사이트]

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 Azure 웹 사이트 기능이 사용되는 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p> </div>

## 개발 환경 설정

이 자습서의 내용은 컴퓨터에 [PHP][PHP], MySQL 명령줄 도구([MySQL][install-mysql]의 일부), 웹 서버 및 [Git][Git]가 설치되어 있다는 것을 전제로 합니다.

> [WACOM.NOTE]
> Windows에서 이 자습서의 내용을 수행하는 경우 [PHP용 Azure SDK][PHP용 Azure SDK]를 설치하여 PHP용 컴퓨터를 설정하고 자동으로 IIS(Windows에서 기본 제공되는 웹 서버)를 구성할 수 있습니다.

## <span id="create-web-site-and-set-up-git"></span></a>Azure 웹 사이트 만들기 및 Git 게시 설정

Azure 웹 사이트 및 MySQL 데이터베이스를 만들려면 다음 단계를 따르십시오.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  포털의 왼쪽 아래에서 **새로 만들기** 아이콘을 클릭합니다.

    ![새 Azure 웹 사이트 만들기][새 Azure 웹 사이트 만들기]

3.  **웹 사이트**를 클릭한 후 **사용자 지정 만들기**를 클릭합니다.

    ![새 웹 사이트 사용자 지정 만들기][새 웹 사이트 사용자 지정 만들기]

    **URL**에 대한 값을 입력하고 **데이터베이스** 드롭다운에서 **새 MySQL 데이터베이스 만들기**를 선택한 후 **지역** 드롭다운에서 웹 사이트에 대한 데이터 센터를 선택합니다. 대화 상자 아래쪽의 화살표를 클릭합니다.

    ![웹 사이트 세부 정보 채우기][웹 사이트 세부 정보 채우기]

4.  데이터베이스의 **이름**에 값을 입력하고 **지역** 드롭다운에서 데이터베이스에 대한 데이터 센터를 선택한 후 약관에 동의함을 나타내는 확인란을 선택합니다. 대화 상자 맨 아래에 있는 확인 표시를 클릭합니다.

    ![새 MySQL 데이터베이스 만들기][새 MySQL 데이터베이스 만들기]

    웹 사이트가 만들어지면 **Creation of Web Site "[SITENAME]" completed successfully** 텍스트가 표시됩니다. 이제 Git 게시를 사용하도록 설정할 수 있습니다.

5.  웹 사이트 목록에 표시된 웹 사이트 이름을 클릭하여 웹 사이트의 **빠른 시작** 대시보드를 엽니다.

    ![웹 사이트 대시보드 열기][웹 사이트 대시보드 열기]

6.  **빠른 시작** 페이지 맨 아래에 있는 **Set up Git publishing**을 클릭합니다.

    ![Git 게시 설정][Git 게시 설정]

7.  Git 게시를 사용하도록 설정하려면 사용자 이름 및 암호를 지정해야 합니다. 만든 사용자 이름 및 암호를 기록해 둡니다. 이전에 Git 리포지토리를 설정한 경우 이 단계를 건너뜁니다.

    ![게시 자격 증명 만들기][게시 자격 증명 만들기]

    리포지토리를 설정하는 데 몇 초 정도 걸립니다.

8.  리포지토리가 준비되면 응용 프로그램 파일을 리포지토리에 푸시하는 지침이 표시됩니다. 이러한 지침은 나중에 필요하므로 기록해 둡니다.

    ![Git 지침][Git 지침]

## 원격 MySQL 연결 정보 가져오기

Azure 웹 사이트에서 실행되는 MySQL 데이터베이스에 연결하려면 연결 정보가 필요합니다. MySQL 연결 정보를 가져오려면 다음 단계를 따르십시오.

1.  웹 사이트의 대시보드에서 페이지의 오른쪽에 있는 **연결 문자열 보기** 링크를 클릭합니다.

    ![데이터베이스 연결 정보 가져오기][데이터베이스 연결 정보 가져오기]

2.  `Database`, `Data Source`, `User Id` 및 `Password` 값을 기록해 둡니다.

## 로컬에서 응용 프로그램 빌드 및 테스트

Azure 웹 사이트를 만들었으므로 로컬에서 응용 프로그램을 개발하여 테스트 후 배포할 수 있습니다.

등록 응용 프로그램은 이름과 전자 메일 주소를 지정하여 이벤트에 등록하는 데 사용할 수 있는 간단한 PHP 응용 프로그램입니다. 이전 등록자에 대한 정보가 테이블에 표시되어 있습니다. 등록 정보는 MySQL 데이터베이스에 저장되어 있습니다. 응용 프로그램은 다음 한 파일로 구성되어 있습니다(아래에서 사용할 수 있는 코드 복사/붙여넣기).

-   **index.php**: 등록 양식 및 등록자 정보가 포함된 테이블을 표시합니다.

응용 프로그램을 빌드하여 로컬에서 실행하려면 아래 단계를 따릅니다. 이러한 단계는 로컬 컴퓨터에 PHP, MySQL 명령줄 도구(MySQL의 일부) 및 웹 서버가 설정되어 있으며 [PDO Extension for MySQL][PDO Extension for MySQL]이 사용하도록 설정되어 있다는 것을 전제로 합니다.

1.  이전에 검색한 `Data Source`, `User Id`, `Password` 및 `Database`의 값을 사용하여 원격 MySQL 서버에 연결합니다.

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2.  MySQL 명령 프롬프트가 나타납니다.

        mysql>

3.  다음 `CREATE TABLE` 명령을 붙여 넣어 데이터베이스에 `registration_tbl` 테이블을 만듭니다.

        mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4.  웹 서버의 루트 디렉터리에서 `registration`이라는 폴더를 만들고 이 폴더 내에 `index.php`라는 파일을 만듭니다.

5.  텍스트 편집기 또는 IDE에서 **index.php** 파일을 열고 다음 코드를 추가하여 `//TODO:` 주석으로 표시된 필수 변경을 완료합니다.

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

이제 **<http://localhost/registration/index.php>**로 이동하여 응용 프로그램을 테스트할 수 있습니다.

## 응용 프로그램 게시

응용 프로그램을 로컬에서 테스트한 후 Git를 사용하여 Azure 웹 사이트에 게시할 수 있습니다. 로컬 Git 리포지토리를 초기화하고 응용 프로그램을 게시하겠습니다.

> [WACOM.NOTE]
> 이들은 앞선 "Azure 웹 사이트 만들기 및 Git 게시 설정" 섹션의 끝에서 포털에 표시된 단계와 동일합니다.

1.  (옵션) Git 원격 리포지토리 URL을 잊어버렸거나 제대로 보관하지 못한 경우 포털의 배포 탭으로 이동합니다.

    ![Git URL 확인][Git 지침]

2.  GitBash를 열거나 Git가 `PATH`에 있는 경우 터미널을 열고 응용 프로그램의 루트 디렉터리로 이동한 후 다음 명령을 실행합니다.

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    이전에 만든 암호를 입력하라는 메시지가 나타납니다.

    ![Git를 통해 최초로 Azure에 푸시][Git를 통해 최초로 Azure에 푸시]

3.  **[http://[사이트][http://[사이트] 이름].azurewebsites.net/index.php**로 이동하여 응용 프로그램의 사용을 시작합니다(이 정보는 계정 대시보드에 저장됨).

    ![Azure PHP 웹 사이트][Azure PHP 웹 사이트]

응용 프로그램을 게시한 후 변경을 시작하고 Git를 사용하여 변경 내용을 게시할 수 있습니다.

## 응용 프로그램의 변경 내용 게시

응용 프로그램에 변경 내용을 게시하려면 다음 단계를 따르십시오.

1.  응용 프로그램을 로컬에서 변경합니다.
2.  GitBash를 열거나 Git가 `PATH`에 있는 경우 터미널을 열고 응용 프로그램의 루트 디렉터리로 이동한 후 다음 명령을 실행합니다.

        git add .
        git commit -m "comment describing changes"
        git push azure master

    이전에 만든 암호를 입력하라는 메시지가 나타납니다.

    ![Git를 통해 사이트 변경 내용을 Azure에 푸시][Git를 통해 사이트 변경 내용을 Azure에 푸시]

3.  **[http://[사이트][http://[사이트] 이름].azurewebsites.net/index.php**로 이동하여 응용 프로그램과 변경한 내용을 확인합니다.

    ![Azure PHP 웹 사이트][Azure PHP 웹 사이트]

4.  또한 Azure 관리 포털의 '배포' 탭에서 새 배포를 볼 수도 있습니다.

    ![웹 사이트 배포 목록][웹 사이트 배포 목록]

  [PHP]: http://www.php.net/manual/en/install.php
  [Git]: http://git-scm.com/
  [Azure PHP 웹 사이트]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
  [Azure 무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A74E0F923
  [PHP용 Azure SDK]: http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids
  [Azure 관리 포털]: https://manage.windowsazure.com
  [새 Azure 웹 사이트 만들기]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
  [새 웹 사이트 사용자 지정 만들기]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
  [웹 사이트 세부 정보 채우기]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
  [새 MySQL 데이터베이스 만들기]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
  [웹 사이트 대시보드 열기]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
  [Git 게시 설정]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
  [게시 자격 증명 만들기]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png
  [Git 지침]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
  [데이터베이스 연결 정보 가져오기]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
  [PDO Extension for MySQL]: http://www.php.net/manual/en/ref.pdo-mysql.php
  [Git를 통해 최초로 Azure에 푸시]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
  [http://[사이트]: http://[site
  [Git를 통해 사이트 변경 내용을 Azure에 푸시]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
  [웹 사이트 배포 목록]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
