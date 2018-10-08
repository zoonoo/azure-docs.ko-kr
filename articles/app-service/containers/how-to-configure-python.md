---
title: Azure App Service에서 기본 제공 Python 이미지 구성
description: 이 자습서에서는 기본 제공 Python 이미지를 사용하여 Azure App Service에서 Python 응용 프로그램을 제작 및 구성하는 옵션을 설명합니다.
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
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228555"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>Azure App Service에서 기본 제공 Python 이미지 구성(미리 보기)

이 문서에서는 Python 응용 프로그램을 실행하도록 [Linux의 App Service](app-service-linux-intro.md)에서 Python 기본 제공 이미지를 구성하는 방법을 보여줍니다.

## <a name="python-version"></a>Python 버전

Linux의 App Service에서 Python 런타임은 `python-3.7.0` 버전을 사용합니다.

## <a name="supported-frameworks"></a>지원되는 프레임워크

`python-3.7` 런타임과 호환되는 모든 버전의 WSGI(웹 서버 게이트웨이 인터페이스) 호환 웹 프레임워크가 지원됩니다.

## <a name="package-management"></a>패키지 관리

Git 게시 중 Kudu 엔진은 리포지토리 루트에서 [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files)를 찾고 `pip`를 사용하여 패키지를 자동으로 설치합니다.

게시 전에 이 파일을 생성하려면 리포지토리 루트로 이동하고 Python 환경에서 다음 명령을 실행합니다.

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>Python 앱 구성

App Service에서 기본 제공 Python 이미지는 [Gunicorn](http://gunicorn.org/) 서버를 사용하여 Python 응용 프로그램을 실행합니다. Gunicorn은 UNIX용 Python WSGI HTTP 서버입니다. App Service는 Django 및 Flask 프로젝트에 대한 Gunicorn을 자동으로 구성합니다.

### <a name="django-app"></a>Django 앱

`wsgi.py` 모듈을 포함하는 Django 프로젝트를 게시하는 경우 Azure는 다음 명령을 사용하여 Gunicorn을 자동으로 호출합니다.

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Flask 앱

Flask 앱을 게시하고, 진입점이 `application.py` 또는 `app.py` 모듈에 있는 경우 Azure는 각각 다음 명령 중 하나를 사용하여 Gunicorn을 자동으로 호출합니다.

```bash
gunicorn application:app
```

또는 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>시작 사용자 지정

앱에 대한 사용자 지정 진입점을 정의하려면 먼저 사용자 지정 Gunicorn 명령을 사용하여 _.txt_ 파일을 만들고 프로젝트의 루트에 배치합니다. 예를 들어 모듈 _helloworld.py_ 및 변수 `app`을 사용하여 서버를 시작하려면 다음 콘텐츠로 _startup.txt_를 만듭니다.

```bash
gunicorn helloworld:app
```

[응용 프로그램 설정](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 페이지에서 **런타임 스택**으로 **Python|3.7**을 선택하고 이전 단계에서 **시작 파일**의 이름을 제공합니다. 예를 들어 _startup.txt_와 같습니다.
