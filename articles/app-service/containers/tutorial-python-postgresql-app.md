---
title: Azure App Service에서 Python 및 PostgreSQL 웹앱 빌드 | Microsoft Docs
description: Azure에서 데이터 기반 Python 앱을 실행하고 PostgreSQL 데이터베이스에 연결하는 방법을 알아봅니다.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435789"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Azure에서 Docker Python 및 PostgreSQL 웹앱 빌드

[Linux의 App Service](app-service-linux-intro.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 PostgreSQL을 데이터베이스 백 엔드로 사용하여 데이터 기반 Python 웹앱을 만드는 방법을 보여줍니다. 작업이 완료되면 Linux의 App Service에 있는 Docker 컨테이너 내에서 Python Flask 응용 프로그램을 실행하게 됩니다.

![Linux의 App Service의 Docker Python Flask 앱](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 PostgreSQL 데이터베이스 만들기
> * PostgreSQL에 Python 앱 연결
> * Azure에 앱 배포
> * 진단 로그 보기
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure Portal에서 앱 관리

macOS에서 이 문서의 단계를 수행할 수 있습니다. Linux와 Windows의 지침은 대부분 동일하지만, 차이점은 이 자습서에서 자세히 설명하지 않습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

1. [Git 설치](https://git-scm.com/)
1. [Python 설치](https://www.python.org/downloads/)
1. [PostgreSQL 설치 및 실행](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>로컬 PostgreSQL 설치 테스트 및 데이터베이스 만들기

로컬 터미널 창에서 `psql` 명령을 실행하여 로컬 PostgreSQL 서버에 연결합니다.

```bash
sudo -u postgres psql postgres
```

`unknown user: postgres`와 비슷한 오류 메시지를 받는 경우 로그인된 사용자 이름을 사용하여 PostgreSQL 설치를 구성할 수 있습니다. 대신 다음 명령을 사용합니다.

```bash
psql postgres
```

연결이 성공하면 PostgreSQL 데이터베이스가 실행되고 있습니다. 그렇지 않으면 [다운로드 - PostgreSQL 핵심 배포](https://www.postgresql.org/download/)에서 운영 체제에 대한 지침을 수행하여 로컬 PostgresQL 데이터베이스가 시작되도록 합니다.

*eventregistration*라는 데이터베이스를 만들고, 암호가 *supersecretpass*인 *manager*라는 별도의 데이터베이스 사용자를 설정합니다.

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다.

<a name="step2"></a>

## <a name="create-local-python-app"></a>로컬 Python 앱 만들기

이 단계에서는 로컬 ASP.NET 프로젝트를 설정합니다.

### <a name="clone-the-sample-app"></a>샘플 앱 복제

터미널 창을 열고 `CD`를 사용하여 작업 디렉터리로 이동합니다.

다음 명령을 실행하여 샘플 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

이 샘플 리포지토리에는 [Flask](http://flask.pocoo.org/) 응용 프로그램이 들어 있습니다.

### <a name="run-the-app-locally"></a>로컬에서 앱 실행하기

필요한 패키지를 설치하고 응용 프로그램을 시작합니다.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

앱이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

브라우저에서 `http://localhost:5000` 으로 이동합니다. **Register!** 를 클릭하고 테스트 사용자를 만듭니다.

![로컬로 Python Flask 응용 프로그램 실행](./media/tutorial-python-postgresql-app/local-app.png)

Flask 샘플 응용 프로그램은 데이터베이스에 사용자 데이터를 저장합니다. 사용자 등록에 성공하면 앱에서 로컬 PostgreSQL 데이터베이스에 데이터를 쓰고 있습니다.

언제든지 Flask 서버를 중지하려면 터미널에서 Ctrl+C를 입력합니다.

## <a name="create-a-production-postgresql-database"></a>프로덕션 PostgreSQL 데이터베이스 만들기

이 단계에서는 Azure에 PostgreSQL 데이터베이스를 만듭니다. Azure에 앱을 배포하면 이 클라우드 데이터베이스가 사용됩니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure Database 서버 만들기

Cloud Shell에서 [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) 명령을 사용하여 PostgreSQL 서버를 만듭니다.

다음 명령 예제에서 *\<postgresql_name>* 을 고유한 서버 이름으로 바꾸고, *\<admin_username>* 및 *\<admin_password>* 를 원하는 사용자 자격 증명으로 바꿉니다. 사용자 자격 증명은 데이터베이스 관리자 계정을 위한 것입니다. 서버 이름은 PostgreSQL 엔드포인트(`https://<postgresql_name>.postgres.database.azure.com`)의 일부로 사용되므로 이름은 Azure의 모든 서버에서 고유해야 합니다.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
```

PostgreSQL용 Azure 데이터베이스 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "<admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> 나중에 사용하기 위해 \<admin_username> 및 \<admin_password>를 기억해 두세요. Postgre 서버 및 해당 데이터베이스에 로그인해야 합니다.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>PostgreSQL 서버에 대한 방화벽 규칙 만들기

Cloud Shell에서 다음 Azure CLI 명령을 실행하여 Azure 리소스에서 데이터베이스에 액세스할 수 있게 합니다.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> 이 설정을 사용하면 Azure 네트워크 내의 모든 IP에서 네트워크 연결을 허용합니다. 프로덕션에서 사용하기 위해 [앱에서 사용하는 아웃바운드 IP 주소를 사용하여](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips) 가장 제한적인 방화벽 규칙을 구성하려고 합니다.

Cloud Shell에서 *\<you_ip_address>* 를 [로컬 IPv4 IP 주소](http://www.whatsmyip.org/)로 바꾸어 로컬 컴퓨터에서 데이터베이스에 액세스할 수 있도록 명령을 다시 실행합니다.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Python 앱을 프로덕션 데이터베이스에 연결

이 단계에서는 Flask 샘플 앱을 앞에서 만든 Azure Database for PostgreSQL 서버에 연결합니다.

### <a name="create-empty-database-and-user-access"></a>빈 데이터베이스 및 사용자 액세스 만들기

로컬 터미널 창에서 아래 명령을 실행하여 데이터베이스에 연결합니다. 관리자 암호를 묻는 메시지가 나타나면 [PostgreSQL 서버용 Azure Database 만들기](#create-an-azure-database-for-postgresql-server)에서 지정한 암호를 사용합니다.

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

로컬 Postgres 서버와 같이 Azure Postgres 서버에서 데이터베이스 및 사용자를 만듭니다.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다.

> [!NOTE]
> 관리 사용자를 사용하는 대신 특정 응용 프로그램에 대해 제한된 사용 권한으로 데이터베이스 사용자를 만드는 것이 좋습니다. 이 예제에서 `manager` 사용자에게는 `eventregistration` 데이터베이스에 대해서_만_ 모든 권한이 있습니다.

### <a name="test-app-connectivity-to-production-database"></a>프로덕션 데이터베이스에 대한 앱 연결 테스트

다시 로컬 터미널 창에서 다음 명령을 실행하여 Flask 데이터베이스 마이그레이션 및 Flask 서버를 실행합니다.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

앱이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

브라우저에서 http://localhost:5000 으로 이동합니다. **Register!** 를 클릭하고 테스트 등록을 만듭니다. 이제 Azure에서 데이터베이스에 데이터를 쓰고 있습니다.

![로컬로 Python Flask 응용 프로그램 실행](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>Deploy to Azure

이 단계에서는 Postgres에 연결된 Python 응용 프로그램을 Azure App Service에 배포합니다.

### <a name="configure-repository"></a>리포지토리 구성

리포지토리 루트에 _application.py_가 있으면 App Service의 Git 배포 엔진은 `pip` 자동화를 호출합니다. 이 자습서에서는 배포 엔진이 자동화를 실행할 수 있게 됩니다. 로컬 터미널 창에서 리포지토리의 루트로 이동하고, 더미 _application.py_를 만들고, 변경 내용을 커밋합니다.

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>웹앱 만들기 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>환경 변수 구성

자습서의 앞부분에서 환경 변수를 정의하여 PostgreSQL 데이터베이스에 연결했습니다.

App Service의 Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용하여 환경 변수를 _앱 설정_으로 설정합니다.

다음 예제에서는 데이터베이스 연결 세부 정보를 앱 설정으로 지정합니다. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 5, done. 
Delta compression using up to 4 threads. 
Compressing objects: 100% (5/5), done. 
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done. 
Total 5 (delta 3), reused 0 (delta 0) 
remote: Updating branch 'master'. 
remote: Updating submodules. 
remote: Preparing deployment for commit id '6c7c716eee'. 
remote: Running custom deployment command... 
remote: Running deployment command... 
remote: Handling node.js deployment. 
. 
. 
. 
remote: Deployment successful. 
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
 * [new branch]      master -> master 
```  

### <a name="configure-entry-point"></a>진입점 구성

기본 제공 이미지가 기본적으로 루트 디렉터리에서 진입점인 _wsgi.py_ 또는 _application.py_를 찾지만 진입점은 _app/app.py_입니다. 이전에 추가한 _application.py_는 비어 있으며 아무 작업도 수행하지 않습니다.

Cloud Shell에서 [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set) 명령을 실행하여 사용자 지정 시작 스크립트를 설정합니다.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

`--startup-file` 매개 변수는 사용자 지정 명령이 포함된 파일에 대한 사용자 지정 명령 또는 경로를 사용합니다. 사용자 지정 명령은 다음과 같은 형식이어야 합니다.

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

사용자 지정 명령에서 루트 디렉터리에 진입점이 없는 경우 `--chdir`이 필요하고 `<subdirectory>`는 하위 디렉터리입니다. `<module>`은 _.py_ 파일의 이름이고 `<variable>`은 웹앱을 나타내는 모듈의 변수입니다.

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기

배포된 웹앱으로 이동합니다. 앱이 처음으로 요청될 때 컨테이너를 다운로드하여 실행해야 하므로 시작될 때까지 시간이 걸립니다. 페이지가 시간 초과 또는 오류 메시지를 표시하는 경우 몇 분 정도 기다렸다가 페이지를 새로 고칩니다.

```bash
http://<app_name>.azurewebsites.net
```

이전 단계에서 Azure 프로덕션 데이터베이스에 저장된 이전에 등록한 게스트를 볼 수 있습니다.

![Azure에서 실행 중인 Python Flask 응용 프로그램](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**축하합니다.** Linux용 App Service에서 Python 앱이 실행되고 있습니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

Python 앱이 컨테이너에서 실행되므로 Linux의 App Service를 통해 컨테이너 내에서 생성된 콘솔 로그에 액세스할 수 있습니다. 로그를 찾으려면 다음 URL로 이동합니다.

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

`href` 속성을 포함하여 두 개의 JSON 개체가 표시됩니다. 하나의 `href`는 Docker 콘솔 로그를 가리키고 `_docker.log`로 끝납니다. 또 다른 `href`는 Python 컨테이너 내에서 생성된 콘솔 로그를 가리킵니다. 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

브라우저 창에 원하는 `href` 값을 복사하여 로그로 이동합니다. 로그는 스트리밍되지 않으므로 약간의 지연이 발생할 수 있습니다. 새 로그를 보려면 브라우저 페이지를 새로 고칩니다.

## <a name="update-data-model-and-redeploy"></a>데이터 모델 업데이트 및 다시 배포

이 단계에서는 `Guest` 모델을 업데이트하여 각 이벤트 등록에 대한 참석자 수를 추가한 다음, Azure에 업데이트를 다시 배포합니다.

로컬 터미널 창에서 다음 Git 명령을 사용하여 `modelChange` 분기의 파일을 확인합니다.

```bash
git checkout origin/modelChange -- .
```

이 확인을 통해 모델, 보기 및 컨트롤러에 필요한 변경 내용을 적용합니다. 또한 *alembic*(`flask db migrate`)를 통해 생성된 데이터베이스 마이그레이션도 포함됩니다. 다음 Git 명령을 통해 모든 변경 내용을 확인할 수 있습니다.

```bash
git diff master origin/modelChange
```

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

로컬 터미널 창에서 다음 명령을 통해 flask 서버를 로컬로 실행하여 로컬에서 변경 내용을 테스트합니다.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

브라우저에서 http://localhost:5000 으로 이동하여 변경 내용을 확인합니다. 테스트 등록을 만듭니다.

![로컬로 실행되는 Docker 컨테이너 기반 Python Flask 응용 프로그램](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

로컬 터미널 창에서 Git의 모든 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Azure Web App으로 이동하여 새 기능을 다시 시험해 봅니다. 페이지를 새로 고쳤는지 확인합니다.

```bash
http://<app_name>.azurewebsites.net
```

![Azure App Service의 Docker Python Flask 앱](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>Azure Portal에서 웹앱 관리

[Azure Portal](https://portal.azure.com)로 이동하여 만든 웹앱을 확인합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/tutorial-python-postgresql-app/app-resource.png)

기본적으로 포털에는 웹앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 PostgreSQL 데이터베이스 만들기
> * PostgreSQL에 Python 앱 연결
> * Azure에 앱 배포
> * 진단 로그 보기
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure Portal에서 앱 관리

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [기본 제공 Python 이미지 구성](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](../app-service-web-tutorial-custom-domain.md)

