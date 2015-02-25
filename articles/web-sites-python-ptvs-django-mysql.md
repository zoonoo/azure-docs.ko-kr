<properties 
	pageTitle="Python Tools 2.1 for Visual Studio가 있는 Azure의 Django 및 MySQL" 
	description="Python Tools for Visual Studio를 사용하여 MySQL 데이터베이스 인스턴스에 데이터를 저장하고 웹 사이트에 배포할 수 있는 Django 응용 프로그램을 만드는 방법에 대해 알아봅니다. 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="huvalo"/>




# Python Tools 2.1 for Visual Studio가 있는 Azure의 Django 및 MySQL 

이 자습서에서는 PTVS 샘플 템플릿 중 하나를 사용하여 간단한 설문 조사 응용 프로그램을 만들어 보겠습니다. 이 자습서는 [비디오](https://www.youtube.com/watch?v=oKCApIrS0Lo)로도 제공됩니다.

Azure에서 호스트된 MySQL 서비스 중 하나를 사용하는 방법, MySQL을 사용하도록 응용 프로그램을 구성하는 방법 및 Azure 웹 사이트에 응용 프로그램을 게시하는 방법을 알아봅니다.

Bottle, Flask 및 Django 웹 프레임워크, MongoDB, Azure 테이블 저장소, MySQL 및 SQL 데이터베이스 서비스를 사용하여 PTVS로 Azure 웹 사이트를 개발하는 내용을 다루는 추가 문서에 대해서는 [Python 개발자 센터][]를 참조하세요.  이 문서는 Azure 웹 사이트를 중점적으로 다루지만 포함된 단계는 [Azure 클라우드 서비스][]를 개발할 때와 비슷합니다.

+ [필수 조건](#prerequisites)
+ [프로젝트 만들기](#create-the-project)
+ [MySQL 데이터베이스 만들기](#create-a-mysql-database)
+ [프로젝트 구성](#configure-the-project)
+ [Azure 웹 사이트에 게시](#publish-to-an-azure-website)
+ [다음 단계](#next-steps)

##<a name="prerequisites"></a>필수 조건

 - Visual Studio 2012 또는 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 샘플 VSIX][]
 - [Azure SDK Tools for VS 2013][] 또는 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 32비트][]

> [AZURE.NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. <a href="http://azure.microsoft.com/ko-kr/pricing/member-offers/msdn-benefits-details/">MSDN 구독자 혜택을 활성화</a>하거나 <a href="http://azure.microsoft.com/ko-kr/pricing/free-trial/">무료 평가판을 등록</a>할 수 있습니다.
> 
> 계정을 등록하기 전에 Azure 웹 사이트를 시작하려면 <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>으로 이동합니다. Azure 웹 사이트에서는 무료로 단기 ASP.NET 시작 사이트를 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

##<a name="create-the-project"></a>프로젝트 만들기

이 섹션에서는 샘플 템플릿을 사용하여 Visual Studio 프로젝트를 만듭니다. 가상 환경을 만들고 필요한 패키지를 설치합니다.  sqlite를 사용하여 로컬 데이터베이스를 만듭니다.  그런 후 응용 프로그램을 로컬로 실행합니다.

1.  Visual Studio에서 **파일**, **새 프로젝트**를 선택합니다.

1.  PTVS 샘플 VSIX의 프로젝트 테플릿은 **Python**, **샘플**에서 사용할 수 있습니다.  **Polls Django Web Project**를 선택하고 확인을 클릭하여 프로젝트를 만듭니다.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  외부 패키지를 설치할지 묻는 메시지가 표시됩니다.  **가상 환경에 설치**를 선택합니다.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  기본 해석기로 **Python 2.7**을 선택합니다.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **Python**, **Django Sync DB**를 선택합니다.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  이렇게 하면 Django Management Console이 열립니다.  프롬프트에 따라 사용자를 만듭니다.

    그러면 프로젝트 폴더에 sqlite 데이터베이스가 만들어집니다.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  <kbd>F5</kbd> 키를 눌러 응용 프로그램이 작동하는지 확인합니다.

1.  위쪽의 탐색 모음에서 **로그인**을 클릭합니다.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  데이터베이스를 동기화할 때 만든 사용자의 자격 증명을 입력합니다.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  **Create Sample Polls**를 클릭합니다.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  poll and vote를 클릭합니다.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>MySQL 데이터베이스 만들기

데이터베이스로, Azure에 ClearDB MySQL 호스트 데이터베이스를 만듭니다.

또는 Azure에서 실행되는 자체 가상 컴퓨터를 만든 후 MySQL을 직접 설치하고 관리할 수도 있습니다.

다음 단계에 따라 무료로 데이터베이스를 만들 수 있습니다.

1.  [Azure 관리 포털][]에 로그인합니다.

1.  탐색 창 맨 아래에 있는 **새로 만들기**를 클릭합니다.

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  **스토어**, **ClearDB MySQL 데이터베이스**를 클릭합니다.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  이름에 데이터베이스 서비스에 사용할 이름을 입력합니다.

1.  데이터베이스 서비스를 찾을 영역/선호도 그룹을 선택합니다. Azure 응용 프로그램에서 데이터베이스를 사용하는 경우 응용 프로그램을 배포할 영역과 동일한 영역을 선택합니다.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  **구입**을 클릭합니다.

##<a name="configure-the-project"></a>프로젝트 구성

이 섹션에서는 방금 만든 MySQL 데이터베이스를 사용하도록 응용 프로그램을 구성합니다.  Azure 포털에서 연결 설정을 가져오는 방법을 알아봅니다.  Django에서 MySQL 데이터베이스를 사용하는 데 필요한 추가 Python 패키지도 설치합니다.  그런 후 응용 프로그램을 로컬로 실행합니다.

1.  [Azure 관리 포털][]에서 **추가 기능**을 클릭하고 이전에 만든 ClearDB MySQL 데이터베이스 서비스를 클릭합니다.

1.  **연결 정보**를 클릭합니다.  복사 단추를 사용하여 **CONNECTIONSTRING** 값을 클립보드에 저장합니다.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  Visual Studio에서  *ProjectName* 폴더의 **settings.py**를 엽니다.  일시적으로 연결 문자열을 편집기에 붙여 넣습니다.  연결 문자열은 다음 형식입니다.

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    MySQL을 사용하도록 기본 데이터베이스 **ENGINE**을 변경하고 **CONNECTIONSTRING**에서 **NAME**, **USER**, **PASSWORD** 및 **HOST** 값을 설정합니다.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  솔루션 탐색기의 **Python Environments**에서 가상 환경을 마우스 오른쪽 단추로 클릭하고 **Install Python Package**를 선택합니다.

1. **easy_install**을 사용하여 패키지  `mysql-python`를 설치합니다.

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **Python**, **Django Sync DB**를 선택합니다.  

    이렇게 하면 이전 섹션에서 만든 MySQL 데이터베이스에 대한 테이블이 만들어집니다.  프롬프트에 따라 사용자를 만듭니다. 이 사용자가 첫 번째 섹션에서 만든 sqlite 데이터베이스의 사용자와 일치할 필요는 없습니다.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  <kbd>F5</kbd> 키를 눌러 응용 프로그램을 실행합니다.  **Create Sample Polls**를 사용하여 만든 설문 조사와 투표를 통해 제출된 데이터는 MySQL 데이터베이스에서 serialize됩니다.

##<a name="publish-to-an-azure-website"></a>Azure 웹 사이트에 게시

PTVS는 Azure 웹 사이트에 웹 응용 프로그램을 배포하는 쉬운 방법을 제공합니다.

1.  **솔루션 탐색기**에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  **Microsoft Azure 웹 사이트**를 클릭합니다.

1.  **새로 만들기**를 클릭하여 새 사이트를 만듭니다.

1.  **사이트 이름** 및 **지역**을 선택하고 **만들기**를 클릭합니다.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  다른 모든 기본값을 그대로 적용하고 **게시**를 클릭합니다.

1.  웹 브라우저가 자동으로 게시된 사이트로 열립니다.  Azure에 호스트된 **MySQL** 데이터베이스를 사용하여 예상한 대로 응용 프로그램이 작동하는지 확인합니다.

    축하합니다.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>다음 단계

Python Tools for Visual Studio, Django 및 MySQL에 대해 자세히 알아보려면 다음 링크를 참조하세요.

- [Python Tools for Visual Studio 설명서][]
  - [웹 프로젝트][]
  - [클라우드 서비스 프로젝트][]
  - [Microsoft Azure의 원격 디버깅][]
- [Django 설명서][]
- [MySQL][]


<!--Link references-->
[Python 개발자 센터]: /ko-kr/develop/python/
[Azure 클라우드 서비스]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure 관리 포털]: https://manage.windowsazure.com
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 샘플 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32비트]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools for Visual Studio 설명서]: http://pytools.codeplex.com/documentation
[Microsoft Azure의 원격 디버깅]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[웹 프로젝트]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[클라우드 서비스 프로젝트]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Django 설명서]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/


<!--HONumber=42-->
