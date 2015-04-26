<properties 
	pageTitle="Azure 웹 사이트로 Drupal 마이그레이션" 
	description="Drupal PHP 사이트를 Azure 웹 사이트로 마이그레이션합니다." 
	services="web-sites" 
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
	ms.date="11/11/2014" 
	ms.author="tomfitz"/>


# Azure 웹 사이트로 Drupal 마이그레이션

Azure 웹 사이트는 PHP와 MySQL을 모두 지원하기 때문에 Drupal 사이트를 Azure 웹 사이트로 마이그레이션하는 일은 비교적 간단합니다. 또한 Drupal 및 PHP는 모든 플랫폼에서 실행되므로, 현재 플랫폼에 관계없이 Drupal을 Azure 웹 사이트로 이동하는 프로세스가 원활하게 작동합니다. 따라서 Drupal 설치는 매우 다양할 수 있으며 다음 자료에서 다루지 않는 고유한 마이그레이션 단계가 있을 수 있습니다. Drush 도구는 Azure 웹 사이트에서 지원되지 않기 때문에 사용하지 않습니다.

> [AZURE.NOTE]
> 대규모의 복잡한 Drupal 응용 프로그램을 이동하는 경우 또 다른 옵션은 Azure 클라우드 서비스를 사용하는 것입니다. 웹 사이트와 클라우드 서비스의 차이에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우</a>를 참조하세요. Drupal을 클라우드 서비스로 이동할 때 도움말을 보려면 <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">LAMP에서 Microsoft Azure로 Drupal 사이트 마이그레이션</a>을 참조하세요.

## 목차

- [Azure 웹 사이트 만들기][]
- [데이터베이스 복사][]
- [Settings.php 수정][]
- [Drupal 코드 배포][]
- [관련 정보][]
 
##<a name="create-siteanddb"></a>1. Azure 웹 사이트 및 MySQL 데이터베이스 만들기

먼저, MySQL을 사용하여 새 웹 사이트를 만드는 방법을 익힙니다. 단계별 자습서인 [][PHP-MySQL Azure 웹 사이트 만들기 및 Git를 사용하여 배포]를 참조하세요. Drupal 사이트를 게시하는 데 Git를 사용하려면 Git 리포지토리를 구성하는 방법을 설명하는 자습서의 단계를 따릅니다. 나중에 해당 정보가 필요하므로 **원격 MySQL 연결 정보 가져오기** 섹션의 지침을 따라야 합니다. Drupal 사이트를 배포하려는 경우에는 자습서의 나머지 부분을 무시해도 됩니다. 하지만 Azure 웹 사이트(및 Git)를 처음으로 접하는 경우에는 나머지 내용을 확인하는 것이 유용합니다.

MySQL 데이터베이스를 사용하여 새 웹 사이트를 설정하고 나면 MySQL 데이터베이스 연결 정보 및 (선택적으로) Git 리포지토리를 얻게 됩니다. 다음 단계는 Azure 웹 사이트에서 데이터베이스를 MySQL로 복사하는 것입니다.

##<a name="copy-database"></a>2. Azure 웹 사이트에서 MySQL로 데이터베이스 복사

Azure로 데이터베이스를 마이그레이션하는 많은 방법이 있습니다. MySQL 데이터베이스에 효율적은 방법은 [MySqlDump][] 도구를 사용하는 것입니다. 다음 명령은 로컬 컴퓨터에서 Azure 웹 사이트로 복사하는 방법을 예로 보여 줍니다.

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

물론 기존 Drupal 데이터베이스의 사용자 이름 및 암호를 제공해야 합니다. 또한 첫 번째 단계에서 만든 MySQL 데이터베이스의 호스트 이름, 사용자 이름, 암호, 데이터베이스 이름도 제공해야 합니다. 이 정보는 이전에 수집한 연결 문자열 정보에서 찾을 수 있습니다. 연결 문자열의 형식은 다음 문자열과 비슷합니다.

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

데이터베이스의 크기에 따라, 복사 프로세스에 몇 분 정도 걸릴 수 있습니다.

이제 Drupal 데이터베이스가 Azure 웹 사이트에서 실행됩니다. Drupal 코드를 배포하기 전에 이 코드가 새 데이터베이스에 연결될 수 있도록 코드를 수정해야 합니다.

##<a name="modify-settingsphp"></a>3. settings.php에서 데이터베이스 연결 정보 수정

이 단계에서도 새 데이터베이스 연결 정보가 필요합니다. 텍스트 편집기에서 **/drupal/sites/default/setting.php** 파일을 열고 **$databases** 배열의  'database',  'username',  'password' 및  'host' 값을 새 데이터베이스의 올바른 값으로 바꿉니다. 마치면 다음과 비슷한 결과를 얻게 됩니다.

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_username',
           'password' => 'remote_password',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

**settings.php** 파일을 저장합니다. 이제 배포할 준비가 되었습니다.

##<a name="deploy-drupalcode"></a>4. Git 또는 FTP를 사용하여 Drupal 코드 배포

마지막 단계는 Git 또는 FTP를 사용하여 Azure 웹 사이트에 코드를 배포하는 것입니다.

FTP를 사용하는 경우 웹 사이트의 대시보드에서 FTP 호스트 이름 및 사용자 이름을 가져옵니다. 그런 다음, FTP 클라이언트를 사용하여 원격 사이트의 **/site/wwwroot** 폴더에 Drupal 파일을 업로드합니다.

Git를 사용하는 경우에는 이전 단계에서 Git 리포지토리를 설정해야 합니다. 로컬 컴퓨터에 Git를 설치해야 합니다. 그런 다음, 리포지토리를 만든 후 나오는 지침을 따릅니다.

> [AZURE.NOTE]
> Git 설정에 따라 .gitignore 파일을 편집해야 할 수 있습니다. 이 파일은 숨겨진 파일이며 git 커밋을 실행한 후에 로컬 루트 디렉터리에 만들어진 .git 폴더의 형제입니다. Drupal 응용 프로그램에서 무시해도 되는 파일을 지정합니다. 이 파일에 배포해야 할 파일이 포함된 경우, 해당 파일이 무시되지 않도록 해당 항목을 제거하십시오.

Drupal을 Azure 웹 사이트에 배포한 후에는 Git 또는 FTP를 통해 지속적으로 업데이트를 배포할 수 있습니다.

##<a name="related-information"></a>관련 정보

자세한 내용은 다음 게시물 및 항목을 참조하세요.

- [Azure 웹 사이트, PHP Perspective][]
- [Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우][]
- [Azure 웹 사이트에서 .user.ini 파일을 사용하여 PHP 구성][]
- [Azure 통합 모듈](https://drupal.org/project/azure_auth)
- [Azure Blob 저장소 모듈](https://drupal.org/project/azure_blob)

  [Azure 웹 사이트 만들기]: #create-siteanddb
  [데이터베이스 복사]: #copy-database
  [Settings.php 수정]: #modify-settingsphp
  [Drupal 코드 배포]: #deploy-drupalcode
  [관련 정보]: #related-information
  [PHP-MySQL Azure 웹 사이트 만들기 및 Git를 사용하여 배포]: http://azure.microsoft.com/develop/php/tutorials/website-w-mysql-and-git/
  
  [Azure 웹 사이트, PHP Perspective]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Azure 웹 사이트에서 .user.ini 파일을 사용하여 PHP 구성]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Azure 통합 모듈]: http://drupal.org/project/azure



<!--HONumber=42-->
