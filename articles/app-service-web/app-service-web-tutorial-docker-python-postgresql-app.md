---
title: "Azure에서 Docker Python 및 PostgreSQL 웹앱 빌드 | Microsoft Docs"
description: "PostgreSQL 데이터베이스에 연결하여 Azure에서 Docker Python 앱이 작동하도록 하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/03/2017
ms.author: beverst
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: ca086f76e50cb27f012bb2e7f05595be5fdcb241
ms.contentlocale: ko-kr
ms.lasthandoff: 06/01/2017

---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Azure에서 Docker Python 및 PostgreSQL 웹앱 빌드
이 자습서에서는 Azure에서 기본 Docker Python 웹앱을 만드는 방법을 보여 줍니다. 또한 이 앱을 PostgreSQL 데이터베이스에 연결합니다. 완료되면 Python Flask 응용 프로그램이 [App Service Web Apps](app-service-web-overview.md)의 Docker 컨테이너 내에서 실행되게 됩니다.

![Azure App Service의 Docker Python Flask 앱](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="before-you-begin"></a>시작하기 전에

이 샘플을 실행하기 전에 다음 필수 조건을 로컬로 설치합니다.

1. [Git 다운로드 및 설치](https://git-scm.com/)
1. [Python 다운로드 및 설치](https://www.python.org/downloads/)
1. [PostgreSQL 다운로드, 설치 및 실행](https://www.postgresql.org/download/)
1. [Docker Community Edition 다운로드 및 설치](https://www.docker.com/community-edition)
1. [Azure CLI 2.0 다운로드 및 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-postgresql-installation-and-create-a-database"></a>로컬 PostgreSQL 설치 테스트 및 데이터베이스 만들기
이 단계에서는 로컬 PostgreSQL 데이터베이스가 실행 중인지 확인합니다.

터미널 창을 열고 `psql postgres`를 실행하여 로컬 PostgreSQL 서버에 연결합니다.

```bash
psql postgres
```

연결이 성공한다면 PostgreSQL 데이터베이스가 이미 실행 중입니다. 연결이 실패하면 [PostgreSQL 다운로드, 설치 및 실행](https://www.postgresql.org/download/)의 단계를 수행하여 로컬 PostgresQL 데이터베이스가 시작되도록 합니다.

`eventregistration`라는 데이터베이스를 만들고 암호 `supersecretpass`를 사용하여 `manager`라는 별도의 데이터베이스 사용자를 설정합니다.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>로컬 Python Flask 응용 프로그램 만들기
이 단계에서는 로컬 ASP.NET 프로젝트를 설정합니다.

### <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

터미널 창을 열고 `CD`를 사용하여 작업 디렉터리로 이동합니다.  

다음 명령을 실행하여 샘플 리포지토리를 복제하고 `0.1-initialapp` 릴리스로 이동합니다.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.gi
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

이 샘플 리포지토리에는 [Flask](http://http://flask.pocoo.org/) 응용 프로그램이 들어 있습니다. 

### <a name="run-the-application"></a>응용 프로그램 실행

> [!NOTE] 
> 프로덕션 데이터베이스에서 Docker 컨테이너를 빌드하여 나중에 이 프로세스를 간소화할 것입니다.

필요한 패키지를 설치하고 응용 프로그램을 시작합니다.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

응용 프로그램이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

브라우저에서 `http://127.0.0.1:5000`으로 이동합니다. **Register!**를 클릭하고 더미 등록을 만듭니다.

![로컬로 Python Flask 응용 프로그램 실행](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

Flask 샘플 응용 프로그램은 데이터베이스에 사용자 데이터를 저장합니다. 작업이 성공하고 앱에서 등록을 볼 수 있으면 앱은 로컬 PostgreSQL 데이터베이스에 데이터를 쓰고 있는 것입니다.

언제든지 Flask 서버를 중지하려면 터미널에 `Ctrl`+`C`를 입력합니다. 

## <a name="create-a-production-postgresql-database"></a>프로덕션 PostgreSQL 데이터베이스 만들기

이 단계에서는 Azure에 PostgreSQL 데이터베이스를 만듭니다. 앱이 Azure에 배포되어 있으면 프로덕션 워크로드에 이 데이터베이스를 사용합니다.

### <a name="log-in-to-azure"></a>Azure에 로그인

이제 터미널 창에서 Azure CLI 2.0을 사용하여 Azure App Service에서 Python 응용 프로그램을 호스트하는 데 필요한 리소스를 만들 예정입니다.  [az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 

```azurecli-interactive 
az login 
``` 
   
### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create)를 사용하여 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 저장소 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 미국 서부 지역의 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

`--location`에 사용할 수 있는 가능한 값을 보려면 `az appservice list-locations` Azure CLI 명령을 사용합니다.

### <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure 데이터베이스 서버 만들기

[az postgres server create](/cli/azure/documentdb#create) 명령으로 PostgreSQL 서버를 만듭니다.

다음 명령에서 `<postgresql_name>` 자리 표시자를 고유한 PostgreSQL 서버 이름으로 대체합니다. 이 고유한 이름은 PostgreSQL 끝점의 일부로 사용되므로(`https://<postgresql_name>.postgres.database.azure.com`) 이름은 Azure의 모든 서버에서 고유해야 합니다. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <my_admin_username>
```

`--admin-user`는 초기 데이터베이스 관리 사용자 계정을 만드는 데 필요합니다. 이 사용자의 암호를 선택하라는 메시지가 표시됩니다.

PostgreSQL용 Azure 데이터베이스 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="creating-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure 데이터베이스 서버에 대한 방화벽 규칙 만들기

이 데이터베이스에 액세스하려면 이제 모든 IP 주소에서 연결할 수 있도록 해야 합니다. 이 작업은 다음 Azure CLI 명령을 통해 수행할 수 있습니다.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

방화벽을 만들면 Azure CLI는 다음과 같이 규칙이 있는지 확인합니다.

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>데이터베이스에 Python Flask 응용 프로그램 연결

이 단계에서 Python Flask 샘플 응용 프로그램을 만든 PostgreSQL용 Azure 데이터베이스 서버에 연결합니다.

### <a name="creating-an-empty-database-and-setting-up-a-new-database-application-user"></a>빈 데이터베이스 만들기 및 새 데이터베이스 응용 프로그램 사용자 설정

단일 데이터베이스에만 액세스할 수 있는 새 데이터베이스 사용자를 만듭니다. 이 단계에서는 관리자 자격 증명을 통해 서버에 대한 모든 액세스 권한을 응용 프로그램에 부여하지 못하게 합니다.

데이터베이스에 연결합니다(관리자 암호를 묻는 메시지 표시).
```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

그런 다음 PostgreSQL CLI에서 데이터베이스 및 사용자를 만듭니다.
```
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Azure PostgreSQL 데이터베이스에 대해 로컬로 응용 프로그램 테스트 

이제 복제된 Github 리포지토리의 `app` 폴더로 돌아간 후 데이터베이스 환경 변수를 업데이트하기만 하면 Python Flask 응용 프로그램을 실행할 수 있습니다.

```bash
FLASK_APP=app.py;DBHOST="<postgresql_name>.postgres.database.azure.com";DBUSER="manager@<postgresql_name>";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

앱이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

브라우저에서 `http://127.0.0.1:5000`으로 이동합니다. **Register!**를 클릭하고 더미 등록을 만듭니다. 이제 Azure에서 프로덕션 데이터베이스에 데이터를 쓸 것입니다.

![로컬로 Python Flask 응용 프로그램 실행](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Docker 컨테이너에서 응용 프로그램 실행

이제 Azure의 PostgreSQL 프로덕션 데이터베이스에 계속 연결된 상태에서 Docker 컨테이너 이미지를 빌드하고 Docker 컨테이너 내에서 로컬로 응용 프로그램을 실행합니다.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker는 컨테이너를 성공적으로 만들었다는 확인을 표시합니다.

```
Successfully built 7548f983a36b
```

데이터베이스 환경 변수를 환경 변수 파일 `db.env`에 추가해 보겠습니다.

```
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

이제 Docker 컨테이너 내에서 앱을 실행합니다. 환경 변수 파일을 지정하고 기본 Flask 포트 5000을 로컬 포트 5000에 매핑합니다.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

당연히 출력은 이전과 비슷합니다. 그러나 초기 데이터베이스 마이그레이션은 더 이상 수행할 필요가 없으므로 건너뜁니다.
```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 ```

 데이터베이스에는 이미 이전에 만든 등록이 포함되어 있습니다.

 ![로컬로 실행되는 Docker 컨테이너 기반 Python Flask 응용 프로그램](./media/app-service-web-tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Docker 컨테이너를 컨테이너 레지스트리로 업로드
이 단계에서는 만든 Docker 컨테이너를 컨테이너 레지스트리로 업로드합니다. Azure Container Registry를 사용하게 되지만 Docker 허브와 같은 인기 높은 다른 레지스트리를 사용할 수도 있습니다.

### <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기

다음 명령에서 컨테이너 레지스트리를 만들려면 `<registry_name>`을 선택한 고유한 Azure Container Registry 이름으로 바꿉니다.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

출력
```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Docker 이미지 밀어넣기 및 끌어오기를 위한 레지스트리 자격 증명 검색

이러한 자격 증명에 액세스하려면 먼저 관리 모드를 사용하도록 설정해야 합니다.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

두 암호가 표시됩니다. 나중을 위해 사용자 이름 및 첫 번째 암호를 기록해 둡니다.
```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Docker 컨테이너를 Azure Container Registry로 업로드

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Azure에 Docker Python Flask 응용 프로그램 배포
이 단계에서는 Docker 컨테이너 기반 Python Flask 응용 프로그램을 Azure App Service에 배포합니다.

### <a name="create-an-app-service-plan"></a>앱 서비스 계획 만들기

[az appservice plan create](/cli/azure/appservice/plan#create) 명령으로 App Service 계획을 만듭니다. 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

다음 예제에서는 S1 가격 책정 계층을 사용하여 `myAppServicePlan`이라는 Linux 기반 App Service 계획을 만듭니다.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service 계획을 만들면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json 
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
``` 

### <a name="create-a-web-app"></a>웹앱 만들기

이제 App Service 계획을 만들었으므로 `myAppServicePlan` App Service 계획 내에서 웹앱을 만듭니다. 웹앱은 코드를 배포할 호스팅 공간을 제공하고, 배포된 응용 프로그램을 확인할 수 있도록 URL도 제공합니다. [az appservice web create](/cli/azure/appservice/web#create) 명령을 사용하여 웹앱을 만듭니다. 

다음 명령에서 `<app_name>` 자리 표시자를 고유한 응용 프로그램 이름으로 대체합니다. 이 고유한 이름은 웹앱에 대한 기본 도메인 이름의 일부로 사용되므로 이름은 Azure에 있는 모든 앱에서 고유해야 합니다. 나중에 사용자에게 노출하기 전에 웹앱에 사용자 지정 DNS 항목을 매핑할 수 있습니다. 

```azurecli-interactive
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

웹앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-the-database-environment-variables"></a>데이터베이스 환경 변수 구성

자습서의 앞부분에서는 PostgreSQL 데이터베이스에 연결하기 위해 환경 변수를 수동으로 정의했습니다.

App Service에서는 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 명령을 사용하여 환경 변수를 _app settings_로 설정합니다. 

다음에 따라 데이터베이스 연결 정보를 앱 설정으로 지정할 수 있습니다. 또한 `PORT` 변수를 사용하여 포트 80에서 HTTP 트래픽을 수신하도록 Docker 컨테이너에서 포트 5000을 매핑할 것임을 지정합니다.

```azurecli-interactive
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Docker 컨테이너 배포 구성 

AppService는 Docker 컨테이너를 자동으로 다운로드하여 실행할 수 있습니다.

[az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 명령을 사용하여 계정 수준 자격 증명을 만듭니다. 

```azurecli
az appservice web config container update --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Docker 컨테이너를 업데이트하거나 위의 설정을 변경할 때마다 앱을 다시 시작하여 모든 설정을 적용하고 최신 컨테이너를 레지스트리에서 끌어옵니다.

```azurecli-interactive
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기 
웹 브라우저를 사용하여 배포된 웹앱으로 이동합니다. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> 컨테이너 구성을 변경하고 웹앱에 처음 액세스할 때 컨테이너가 다운로드되고 시작될 때까지 여유 시간을 둡니다.

이전 단계에서 Azure 프로덕션 데이터베이스에 저장된 등록된 게스트를 볼 수 있습니다.

 ![로컬로 실행되는 Docker 컨테이너 기반 Python Flask 응용 프로그램](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**축하합니다.** Azure App Service에서 Docker 컨테이너 기반 Python Flask 앱을 실행하고 있습니다.


## <a name="update-data-model-and-redeploy"></a>데이터 모델 업데이트 및 다시 배포

이 단계에서는 게스트 모델을 업데이트하여 각 이벤트 등록에 대한 참석자 수를 추가합니다.

다음 git 명령을 사용하여 `0.2-migration` 릴리스를 확인합니다.
```bash
git checkout tags/0.2-migration
```

이 릴리스에서는 보기, 컨트롤러 및 모델에 대해 필요한 변경이 이미 수행되었습니다. 또한 *alembic*(`flask db migrate`)를 통해 생성된 데이터베이스 마이그레이션도 포함됩니다. 다음 git 명령을 통해 수행된 모든 변경 내용을 볼 수 있습니다.

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

다음 명령을 통해 Flask 서버를 로컬로 실행하여 로컬에서 변경 내용을 테스트합니다.

Mac / Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

브라우저에서 `http://127.0.0.1:5000`으로 이동하여 변경 내용을 확인합니다. 새 더미 등록을 만듭니다.

![로컬로 실행되는 Docker 컨테이너 기반 Python Flask 응용 프로그램](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

새 docker 이미지를 빌드하고 컨테이너 레지스트리에 밀어 넣은 후 앱을 다시 시작합니다.

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Azure Web App으로 이동하여 새 기능을 다시 시험해 봅니다. 다른 이벤트 등록을 만듭니다.

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service의 Docker Python Flask 앱](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

만든 웹앱을 보려면 Azure Portal로 이동합니다.

이 작업을 수행하려면 [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.

왼쪽 메뉴에서 **App Service**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/app-service-web-tutorial-docker-python-postgresql-app/app-resource.png)

웹앱의 _블레이드_(가로로 열리는 포털 페이지)로 이동했습니다.

기본적으로 웹앱의 블레이드는 **개요** 페이지를 표시합니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 블레이드의 왼쪽에 있는 탭에서는 열 수 있는 다른 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 블레이드](./media/app-service-web-tutorial-docker-python-postgresql-app/app-mgmt.png)

블레이드의 이러한 탭은 웹앱에 추가할 수 있는 유용한 많은 기능을 보여 줍니다. 다음은 몇 가지 가능성을 제공합니다.

* 사용자 지정 DNS 이름 매핑
* 사용자 지정 SSL 인증서 바인딩
* 지속적 배포 구성
* 수평 및 수직 확장
* 사용자 인증 추가


