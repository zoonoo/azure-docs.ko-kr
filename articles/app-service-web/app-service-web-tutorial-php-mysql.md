---
title: "Azure에서 PHP 및 MySQL 웹앱 빌드 | Microsoft Docs"
description: "MySQL 데이터베이스에 연결하여 Azure에서 PHP 앱이 작동하도록 하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3ad716fab4f5084c38c83f4bc90a616856666b38
ms.contentlocale: ko-kr
ms.lasthandoff: 06/01/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Azure에서 PHP 및 MySQL 웹앱 빌드
이 자습서에서는 Azure에서 PHP 웹앱을 만들고 MySQL 데이터베이스에 연결하는 방법을 보여 줍니다. 완료되면 [Laravel](https://laravel.com/) 응용 프로그램이 [Azure App Service Web Apps](app-service-web-overview.md)에서 실행되고 있을 것입니다.

![Azure App Service에서 실행 중인 PHP 앱](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 MySQL 데이터베이스 만들기
> * MySQL에 PHP 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하기 전에 다음 필수 조건을 로컬로 설치합니다.

1. [Git 다운로드 및 설치](https://git-scm.com/)
1. [PHP 5.6.4 이상 다운로드 및 설치](http://php.net/downloads.php)
1. [Composer 다운로드 및 설치](https://getcomposer.org/doc/00-intro.md)
1. PHP 확장 Laravel에 필요한 OpenSSL, Pdo-mysql, Mbstring, Tokenizer, XML 사용
1. [MySQL 다운로드, 설치 및 시작](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Azure CLI 2.0 다운로드 및 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>로컬 MySQL 준비

이 단계에서는 이 자습서에서 사용할 데이터베이스를 로컬 MySQL 서버에 만듭니다.

### <a name="connect-to-mysql-server"></a>MySQL 서버에 연결
터미널 창에서 로컬 MySQL 서버에 연결합니다.

```bash
mysql -u root -p
```

암호를 묻는 메시지가 표시되면 `root` 계정에 대한 암호를 입력합니다. 루트 계정 암호를 기억하지 못하는 경우 [MySQL: 루트 암호를 재설정하는 방법](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)을 참조하세요.

명령이 성공적으로 실행되면 MySQL 서버가 이미 실행되고 있는 것입니다. 그렇지 않은 경우 [MySQL 설치 후 단계](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)에 따라 로컬 MySQL 서버가 시작되었는지 확인합니다.

### <a name="create-a-database"></a>데이터베이스 만들기

`mysql` 프롬프트에서 데이터베이스를 만듭니다.

```sql
CREATE DATABASE sampledb;
```

`quit`을 입력하여 서버 연결을 종료합니다.

```sql
quit
```

<a name="step2"></a>

## <a name="create-local-php-app"></a>로컬 PHP 앱 만들기
이 단계에서는 Laravel 샘플 응용 프로그램 가져오고, 해당 데이터베이스를 구성한 후 로컬로 실행합니다. 

### <a name="clone-the-sample"></a>샘플 복제

터미널 창을 열고 `cd`를 사용하여 작업 디렉터리로 이동합니다.  

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

복제된 디렉터리로 `cd`를 실행한 후 필요한 패키지를 설치합니다.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL 연결 구성

리포지토리 루트에서 _.env_ 파일을 만든 후 다음 변수를 복사합니다. _&lt;root_password>_ 자리 표시자를 루트 사용자의 암호로 바꿉니다.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

> [!NOTE]
> Laravel에서 이 _.env_ 파일을 사용하는 방법에 대한 자세한 내용은 [Laravel 환경 구성](https://laravel.com/docs/5.4/configuration#environment-configuration)을 참조하세요.
>
>

### <a name="run-the-sample"></a>샘플 실행

[Laravel database migrations](https://laravel.com/docs/5.4/migrations)을 실행하여 응용 프로그램에 필요한 테이블을 만듭니다. 마이그레이션에서 생성된 테이블을 보려면 Git 리포지토리의 _database/migrations_ 디렉터리를 확인합니다.

```bash
php artisan migrate
```

새 Laravel 응용 프로그램 키를 생성합니다.

```bash
php artisan key:generate 
```

응용 프로그램을 실행합니다.

```bash
php artisan serve
```

브라우저에서 `http://localhost:8000`으로 이동합니다. 해당 페이지에서 몇 가지 작업을 추가합니다.

![PHP가 MySQL 연결에 성공](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

언제든지 PHP를 중지하려면 터미널에 `Ctrl`+`C`를 입력합니다. 

## <a name="create-production-mysql-in-azure"></a>Azure에서 프로덕션 MySQL 만들기

이 단계에서는 [MySQL용 Azure 데이터베이스(미리 보기)](/azure/mysql)에서 MySQL 데이터베이스를 만듭니다. 나중에 이 데이터베이스에 연결하도록 PHP 응용 프로그램을 구성합니다.

### <a name="log-in-to-azure"></a>Azure에 로그인

이제 터미널 창에서 Azure CLI 2.0을 사용하여 Azure App Service에서 PHP 응용 프로그램을 호스트하는 데 필요한 리소스를 만들 예정입니다. [az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 

```azurecli-interactive 
az login 
``` 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 저장소 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예wp에서는 북유럽 지역의 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```

`--location`에 사용할 수 있는 가능한 값을 보려면 [az appservice list-locations](/cli/azure/appservice#list-locations) 명령을 사용합니다.

### <a name="create-a-mysql-server"></a>MySQL 서버 만들기

[az mysql server create](/cli/azure/mysql/server#create) 명령을 사용하여 MySQL용 Azure 데이터베이스의 서버를 만듭니다.

다음 명령에서 _&lt;mysql_server_name>_ 자리 표시자를 고유한 MySQL 서버 이름으로 대체합니다. 이 이름은 MySQL 서버의 호스트 이름인 `<mysql_server_name>.database.windows.net`에 속하므로 전역적으로 고유해야 합니다. 마찬가지로 _&lt;admin_user>_ 및 _&lt;admin_password>_를 고유한 값으로 바꿉니다.

```azurecli-interactive
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --user <admin_user> \
    --password <admin_password>
```

MySQL 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "<admin_user>",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>서버 방화벽 구성

[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) 명령을 사용하여 클라이언트 연결을 허용하도록 MySQL 서버에 대한 방화벽 규칙을 만듭니다. 

```azurecli-interactive
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> MySQL용 Azure 데이터베이스(미리 보기)에서는 Azure 서비스의 연결만 사용하도록 설정하지 않습니다. Azure의 IP 주소는 동적으로 할당되므로 지금은 모든 IP 주소를 사용하도록 설정하는 것이 좋습니다. 이 서비스는 미리 보기로 제공되며, 데이터베이스를 보호하기 위한 더 나은 방법이 곧 제공될 예정입니다.
>
>

### <a name="connect-to-production-mysql-server"></a>프로덕션 MySQL 서버에 연결

터미널 창에서 Azure의 MySQL 서버에 연결합니다. _&lt;admin_user>_ 및 _&lt;mysql_server_name>_에 대해 이전에 지정한 값을 사용합니다.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>프로덕션 데이터베이스 만들기

`mysql` 프롬프트에서 데이터베이스를 만듭니다.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>사용 권한이 있는 사용자 만들기

데이터베이스 사용자를 만들고 `sampledb` 데이터베이스에서 모든 권한을 부여합니다. 자리 표시자 _&lt;phpapp_user>_ 및 _&lt;phpapp_password>_를 고유한 앱 이름으로 바꿉니다.

```sql
CREATE USER '<phpapp_user>' IDENTIFIED BY '<phpapp_password>'; 
GRANT ALL PRIVILEGES ON sampledb.* TO '<phpapp_user>';
```

`quit`을 입력하여 서버 연결을 종료합니다.

```sql
quit
```

## <a name="connect-app-to-production-mysql"></a>프로덕션 MySQL에 앱 연결

이 단계에서는 PHP 응용 프로그램을 MySQL용 Azure 데이터베이스(미리 보기)에서 방금 만든 MySQL 데이터베이스에 연결합니다. 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>연결 구성 

리포지토리 루트에서 _.env.production_ 파일을 만든 후 다음 변수를 복사합니다. 자리 표시자 _&lt;mysql_server_name>_, _&lt;phpapp_user>_ 및 _&lt;phpapp_password>_를 바꿉니다.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=<phpapp_user>@<mysql_server_name>
DB_PASSWORD=<phpapp_password>
```

변경 내용을 저장합니다.

### <a name="test-the-application"></a>응용 프로그램 테스트

_.env.production_을 환경 파일로 사용해서 Laravel 데이터베이스 마이그레이션을 실행하고 MySQL용 Azure 데이터베이스(미리 보기)에서 MySQL 데이터베이스에 테이블을 만듭니다.

```bash
php artisan migrate --env=production --force
```

_.env.production_에는 아직 유효한 응용 프로그램 키가 없습니다. 터미널에서 새 키를 생성합니다. 

```bash
php artisan key:generate --env=production --force
```

_.env.production_을 환경 파일로 사용해서 샘플 응용 프로그램을 실행합니다.

```bash
php artisan serve --env=production
```

브라우저에서 `http://localhost:8000`으로 이동합니다. 오류 없이 페이지가 로드되면 PHP 응용 프로그램이 Azure의 MySQL 데이터베이스에 연결됩니다. 

해당 페이지에서 몇 가지 작업을 추가합니다.

![PHP가 MySQL용 Azure 데이터베이스(미리 보기)에 데이터베이스에 성공적으로 연결됨](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

언제든지 PHP를 중지하려면 터미널에 `Ctrl`+`C`를 입력합니다. 

### <a name="secure-sensitive-data"></a>중요 데이터 보안 유지

_.env.production_의 중요 데이터가 Git에 커밋되지 않았는지 확인해야 합니다.

이렇게 하려면 리포지토리 루트에서 _.gitignore_를 열고 새 줄에 파일 이름을 추가합니다.

```
.env.production
```

변경 내용을 저장하고 Git에 변경 내용을 커밋합니다.

```bash
git add .gitignore
git commit -m "keep sensitive data out of git"
```

나중에 MySQL용 Azure 데이터베이스(미리 보기)의 데이터베이스에 연결하도록 App Service의 환경 변수를 구성하는 방법을 배우게 되지만 App Service에서는 `.env` 파일이 필요하지 않습니다. 

## <a name="deploy-php-app-to-azure"></a>Azure에 PHP 앱 배포
이 단계에서는 MySQL에 연결된 PHP 응용 프로그램을 Azure App Service에 배포합니다.

### <a name="create-an-app-service-plan"></a>앱 서비스 계획 만들기

[az appservice plan create](/cli/azure/appservice/plan#create) 명령으로 App Service 계획을 만듭니다. 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

다음 예제에서는 **무료** 가격 책정 계층을 사용하여 _myAppServicePlan_이라는 App Service 계획을 만듭니다.

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

App Service 계획을 만들면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-a-web-app"></a>웹앱 만들기

이제 App Service 계획을 만들었으므로 _myAppServicePlan_ App Service 계획 내에서 웹앱을 만듭니다. 웹앱은 코드를 배포할 호스팅 공간을 제공하고, 배포된 응용 프로그램을 확인할 수 있도록 URL도 제공합니다. [az appservice web create](/cli/azure/appservice/web#create) 명령을 사용하여 웹앱을 만듭니다. 

다음 명령에서 _&lt;appname>_ 자리 표시자를 고유한 응용 프로그램 이름으로 대체합니다. 이 고유한 이름은 웹앱에 대한 기본 도메인 이름의 일부로 사용되므로 이름은 Azure에 있는 모든 앱에서 고유해야 합니다. 나중에 사용자에게 노출하기 전에 웹앱에 사용자 지정 DNS 항목을 매핑할 수 있습니다. 

```azurecli-interactive
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

웹앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="set-the-php-version"></a>PHP 버전 설정

[az appservice web config update](/cli/azure/appservice/web/config#update) 명령을 사용하여 응용 프로그램에 필요한 PHP 버전을 설정합니다.

다음 명령은 PHP 버전을 _7.0_으로 설정합니다.

```azurecli-interactive
az appservice web config update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>데이터베이스 설정 구성

앞서 설명한 것처럼 App Service에서 환경 변수를 사용하여 Azure MySQL 데이터베이스에 연결할 수 있습니다.

App Service에서는 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 명령을 사용하여 환경 변수를 _app settings_로 설정합니다. 

다음 명령을 사용하면 앱 설정 `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` 및 `DB_PASSWORD`를 구성할 수 있습니다. 자리 표시자 _&lt;appname>_, _&lt;mysql_server_name>_, _&lt;phpapp_user>_ 및 _&lt;phpapp_password>_를 바꿉니다.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="<phpapp_user>@<mysql_server_name>" DB_PASSWORD="<phpapp_password>"
```

PHP [getenv()](http://www.php.net/manual/function.getenv.php) 메서드를 사용하여 설정에 액세스할 수 있습니다. Laravel 코드는 PHP `getenv()`에 대해 [env()](https://laravel.com/docs/5.4/helpers#method-env) 래퍼를 사용합니다. 예를 들어 _config/database.php_의 MySQL 구성은 다음과 유사합니다.

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel 환경 변수 구성

로컬 컴퓨터의 경우처럼 Laravel은 App Service에서 응용 프로그램 키가 필요합니다. 앱 설정을 사용하여 이러한 키를 구성할 수도 있습니다.

`php artisan`을 사용하면 _.env_로 저장하지 않고도 새 응용 프로그램 키를 생성할 수 있습니다.

```bash
php artisan key:generate --show
```

[az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 명령을 사용하여 App Service Web App에서 응용 프로그램 키를 설정합니다. 자리 표시자 _&lt;appname>_ 및 _&lt;outputofphpartisankey:generate>_를 바꿉니다.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

> [!NOTE]
> `APP_DEBUG="true"`는 배포된 웹앱에 오류가 발생할 경우 디버깅 정보를 반환하도록 Laravel에 지시합니다. 프로덕션 응용 프로그램을 실행할 경우 더 나은 보안을 위해 대신 `false`로 설정해야 합니다.
>
>

### <a name="set-the-virtual-application-path"></a>가상 응용 프로그램 경로 설정

웹앱에 대한 가상 응용 프로그램 경로를 설정합니다. [Laravel 응용 프로그램 수명 주기](https://laravel.com/docs/5.4/lifecycle)는 응용 프로그램의 루트 디렉터리가 아닌 _public_ 디렉터리에서 시작되므로 이 단계를 수행해야 합니다. 해당 수명 주기가 루트 디렉터리에서 시작하는 다른 PHP 프레임워크는 가상 응용 프로그램 경로를 수동으로 구성하지 않아도 작동될 수 있습니다.

[az resource update](/cli/azure/resource#update) 명령을 사용하여 가상 응용 프로그램 경로를 설정합니다. _&lt;appname>_ 자리 표시자를 바꿉니다.

```bash
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

> [!NOTE]
> 기본적으로 Azure App Service는 루트 가상 응용 프로그램 경로(_/_)가 배포된 응용 프로그램 파일의 루트 디렉터리(_sites\wwwroot_)를 가리키도록 합니다. 
>
>

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

FTP 및 로컬 Git의 경우 배포에 인증하기 위해 배포 사용자를 서버에 구성할 필요가 있습니다. 

> [!NOTE] 
> 배포 사용자는 Web App에 대한 FTP 및 로컬 Git 배포가 필요합니다. 사용자 이름 및 암호는 계정 수준입니다. 따라서 Azure 구독 자격 증명과 다릅니다.

배포 사용자 이름 및 암호를 이전에 만든 경우 다음 명령을 사용하여 사용자 이름을 표시할 수 있습니다.

```azurecli-interactive
az appservice web deployment user show
```

배포 사용자가 아직 없는 경우 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 명령을 실행하여 배포 자격 증명을 만듭니다. 

```azurecli-interactive
az appservice web deployment user set \
    --user-name <username> \
    --password <minimum-8-char-capital-lowercase-number>
```

사용자 이름은 고유해야 하고 암호는 강력해야 합니다. `'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. `'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다.

이 배포 사용자는 한 번만 만들어야 하며 모든 Azure 배포에서 사용할 수 있습니다.

나중에 응용 프로그램을 배포할 때 사용하므로 사용자 이름 및 암호를 기록해 둡니다.

### <a name="configure-local-git-deployment"></a>로컬 Git 배포 구성 

FTP, 로컬 Git, GitHub, Visual Studio Team Services 및 BitBucket과 같은 다양한 방법으로 응용 프로그램을 Azure App Service에 배포할 수 있습니다. 

[az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 명령을 사용하여 Azure 웹앱에 대한 로컬 Git 액세스 권한을 구성합니다. 

```azurecli-interactive
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

배포 사용자가 구성되면 Azure CLI에 다음과 같은 형식으로 Azure 웹앱의 배포 URL이 표시됩니다.

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

다음 단계에서 사용할 수 있는 터미널의 출력을 복사합니다. 

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

로컬 Git 리포지토리에 Azure 원격을 추가합니다. 

```bash
git remote add azure <paste_copied_url_here> 
```

Azure 원격에 푸시하여 PHP 응용 프로그램을 배포합니다. 배포 사용자를 만드는 작업의 일부로 이전에 입력한 암호를 묻는 메시지가 나타납니다. 

```bash
git push azure master
```

배포하는 동안 Azure App Service는 진행 상황을 Git에 전합니다.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
``` 

> [!NOTE]
> 배포 프로세스를 진행하면 마지막에 [Composer](https://getcomposer.org/) 패키지가 설치되는 것을 알 수 있습니다. App Service는 기본 배포 중에 이러한 자동화를 실행하지 않으므로 이 샘플 리포지토리는 사용 설정에 사용되는 추가 파일 3개가 루트 디렉터리에 들어 있습니다. 
>
> - `.deployment` - 이 파일은 App Service에서 `bash deploy.sh`를 사용자 지정 배포 스크립트로 실행하게 만듭니다.
> - `deploy.sh` - 사용자 지정 배포 스크립트입니다. 파일을 검토하는 경우 `npm install` 다음에 `php composer.phar install`이 실행되는 것을 볼 수 있습니다. 
> - `composer.phar` - Composer 패키지 관리자입니다.
>
> 이 방식으로 App Service에 대한 Git 기반 배포에 어떤 단계든 추가할 수 있습니다. 자세한 내용은 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 참조하세요.
>
>

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기

`http://<app_name>.azurewebsites.net`으로 이동한 후 목록에 몇 가지 작업을 추가합니다. 

![Azure App Service에서 실행 중인 PHP 앱](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

**축하합니다.** Azure App Service에서 데이터 기반 PHP 앱을 실행합니다.

## <a name="update-data-model-and-redeploy"></a>데이터 모델 업데이트 및 다시 배포

이 단계에서는 `task` 데이터 모델을 약간 변경하고 변경 내용을 Azure에 게시합니다.

작업 시나리오에서는 작업을 완료한 것으로 표시할 수 있게 응용 프로그램을 수정하려고 합니다. 

### <a name="add-a-column"></a>열 추가

터미널에서 Git 리포지토리의 루트에 있는지 확인한 후 `tasks` 테이블에 대한 새 데이터베이스 마이그레이션을 생성합니다.

```bash
php artisan make:migration add_complete_column --table=tasks
```

이 명령은 생성되는 마이그레이션 파일의 이름을 표시합니다. _database/migrations_에서 이 파일을 찾은 후 텍스트 편집기에서 엽니다.

up() 메서드를 다음 코드로 바꿉니다.

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

이 코드는 `complete`라는 `tasks` 테이블에 부울 열을 추가합니다.

down() 메서드를 롤백 작업을 위한 다음 코드로 바꿉니다.

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

터미널에서 Laravel 데이터베이스 마이그레이션을 로컬로 실행하여 로컬 데이터베이스를 변경합니다.

```bash
php artisan migrate
```

[Laravel 명명 규칙](https://laravel.com/docs/5.4/eloquent#defining-models)에 따라 모델 `Task`(_app/Task.php_ 참조)은 기본적으로 `tasks` 테이블에 매핑되므로 데이터 모델 업데이트가 끝났습니다.

### <a name="update-application-logic"></a>응용 프로그램 논리 업데이트

_routes/web.php_를 엽니다. 샘플 응용 프로그램은 여기에 해당 경로 및 비즈니스 논리를 정의합니다.

파일의 끝에 다음 코드를 포함하는 경로를 추가합니다.

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

이 코드는 `complete` 값을 설정/해제하여 데이터 모델을 간단히 업데이트합니다.

### <a name="update-the-view"></a>보기 업데이트

_resources/views/tasks.blade.php_를 엽니다. `<tr>` 여는 태그를 찾아 다음으로 바꿉니다.

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

이렇게 하면 작업이 완료되었는지에 따라 행 색이 변경됩니다.

다음 줄에는 다음 코드가 있습니다.

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

전체 줄을 다음 코드로 바꿉니다.

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

이 코드는 이전에 정의한 경로를 참조하는 전송 단추를 추가합니다.

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

Git 리포지토리의 루트 디렉터리에서 개발 서버를 다시 실행합니다.

```bash
php artisan serve
```

브라우저에서 `http://localhost:8000`로 이동한 후 해당 확인란을 클릭하여 그에 따른 작업 상태 변경을 확인합니다.

![작업에 확인란이 추가됨](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

터미널에서 프로덕션 연결 문자열을 통해 Laravel 데이터베이스 마이그레이션을 실행하여 Azure에서 프로덕션 데이터베이스를 변경합니다.

```bash
php artisan migrate --env=production --force
```

Git에서 모든 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push`가 완료되면 Azure Web App으로 다시 이동하여 새 기능을 시험해 봅니다.

![Azure에 게시된 모델 및 데이터베이스 변경 내용](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

> [!NOTE]
> 이전에 작업을 추가했으면 해당 작업을 지금도 볼 수 있습니다. 데이터 스키마가 업데이트되면 기존 데이터는 그대로 유지됩니다.
>
>

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림 

PHP 응용 프로그램을 Azure App Service에서 실행하는 동안 콘솔 로그를 바로 터미널에 보낼 수 있습니다. 이 방법으로 응용 프로그램 오류를 디버깅하는 데 도움이 되는 진단 메시지를 동일하게 받을 수 있습니다.

로그 스트리밍을 시작하려면 [az appservice web log tail](/cli/azure/appservice/web/log#tail) 명령을 사용합니다.

```azurecli-interactive 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

로그 스트리밍이 시작되고 나면 브라우저에서 Azure 웹앱을 새로 고쳐 웹 트래픽을 만듭니다. 이제 콘솔 로그가 터미널에 표시됩니다.

언제든지 로그 스트리밍을 중지하려면 `Ctrl`+`C`를 입력합니다. 

> [!TIP]
> PHP 응용 프로그램은 표준 [error_log()](http://php.net/manual/function.error-log.php)를 사용하여 콘솔에 출력할 수 있습니다. 샘플 응용 프로그램에서 _app/Http/routes.php_에서 이 접근 방식을 사용합니다.
>
> 웹 프레임워크로서 [Laravel은 Monolog](https://laravel.com/docs/5.4/errors) 로깅 공급자로 사용합니다. Monolog에서 콘솔에 메시지를 출력하게 하는 방법을 보려면 [PHP: monolog를 사용하여 콘솔에 로깅하는 방법(php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out)을 참조하세요.
>
>

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

만든 웹앱을 보려면 Azure Portal로 이동합니다.

이 작업을 수행하려면 [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.

왼쪽 메뉴에서 **App Service**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/app-service-web-tutorial-php-mysql/access-portal.png)

웹앱의 _블레이드_(가로로 열리는 포털 페이지)로 이동했습니다.

기본적으로 웹앱의 블레이드는 **개요** 페이지를 표시합니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 블레이드의 왼쪽에 있는 탭에서는 열 수 있는 다른 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 블레이드](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

블레이드의 이러한 탭은 웹앱에 추가할 수 있는 유용한 많은 기능을 보여 줍니다. 다음은 몇 가지 가능성을 제공합니다.

* 사용자 지정 DNS 이름 매핑
* 사용자 지정 SSL 인증서 바인딩
* 지속적 배포 구성
* 수평 및 수직 확장
* 사용자 인증 추가

## <a name="clean-up-resources"></a>리소스 정리
 
다른 자습서에서 이러한 리소스가 필요하지 않으면([다음 단계](#next) 참조) 다음 명령을 실행하여 삭제할 수 있습니다. 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>다음 단계

이 자습서에서 학습한 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Azure에서 MySQL 데이터베이스 만들기
> * MySQL에 PHP 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

사용자 지정 DNS 이름을 매핑하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"] 
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)

