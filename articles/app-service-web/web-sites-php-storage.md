<properties 
	pageTitle="Azure 저장소를 사용하는 Azure 앱 서비스에서 PHP 웹 앱 만들기" 
	description="이 자습서에서는 Azure 앱 서비스에서 PHP 웹 앱을 만들고 백 엔드에서 Azure 테이블 저장소 서비스를 사용하는 방법을 설명합니다." 
	services="app-service\web, storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="tomfitz"/>

# Azure 저장소를 사용하는 Azure 앱 서비스에서 PHP 웹 앱 만들기

이 자습서에서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에서 PHP 웹앱을 만들고 백 엔드에서 Azure 테이블 저장소 서비스를 사용하는 방법을 설명합니다. 이 자습서의 내용은 컴퓨터에 [PHP][install-php] 및 웹 서버가 설치되어 있다는 것을 전제로 합니다. 이 자습서의 지침은 Windows, Mac 및 Linux를 포함하여 모든 운영 체제에 적용될 수 있습니다. 이 가이드를 완료하면 Azure에서 실행하고 테이블 저장소 서비스에 액세스하는 PHP 웹 앱이 완성됩니다.
 
다음 내용을 배웁니다.

* Azure 클라이언트 라이브러리를 설치하고 응용 프로그램에 포함하는 방법
* 클라이언트 라이브러리를 사용하여 테이블을 생성하고 테이블 엔터티를 생성, 쿼리 및 삭제하는 방법
* Azure 저장소 계정을 만들고 응용 프로그램을 설정하여 사용하는 방법
* Azure 웹 앱을 만들고 Git를 사용하여 배포하는 방법
 
PHP에서 간단한 작업 목록 응용 프로그램을 빌드할 것입니다. 아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure PHP 웹 앱][ws-storage-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

##Azure 클라이언트 라이브러리 설치

작성기를 통해 Azure용 PHP 클라이언트 라이브러리를 설치하려면 다음 단계를 따르세요.

1. [Git 설치][install-git]

> [AZURE.NOTE]
> Windows에서는 PATH 환경 변수에도 Git 실행 파일을 추가해야 합니다.

2. 프로젝트 루트에 **composer.json**이라는 파일을 만들고 다음 코드를 추가합니다.

		{
             "repositories": [
                 {
                     "type": "pear",
                     "url": "http://pear.php.net"
                 }
             ],
             "require": {
                 "pear-pear.php.net/mail_mime" : "*",
                 "pear-pear.php.net/http_request2" : "*",
                 "pear-pear.php.net/mail_mimedecode" : "*",
                 "microsoft/windowsazure": "*"
             }
		}

3. 프로젝트 루트에 **[composer.phar][composer-phar]**을 다운로드합니다.

4. 명령 프롬프트를 열고 프로젝트 루트에서 이 파일을 실행합니다.

		php composer.phar install

##클라이언트 라이브러리 시작

라이브러리를 사용하여 Azure API에 대해 호출하기 전 수행해야 할 기본적인 네 가지 단계가 있습니다. 이러한 단계를 수행할 초기화 스크립트를 작성할 것입니다.

* **init.php**라는 파일을 만듭니다.

* 먼저 자동 로더 스크립트를 포함합니다.

		require_once 'vendor\autoload.php'; 
	
* 사용할 네임스페이스를 포함합니다.

	Azure 서비스 클라이언트를 만들려면 **ServicesBuilder** 클래스를 사용해야 합니다.

		use WindowsAzure\Common\ServicesBuilder;

	API 호출에서 생성하는 예외를 파악하려면 **ServiceException** 클래스가 필요합니다.

		use WindowsAzure\Common\ServiceException;
	
* 또한 서비스 클라이언트를 인스턴스화하려면 유효한 연결 문자열이 필요합니다. 테이블 서비스 연결 문자열 형식은 다음과 같습니다.

	Live 서비스에 액세스하는 경우:
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	에뮬레이터 저장소에 액세스하는 경우:
	
		UseDevelopmentStorage=true

* `ServicesBuilder::createTableService` 팩터리 메서드를 사용하여 테이블 서비스 호출의 래퍼를 인스턴스화합니다.

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy`에는 Azure 테이블에서 사용할 수 있는 모든 REST 호출에 대한 메서드가 포함됩니다.


## 테이블 만들기

데이터를 저장하기 전에 컨테이너 즉 테이블을 만들어야 합니다.

* **createtable.php**라는 파일을 만듭니다.

* 먼저 만든 초기화 스크립트를 포함합니다. Azure를 액세스하는 모든 파일에 이 스크립트를 포함시킵니다.

		<?php
		require_once "init.php";

* 그런 다음 테이블 이름에서 전달하는 *createTable*에 대해 호출합니다. 다른 NoSQL 테이블 저장과 마찬가지로 Azure 테이블에 대해서 스키마가 필요하지 않습니다.
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	[http://msdn.microsoft.com/library/windowsazure/dd179438.aspx][msdn-errors]에서 오류 코드 및 메시지 검색을 찾을 수 있습니다.


##테이블 쿼리

작업 목록 응용 프로그램의 홈페이지에는 기존 작업이 모두 나열되어야 하며 새 작업을 삽입할 수 있어야 합니다.

* **index.php**라는 파일을 만들고 페이지 머리글을 형성하는 다음 HTML 및 PHP 코드를 삽입합니다.
	
		<html>
		<head>
			<title>Index</title>
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
		<h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
		<?php		
		require_once "init.php";

* **tasks** 테이블에 저장되는 *모든 엔터티*에 대해 Azure 테이블을 쿼리하기 위해 테이블 이름만 전달하는 *queryEntities* 메서드를 호출합니다. 아래 **엔터티 업데이트** 섹션에 특정 엔터티를 쿼리하는 필터를 전달하는 방법도 나와 있습니다.

		try {
		    $result = $tableRestProxy->queryEntities('tasks');
		}
		catch(ServiceException $e){
		    $code = $e->getCode();
		    $error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		
* 결과 집합에서 엔터티를 반복합니다.

		$entities = $result->getEntities();
			
		for ($i = 0; $i < count($entities); $i++) {

* `Entity`를 가져온 후 데이터를 읽기 위한 모델은 `Entity->getPropertyValue('[name]')`입니다.

			if ($i == 0) {
				echo "<table border='1'>
				<tr>
					<td>Name</td>
					<td>Category</td>
					<td>Date</td>
					<td>Mark Complete?</td>
					<td>Delete?</td>
				</tr>";
			}
			echo "
				<tr>
					<td>".$entities[$i]->getPropertyValue('name')."</td>
					<td>".$entities[$i]->getPropertyValue('category')."</td>
					<td>".$entities[$i]->getPropertyValue('date')."</td>";
					if ($entities[$i]->getPropertyValue('complete') == false)
						echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
					else
						echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
					echo "
					<td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
				</tr>";
		}
	
		if ($i > 0)
			echo "</table>";
		else
			echo "<h3>No items on list.</h3>";
		?>

* 마지막으로, 작업 삽입 스크립트에 데이터를 공급하는 형식을 삽입하고 HTML을 완성해야 합니다.

			<hr/>
			<form action="additem.php" method="post">
				<table border="1">
					<tr>
						<td>Item Name: </td>
						<td><input name="itemname" type="textbox"/></td>
					</tr>
					<tr>
						<td>Category: </td>
						<td><input name="category" type="textbox"/></td>
					</tr>
					<tr>
						<td>Date: </td>
						<td><input name="date" type="textbox"/></td>
					</tr>
				</table>
				<input type="submit" value="Add item"/>
			</form>
		</body>
		</html>

## 테이블에 엔터티 삽입

이제 응용 프로그램이 테이블에 저장된 항목을 모두 읽을 수 있습니다. 처음에는 아무것도 없으므로 데이터베이스에 데이터를 쓰는 기능을 추가해 보십시오.

* **additem.php**라는 파일을 만듭니다.

* 파일에 다음을 추가합니다.

		<?php		
		require_once "init.php";		
		use WindowsAzure\Table\Models\Entity;
		use WindowsAzure\Table\Models\EdmType;		

* 엔터티를 삽입하는 첫 번째 단계는 `Entity` 개체를 인스턴스화하고 속성을 설정하는 것입니다.
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* 그런 다음 방금 만든 `$entity`를 `insertEntity` 메서드에 전달할 수 있습니다.

		try{
			$tableRestProxy->insertEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* 마지막으로, 엔터티를 삽입한 후 페이지가 홈페이지로 돌아가도록 만듭니다.

		header('Location: index.php');		
		?>
	
## 엔터티 업데이트

작업 목록 응용 프로그램에는 항목을 완료로 표시하고 표시 해제하는 기능이 있습니다. 홈페이지는 엔터티의 *RowKey* 및 *PartitionKey* 및 대상 상태(표시==1, 표시 해제==0)를 전달합니다.

* **markitem.php**라는 파일을 만들고 초기화 부분을 추가합니다.

		<?php		
		require_once "init.php";
		

* 엔터티를 업데이트하는 첫 번째 단계는 테이블에서 가져오는 것입니다.
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq ''.$_GET['pk'].'' and RowKey eq ''.$_GET['rk'].''');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	표시된 것처럼, 쿼리 필터에 전달된 형식은 `Key eq 'Value'`입니다. 쿼리 구문에 대한 전체 설명은 [여기][msdn-table-query-syntax]에서 볼 수 있습니다.

* 그런 다음 속성을 변경할 수 있습니다.

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

* 또한 `updateEntity` 메서드는 업데이트를 수행합니다.

		try{
			$result = $tableRestProxy->updateEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* 엔터티를 삽입한 후 페이지가 홈페이지로 돌아가도록 만들려면 다음과 같이 합니다.

		header('Location: index.php');		
		?>


## 엔터티 삭제

항목 삭제는 `deleteItem`에 대한 단일 호출을 통해 수행합니다. 값에 전달된 **PartitionKey** 및 **RowKey**은 함께 엔터티의 기본 키를 구성합니다. **deleteitem.php**라는 파일을 만들고 다음 코드를 삽입합니다.

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Azure 저장소 계정 만들기

응용 프로그램에서 데이터를 클라우드에 저장하도록 만들려면 먼저 Azure에 저장소 계정을 만든 다음 적절한 인증 정보를 *Configuration* 클래스에 전달해야 합니다.

1. [Azure 포털][management-portal]에 로그인합니다.

2. 포털의 왼쪽 아래에서 **새로 만들기** 아이콘을 클릭한 다음 **데이터 + 저장소** > **저장소**를 클릭합니다. 저장소 계정에 고유한 이름을 지정하고 이를 위한 새 [리소스 그룹](../resource-group-overview.md)을 만듭니다.

	![새 저장소 계정 만들기][storage-quick-create]
	
	저장소 계정이 만들어지면 **알림** 단추가 녹색 **성공**으로 깜박이고 저장소 계정의 블레이드가 열려 새로 만든 리소스 그룹에 속한 것을 보여줍니다.

5. 저장소 계정 블레이드에서 **설정** 부분을 클릭합니다. 계정 이름 및 기본 키를 기록해 둡니다.

	![키 관리 선택][storage-access-keys]

7. **init.php**를 열고 마지막 단계에서 메모한 계정 이름 및 키를 사용하여 `[YOUR_STORAGE_ACCOUNT_NAME]` 및 `[YOUR_STORAGE_ACCOUNT_KEY]`를 바꿉니다. 파일을 저장합니다.

## Azure 웹 앱 만들기 및 Git 게시 설정

다음 단계에 따라 Azure 웹 앱을 만듭니다.

1. [Azure 포털][management-portal]에 로그인합니다.

2. [방법: Azure 포털을 사용하여 웹앱 만들기](../web-sites-create-deploy.md#createawebsiteportal)의 지침에 따라 빈 웹앱을 만듭니다. 새 [앱 서비스 계획](azure-web-sites-web-hosting-plans-in-depth-overview)을 만들고 저장소 계정을 위해 이미 만든 리소스 그룹을 선택해야 합니다.

	웹앱이 만들어지면 **알림** 단추가 녹색 **성공**으로 깜박이고 웹앱 블레이드가 열려 새로 만든 리소스 그룹에 속한 것을 보여줍니다.

6. 웹앱 블레이드에서 **연속 배포 설정**을 클릭하고 **로컬 Git 리포지토리**를 선택합니다. **확인**을 클릭합니다.

	![Git 게시 설정][setup-git-publishing]

7. 로컬 Git 리포지토리를 Azure에 배포하려면 배포 자격 증명도 설정해야 합니다. 웹앱 블레이드에서 **모든 설정** > **배포 자격 증명**을 클릭하여 자격 증명을 구성합니다. 완료되면 **저장**을 클릭합니다.

	![게시 자격 증명 만들기][credentials]

	리포지토리를 설정하는 데 몇 초 정도 걸립니다.

8. Git 리포지토리가 준비되면 이제 변경 내용을 푸시합니다. 웹 앱 블레이드에서 동일한 배포 부분을 클릭하여 리포지토리 URL을 찾을 수 있습니다.

	![웹 앱용 리포지토리 생성 후 반환된 Git 배포 지침.][git-instructions]

	다음 섹션에서 응용 프로그램을 게시할 때에도 사용되므로 위 지침을 메모하세요.

##응용 프로그램 게시

Git를 사용하여 응용 프로그램을 게시하려면 아래 단계를 따르십시오.

1. 응용 프로그램 루트에서 **vendor/microsoft/windowsazure** 폴더를 열고 다음 파일 및 폴더를 삭제합니다.
	* .git
	* .gitattributes
	* .gitignore
			
	작성기 패키지 관리자가 Azure 클라이언트 라이브러리 및 해당 종속성을 다운로드할 때 해당 라이브러리와 종속성이 있는 GitHub 리포지토리를 복제하여 수행합니다. 다음 단계에서 응용 프로그램의 루트 폴더 외부에 리포지토리를 만들어 Git를 통해 응용 프로그램을 배포합니다. Git는 리포지토리별 파일이 제거되지 않는 한, 클라이언트 라이브러리가 있는 하위 리포지토리를 무시합니다.

2. GitBash를 열거나 Git가 `PATH`에 있는 경우 터미널을 열고 응용 프로그램의 루트 디렉터리로 이동한 후 다음 명령을 실행합니다.

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다.

3. **http://[your 웹앱 도메인]/createtable.php**로 이동하여 응용 프로그램의 테이블을 만듭니다.
4. **http://[your 웹앱 도메인]/index.php**로 이동하여 응용 프로그램을 사용하기 시작합니다.

응용 프로그램을 게시한 후 변경을 시작하고 Git를 사용하여 변경 내용을 게시할 수 있습니다.

##응용 프로그램에 변경 내용 게시

응용 프로그램에 변경 내용을 게시하려면 다음 단계를 따르십시오.

1. 응용 프로그램을 로컬에서 변경합니다.
2. GitBash를 열거나 Git가 `PATH`에 있는 경우 터미널을 열고 응용 프로그램의 루트 디렉터리로 이동한 후 다음 명령을 실행합니다.

		git add .
		git commit -m "comment describing changes"
		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다.

3. **http://[your 웹앱 도메인]/index.php**로 이동하여 변경 내용을 표시합니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.




[install-php]: http://www.php.net/manual/en/install.php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
[msdn-errors]: http://msdn.microsoft.com/library/windowsazure/dd179438.aspx

[msdn-table-query-syntax]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://portal.azure.com

[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png

[git-instructions]: ./media/web-sites-php-storage/git-instructions.png
 

<!----HONumber=July15_HO4-->