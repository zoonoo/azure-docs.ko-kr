<properties linkid="develop-python-web-app-with-django-mac" urlDisplayName="Web with Django" pageTitle="Python web app with Django on Mac - Azure tutorial" metaKeywords="" description="A tutorial that shows how to host a Django-based web site on Azure using a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application (mac-linux)" authors="" solutions="" manager="" editor="" />

#Django Hello World 웹 응용 프로그램(mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/en-us/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

이 자습서에서는 Azure에서 Linux 가상 컴퓨터를 사용하여 Django 기반 웹 사이트를 호스트하는 방법을 설명합니다. 이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 가이드를 완료하면 클라우드에서 Django 기반 응용 프로그램을 실행할 수 있게 됩니다.

다음 방법에 대해 알아봅니다.

-   Django를 호스트하도록 Azure 가상 컴퓨터 설정. 이 자습서에서는 **Linux**에서 설정하는 방법을 설명하지만 Azure에서 호스트되는 Windows Server VM에서도 같은 작업을 수행할 수 있습니다.
-   Linux에서 새 Django 응용 프로그램 만들기

이 자습서의 지침에 따라 간단한 Hello World 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 Azure 가상 컴퓨터에서 호스트됩니다.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure의 hello world 페이지를 표시하는 브라우저 창](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Django를 호스트하기 위해 Azure 가상 컴퓨터 만들기 및 구성
---------------------------------------------------------

1.  [여기](/en-us/manage/linux/tutorials/virtual-machine-from-gallery/)에 나와 있는 지침에 따라 *Ubuntu Server 12.04* 배포판의 Azure 가상 컴퓨터를 만듭니다.

 **참고:** *단지* 가상 컴퓨터만 만들면 됩니다. *가상 컴퓨터를 만든 후 로그온하는 방법* 섹션을 참조하십시오.

1.  웹을 통해 들어오는 포트 **80** 트래픽을 가상 컴퓨터의 포트 **80**으로 보내도록 Azure에 지시합니다.
    -   Azure 포털에서 새로 만든 가상 컴퓨터로 이동하고 *끝점* 탭을 클릭합니다.
    -   화면 맨 아래에 있는 *끝점 추가* 단추를 클릭합니다.

       ![끝점 추가](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
    -   *TCP* 프로토콜의 *공용 포트 80*을 *개인 포트 80*으로 엽니다.

       ![포트 80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

개발 환경 설정
--------------

**참고:** Python을 설치해야 하거나 클라이언트 라이브러리를 사용하고 싶으면 [Python 설치 가이드](../python-how-to-install/)(영문)를 참조하십시오.

Ubuntu Linux VM에는 Python 2.7이 사전 설치되어 있지만, Apache 또는 django는 설치되어 있지 않습니다. 다음 단계에 따라 VM에 연결하고 Apache 및 django를 설치하십시오.

1.  새 **터미널** 창을 시작합니다.

2.  다음 명령을 입력하여 Azure VM에 연결합니다.

        $ ssh yourusername@yourVmUrl

3.  다음 명령을 입력하여 django를 설치합니다.

        $ sudo apt-get install python-setuptools
        $ sudo easy_install django

4.  다음 명령을 입력하여 Apache를 mod-wsgi와 함께 설치합니다.

        $ sudo apt-get install apache2 libapache2-mod-wsgi

새 Django 응용 프로그램 만들기
------------------------------

1.  이전 섹션에서 사용한 **터미널** 창을 열고 ssh로 VM에 연결합니다.

2.  다음 명령을 입력하여 새 Django 프로젝트를 만듭니다.

    ![django-admin 명령의 결과](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-dir.png)

    **django-admin.py** 스크립트는 Django 기반 웹 사이트의 기본 구조를 생성합니다.
    - **manage.py**는 Django 기반 웹 사이트의 호스팅을 시작 및 중지하는 데 도움이 되고 
    - **helloworld\\settings.py**에는 응용 프로그램에 대한 Django 설정이 포함되어 있습니다.
    - **helloworld\\urls.py**에는 각 url과 뷰 사이의 매핑 코드가 들어 있습니다.

3.  **urls.py**의 형제로 **views.py**라는 새 파일을 *django\\helloworld*의 *helloworld* 하위 디렉터리에 만듭니다. 이 파일에는 "hello world" 페이지를 렌더링하는 뷰가 포함되어 있습니다. 편집기를 시작하고 다음을 입력합니다.

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

4.  이제 **urls.py** 파일의 내용을 다음과 같이 바꿉니다.

        from django.conf.urls.defaults import patterns, include, url
        from helloworld.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

Django 웹 사이트 배포 및 실행
-----------------------------

1.  apache 구성 파일 **/etc/apache2/httpd.conf**를 편집하여 다음을 추가하고 *username*을 VM 만들기 중에 지정한 사용자 이름으로 바꿉니다.

        WSGIScriptAlias / /home/*username*/django/helloworld/helloworld/wsgi.py
        WSGIPythonPath /home/*username*/django/helloworld

        <Directory /home/*username*/django/helloworld/helloworld>
        <Files wsgi.py>
        Order deny,allow
        Allow from all
        </Files>
        </Directory>

2.  다음 명령으로 apache를 다시 시작합니다.

        $ sudo apachectl restart

3.  끝으로, 브라우저에서 웹 페이지를 로드합니다.

    ![Azure의 hello world 페이지를 표시하는 브라우저 창](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

Azure 가상 컴퓨터 종료
----------------------

이 자습서를 완료했으므로 새로 만든 Azure 가상 컴퓨터를 종료하거나 종료한 후 제거하여 다른 자습서에 사용할 리소스를 늘리고 Azure 사용량에 따른 요금 부과를 방지하십시오.

