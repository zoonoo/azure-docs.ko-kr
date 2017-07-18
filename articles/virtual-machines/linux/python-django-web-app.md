---
title: "Azure Linux VM에서 Django를 사용하는 Python 웹앱 | Microsoft Docs"
description: "Azure에서 Linux VM을 사용하여 Django 기반 웹앱을 호스트하는 방법에 대해 알아봅니다."
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
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6e2ab8c7da7496d0e2b567a4bdc9341adcf01552
ms.contentlocale: ko-kr
ms.lasthandoff: 06/22/2017


---
# <a name="django-hello-world-web-app-on-a-linux-vm"></a>Linux VM에서 Django Hello World 웹앱
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

이 자습서는 Azure Virtual Machines의 Linux에서 Django 기반 웹 사이트를 호스트하는 방법을 보여 줍니다. 이 자습서에서는 사전에 Azure 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 Django 기반 응용 프로그램을 실행할 수 있게 됩니다.

방법 배우기:

* Django를 호스트하도록 Azure 가상 컴퓨터를 설정합니다. 이 자습서에서는 **Linux**에서 작업을 수행하는 방법을 설명하지만, Azure에서 호스트되는 Windows Server VM에 동일한 작업을 수행할 수 있습니다. 
* Linux에서 새 Django 응용 프로그램을 만듭니다.

자습서는 기본적인 Hello World 웹 응용 프로그램을 빌드하는 방법을 보여 줍니다. 응용 프로그램은 Azure 가상 컴퓨터에 호스트됩니다.

다음 스크린샷에 완성된 응용 프로그램이 나와 있습니다.

![Azure의 Hello World 페이지가 표시된 브라우저 창](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Django를 호스트하기 위해 Azure 가상 컴퓨터 만들기 및 설정

1. Ubuntu Server 14.04 LTS 배포를 사용하여 Azure 가상 컴퓨터를 만들려면 참조 [Azure Portal에서 Linux 가상 컴퓨터 만들기](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. SSH 공개 키를 사용하는 대신 암호 인증을 선택할 수도 있습니다.
2. 포트 80에 들어오는 HTTP 트래픽을 허용하도록 네트워크 보안 그룹을 편집하려면 [Azure Portal에서 네트워크 보안 그룹 만들기](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md)를 참조하세요.
3. (선택 사항) 기본적으로 새 가상 컴퓨터에는 FQDN(정규화된 도메인 이름)이 없습니다.  FQDN을 사용하여 VM을 만들려면 [Windows VM에 대 한 Azure Portal의 FQDN을 만들기](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 이 단계는 이 자습서를 완료하는 데 필수는 아닙니다.

## <a id="setup"> </a>개발 환경 설정
> [!NOTE]
> Python을 설치해야 하거나 클라이언트 라이브러리를 사용하고 싶으면 [Python 설치 가이드](../../python-how-to-install.md)를 참조하세요.

Ubuntu Linux VM에는 Python 2.7이 미리 설치되어 있지만 Apache 또는 Django를 제공하지는 않습니다. 다음 단계를 완료하여 VM에 연결하고 Apache 및 Django를 설치합니다.

1. 새 터미널 창을 엽니다.
2. Azure VM에 연결하려면 다음 명령을 입력합니다. FQDN을 만들지 않은 경우 Azure Portal의 가상 컴퓨터 요약에 표시되는 공용 IP 주소를 사용하여 연결할 수 있습니다.
   
       $ ssh yourusername@yourVmUrl
3. Django를 설치하려면 다음 명령을 입력합니다.
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. Apache를 mod-wsgi와 함께 설치하려면 다음 명령을 입력합니다.
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="create-a-new-django-app"></a>새 Django 앱 만들기
1. SSH를 사용하여 VM에 액세스하려면 이전 섹션에서 사용한 터미널 창을 엽니다.
2. 새 Django 프로젝트를 만들려면 다음 명령을 입력합니다.
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   `django-admin.py` 스크립트는 Django 기반 웹 사이트의 기본 구조를 생성합니다.
   
   * `helloworld/manage.py`는 Django 기반 웹 사이트의 호스팅을 시작 및 중지하는 데 도움이 됩니다.
   * `helloworld/helloworld/settings.py`에는 응용 프로그램에 대한 Django 설정이 있습니다.
   * `helloworld/helloworld/urls.py`에는 각 URL과 뷰 사이 매핑 코드가 있습니다.
3. /var/www/helloworld/helloworld 디렉터리에서 views.py라는 이름의 새 파일을 만듭니다. 이 파일에는 “hello world” 페이지를 렌더링하는 뷰가 포함되어 있습니다. 코드 편집기에서 다음 명령을 입력합니다.
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. urls.py 파일의 콘텐츠를 다음 명령으로 바꿉니다.
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-apache"></a>Apache 설정
1. /etc/apache2/sites-available/helloworld.conf 폴더에서 Apache 가상 호스트 구성 파일을 만듭니다. 콘텐츠를 다음 값으로 설정합니다. *yourVmName*을 현재 사용 중인 컴퓨터의 실제 이름(예: *pyubuntu*)으로 바꿉니다.
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. 사이트를 활성화하려면 다음 명령을 사용합니다.
   
       $ sudo a2ensite helloworld
3. Apache를 다시 시작하려면 다음 명령을 사용합니다.
   
       $ sudo service apache2 reload
4. 브라우저에서 웹 페이지를 로드합니다.
   
   ![Azure의 hello world 페이지가 표시된 브라우저 창](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shut-down-your-azure-virtual-machine"></a>Azure 가상 컴퓨터 종료
이 자습서를 완료하면 자습서용으로 만든 Azure VM을 종료하거나 제거하는 것이 좋습니다. 그러면 다른 자습서에 대한 리소스가 해제되어 Azure 사용량 요금이 발생하는 것을 방지할 수 있습니다.


