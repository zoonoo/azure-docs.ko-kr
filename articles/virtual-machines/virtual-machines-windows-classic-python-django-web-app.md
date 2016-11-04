---
title: Django를 사용하는 Python 웹 앱 | Microsoft Docs
description: 이 자습서에서는 클래식 배포 모델로 만든 Windows Server 2012 R2 Datacenter 가상 컴퓨터를 사용하여 Azure에서 Django 기반 웹 사이트를 호스트하는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 08/04/2015
ms.author: huvalo

---
# Windows Server VM의 Django Hello World 웹 응용 프로그램
> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-classic-python-django-web-app.md)
> * [Mac/Linux](virtual-machines-linux-python-django-web-app.md)
> 
> 

<br>

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

이 자습서에서는 Microsoft Azure에서 Windows Server 가상 컴퓨터를 사용하여 Django 기반 웹 사이트를 호스트하는 방법을 설명합니다. 이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 Django 기반 응용 프로그램을 실행할 수 있게 됩니다.

다음 방법에 대해 알아봅니다.

* Django를 호스트하도록 Azure 가상 컴퓨터 설정. 이 자습서에서는 Windows Server에서 설정하는 방법을 설명하지만 Azure에서 호스트되는 Linux VM에서도 같은 작업을 수행할 수 있습니다.
* Windows에서 새 Django 응용 프로그램 만들기

이 자습서의 지침에 따라 간단한 Hello World 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 Azure 가상 컴퓨터에서 호스트됩니다.

완성된 응용 프로그램의 스크린샷이 다음으로 표시됩니다.

![Azure의 hello world 페이지를 표시하는 브라우저 창][1]

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Django를 호스트하기 위해 Azure 가상 컴퓨터 만들기 및 구성
1. [여기](virtual-machines-windows-classic-tutorial.md)에 나와 있는 지침에 따라 Windows Server 2012 R2 Datacenter 배포의 Azure 가상 컴퓨터를 만듭니다.
2. 웹을 통해 들어오는 포트 80 트래픽을 가상 컴퓨터의 포트 80으로 보내도록 Azure에 지시합니다.
   
   * Azure 클래식 포털에서 새로 만든 가상 컴퓨터로 이동하고 **끝점** 탭을 클릭합니다.
   * 화면 아래쪽에 있는 **추가** 단추를 클릭합니다. ![끝점 추가](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)
   * **TCP** 프로토콜의 **공용 포트 80**을 **개인 포트 80**으로 엽니다. ![][port80]
3. **대시보드** 탭에서 **연결**을 클릭하여 **원격 데스크톱**을 사용해 새로 만든 Azure 가상 컴퓨터에 원격으로 로그인합니다.

**중요:** 아래에 설명된 모든 지침에서는 가상 컴퓨터에 올바로 로그인했고 로컬 컴퓨터가 아니라 가상 컴퓨터에서 명령을 실행하는 것으로 가정합니다.

## <a id="setup"> </a>Python, Django, WFastCGI 설치
**참고:** Internet Explorer를 사용하여 다운로드하려면 IE ESC 설정을 구성해야 할 수도 있습니다(시작/관리 도구/서버 관리자/로컬 서버에서 **IE 보안 강화 구성**을 클릭하고 끄기로 설정).

1. [python.org][python.org]에서 최신 Python 2.7 또는 3.4를 설치합니다.
2. pip를 사용하여 wfastcgi 및 django 패키지를 설치합니다.
   
    Python 2.7의 경우 다음 명령을 사용합니다.
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Python 3.4의 경우 다음 명령을 사용합니다.
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## FastCGI를 포함하여 IIS 설치
1. FastCGI 지원을 포함하여 IIS를 설치합니다. 이 설치를 실행하려면 몇 분 정도 걸릴 수 있습니다.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## 새 Django 응용 프로그램 만들기
1. *C:\\inetpub\\wwwroot*에서 다음 명령을 입력하여 새 Django 프로젝트를 만듭니다.
   
   Python 2.7의 경우 다음 명령을 사용합니다.
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Python 3.4의 경우 다음 명령을 사용합니다.
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![New-AzureService 명령의 결과](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. **django-admin** 명령은 Django 기반 웹 사이트의 기본 구조를 생성합니다.
   
   * **helloworld\\manage.py**는 Django 기반 웹 사이트의 호스팅을 시작 및 중지하는 데 도움이 됩니다.
   * **helloworld\\helloworld\\settings.py**에는 응용 프로그램에 대한 Django 설정이 포함되어 있습니다.
   * **helloworld\\helloworld\\urls.py**에는 각 url과 뷰 사이의 매핑 코드가 들어 있습니다.
3. **views.py**라는 새 파일을 *C:\\inetpub\\wwwroot\\helloworld\\helloworld* 디렉터리에 만듭니다. 이 파일에는 "hello world" 페이지를 렌더링하는 뷰가 포함되어 있습니다. 편집기를 시작하고 다음을 입력합니다.
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. urls.py 파일의 내용을 다음과 같이 바꿉니다.
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## IIS 구성
1. 전역 applicationhost.config에 있는 처리기 섹션의 잠금을 해제합니다. 이렇게 하면 web.config 파일에 python 처리기를 사용합니다.
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. WFastCGI를 사용하도록 설정합니다. 이렇게 하면 Python 인터프리터 실행 파일과 wfastcgi.py 스크립트를 참조하는 전역 applicationhost.config에 응용 프로그램이 추가됩니다.
   
    Python 2.7:
   
        c:\python27\scripts\wfastcgi-enable
   
    Python 3.4:
   
        c:\python34\scripts\wfastcgi-enable
3. *C:\\inetpub\\wwwroot\\helloworld*에 web.config 파일을 만듭니다. `scriptProcessor` 특성의 값은 이전 단계의 출력과 일치해야 합니다. wfastcgi 설정에 대한 자세한 내용은 pypi의 [wfastcgi][wfastcgi] 페이지를 참조하세요.
   
    Python 2.7:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
    Python 3.4:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. django 프로젝트 폴더를 가리키도록 IIS 기본 웹 사이트의 위치를 업데이트 합니다.
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. 끝으로, 브라우저에서 웹 페이지를 로드합니다.

![Azure의 hello world 페이지를 표시하는 브라우저 창][1]

## Azure 가상 컴퓨터 종료
이 자습서를 완료한 후에는 새로 만든 Azure 가상 컴퓨터를 종료하거나 종료한 후 제거하여 다른 자습서에 사용할 리소스를 늘리고 Azure 사용량에 따른 요금 부과를 방지하세요.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

<!---HONumber=AcomDC_0629_2016-->