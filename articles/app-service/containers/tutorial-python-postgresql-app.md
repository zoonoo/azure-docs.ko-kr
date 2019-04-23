---
title: Linux에서 PostgreSQL을 사용하는 Python(Django) - Azure App Service | Microsoft Docs
description: Azure에서 데이터 기반 Python 앱을 실행하고 PostgreSQL 데이터베이스에 연결하는 방법을 알아봅니다. Django는 자습서에서 사용됩니다.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: b3d262a33ecbc35ada278019ee0998486bc92efe
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678924"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>Azure App Service에서 Python 및 PostgreSQL 앱 빌드

[Linux의 App Service](app-service-linux-intro.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 PostgreSQL을 데이터베이스 백 엔드로 사용하여 데이터 기반 Python 앱을 만드는 방법을 보여줍니다. 완료되면 Linux의 App Service에서 Django 애플리케이션을 실행하게 됩니다.

![Linux의 App Service의 Python Django 앱](./media/tutorial-python-postgresql-app/django-admin-azure.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 PostgreSQL 데이터베이스 만들기
> * PostgreSQL에 Python 앱 연결
> * Azure에 앱 배포
> * 진단 로그 보기
> * Azure Portal에서 앱 관리

> [!NOTE]
> Azure Database for PostgreSQL를 만들기 전에, [해당 Azure 지역에서 사용할 수 있는 컴퓨팅 세대](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores)를 확인하세요.

macOS에서 이 문서의 단계를 수행할 수 있습니다. Linux와 Windows의 지침은 대부분 동일하지만, 차이점은 이 자습서에서 자세히 설명하지 않습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

1. [Git 설치](https://git-scm.com/)
2. [Python 설치](https://www.python.org/downloads/)
3. [PostgreSQL 설치 및 실행](https://www.postgresql.org/download/)

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

*pollsdb*라는 데이터베이스를 만들고, 암호가 *supersecretpass*인 *manager*라는 별도의 데이터베이스 사용자를 설정합니다.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다.

<a name="step2"></a>

## <a name="create-local-python-app"></a>로컬 Python 앱 만들기

이 단계에서는 로컬 Python Django 프로젝트를 설정합니다.

### <a name="clone-the-sample-app"></a>샘플 앱 복제

터미널 창을 열고 `CD`를 사용하여 작업 디렉터리로 이동합니다.

다음 명령을 실행하여 샘플 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

이 샘플 리포지토리에는 [Django](https://www.djangoproject.com/) 애플리케이션이 들어 있습니다. [Django 설명서의 자습서 시작](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)을 수행하여 얻는 것과 동일한 데이터 기반 앱입니다. 이 자습서에서는 Django를 설명하지 않지만 App Service에 Django 앱(또는 다른 데이터 기반 Python 앱)을 배포하고 실행하는 방법을 보여줍니다.

### <a name="configure-environment"></a>환경 구성

Python 가상 환경을 만들고 스크립트를 사용하여 데이터베이스 연결 설정을 설정합니다.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

*env.sh* 및 *env.ps1*에 정의된 환경 변수는 데이터베이스 설정을 정의하기 위해 _azuresite/settings.py_에서 사용됩니다.

### <a name="run-app-locally"></a>로컬에서 앱 실행

필요한 패키지를 설치하고, [Django 마이그레이션을 실행](https://docs.djangoproject.com/en/2.1/topics/migrations/)하고 [관리 사용자를 만듭니다](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

관리 사용자가 만들어지면 Django 서버를 실행합니다.

```bash
python manage.py runserver
```

앱이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

브라우저에서 `http://localhost:8000` 으로 이동합니다. 메시지 `No polls are available.`이 표시되어야 합니다. 

`http://localhost:8000/admin`으로 이동하고 마지막 단계에서 만든 관리 사용자를 사용하여 로그인합니다. **질문** 옆의 **추가**를 클릭하고 몇 가지 옵션을 사용하여 설문 조사 질문을 만듭니다.

![로컬로 Python Django 애플리케이션 실행](./media/tutorial-python-postgresql-app/django-admin-local.png)

`http://localhost:8000`으로 다시 이동하고 표시되는 설문 조사 질문을 확인합니다.

Django 샘플 애플리케이션은 데이터베이스에 사용자 데이터를 저장합니다. 설문 조사 질문 추가에 성공하면 앱에서 로컬 PostgreSQL 데이터베이스에 데이터를 쓰고 있습니다.

언제든지 Django 서버를 중지하려면 터미널에서 Ctrl+C를 입력합니다.

## <a name="create-a-production-postgresql-database"></a>프로덕션 PostgreSQL 데이터베이스 만들기

이 단계에서는 Azure에 PostgreSQL 데이터베이스를 만듭니다. Azure에 앱을 배포하면 이 클라우드 데이터베이스가 사용됩니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure Database 서버 만들기

Cloud Shell에서 [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) 명령을 사용하여 PostgreSQL 서버를 만듭니다.

다음 명령 예제에서 *\<postgresql-name>* 을 고유한 서버 이름으로 바꾸고, *\<admin-username>* 및 *\<admin-password>* 를 원하는 사용자 자격 증명으로 바꿉니다. 사용자 자격 증명은 데이터베이스 관리자 계정을 위한 것입니다. 서버 이름은 PostgreSQL 엔드포인트(`https://<postgresql-name>.postgres.database.azure.com`)의 일부로 사용되므로 이름은 Azure의 모든 서버에서 고유해야 합니다.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

PostgreSQL용 Azure 데이터베이스 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
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
> 나중에 사용할 수 있도록 \<admin-username> 및 \<admin-password>를 기억해 두세요. Postgre 서버 및 해당 데이터베이스에 로그인해야 합니다.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>PostgreSQL 서버에 대한 방화벽 규칙 만들기

Cloud Shell에서 다음 Azure CLI 명령을 실행하여 Azure 리소스에서 데이터베이스에 액세스할 수 있게 합니다.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> 이 설정을 사용하면 Azure 네트워크 내의 모든 IP에서 네트워크 연결을 허용합니다. 프로덕션에서 사용하기 위해 [앱에서 사용하는 아웃바운드 IP 주소를 사용하여](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips) 가장 제한적인 방화벽 규칙을 구성하려고 합니다.

Cloud Shell에서 *\<your-ip-address>* 를 [로컬 IPv4 IP 주소](https://www.whatsmyip.org/)로 바꾸어 로컬 컴퓨터에서 액세스할 수 있도록 명령을 다시 실행합니다.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Python 앱을 프로덕션 데이터베이스에 연결

이 단계에서는 Django 샘플 앱을 앞에서 만든 Azure Database for PostgreSQL 서버에 연결합니다.

### <a name="create-empty-database-and-user-access"></a>빈 데이터베이스 및 사용자 액세스 만들기

Cloud Shell에서 아래 명령을 실행하여 데이터베이스에 연결합니다. 관리자 암호를 묻는 메시지가 나타나면 [PostgreSQL 서버용 Azure Database 만들기](#create-an-azure-database-for-postgresql-server)에서 지정한 암호를 사용합니다.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

로컬 Postgres 서버와 같이 Azure Postgres 서버에서 데이터베이스 및 사용자를 만듭니다.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다.

> [!NOTE]
> 관리 사용자를 사용하는 대신 특정 응용 프로그램에 대해 제한된 사용 권한으로 데이터베이스 사용자를 만드는 것이 좋습니다. 이 예제에서 `manager` 사용자에게는 `pollsdb` 데이터베이스에 대해서_만_ 모든 권한이 있습니다.

### <a name="test-app-connectivity-to-production-database"></a>프로덕션 데이터베이스에 대한 앱 연결 테스트

로컬 터미널 창에서 데이터베이스 환경 변수(*env.sh* 또는 *env.ps1*을 실행하여 이전에 구성한)를 변경합니다.

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Azure 데이터베이스에 Django 마이그레이션을 실행하고 관리 사용자를 만듭니다.

```bash
python manage.py migrate
python manage.py createsuperuser
```

관리 사용자가 만들어지면 Django 서버를 실행합니다.

```bash
python manage.py runserver
```

`http://localhost:8000`으로 다시 이동합니다. 메시지 `No polls are available.`이 다시 표시되어야 합니다. 

`http://localhost:8000/admin`으로 이동하고 만든 관리 사용자를 사용하여 로그인하고, 전과 같이 설문 조사 질문을 만듭니다.

![로컬로 Python Django 애플리케이션 실행](./media/tutorial-python-postgresql-app/django-admin-local.png)

`http://localhost:8000`으로 다시 이동하고 표시되는 설문 조사 질문을 확인합니다. 이제 앱이 Azure에서 데이터베이스에 데이터를 쓰고 있습니다.

## <a name="deploy-to-azure"></a>Deploy to Azure

이 단계에서는 Postgres에 연결된 Python 응용 프로그램을 Azure App Service에 배포합니다.

### <a name="configure-repository"></a>리포지토리 구성

Django가 들어오는 요청에서 `HTTP_HOST` 헤더의 유효성을 검사합니다. Django 앱을 App Service에서 작동하도록 하려면 허용된 호스트에 앱의 정규화된 도메인 이름을 추가해야 합니다. _azuresite/settings.py_를 열고 `ALLOWED_HOSTS` 설정을 찾습니다. 줄을 다음으로 변경합니다.

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

다음으로 Django는 [프로덕션 환경에서 정적 파일 제공](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)을 지원하지 않으므로 이 작업을 수동으로 활성화해야 합니다. 이 자습서의 경우 [WhiteNoise](https://whitenoise.evans.io/en/stable/)를 사용합니다. WhiteNoise 패키지는 _requirements.txt_에 이미 포함되어 있습니다. Django를 사용하려면 구성하기만 하면 됩니다. 

_azuresite/settings.py_에서 `MIDDLEWARE` 설정을 찾고, `django.middleware.security.SecurityMiddleware` 미들웨어 바로 아래의 목록에 `whitenoise.middleware.WhiteNoiseMiddleware` 미들웨어를 추가합니다. `MIDDLEWARE` 설정은 다음과 비슷합니다.

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

_azuresite/settings.py_의 끝에 다음 줄을 추가합니다.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

WhiteNoise 구성에 대한 자세한 내용은 [WhiteNoise 설명서](https://whitenoise.evans.io/en/stable/)를 참조하세요.

> [!IMPORTANT]
> 데이터베이스 설정 섹션은 이미 환경 변수를 사용하는 보안 모범 사례를 따릅니다. 전체 배포 권장 사항은 [Django 설명서: 배포 검사 목록](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)을 참조하세요.

리포지토리에 변경 내용을 커밋합니다.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>App Service 플랜 만들기

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>환경 변수 구성

자습서의 앞부분에서 환경 변수를 정의하여 PostgreSQL 데이터베이스에 연결했습니다.

App Service의 Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용하여 환경 변수를 _앱 설정_으로 설정합니다.

다음 예제에서는 데이터베이스 연결 세부 정보를 앱 설정으로 지정합니다. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

이러한 앱 설정이 코드로 액세스되는 방법에 대한 자세한 내용은 [환경 변수 액세스](how-to-configure-python.md#access-environment-variables)를 참조하세요.

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

App Service 배포 서버는 리포지토리 루트에서 _requirements.txt_를 살펴보고 `git push` 후에 Python 패키지 관리를 자동으로 실행합니다.

### <a name="browse-to-the-azure-app"></a>Azure 앱으로 이동

배포된 앱으로 이동합니다. 앱이 처음으로 요청될 때 컨테이너를 다운로드하여 실행해야 하므로 시작될 때까지 시간이 걸립니다. 페이지가 시간 초과 또는 오류 메시지를 표시하는 경우 몇 분 정도 기다렸다가 페이지를 새로 고칩니다.

```bash
http://<app-name>.azurewebsites.net
```

이전에 만든 설문 조사 질문이 표시됩니다. 

App Service는 기본적으로 `manage.py startproject`에서 만들어진 각 하위 디렉터리에서 _wsgi.py_를 검색하여 리포지토리에서 Django 프로젝트를 검색합니다. 파일을 찾으면 Django 앱을 로드합니다. App Service에서 Python 앱을 로드하는 방법에 대한 자세한 내용은 [기본 제공 Python 이미지 구성](how-to-configure-python.md)을 참조하세요.

`<app-name>.azurewebsites.net`으로 이동하고 사용자가 만든 동일한 관리 사용자를 사용하여 로그인합니다. 원하는 경우 자세한 일부 설문 조사 질문을 만들어 보세요.

![로컬로 Python Django 애플리케이션 실행](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**축하합니다.** Linux용 App Service에서 Python 앱이 실행되고 있습니다.

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Azure Portal에서 앱 관리

[Azure Portal](https://portal.azure.com)로 이동하여 만든 앱을 확인합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음, Azure 앱의 이름을 클릭합니다.

![Azure 앱에 대한 포털 탐색](./media/tutorial-python-postgresql-app/app-resource.png)

기본적으로 포털에 앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 PostgreSQL 데이터베이스 만들기
> * PostgreSQL에 Python 앱 연결
> * Azure에 앱 배포
> * 진단 로그 보기
> * Azure Portal에서 앱 관리

다음 자습서로 이동하여 사용자 지정 DNS 이름을 앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 앱에 사용자 지정 DNS 이름 매핑](../app-service-web-tutorial-custom-domain.md)

또는 다른 리소스를 확인합니다.

> [!div class="nextstepaction"]
> [Python 앱 구성](how-to-configure-python.md)