---
title: '자습서: Postgre를 사용하는 Linux Python 앱'
description: Azure의 PostgreSQL 데이터베이스와 연결하여 Azure App Service에서 Linux Python 앱이 작동하도록 하는 방법에 대해 알아봅니다. 이 자습서에서는 Django가 사용됩니다.
ms.devlang: python
ms.topic: tutorial
ms.date: 12/14/2019
ms.custom:
- mvc
- seodec18
- seo-python-october2019
ms.openlocfilehash: e0880cd1c16a8a0080551bbeaefe04f2f8dd705b
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681047"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>자습서: Azure App Service에서 PostgreSQL을 사용하여 Python(Django) 웹앱 실행

[Azure App Service](app-service-linux-intro.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 데이터 기반 Python Django 웹앱을 Azure Database for PostgreSQL 데이터베이스에 연결하고, Azure App Service에서 앱을 배포하고 실행하는 방법을 보여 줍니다.

![Azure App Service의 Python Django 웹앱](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Database for PostgreSQL 데이터베이스 만들기 및 웹앱 연결
> * Azure App Service에 웹앱 배포
> * 진단 로그 보기
> * Azure Portal에서 웹앱 관리

이 문서의 단계는 macOS, Linux 또는 Windows에서 수행할 수 있습니다. 이러한 단계는 대부분의 경우 비슷하지만, 차이점은 이 자습서에서 자세히 설명하지 않습니다. 아래 예제에서는 대부분 Linux에서 `bash` 터미널 창을 사용합니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 먼저 다음을 수행합니다.

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- [Git](https://git-scm.com/)를 설치합니다.
- [Python 3](https://www.python.org/downloads/)을 설치합니다.
- [PostgreSQL](https://www.postgresql.org/download/)을 설치하고 실행합니다.

## <a name="test-postgresql-installation-and-create-a-database"></a>PostgreSQL 설치 테스트 및 데이터베이스 만들기

먼저 로컬 PostgreSQL 서버에 연결하고, 데이터베이스를 만듭니다. 

로컬 터미널 창에서 기본 제공 `postgres` 사용자 권한으로 `psql`을 실행하여 로컬 PostgreSQL 서버에 연결합니다.

```bash
psql -U postgres
```

연결이 성공하면 PostgreSQL 데이터베이스가 실행되고 있습니다. 그렇지 않으면 [다운로드 - PostgreSQL 핵심 배포](https://www.postgresql.org/download/)에서 운영 체제에 대한 지침을 수행하여 로컬 PostgresQL 데이터베이스가 시작되도록 합니다.

*pollsdb*라는 새 데이터베이스를 만들고, 암호가 *supersecretpass*인 *manager*라는 데이터베이스 사용자를 설정합니다.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>로컬 Python 앱 만들기 및 실행

다음으로, 샘플 Python Django 웹앱을 설정하고 실행합니다.

[djangoapp](https://github.com/Azure-Samples/djangoapp) 샘플 리포지토리에는 Django 설명서의 [첫 번째 Django 앱 작성](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)을 수행하여 가져올 수 있는 데이터 기반 [Django](https://www.djangoproject.com/) 설문 조사 앱이 포함되어 있습니다.

### <a name="clone-the-sample-app"></a>샘플 앱 복제

터미널 창에서 다음 명령을 실행하여 샘플 앱 리포지토리를 복제하고, 새 작업 디렉터리로 변경합니다.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Python 가상 환경 구성

앱을 실행할 Python 가상 환경을 만들고 활성화합니다.

```bash
python3 -m venv venv
source venv/bin/activate
```
또는
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

`venv` 환경에서 *env.sh* 또는 *env.ps1*을 실행하여 *azuresite/settings.py*에서 데이터베이스 연결 설정에 사용할 환경 변수를 설정합니다.

```bash
source ./env.sh
```
또는
```PowerShell
.\env.ps1
```

*requirements.txt*에서 필수 패키지를 설치하고, [Django 마이그레이션](https://docs.djangoproject.com/en/2.1/topics/migrations/)을 실행하고, [관리 사용자를 만듭니다](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>웹앱 실행

관리 사용자가 만들어지면 Django 서버를 실행합니다.

```bash
python manage.py runserver
```

Django 웹앱이 완전히 로드되면 다음 메시지와 같은 내용이 반환됩니다.

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

브라우저에서 *http:\//localhost:8000*으로 이동합니다. 그러면 **사용할 수 있는 설문 조사가 없습니다**라는 메시지가 표시됩니다. 

*http:\//localhost:8000/admin*으로 이동하고, 마지막 단계에서 만든 관리 사용자를 사용하여 로그인합니다. **질문** 옆의 **추가**를 선택하고, 몇 가지 선택 항목이 있는 설문 조사 질문을 만듭니다.

![App Service에서 Python Django 앱을 로컬로 실행](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

*http:\//localhost:8000*으로 다시 이동하여 설문 조사 질문을 확인하고 질문에 대답합니다. 로컬 Django 샘플 애플리케이션에서 사용자 데이터를 작성하여 로컬 PostgreSQL 데이터베이스에 저장합니다.

Django 서버를 중지하려면 터미널에서 Ctrl+C를 입력합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

이 문서의 나머지 단계에서는 대부분 Azure Cloud Shell에서 Azure CLI 명령을 사용합니다. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 만들기 및 연결

이 섹션에서는 Azure Database for PostgreSQL 서버 및 데이터베이스를 만들고, 웹앱을 이 데이터베이스에 연결합니다. 웹앱을 Azure App Service에 배포하는 경우 앱에서 이 클라우드 데이터베이스를 사용합니다. 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure Database for PostgreSQL 서버에 대한 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있습니다. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure Database 서버 만들기

Cloud Shell에서 [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) 명령을 사용하여 PostgreSQL 서버를 만듭니다.

> [!NOTE]
> Azure Database for PostgreSQL을 만들기 전에 먼저 해당 지역에서 사용할 수 있는 [컴퓨팅 세대](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores)를 확인하세요. 지역에서 Gen4 하드웨어를 지원하지 않는 경우 다음 명령줄에서 *-sku-name*을 해당 지역에서 지원되는 값(예: Gen5)으로 변경합니다. 

다음 명령에서 *\<postgresql-name>* 을 고유한 서버 이름으로 바꿉니다. 서버 이름은 PostgreSQL 엔드포인트(*https://\<postgresql-name>.postgres.database.azure.com*)의 일부이므로 Azure의 모든 서버에서 고유해야 합니다. 

*\<resourcegroup-name>* 및 *\<region>* 을 사용하려는 리소스 그룹의 이름 및 지역으로 바꿉니다. *\<admin-username>* 및 *\<admin-password>* 의 경우 데이터베이스 관리자 계정에 대한 사용자 자격 증명을 만듭니다. 나중에 PostgreSQL 서버 및 데이터베이스에 로그인하는 데 사용할 *\<admin-username>* 및 *\<admin-password>* 를 잊지 마세요.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

Azure Database for PostgreSQL 서버가 만들어지면 Azure CLI에서 다음 예제와 같은 JSON 코드를 반환합니다.

```json
{
  "administratorLogin": "myusername",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westus",
  "name": "myservername",
  "resourceGroup": "myresourcegroup",
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

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버에 대한 방화벽 규칙 만들기

[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) 명령을 실행하여 Azure 리소스에서 데이터베이스에 액세스할 수 있도록 합니다. *\<postgresql-name>* 및 *\<resourcegroup-name>* 자리 표시자를 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> 이전 설정을 사용하면 Azure 네트워크 내의 모든 IP 주소에서 네트워크를 연결할 수 있습니다. 프로덕션에서 사용하려면 [앱에서 사용하는 아웃바운드 IP 주소만 허용](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)하여 가능한 가장 제한적인 방화벽 규칙을 구성해 보세요.

`firewall-rule create` 명령을 다시 실행하여 로컬 컴퓨터에서 액세스할 수 있도록 합니다. *\<your-ip-address>* 를 [로컬 IPv4 IP 주소](https://www.whatsmyip.org/)로 바꿉니다. *\<postgresql-name>* 및 *\<resourcegroup-name>* 자리 표시자를 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Azure Database for PostgreSQL 데이터베이스 만들기 및 연결

다음 명령을 실행하여 Azure Database for PostgreSQL 서버에 연결합니다. 사용자 고유의 *\<postgresql-name>* 및 *\<admin-username>* 을 사용하고, 사용자가 만든 암호를 사용하여 로그인합니다.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

로컬 PostgreSQL 서버에서와 마찬가지로 Azure Database for PostgreSQL 서버에서 데이터베이스와 사용자를 만듭니다.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> 관리 사용자를 사용하는 대신 특정 앱에 대해 제한된 권한이 있는 데이터베이스 사용자를 만드는 것이 가장 좋습니다. `manager` 사용자는 `pollsdb` 데이터베이스에 *대해서만* 모든 권한을 갖습니다.

`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Azure PostgreSQL 데이터베이스에 대한 앱 연결 테스트

*\< postgresql-name>* 을 Azure Database for PostgreSQL 서버 이름으로 바꿔 클라우드 PostgreSQL 데이터베이스를 가리키도록 로컬 *env.sh* 또는 *env.ps1* 파일을 편집합니다.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
또는
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

로컬 터미널 창의 `venv` 환경에서 편집된 *env.sh* 또는 *env.ps1*을 실행합니다. 
```bash
source ./env.sh
```
또는
```PowerShell
.\env.ps1
```

Azure 데이터베이스에 대한 Django 마이그레이션을 실행하고, 관리 사용자를 만듭니다.

```bash
python manage.py migrate
python manage.py createsuperuser
```

관리 사용자가 만들어지면 Django 서버를 실행합니다.

```bash
python manage.py runserver
```

브라우저에서 *http:\//localhost:8000*으로 이동합니다. 그러면 **사용할 수 있는 설문 조사가 없습니다**라는 메시지가 다시 표시됩니다. 

*http:\//localhost:8000/admin*으로 이동하고, 만든 관리 사용자를 사용하여 로그인하고, 이전처럼 설문 조사 질문을 만듭니다.

![App Service에서 Python Django 앱을 로컬로 실행](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

*http:\//localhost:8000*으로 다시 이동하여 표시된 설문 조사 질문을 확인합니다. 이제 앱에서 데이터를 Azure Database for PostgreSQL 데이터베이스에 쓰고 있습니다.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Azure App Service에 웹앱 배포

이 단계에서는 Azure Database for PostgreSQL 데이터베이스에 연결된 Python 앱을 Azure App Service에 배포합니다.

### <a name="configure-repository"></a>리포지토리 구성

이 자습서에서는 Django 샘플을 사용하므로 Azure App Service를 사용하도록 *djangoapp/azuresite/settings.py* 파일에서 일부 설정을 변경하고 추가해야 합니다. 

1. Django가 들어오는 요청에서 `HTTP_HOST` 헤더의 유효성을 검사합니다. Django 웹앱을 App Service에서 작동하려면 앱의 정규화된 도메인 이름을 허용된 호스트에 추가해야 합니다. 
   
   *azuresite/settings.py*를 편집하여 `ALLOWED_HOSTS` 줄을 다음과 같이 변경합니다.
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django는 [프로덕션에서 정적 파일 서비스](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)를 지원하지 않습니다. 이 자습서에서는 [WhiteNoise](https://whitenoise.evans.io/en/stable/)를 사용하여 파일 서비스를 사용하도록 설정합니다. WhiteNoise 패키지는 *requirements.txt*와 함께 이미 설치되어 있습니다. 
   
   Django에서 WhiteNoise를 사용하도록 구성하려면 *azuresite/settings.py*에서 `MIDDLEWARE` 설정을 찾아 `whitenoise.middleware.WhiteNoiseMiddleware`를 `django.middleware.security.SecurityMiddleware` 줄 바로 뒤의 목록에 추가합니다. `MIDDLEWARE` 설정은 다음과 비슷합니다.
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. *azuresite/settings.py*의 끝에 다음 줄을 추가합니다.
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   WhiteNoise를 구성하는 방법에 대한 자세한 내용은 [WhiteNoise 설명서](https://whitenoise.evans.io/en/stable/)를 참조하세요.

> [!IMPORTANT]
> 데이터베이스 설정 섹션은 이미 환경 변수를 사용하는 보안 모범 사례를 따릅니다. 전체 배포 추천 사항은 [Django 설명서: 배포 검사 목록](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)을 참조하세요.

변경 내용을 *djangoapp* 리포지토리의 포크에 커밋합니다.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>App Service 플랜 만들기

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>환경 변수 구성

자습서의 앞부분에서 환경 변수를 정의하여 PostgreSQL 데이터베이스에 연결했습니다.

Azure App Service에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용하여 환경 변수를 *앱 설정*으로 설정합니다.

Azure Cloud Shell에서 다음 명령을 실행하여 데이터베이스 연결 세부 정보를 앱 설정으로 지정합니다. *\<app-name>* , *\<resourcegroup-name>* 및 *\<postgresql-name>* 을 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

코드에서 이러한 앱 설정에 액세스하는 방법에 대한 자세한 내용은 [환경 변수 액세스](how-to-configure-python.md#access-environment-variables)를 참조하세요.

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

App Service 배포 서버는 리포지토리 루트에서 *requirements.txt*를 살펴보고 `git push` 후에 Python 패키지 관리를 자동으로 실행합니다.

### <a name="browse-to-the-azure-app"></a>Azure 앱 찾아보기

*http:\//\<app-name>.azurewebsites.net* URL을 사용하여 배포된 앱으로 이동합니다. 앱을 처음 요청하는 경우 컨테이너를 다운로드하여 실행해야 하므로 시작하는 데 다소 시간이 걸립니다. 페이지가 시간 초과 또는 오류 메시지를 표시하는 경우 몇 분 정도 기다렸다가 페이지를 새로 고칩니다.

이전에 만든 설문 조사 질문이 표시됩니다. 

App Service는 `manage.py startproject`에서 기본적으로 만드는 *wsgi.py* 파일을 각 하위 디렉터리에서 찾아 리포지토리에서 Django 프로젝트를 검색합니다. App Service에서 파일을 찾으면 Django 웹앱이 로드됩니다. App Service에서 Python 앱을 로드하는 방법에 대한 자세한 내용은 [기본 제공 Python 이미지 구성](how-to-configure-python.md)을 참조하세요.

*http:\//\<app-name>.azurewebsites.net/admin*으로 이동하고, 만든 관리 사용자를 사용하여 로그인합니다. 원하는 경우 몇 가지 추가 설문 조사 질문을 만듭니다.

![Azure의 App Service에서 Python Django 앱 실행](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**축하합니다.** Linux용 Azure App Service에서 Python(Django) 웹앱이 실행되고 있습니다.

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Azure Portal에서 앱 관리

[Azure Portal](https://portal.azure.com)에서 만든 앱을 검색하여 선택합니다.

![Azure Portal에서 Python Django 앱으로 이동합니다.](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

기본적으로 포털에 앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기서는 찾아보기, 중지, 다시 시작 및 삭제와 같은 기본 관리 작업을 수행할 수도 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 개요 페이지에서 Python Django 앱 관리](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

다음 자습서로 이동하여 사용자 지정 DNS 이름을 앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 앱에 사용자 지정 DNS 이름 매핑](../app-service-web-tutorial-custom-domain.md)

또는 다른 리소스를 확인합니다.

> [!div class="nextstepaction"]
> [Python 앱 구성](how-to-configure-python.md)
