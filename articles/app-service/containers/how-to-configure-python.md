---
title: Linux의 Azure App Service용 Python 앱 구성
description: 이 자습서에서는 Linux의 Azure App Service용 Python 앱을 작성하고 구성하는 옵션을 설명합니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/09/2018
ms.author: astay;cephalin;kraigb
ms.custom: mvc
ms.openlocfilehash: 9474b2d64c97b6e6d0fc06c3c448fa6e0515e70c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633651"
---
# <a name="configure-your-python-app-for-the-azure-app-service-on-linux"></a>Linux의 Azure App Service용 Python 앱 구성

이 문서에서는 [Linux의 Azure App Service](app-service-linux-intro.md)에서 Python 앱을 실행하는 방법 및 필요한 경우 App Service의 동작을 사용자 지정하는 방법에 대해 설명합니다.

## <a name="set-python-version"></a>Python 버전 설정

두 가지 기본 이미지 Python 3.6 및 Python 3.7을 사용할 수 있습니다. 원하는 Python 기반 이미지를 사용하여 앱을 만들 수 있습니다. 예를 들어 Python 3.7을 사용하여 앱을 만들려면 Cloud Shell에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp create --resource-group <group_name> --plan <plan_name> --name <app_name> --runtime "PYTHON|3.7"
```

Python 버전(기본 이미지)을 Python 3.6으로 변경하려면 Cloud Shell에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <group_name> --name <app_name> --linux-fx-version "PYTHON|3.6"
```

다른 버전의 Python이 필요한 경우 대신 자신의 컨테이너 이미지를 빌드하고 배포해야 합니다. 자세한 내용은 [Web App for Containers에 사용자 지정 Docker 이미지 사용](tutorial-custom-docker-image.md)을 참조하세요.

## <a name="container-characteristics"></a>컨테이너 특성

Linux 기반 App Service에 배포된 Python 앱은 GitHub 리포지토리에 정의된 Docker 컨테이너 [Python 3.6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) 또는 [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0) 내에서 실행됩니다.

이 컨테이너에는 다음과 같은 특성이 있습니다.

- 앱은 `--bind=0.0.0.0 --timeout 600` 추가 인수를 통해 [Gunicorn WSGI HTTP 서버](http://gunicorn.org/)를 사용하여 실행됩니다.

- 기본적으로 기본 이미지에는 Flask 웹 프레임워크가 포함되어 있지만, 컨테이너는 WSGI 및 Python 3.7과 호환되는 다른 프레임워크(예: Django)를 지원합니다.

- Django와 같은 추가 패키지를 설치하려면 `pip freeze > requirements.txt`를 사용하여 프로젝트의 루트에 [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) 파일을 만듭니다. 그런 다음, Git 배포를 사용하여 App Service에 앱을 게시합니다. Git 배포는 컨테이너에서 `pip install -r requirements.txt`를 자동으로 실행하여 앱의 종속성을 설치합니다.

## <a name="container-startup-process-and-customizations"></a>컨테이너 시작 프로세스 및 사용자 지정

시작하는 동안 Linux의 App Service 컨테이너에서 실행하는 단계는 다음과 같습니다.

1. 제공되는 경우 사용자 지정 시작 명령을 확인하고 적용합니다.
1. Django 앱의 *wsgi.py* 파일이 있는지 확인합니다. 그렇다면 해당 파일을 사용하여 Gunicorn을 시작합니다.
1. *application.py*라는 파일이 있는지 확인합니다. 그렇다면 Flask 앱이라는 가정하에 `application:app`을 사용하여 Gunicorn을 시작합니다.
1. 다른 앱이 없으면 컨테이너에 기본적으로 제공되는 기본 앱을 시작합니다.

다음 섹션에서는 각 옵션에 대한 추가 세부 정보를 제공합니다.

### <a name="django-app"></a>Django 앱

Django 앱의 경우 App Service는 앱 코드 내에서 `wsgi.py`라는 파일을 찾고, 다음 명령을 사용하여 Gunicorn을 실행합니다.

```bash
# <module> is the path to the folder containing wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

시작 명령에 대해 더 구체적으로 제어하려면 [사용자 지정 시작 명령](#custom-startup-command)을 사용하고, `<module>`을 *wsgi.py*가 포함된 모듈의 이름으로 바꿉니다.

### <a name="flask-app"></a>Flask 앱

Flask의 경우 App Service는 *application.py*라는 파일을 찾고, Gunicorn을 다음과 같이 시작합니다.

```bash
gunicorn --bind=0.0.0.0 --timeout 600 application:app
```

기본 앱 모듈이 다른 파일에 포함되어 있거나, 앱 개체에 대해 다른 이름을 사용하거나, Gunicorn에 인수를 추가로 제공하려면 [사용자 지정 시작 명령](#custom-startup-command)을 사용합니다. 이 섹션에서는 *hello.py*의 항목 코드와 `myapp`이라는 Flask 앱 개체를 사용하여 Flask에 대한 예제를 제공합니다.

### <a name="custom-startup-command"></a>사용자 지정 시작 명령

사용자 지정 Gunicorn 시작 명령을 제공하여 컨테이너의 시작 동작을 제어할 수 있습니다. 예를 들어 기본 모듈이 *hello.py*이고 해당 파일에서 Flask 앱 개체의 이름이 `myapp`인 Flask 앱이 있는 경우 명령은 다음과 같습니다.

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

기본 모듈이 하위 폴더(예: `website`)인 경우 `--chdir` 인수로 이 폴더를 지정합니다.

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

또한 Gunicorn에 대한 추가 인수(예: `--workers=4`)를 명령에 추가할 수도 있습니다. 자세한 내용은 [Gunicorn 실행](http://docs.gunicorn.org/en/stable/run.html)(docs.gunicorn.org)을 참조하세요.

사용자 지정 명령을 제공하려면 다음 단계를 수행합니다.

1. Azure Portal에서 [응용 프로그램 설정](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 페이지로 이동합니다.

1. **런타임** 설정에서 **스택** 옵션을 **Python 3.7**로 설정하고, **시작 파일** 필드에 명령을 직접 입력합니다.

    또는 *startup.txt*(또는 원하는 이름)와 같은 이름을 사용하여 프로젝트의 루트에 있는 텍스트 파일에 명령을 저장할 수 있습니다. 그런 다음, 해당 파일을 App Service에 배포하고, **시작 파일** 필드에 파일 이름을 대신 지정합니다. 이 옵션을 사용하면 Azure Portal이 아닌 소스 코드 리포지토리리포지토리 내에서 명령을 관리할 수 있습니다.

1. **저장**을 선택합니다. App Service가 자동으로 다시 시작되고, 몇 초 후에 적용된 사용자 지정 시작 명령이 표시됩니다.

> [!Note]
> App Service는 사용자 지정 명령 파일을 처리할 때 발생하는 모든 오류를 무시한 다음, Django 및 Flask 앱을 찾아 시작 프로세스를 계속 진행합니다. 예상한 동작이 표시되지 않으면 시작 파일이 App Service에 배포되고 오류가 없는지 확인합니다.

### <a name="default-behavior"></a>기본 동작

App Service에서 사용자 지정 명령, Django 앱 또는 Flask 앱을 찾지 못하면 _opt/defaultsite_ 폴더에 있는 기본 읽기 전용 앱을 실행합니다. 기본 앱은 다음과 같이 표시됩니다.

![Linux의 App Service 기본 웹 페이지](media/how-to-configure-python/default-python-app.png)

## <a name="troubleshooting"></a>문제 해결

- **사용자 고유의 앱 코드가 배포되면 기본 앱이 표시됩니다.**  실제로 코드를 App Service에 앱 배포하지 않았거나 App Service에서 앱 코드를 찾지 못하여 기본 앱을 대신 실행했기 때문에 기본 앱이 표시됩니다.
  - App Service를 다시 시작하고, 15-20초 동안 기다린 다음, 앱을 다시 확인합니다.
  - Windows 기반 인스턴스보다는 Linux용 App Service를 사용해야 합니다. Azure CLI에서 명령 `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`을 실행하여 `<resource_group_name>` 및 `<app_service_name>`을 적절하게 대체합니다. `app,linux`가 출력으로 표시되어야 합니다. 그렇지 않으면 App Service를 다시 만들고 Linux를 선택합니다.
    - SSH 또는 Kudu 콘솔을 사용하여 App Service에 직접 연결하고, 파일이 *site/wwwroot* 아래에 있는지 확인합니다. 파일이 없으면 배포 프로세스를 검토하고 앱을 다시 배포합니다.
  - 파일이 있으면 App Service에서 특정 시작 파일을 식별하지 못한 것입니다. 앱이 [Django](#django-app) 또는 [Flask](#flask-app)에 대해 예상되는 App Service로 구성되었는지 확인하거나 [사용자 지정 시작 명령](#custom-startup-command)을 사용합니다.
  
- **브라우저에 "서비스를 사용할 수 없음"이라는 메시지가 표시됩니다.** App Service에서 Gunicorn 서버를 시작했음을 나타내는 App Service의 응답을 기다리는 동안 브라우저에서 시간이 초과되었지만 앱 코드를 지정하는 인수가 올바르지 않습니다.
  - 특히 App Service 계획에서 가장 낮은 가격 책정 계층을 사용하는 경우 브라우저를 새로 고칩니다. 예를 들어 체험 계층을 사용하는 경우 앱을 시작하는 데 시간이 더 오래 걸릴 수 있으며, 브라우저를 새로 고친 후에 응답하게 됩니다.
  - 앱이 [Django](#django-app) 또는 [Flask](#flask-app)에 대해 예상되는 App Service로 구성되었는지 확인하거나 [사용자 지정 시작 명령](#custom-startup-command)을 사용합니다.
  - SSH 또는 Kudu 콘솔을 사용하여 App Service에 연결한 다음, *LogFiles* 폴더에 저장된 진단 로그를 검사합니다. 로깅에 대한 자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 설정](../web-sites-enable-diagnostic-log.md)을 참조하세요.
