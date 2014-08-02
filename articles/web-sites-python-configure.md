<properties linkid="develop-python-tutorials-web-sites-configuration" urlDisplayName="Configuring Python with Azure Web Sites" pageTitle="Configuring Python with Azure Web Sites" metaKeywords="" description="This tutorial describes options for authoring and configuring a basic Web server Gateway Interface (WSGI) compliant Python application on Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="Python" title="Configuring Python with Azure Web Sites" authors="" solutions="" manager="" editor="" />

Azure 웹 사이트를 사용하여 Python 구성
======================================

이 자습서에서는 Azure 웹 사이트에서 기본 WSGI(Web Server Gateway Interface) 규격 Python 응용 프로그램을 제작 및 구성하는 옵션을 설명합니다. Azure 웹 사이트를 시작하기는 간단하며, Python 응용 프로그램은 다른 Azure 서비스로 확장할 여유가 있습니다. Azure 웹 사이트 플랫폼에는 Python 2.7 및 Python용 일반 wfastcgi.py FastCGI 처리기가 포함되어 있습니다. Python 처리기를 사용하도록 웹 사이트를 구성하기만 하면 됩니다.

Azure 웹 사이트에서 Django 프레임워크 구성에 대한 자세한 예는 다음 자습서를 참조하십시오. <http://www.windowsazure.com/en-us/develop/python/tutorials/web-sites-with-django>(영문)

WSGI 지원
---------

WSGI는 웹 서버와 Python 간 인터페이스를 정의하는 [PEP 3333](http://www.python.org/dev/peps/pep-3333/)(영문)에 설명된 Python 표준입니다. WSGI는 Python을 사용하여 다양한 웹 응용 프로그램 및 프레임워크 쓰기에 대해 표준화된 인터페이스를 제공합니다. 오늘날 인기 있는 Python 웹 프레임워크는 WSGI를 사용합니다. Azure 웹 사이트는 그러한 프레임워크에 대한 지원을 제공하며, 고급 사용자는 사용자 지정 처리기가 WSGI 사양 지침을 따르는 경우 고유한 프레임워크를 제작할 수도 있습니다.

웹 사이트 만들기
----------------

이 자습서에서는 기존 Azure 구독 및 Azure 관리 포털에 대한 액세스 권한이 있다고 가정합니다. 웹 사이트 만들기에 대한 자세한 지침은 <http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-create-websites>(영문)에서 볼 수 있습니다.

요컨대 기존 웹 사이트가 없는 경우 Azure 관리 포털에서 웹 사이트를 만들 수 있습니다. 웹 사이트 기능을 선택하고 빠른 생성 옵션을 사용하여 웹 사이트에 대한 URL을 지정합니다.

![](./media/web-sites-python-configure/configure-python-create-website.png)

Git 게시
--------

새로 만든 웹 사이트에 대해 빠른 시작 또는 대시보드 탭을 사용하여 Git 게시를 구성합니다. 이 자습서에서는 Git를 사용하여 Python 웹 사이트를 만들고 관리하며 Azure 웹 사이트에 게시합니다.

![](./media/web-sites-python-configure/configure-python-git.png)

Git 게시가 설정되면 Git 리포지토리가 생성되고 웹 사이트와 연결됩니다. 리포지토리의 URL이 표시되고 로컬 개발 환경에서 클라우드로 데이터를 보내는 데 사용할 수 있습니다. Git를 통해 응용 프로그램을 게시하려면 Git 클라이언트가 설치되어 있는지 확인하고 제공된 지침에 따라 웹 사이트 콘텐츠를 Azure 웹 사이트에 게시합니다.

웹 사이트 콘텐츠
----------------

Azure 웹 사이트에서 Python 지원을 활용하는 데 필요한 최소한의 작업량을 설명하는 기본 WSGI 처리기와 함께 기본 Python 응용 프로그램을 예로 사용합니다. 이 기본적인 Python 응용 프로그램은 아래 예에서 모든 기능을 갖춘 웹 프레임워크까지 다양한 솔루션 제작을 시작하는 데 사용할 수 있습니다.

다음은 기본 WSGI 처리기에 대한 코드입니다. 이 코드는 WSGI 규격 응용 프로그램의 시작 지점으로서 [PEP 3333](http://www.python.org/dev/peps/pep-3333/) 사양에 제시된 코드와 비슷합니다. 웹 사이트 루트 아래에 ConfigurePython 폴더에 생성한 ConfigurePython.py라는 파일에 이 콘텐츠를 저장했습니다.

    def application(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        yield 'Hello from Azure Websites\n'

*application*은 WSGI 규격 서버에서 호출하는 진입점으로 사용하는 Python 호출 가능 개체입니다. 이 호출 가능 개체는 2개의 위치 인수를 수락합니다.

-   *environ*: 다양한 환경 변수가 포함된 사전
-   *start\_response*: HTTP 상태 및 응답 헤더 전송에 대해 웹 서버에서 제공하는 호출 가능한 위치 인수

이 처리기는 모든 요청에 대해 "Hello from Azure Websites"라는 일반 텍스트를 반환합니다.

구성 옵션
---------

Azure 웹 사이트와 함께 Python 응용 프로그램을 구성하는 데에는 2가지 옵션이 있습니다.

### 옵션 1: 포털

1.1. 포털의 구성 탭에서 FastCGI 처리기를 등록합니다.
이 예에서는 Azure 웹 사이트에 포함된 Python용 FastCGI 처리기를 사용합니다. 스크립트 프로세서 및 FastCGI 처리기 인수에 대해 다음 경로를 사용하십시오.

-   Python 스크립트 프로세서 경로: D:\\python27\\python.exe
-   Python FastCGI 처리기 경로: D:\\python27\\scripts\\wfastcgi.py

![](./media/web-sites-python-configure/configure-python-handler-mapping.png)

1.2. 포털에서 같은 구성 탭을 통해 앱 설정을 구성합니다.
앱 설정이 환경 변수로 변환됩니다. 이것은 Python 응용 프로그램에서 요구하는 구성 값에 사용할 수 있는 메커니즘입니다. 이 기본 예제 응용 프로그램에 대해 다음을 구성했습니다.

-   PYTHONPATH는 모듈을 검색하는 디렉터리에 대해 Python에 알립니다. Azure 웹 사이트는 웹 사이트의 루트를 가리키는 문법적 편의(syntactic sugar)로 D:\\home\\site\\wwwroot를 제공합니다.
-   WSGI\_HANDLER는 모듈 또는 패키지 이름과 사용되는 특성을 기록합니다.

![](./media/web-sites-python-configure/configure-python-app-settings.png)

### 옵션 2: web.config

다른 구성 방법으로, 아래에 설명된 작업에 대해 웹 사이트 루트의 web.config 파일을 사용합니다. web.config 옵션을 사용하면 웹 응용 프로그램에 대한 이동성이 더 뛰어납니다. 웹 응용 프로그램으로 요청을 라우팅할 수 있는 2가지 방법이 있습니다. Python을 통해 들어 오는 모든 수신 요청을 라우팅하도록 IIS를 지시하는 \* 경로를 처리하는 처리기를 설정하거나, Python이 처리하고 URL 다시 쓰기를 사용하여 여러 URL을 선택한 경로로 리디렉션하는 특정 경로를 설정합니다. 사실 두 번째 방법이 권장됩니다. 요청 대상(예에서는 handler.fcgi)으로 사용하기 위해 웹 사이트 루트의 빈 처리기 파일을 사용하는 것이 더 좋습니다. 이전 시나리오에서는 정적 콘텐츠(예: 이미지 파일 및 스타일시트)에 대한 요청을 비롯하여 모든 요청이 Python을 거쳐야 해서 웹 서버에서 정적 파일 액세스에 대해 제공하는 최적화를 무너뜨립니다. 두 번째 방법을 사용하면 정적 콘텐츠를 효율적으로 사용하여 필요한 경우에만 Python을 호출할 수 있습니다.

2.1. PYTHONPATH 변수를 지정합니다. 
> Python에 응용 프로그램 코드를 찾을 위치를 알립니다. 여기에서 웹 사이트에 대한 절대 경로로서 D:\\home\\site\\wwwroot도 사용됩니다.

2.2. WSGI\_HANDLER 변수를 설정합니다. 
> Azure 웹 사이트는 이 값을 사용하여 Python에 WSGI 처리기를 호출하도록 지시합니다. 이 변수의 값은 실행 시 WSGI 처리기를 나타내는 호출 가능한 개체를 반환해야 하는 Python 식입니다.

2.3. Python용 처리기를 추가합니다. 
> Python이 handler.fcgi 경로에 대한 요청을 처리해야 함을 Azure 웹 사이트에 알립니다. 고유한 FastCGI 처리기 또는 Python 개발 스택을 가져오지 않는 한, 처리기 구문이 아래 예제의 &lt;handlers\> 태그 내의 구문과 똑같이 보이는 것이 중요합니다.

2.4. handler.fcgi에 URL을 다시 씁니다. 
> 항상 handler.fcgi를 요청하는 것이 좋은 생각이 아닐 수 있습니다. Python 처리기에서 처리할 파일의 경로를 선택하기 위해 URL 다시 쓰기를 사용했으므로 Python 처리기에서 모든 URL을 처리합니다.

	<configuration>
  		<appSettings>
    		<add key="pythonpath" value="D:\home\site\wwwroot\ConfigurePython" />
    		<add key="WSGI_HANDLER" value="ConfigurePython.application" />
  		</appSettings>
  		<system.webServer>
    		<handlers>
      			<add name="PythonHandler" 
           		verb="*" path="handler.fcgi" 
           		modules="FastCgiModule" 
           		scriptProcessor="D:\Python27\Python.exe|D:\Python27\Scripts\wfastcgi.py" 
           		resourceType="Either" />
   			</handlers>
			<rewrite>
	    		<rules>
					<rule name="Configure Python" stopProcessing="true">
		    			<match url="(.*)" ignoreCase="false" />
		    			<conditions>
							<add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
		    			</conditions>
		    			<action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
					</rule>
	    		</rules>
			</rewrite>
  		</system.webServer>
	</configuration> 

웹 사이트 루트의 예제에 대한 폴더 구조는 다음과 같습니다(Python 폴더 및 파일 이름의 대/소문자는 중요하며 web.config에 반영됨).

-   ConfigurePython\\ConfigurePython.py
-   web.config
-   handler.fcgi

모든 URL을 handler.fcgi에 다시 쓰고 해당 경로를 FastCGI를 통해 Python에 전달하기 때문에, IIS에서 HTTP 404 오류를 반환하지 않도록 같은 이름을 가진 자리 표시자 파일을 만들어야 합니다. 이는 요청되는 파일이 지정된 스크립트 프로세서 응용 프로그램에 전달되기 전에 존재하도록 하는 IIS FastCGI 모듈의 내부 동작 때문입니다.

웹 사이트로 이동하여 올바른 구성인지 테스트합니다. 이 예제에서는 액세스 시 "Hello from Azure Websites" 메시지가 표시됩니다.

![](./media/web-sites-python-configure/configure-python-result.png)

