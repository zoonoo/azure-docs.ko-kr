---
title: "Linux에서 Django를 사용하는 Python 웹앱 | Microsoft Docs"
description: "Azure에서 Linux 가상 컴퓨터를 사용하여 Django 기반 웹 응용 프로그램을 호스트하는 방법에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 11/17/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 25a6507e3d7b473d9985114de4061da8c602e987
ms.lasthandoff: 03/25/2017


---
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Linux VM에서 Django Hello World 웹 응용 프로그램
> [!div class="op_single_selector"]
> * [Windows](windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](virtual-machines-linux-python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

이 자습서에서는 Microsoft Azure에서 Linux 가상 컴퓨터를 사용하여 Django 기반 웹사이트를 호스트하는 방법을 설명합니다. 이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 가이드를 완료하면 클라우드에서 Django 기반 응용 프로그램을 실행할 수 있게 됩니다.

다음 방법에 대해 알아봅니다.

* Django를 호스트하도록 Azure 가상 컴퓨터 설정. 이 자습서에서는 **Linux**에서 설정하는 방법을 설명하지만 Azure에서 호스트되는 Windows Server VM에서도 같은 작업을 수행할 수 있습니다. 
* Linux에서 새 Django 응용 프로그램 만들기

이 자습서의 지침에 따라 간단한 Hello World 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 Azure 가상 컴퓨터에서 호스트됩니다.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure의 hello world 페이지를 표시하는 브라우저 창](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Django를 호스트하기 위해 Azure 가상 컴퓨터 만들기 및 구성
1. [여기](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 나와 있는 지침에 따라 *Ubuntu Server 14.04 LTS* 배포판의 Azure 가상 컴퓨터를 만듭니다.  원하는 경우 SSH 공용 키 대신 암호 인증을 선택할 수 있습니다.
2. 들어오는 HTTP 트래픽을 포트 80에 허용하려면 [여기](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)의 지침을 사용하여 네트워크 보안 그룹을 편집합니다.
3. 기본적으로 새 가상 컴퓨터에는 정규화된 도메인 이름 (FQDN)이 없습니다.  [여기](virtual-machines-linux-portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 지침에 따라서 만들 수 있습니다.  이 단계는 자습서를 완료하기 위한 선택 사항입니다.

## <a id="setup"> </a>개발 환경 설정
**참고:** Python을 설치해야 하거나 클라이언트 라이브러리를 사용하고 싶으면 [Python 설치 가이드](../python-how-to-install.md)를 참조하세요.

Ubuntu Linux VM에는 Python 2.7이 사전 설치되어 있지만, Apache 또는 django는 설치되어 있지 않습니다.  다음 단계에 따라 VM에 연결하고 Apache 및 django를 설치하십시오.

1. 새 **터미널** 창을 시작합니다.
2. 다음 명령을 입력하여 Azure VM에 연결합니다.  FQDN을 만들지 않았으면, Azure 클래식 포털의 가상 컴퓨터 요약에 표시되는 공용 IP 주소를 사용하여 연결할 수 있습니다.
   
       $ ssh yourusername@yourVmUrl
3. 다음 명령을 입력하여 django를 설치합니다.
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. 다음 명령을 입력하여 Apache를 mod-wsgi와 함께 설치합니다.
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="creating-a-new-django-application"></a>새 Django 응용 프로그램 만들기
1. 이전 섹션에서 사용한 **터미널** 창을 열고 ssh로 VM에 연결합니다.
2. 다음 명령을 입력하여 새 Django 프로젝트를 만듭니다.
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   **django-admin.py** 스크립트는 Django 기반 웹 사이트의 기본 구조를 생성합니다.
   
   * **helloworld/manage.py** 는 Django 기반 웹 사이트의 호스팅을 시작 및 중지하는 데 도움이 됩니다.
   * **helloworld/helloworld/settings.py** 에는 응용 프로그램에 대한 Django 설정이 포함되어 있습니다.
   * **helloworld/helloworld/urls.py** 에는 각 url과 뷰 사이의 매핑 코드가 들어 있습니다.
3. **/var/www/helloworld/helloworld** 디렉터리에 **views.py**라는 새 파일을 만듭니다. 이 파일에는 "hello world" 페이지를 렌더링하는 뷰가 포함되어 있습니다. 편집기를 시작하고 다음을 입력합니다.
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. 이제 **urls.py** 파일의 내용을 다음과 같이 바꿉니다.
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="setting-up-apache"></a>Apache 설정
1. Apache 가상 호스트 구성 파일인 **/etc/apache2/sites-available/helloworld.conf**를 만듭니다. 콘텐츠를 다음으로 설정하고 *yourVmName*을 현재 사용 중인 컴퓨터의 실제 이름(예: *pyubuntu*)으로 바꿉니다.
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. 다음 명령으로 사이트를 사용하도록 설정합니다.
   
       $ sudo a2ensite helloworld
3. 다음 명령으로 Apache를 다시 시작합니다.
   
       $ sudo service apache2 reload
4. 끝으로, 브라우저에서 웹 페이지를 로드합니다.
   
   ![Azure의 hello world 페이지를 표시하는 브라우저 창](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shutting-down-your-azure-virtual-machine"></a>Azure 가상 컴퓨터 종료
이 자습서를 완료했으므로 새로 만든 Azure 가상 컴퓨터를 종료하거나 종료한 후 제거하여 다른 자습서에 사용할 리소스를 늘리고 Azure 사용량에 따른 요금 부과를 방지하세요.


