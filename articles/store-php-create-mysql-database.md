<properties
	pageTitle="Azure에서 MySQL 데이터베이스 만들기 및 연결"
	description="Azure 포털을 사용하여 MySQL 데이터베이스를 만든 후 Azure의 PHP 웹앱에서 연결하는 방법을 알아봅니다."
	documentationCenter="php"
	services="app-service\web"
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm;cephalin"/>

# Azure에서 MySQL 데이터베이스 만들기 및 연결

이 가이드에서는 [Azure 포털](https://portal.azure.com)(공급자가 [ClearDB](http://www.cleardb.com/))에서 MySQL 데이터베이스를 만들고 [Azure 앱 서비스](./app-service/app-service-value-prop-what-is.md)에서 실행 중인 PHP 웹앱에서 연결하는 방법을 보여 줍니다.

> [AZURE.NOTE] [마켓플레이스 앱 템플릿](./app-service-web/app-service-web-create-web-app-from-marketplace.md)의 일부로 MySQL 데이터베이스를 만들 수도 있습니다.

## Azure 포털에서 MySQL 데이터베이스 만들기

Azure 포털에서 MySQL 데이터베이스를 만들려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴에서 **새로 만들기** > **데이터 + 저장소** > **MySQL 데이터베이스**를 클릭합니다.

	![Azure에서 MySQL 데이터베이스 만들기 - 시작](./media/store-php-create-mysql-database/create-db-1-start.png)

2. 새 MySQL 데이터베이스 [블레이드](azure-portal-overview.md)에서 다음과 같이 새 MySQL 데이터베이스를 구성합니다(*블레이드*: 가로로 열리는 포털 페이지).

	- **데이터베이스 이름**: 고유하게 식별 가능한 이름을 입력합니다.
	- **구독**: 사용할 구독을 선택합니다.
	- **데이터베이스 유형**: 저비용 또는 무료 계층의 경우 **공유**를, 전용 리소스를 가져오려면 **전용**을 선택합니다.
	- **리소스 그룹**: 기존 [리소스 그룹](../resource-group-overview.md)에 MySQL 데이터베이스를 추가하거나 새 그룹에 넣습니다. 동일한 그룹의 리소스는 쉽게 함께 관리할 수 있습니다.
	- **위치**: 가까운 위치를 선택합니다. 기존 리소스 그룹에 추가할 때 리소스 그룹의 위치로 고정됩니다.
	- **가격 책정 계층**: **가격 책정 계층**을 클릭한 후 가격 책정 옵션(**Mercury** 계층은 무료)을 선택한 후 **선택**을 클릭합니다.
	- **약관**: **약관**을 클릭하고 구매 정보를 검토한 후 **구매**를 클릭합니다.
	- **대시보드에 고정**: 대시보드에서 직접 액세스하려는 경우 선택합니다. 포털 탐색에 아직 익숙하지 않은 경우 특히 유용합니다.
	
    다음 스크린 샷은 MySQL 데이터베이스를 구성하는 방법을 예로 든 것 뿐입니다. ![Azure에서 MySQL 데이터베이스 만들기 - 구성](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. 구성을 완료하면 **만들기**를 클릭합니다.

	![Azure에서 MySQL 데이터베이스 만들기 - 만들기](./media/store-php-create-mysql-database/create-db-3-create.png)

	배포가 시작되었음을 알려주는 팝업 알림이 표시됩니다.

	![Azure에서 MySQL 데이터베이스 만들기 - 진행 중](./media/store-php-create-mysql-database/create-db-4-started-status.png)

	배포에 성공하면 다른 팝업을 받게 됩니다. 포털에서 MySQL 데이터베이스 블레이드가 자동으로 열립니다.

## Azure의 PHP 웹앱에서 MySQL 데이터베이스에 연결

새 MySQL 데이터베이스에 대한 연결 정보를 보려면 **속성**을 클릭하면 됩니다. **설정** 블레이드가 표시되지 않으면 **설정** > **속성**을 클릭합니다.
	
![Azure에서 MySQL 데이터베이스 만들기- MySQL 데이터베이스 블레이드](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

이제 모든 웹앱에서 연결 정보를 사용할 수 있습니다. 간단한 PHP 앱에서 연결 정보를 사용하는 방법을 보여 주는 샘플은 [여기](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)에서 사용할 수 있습니다.

### Laravel 웹앱 연결(PHP 시작 자습서에서)

방금 [PHP 웹앱 만들기, 구성 및 Azure에 배포](./app-service-web/app-service-web-php-get-started.md) 자습서를 완료했고 [Laravel](https://www.laravel.com/) 웹앱이 Azure에서 실행 중이라고 가정하면 Laravel 앱에 데이터베이스 기능을 쉽게 추가할 수 있습니다. 다음 단계를 따르기만 하면 됩니다.

>[AZURE.NOTE] 다음 단계에서는 [PHP 웹앱 만들기, 구성 및 Azure에 배포](./app-service-web/app-service-web-php-get-started.md) 자습서를 완료했다고 가정합니다.

1. 로컬 개발 환경에서 MySQL 데이터베이스를 가리키도록 Laravel 앱을 구성합니다. 이 작업을 수행하려면 Laravel 앱의 루트 디렉터리에서 `.env`를 열고 MySQL 데이터베이스 옵션을 구성합니다.

		DB_CONNECTION=mysql
		DB_HOST=<HOSTNAME_from_properties_blade>
		DB_PORT=<PORT_from_properties_blade>
		DB_DATABASE=<see_note_below>
		DB_USERNAME=<USERNAME_from_properties_blade>
		DB_PASSWORD=<PASSWORD_from_properties_blade>

	>[AZURE.NOTE] **속성** 블레이드에서 MySQL 데이터베이스의 이름은 **데이터베이스 이름** 필드에 표시된 것이거나 다른 것일 수 있습니다. **연결 문자열** 필드에서 데이터베이스 매개 변수를 확인하는 것이 좋습니다.
	>
	>![Azure에서 MySQL 데이터베이스 만들기 - 진행 중](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. MySQL 액세스 권한이 있는지 확인하는 가장 빠른 방법은 [Laravel의 기본 인증 스캐폴딩](https://laravel.com/docs/5.2/authentication#authentication-quickstart)을 사용하는 것입니다. 명령줄 터미널에서, Laravel 앱의 루트 디렉터리에서 다음 명령을 실행합니다.

		 php artisan migrate
		 php artisan make:auth

	첫 번째 명령은 `database/migrations` 디렉터리에 미리 정의된 마이그레이션에 따라 Azure에 테이블을 만들고 두 번째 명령은 사용자 등록 및 인증을 위해 기본 보기 및 경로를 스캐폴딩합니다.

3. 이제 개발 서버를 실행합니다.

		php artisan serve

4. 브라우저에서 http://localhost:8000으로 이동하고 표시된 것처럼 새 사용자를 등록합니다.

	![Azure에서 MySQL 데이터베이스에 연결 - 사용자 등록](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

	UI 프롬프트에 따라 등록을 완료합니다. 등록이 완료되면 로그인됩니다.
	
	![Azure에서 MySQL 데이터베이스에 연결 - 사용자 등록](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

	이제 Azure에서 MySQL 데이터베이스에 대한 앱을 개발합니다.

5. `.env` 설정을 Azure 웹앱에 복제해야 합니다. 다음 Azure CLI 명령을 실행합니다.

		azure site appsetting add DB_CONNECTION=mysql
		azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
		azure site appsetting add DB_PORT=<PORT_from_properties_blade>
		azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
		azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
		azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

	[Azure 웹앱 구성](./app-service-web/app-service-web-php-get-started.md#configure)에서 이 명령이 어떻게 작동하는지 알아보세요.

6. 다음으로, `php artisan make:auth`를 실행하는 동안 이전에 발생한 로컬 변경 내용을 Azure에 커밋 및 푸시합니다.

		git add .
		git commit -m "scaffold auth views and routes"
		git push azure master

7. Azure 웹앱으로 이동 합니다.

		azure site browse

8. 이전에 만든 사용자 자격 증명을 사용하여 로그인합니다.

	![Azure에서 MySQL 데이터베이스에 연결 - Azure 웹앱으로 이동](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

	로그인하면 익숙한 로그인 후 화면이 표시됩니다.
	
	![Azure에서 MySQL 데이터베이스에 연결 - 로그인](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

	축하합니다. 이제 Azure의 PHP 웹앱으로 MySQL 데이터베이스에서 데이터에 액세스합니다.

## 다음 단계

자세한 내용은 [PHP 개발자 센터](/develop/php/)를 참조하세요.

<!---HONumber=AcomDC_0817_2016-->