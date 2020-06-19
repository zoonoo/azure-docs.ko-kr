---
title: '자습서: Postgres로 Python(Django) 배포'
description: PostgreSQL 데이터베이스를 사용하여 Python 앱을 만들고 Linux의 Azure App Service에 배포하는 방법에 대해 알아봅니다. 이 자습서에서는 데모를 위해 Django 샘플 앱을 사용합니다.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- tracking-python
ms.openlocfilehash: 29aeae7683c46b1e10acdf1b2c4a7183c22eb408
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807327"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>자습서: Azure App Service에서 PostgreSQL을 사용하는 Python(Django) 웹앱 배포

이 자습서에서는 데이터 기반 Python(Django) 웹앱을 [Azure App Service](app-service-linux-intro.md)에 배포하고 Azure Database for PostgreSQL 데이터베이스에 연결하는 방법을 보여 줍니다. App Service는 확장성이 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다.

![Azure App Service에 Python Django 웹앱 배포](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Database for PostgreSQL 데이터베이스 만들기
> * Azure App Service에 코드 배포 및 Postgres에 연결
> * 코드 업데이트 및 다시 배포
> * 진단 로그 보기
> * Azure Portal에서 웹앱 관리

이 문서의 단계는 macOS, Linux 또는 Windows에서 수행할 수 있습니다.

## <a name="install-dependencies"></a>종속성 설치

이 자습서를 시작하기 전에 먼저 다음을 수행합니다.

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
- [Git](https://git-scm.com/)를 설치합니다.
- [Python 3](https://www.python.org/downloads/)을 설치합니다.

## <a name="clone-the-sample-app"></a>샘플 앱 복제

터미널 창에서 다음 명령을 실행하여 샘플 앱 리포지토리를 복제하고, 리포지토리 루트로 변경합니다.

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

djangoapp 샘플 리포지토리에는 Django 설명서의 [첫 번째 Django 앱 작성](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)을 수행하여 가져올 수 있는 데이터 기반 [Django](https://www.djangoproject.com/) 설문 조사 앱이 포함되어 있습니다. 편의를 위해 여기에 제공됩니다.

## <a name="prepare-app-for-app-service"></a>App Service 앱 준비

많은 Python 웹 프레임워크와 마찬가지로 Django는 [프로덕션 서버에서 실행되기 전에 특정 설정을 변경](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)해야 하며, App Service와 다르지 않습니다. 앱이 App Service에 배포되면 작동하도록 기본 *azuresite/settings.py* 파일에서 일부 설정을 변경하고 추가해야 합니다. 

App Service에 필요한 구성을 수행하는 *azuresite/production.py*를 살펴보세요. 대략적으로 다음을 수행합니다.

- *azuresite/settings.py*의 모든 설정을 상속합니다.
- App Service 앱의 정규화된 도메인 이름을 허용된 호스트에 추가합니다. 
- Django는 기본적으로 프로덕션에서 정적 파일을 제공하지 않으므로 [WhiteNoise](https://whitenoise.evans.io/en/stable/)를 사용하여 프로덕션에서 정적 파일을 처리하도록 설정합니다. WhiteNoise 패키지는 *requirements.txt*에 이미 포함되어 있습니다.
- PostgreSQL 데이터베이스 구성을 추가합니다. Django는 기본적으로 Sqlite3를 데이터베이스로 사용하지만 프로덕션 앱에는 적합하지 않습니다. [psycopg2-binary](https://pypi.org/project/psycopg2-binary/) 패키지는 이미 *requirements.txt*에 포함되어 있습니다.
- Postgres 구성은 환경 변수를 사용합니다. 나중에 App Service에서 환경 변수를 설정하는 방법에 대해 알아봅니다.

*azuresite/production.py*는 편의상 리포지토리에 포함되지만 아직 앱에서 사용되지 않습니다. App Service에서 해당 설정이 사용되도록 하려면 이 파일에 액세스할 수 있도록 *manage.py* 및 *azuresite/wsgi.py*라는 두 파일을 구성해야 합니다.

- *manage.py*에서 다음 줄을

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    다음 코드로 이동:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    `DJANGO_ENV` 환경 변수는 나중에 App Service 앱을 구성할 때 설정됩니다.

- *azuresite/wsgi.py*에서 위와 동일하게 변경합니다.

    App Service에서는 *manage.py*를 사용하여 데이터베이스 마이그레이션을 실행하고, *azuresite/wsgi.py*를 사용하여 프로덕션에서 Django 앱을 실행합니다. 두 파일을 이렇게 변경하면 두 경우 모두에서 프로덕션 설정이 사용됩니다.

## <a name="sign-in-to-azure-cli"></a>Azure CLI에 로그인

Azure CLI가 이미 설치되어 있어야 합니다. [Azure CLI](/cli/azure/what-is-azure-cli)를 사용하면 명령줄 터미널에서 Azure 리소스를 사용할 수 있습니다. 

Azure에 로그인하려면 [`az login`](/cli/azure/reference-index#az-login) 명령을 실행합니다.

```azurecli
az login
```

터미널의 지침에 따라 Azure 계정에 로그인합니다. 완료되면 구독이 터미널 출력에서 JSON 형식으로 표시됩니다.

## <a name="create-postgres-database-in-azure"></a>Azure에서 Postgres 데이터베이스 만들기

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

이 섹션에서는 Azure Database for PostgreSQL 서버 및 데이터베이스를 만듭니다. 시작하려면 다음 명령을 사용하여 `db-up` 확장을 설치합니다.

```azurecli
az extension add --name db-up
```

다음 예제와 같이 Azure에서 [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) 명령을 사용하여 Postgres 데이터베이스를 만듭니다. *\<postgresql-name>* 를 *고유* 이름으로 바꿉니다(서버 엔드포인트는 *https://\<postgresql-name>.postgres.database.azure.com*임). *\<admin-username>* 및 *\<admin-password>* 의 경우 이 Postgres 서버에 대한 관리자 사용자를 만들기 위한 자격 증명을 지정합니다.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

이 명령은 다음 작업을 수행하므로 시간이 좀 걸릴 수 있습니다.

- 없는 경우 `myResourceGroup`이라고 하는 [리소스 그룹](../../azure-resource-manager/management/overview.md#terminology)을 만듭니다. 모든 Azure 리소스는 다음 중 하나에 있어야 합니다. `--resource-group`는 선택 사항입니다.
- 관리 사용자 권한으로 Postgres 서버를 만듭니다.
- `pollsdb` 데이터베이스를 만듭니다.
- 로컬 IP 주소에서의 액세스를 허용합니다.
- Azure 서비스에서의 액세스를 허용합니다.
- `pollsdb` 데이터베이스에 액세스할 수 있는 데이터베이스 사용자를 만듭니다.

모든 단계는 다른 `az postgres` 및 `psql` 명령으로 개별적으로 수행할 수 있지만 `az postgres up`은 이러한 모든 단계를 한 번에 수행합니다.

명령이 완료되면 `Ran Database Query:`가 있는 출력 줄을 찾습니다. `root` 사용자 이름 및 `Pollsdb1` 암호를 사용하여 사용자를 위해 만든 데이터베이스의 사용자를 표시합니다. 이러한 정보는 나중에 앱을 데이터베이스에 연결하는 데 사용합니다.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/) 중 하나로 설정할 수 있습니다. 구독에 사용할 수 있는 지역은 [`az account list-locations`](/cli/azure/account#az-account-list-locations) 명령을 사용하여 가져올 수 있습니다. 프로덕션 앱의 경우 데이터베이스와 앱을 동일한 위치에 배치합니다.

## <a name="deploy-the-app-service-app"></a>App Service 앱 배포

이 섹션에서는 App Service 앱을 만듭니다. 이 앱을 사용자가 만든 Postgres 데이터베이스에 연결하고 코드를 배포합니다.

### <a name="create-the-app-service-app"></a>App Service 앱 만들기

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

앱이 리포지토리 루트(`djangoapp`) 디렉터리에서 배포되므로 이 디렉터리로 다시 이동해야 합니다.

다음 예제와 같이 [`az webapp up`](/cli/azure/webapp#az-webapp-up) 명령을 사용하여 App Service 앱을 만듭니다. *\<app-name>* 를 *고유* 이름으로 바꿉니다(서버 엔드포인트는 *https://\<app-name>.azurewebsites.net*임). *\<app-name>* 에 허용되는 문자는 `A`-`Z`, `0`-`9` 및 `-`입니다.

```azurecli
az webapp up --plan myAppServicePlan --location westus2 --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

이 명령은 다음 작업을 수행하므로 시간이 좀 걸릴 수 있습니다.

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- [리소스 그룹](../../azure-resource-manager/management/overview.md#terminology)을 자동으로 생성합니다.
- *myAppServicePlan*이라는 [App Service 계획](../overview-hosting-plans.md)이 기본 가격 책정 계층(B1)에 없는 경우 이 계획을 만듭니다. `--plan` 및 `--sku`는 선택 사항입니다.
- 없는 경우 App Service 앱을 만듭니다.
- 아직 사용하도록 설정되지 않은 경우 기본 앱 로깅을 사용하도록 설정합니다.
- 빌드 자동화를 사용하도록 설정된 ZIP 배포를 사용하여 리포지토리를 업로드합니다.

배포가 완료되면 다음과 같은 JSON 출력이 표시됩니다.

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

*\<app-resource-group>* 의 값을 복사합니다. 이는 나중에 앱을 구성하는 데 필요합니다. 

> [!TIP]
> 관련 설정은 리포지토리의 숨겨진 *.azure* 디렉터리에 저장됩니다. 나중에 간단한 명령을 사용하여 변경 내용을 배포하고 다음을 사용하여 진단 로그를 즉시 사용하도록 설정할 수 있습니다.
> 
> ```azurecli
> az webapp up
> ```

이제 샘플 코드가 배포되었지만 앱이 아직 Azure의 Postgres 데이터베이스에 연결되지 않았습니다. 이렇게 하려면 다음을 수행합니다.

### <a name="configure-environment-variables"></a>환경 변수 구성

앱을 로컬로 실행하는 경우 터미널 세션에서 환경 변수를 설정할 수 있습니다. App Service에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 명령을 사용하여 *앱 설정*을 통해 이 작업을 수행합니다.

다음 명령을 실행하여 데이터베이스 연결 세부 정보를 앱 설정으로 지정합니다. *\<app-name>* , *\<app-resource-group>* 및 *\<postgresql-name>* 를 사용자 고유의 값으로 바꿉니다. `root` 및 `Pollsdb1` 사용자 자격 증명은 `az postgres up`을 통해 만들어졌습니다.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

코드에서 이러한 앱 설정에 액세스하는 방법에 대한 자세한 내용은 [환경 변수 액세스](how-to-configure-python.md#access-environment-variables)를 참조하세요.

### <a name="run-database-migrations"></a>데이터베이스 마이그레이션 실행

App Service에서 데이터베이스 마이그레이션을 실행하려면 *https://\<app-name>.scm.azurewebsites.net/webssh/host*로 이동하여 브라우저에서 SSH 세션을 엽니다.

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

SSH 세션에서 다음 명령을 실행합니다.

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install packages
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Azure 앱 찾아보기

브라우저에서 *http:\//\<app-name>.azurewebsites.net* URL을 사용하여 배포된 앱으로 이동합니다. 그러면 **사용할 수 있는 설문 조사가 없습니다**라는 메시지가 표시됩니다. 

*http:\//\<app-name>.azurewebsites.net/admin*으로 이동하고 마지막 단계에서 만든 관리 사용자를 사용하여 로그인합니다. **질문** 옆의 **추가**를 선택하고, 몇 가지 선택 항목이 있는 설문 조사 질문을 만듭니다.

*http:\//\<app-name>.azurewebsites.net* URL을 사용하여 배포된 앱으로 이동하여 몇 가지 설문 조사 질문을 만듭니다. *http:\//\<app-name>.azurewebsites.net/* 에서 질문을 확인할 수 있습니다. 

![Azure의 App Service에서 Python Django 앱 실행](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

*http:\//\<app-name>.azurewebsites.net* URL을 사용하여 배포된 앱으로 다시 이동하여 설문 조사 질문을 확인하고 질문에 답변합니다.

App Service는 `manage.py startproject`에서 기본적으로 만드는 *wsgi.py* 파일을 각 하위 디렉터리에서 찾아 리포지토리에서 Django 프로젝트를 검색합니다. App Service에서 파일을 찾으면 Django 웹앱이 로드됩니다. App Service에서 Python 앱을 로드하는 방법에 대한 자세한 내용은 [기본 제공 Python 이미지 구성](how-to-configure-python.md)을 참조하세요.

**축하합니다.** Linux용 Azure App Service에서 Python(Django) 웹앱이 실행되고 있습니다.

## <a name="develop-app-locally-and-redeploy"></a>로컬로 앱 개발 및 다시 배포

이 섹션에서는 로컬 환경에서 앱을 개발하고 코드를 App Service에 다시 배포합니다.

### <a name="set-up-locally-and-run"></a>로컬로 설정 및 실행

로컬 개발 환경을 설정하고 샘플 앱을 처음으로 실행하려면 다음 명령을 실행합니다.

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Django 웹앱이 완전히 로드되면 다음 메시지와 같은 내용이 반환됩니다.

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

브라우저에서 *http:\//localhost:8000*으로 이동합니다. 그러면 **사용할 수 있는 설문 조사가 없습니다**라는 메시지가 표시됩니다. 

*http:\//localhost:8000/admin*으로 이동하고, 마지막 단계에서 만든 관리 사용자를 사용하여 로그인합니다. **질문** 옆의 **추가**를 선택하고, 몇 가지 선택 항목이 있는 설문 조사 질문을 만듭니다.

![App Service에서 Python Django 앱을 로컬로 실행](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

*http:\//localhost:8000*으로 다시 이동하여 설문 조사 질문을 확인하고 질문에 대답합니다. 로컬 Django 샘플 애플리케이션은 사용자 데이터를 로컬 Sqlite3 데이터베이스에 쓰고 저장하므로 프로덕션 데이터베이스 손상에 대해 걱정할 필요가 없습니다. 개발 환경이 Azure 환경과 일치하도록 하려면 Postgres 데이터베이스를 로컬로 대신 사용하는 것이 좋습니다.

Django 서버를 중지하려면 Ctrl+C를 입력합니다.

### <a name="update-the-app"></a>앱 업데이트

앱 업데이트가 작동하는 방법을 확인하려면 `polls/models.py`를 약간 변경합니다. 다음과 같은 줄을 찾습니다.

<pre>
choice_text = models.CharField(max_length=200)
</pre>

그리고 다음과 같이 변경합니다.

```python
choice_text = models.CharField(max_length=100)
```

데이터 모델이 변경되면 새 Django 마이그레이션을 만들어야 합니다. 다음 명령을 사용하여 이 작업을 수행합니다.

```
python manage.py makemigrations
```

마이그레이션을 실행하고 개발 서버를 실행하고, *http:\//localhost:8000/admin*으로 이동하여 변경 내용을 로컬로 테스트할 수 있습니다.

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Azure에 코드 다시 배포

변경 내용을 다시 배포하려면 리포지토리 루트에서 다음 명령을 실행합니다.

```azurecli
az webapp up
```

App Service에서 앱이 있는지 검색하고 코드만 배포합니다.

### <a name="rerun-migrations-in-azure"></a>Azure에서 마이그레이션 다시 실행

데이터 모델이 변경되었으므로 App Service에서 데이터베이스 마이그레이션을 다시 실행해야 합니다. *https://\<app-name>.scm.azurewebsites.net/webssh/host*로 이동하여 브라우저에서 SSH 세션을 엽니다. 다음 명령을 실행합니다.

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>프로덕션에서 앱 검토

*http:\//\<app-name>.azurewebsites.net*으로 이동하여 프로덕션에서 라이브로 실행되는 변경 내용을 확인합니다. 

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림

컨테이너 내부에서 생성된 콘솔 로그에 액세스할 수 있습니다.

> [!TIP]
> `az webapp up`은 사용자의 기본 로깅을 설정합니다. 성능상의 이유로 이 로깅은 일정 시간 후에 자체적으로 해제되지만, `az webapp up`을 다시 실행할 때마다 다시 설정됩니다. 수동으로 설정하려면 다음 명령을 실행합니다.
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

다음 Azure CLI 명령을 실행하여 로그 스트림을 확인합니다.

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

콘솔 로그가 즉시 표시되지 않으면 30초 후에 다시 확인합니다.

> [!NOTE]
> `https://<app-name>.scm.azurewebsites.net/api/logs/docker`의 브라우저에서 로그 파일을 검사할 수도 있습니다.

언제든지 로그 스트리밍을 중지하려면 `Ctrl`+`C`를 입력합니다.

## <a name="manage-your-app-in-the-azure-portal"></a>Azure Portal에서 앱 관리

[Azure Portal](https://portal.azure.com)에서 만든 앱을 검색하여 선택합니다.

![Azure Portal에서 Python Django 앱으로 이동합니다.](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

기본적으로 포털에 앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기서는 찾아보기, 중지, 다시 시작 및 삭제와 같은 기본 관리 작업을 수행할 수도 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 개요 페이지에서 Python Django 앱 관리](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>리소스 정리

나중에 이러한 리소스가 필요하지 않으면 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Database for PostgreSQL 데이터베이스 만들기
> * Azure App Service에 코드 배포 및 Postgres에 연결
> * 코드 업데이트 및 다시 배포
> * 진단 로그 보기
> * Azure Portal에서 웹앱 관리

다음 자습서로 이동하여 사용자 지정 DNS 이름을 앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 앱에 사용자 지정 DNS 이름 매핑](../app-service-web-tutorial-custom-domain.md)

또는 다른 리소스를 확인합니다.

> [!div class="nextstepaction"]
> [Python 앱 구성](how-to-configure-python.md)
