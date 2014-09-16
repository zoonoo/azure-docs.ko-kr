<properties linkid="develop-python-web-app-with-django" urlDisplayName="Web with Django (Windows)" pageTitle="Python web app with Django - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to host a Django-based website on Azure using a Windows Server 2008 R2 virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application" authors="" solutions="" manager="" editor="" />

#Django Hello World 웹 응용 프로그램

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/en-us/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

이 자습서에서는 Azure에서 Windows Server 가상 컴퓨터를 사용하여 Django 기반 웹 사이트를 호스트하는 방법을 설명합니다. 이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 가이드를 완료하면 클라우드에서 Django 기반 응용 프로그램을 실행할 수 있게 됩니다.

다음 방법에 대해 알아봅니다.

-   Django를 호스트하도록 Azure 가상 컴퓨터 설정. 이 자습서에서는 **Windows Server**에서 설정하는 방법을 설명하지만 Azure에서 호스트되는 Linux VM에서도 같은 작업을 수행할 수 있습니다.
-   Windows에서 새 Django 응용 프로그램 만들기

이 자습서의 지침에 따라 간단한 Hello World 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 Azure 가상 컴퓨터에서 호스트됩니다.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure의 hello world 페이지를 표시하는 브라우저 창](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Django를 호스트하기 위해 Azure 가상 컴퓨터 만들기 및 구성
---------------------------------------------------------

1.  [여기](/en-us/manage/windows/tutorials/virtual-machine-from-gallery/)에 나와 있는 지침에 따라 *Windows Server 2012 Datacenter* 배포판의 Azure 가상 컴퓨터를 만드십시오.

2.  웹을 통해 들어오는 포트 **80** 트래픽을 가상 컴퓨터의 포트 **80**으로 보내도록 Azure에 지시합니다.

    -   Azure 포털에서 새로 만든 가상 컴퓨터로 이동하고 *끝점* 탭을 클릭합니다.
    -   화면 맨 아래에 있는 *끝점 추가* 단추를 클릭합니다.
        ![끝점 추가](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

    -   *TCP* 프로토콜의 *공용 포트 80*을 *개인 포트 80*으로 엽니다.
![](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png)
1.  Windows *원격 데스크톱*을 사용하여 새로 만든 Azure 가상 컴퓨터에 원격으로 로그인합니다.

**중요:** 아래에 설명된 모든 지침에서는 가상 컴퓨터에 올바로 로그인했고 로컬 컴퓨터가 아니라 가상 컴퓨터에서 명령을 실행하는 것으로 가정합니다.

개발 환경 설정
--------------

Python 및 Django 환경을 설정하려면 [설치 가이드](../python-how-to-install/)(영문)에서 자세한 내용을 참조하십시오.

**참고 1:** *이* 자습서의 내용대로 작동되게 하려면 Azure 가상 컴퓨터에서 Windows WebPI 설치 관리자를 통해 **Django** 제품을 *설치하기만 하면 됩니다*.

**참고 2:** WebPI 설치 관리자를 다운로드하려면 IE ESC 설정을 구성해야 할 수도 있습니다(시작/관리 도구/서버 관리자, **IE ESC 구성** 클릭, 끄기로 설정).

FastCGI를 포함하여 IIS 설정
---------------------------

1.  FastCGI 지원을 포함하여 IIS 설치

         start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

2.  Python Fast CGI 처리기 설정

         %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

3.  이 사이트에 처리기 등록

         %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

4.  Django 응용 프로그램을 실행하도록 처리기 구성

         %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

5.  Python 인터프리터가 Django 앱을 찾을 수 있도록 PYTHONPATH 구성

         %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

    다음이 표시되어야 합니다.

    ![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png)

6.  FastCGI가 WSGI 게이트웨이에게 어떤 WSGI 처리기를 사용할지 알려 줍니다.

         %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

7.  wfastcgi.py를 [codeplex](http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409)(영문)에서 다운로드하고 C:\\Python27\\Scripts에 저장합니다. 이 디렉터리는 FastCGI 처리기를 등록하기 위해 이전 명령에서 사용한 위치입니다. 웹 플랫폼 설치 관리자를 사용하여 설치할 수도 있습니다. 'WFastCGI'를 검색합니다.

새 Django 응용 프로그램 만들기
------------------------------

1.  cmd.exe를 시작합니다.

2.  C:\\inetpub\\wwwroot로 디렉터리를 변경합니다.

3.  다음 명령을 입력하여 새 Django 프로젝트를 만듭니다.

    C:\\Python27\\python.exe -m django.bin.django-admin startproject DjangoApplication

    ![New-AzureService 명령의 결과](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

 **django-admin.py** 스크립트는 Django 기반 웹 사이트의 기본 구조를 생성합니다.

-   **manage.py**는 Django 기반 웹 사이트의 호스팅을 시작 및 중지하는 데 도움이 됩니다.
-   **DjangoApplication\\settings.py**에는 응용 프로그램에 대한 Django 설정이 포함되어 있습니다.
-   **DjangoApplication\\urls.py**에는 각 URL과 뷰 사이의 매핑 코드가 들어 있습니다.

1.  **urls.py**의 형제로 **views.py**라는 새 파일을 *C:\\inetpub\\wwwroot\\DjangoApplication*의 *DjangoApplication* 하위 디렉터리에 만듭니다. 이 파일에는 "hello world" 페이지를 렌더링하는 뷰가 포함되어 있습니다. 편집기를 시작하고 다음을 입력합니다.

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  이제 **urls.py** 파일의 내용을 다음과 같이 바꿉니다.

        from django.conf.urls.defaults import patterns, include, url
        from DjangoApplication.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

3.  끝으로, 브라우저에서 웹 페이지를 로드합니다.

![Azure의 hello world 페이지를 표시하는 브라우저 창](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png)

Azure 가상 컴퓨터 종료
----------------------

이 자습서를 완료했으므로 새로 만든 Azure 가상 컴퓨터를 종료하거나 종료한 후 제거하여 다른 자습서에 사용할 리소스를 늘리고 Azure 사용량에 따른 요금 부과를 방지하십시오.

