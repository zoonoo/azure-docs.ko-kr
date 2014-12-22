<properties linkid="web-sites-python-ptvs-bottle-mongodb" title="Bottle and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Python Tools 2.1 for Visual Studio가 있는 Azure의 Bottle 및 MongoDB" description="Learn how to use the Python Tools for Visual Studio to create a Bottle application that stores data in a MongoDB database instance and can be deployed to a web site." metaKeywords="" services="web-sites" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Python Tools 2.1 for Visual Studio가 있는 Azure의 Bottle 및 MongoDB

이 자습서에서는 PTVS 샘플 템플릿 중 하나를 사용하여 간단한 설문 조사 응용 프로그램을 만들 것입니다.

설문 조사 응용 프로그램은 리포지토리에 대한 추출을 정의하므로 다양한 유형의 리포지토리(메모리 내, Azure 테이블 저장소, MongoDB) 간을 쉽게 전환할 수 있습니다.

Azure에서 호스트되는 MongoDB 서비스 중 하나를 사용하는 방법, MongoDB를 사용하도록 응용 프로그램을 구성하는 방법 및 응용 프로그램을 Azure 웹 사이트에 게시하는 방법을 알아보겠습니다.

Bottle, Flask 및 Django 웹 프레임워크를 통한 PTVS, MongoDB, Azure 테이블 저장소, MySQL 및 SQL 데이터베이스 서비스를 사용한 Azure 웹 사이트 개발을 소개하는 추가 문서에 대해서는 [Python 개발자 센터][]를 참조하세요.  이 문서는 Azure 웹 사이트에 중점을 두고 있지만 단계는 [Azure 클라우드 서비스][]를 개발할 때와 비슷합니다.

+ [필수 조건](#prerequisites)
+ [프로젝트 만들기](#create-the-project)
+ [MongoDB 데이터베이스 만들기](#create-a-mongodb-database)
+ [프로젝트 구성](#configure-the-project)
+ [MongoDB 데이터베이스 탐색](#explore-the-mongodb-database)
+ [Azure 웹 사이트에 게시](#publish-to-an-azure-website)
+ [Azure 웹 사이트 구성](#configure-the-azure-website)
+ [다음 단계](#next-steps)

##<a name="prerequisites"></a>필수 조건

 - Visual Studio 2012 또는 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 샘플 VSIX][]
 - [Azure SDK Tools for VS 2013][] 또는 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 32비트][] 또는 [Python 3.4 32비트][]
 - [RoboMongo][](선택 사항)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>프로젝트 만들기

이 섹션에서는 샘플 템플릿을 사용하여 Visual Studio 프로젝트를 만듭니다.  가상 환경을 만들고 필요한 패키지를 설치합니다.  그런 후 기본 메모리 내 리포지토리를 사용하여 로컬로 응용 프로그램을 실행합니다.

1.  Visual Studio에서 **파일**, **새 프로젝트**를 선택합니다. 

1.  PTVS 샘플 VSIX의 프로젝트 템플릿은 **Python**, **Samples**에서 사용할 수 있습니다.  **Polls Bottle Web Project**를 선택하고 확인을 클릭하여 프로젝트를 만듭니다.

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  외부 패키지를 설치할지 묻는 메시지가 표시됩니다.  **Install into a virtual environment**를 선택합니다.

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  기본 해석기로 **Python 2.7** 또는 **Python 3.4**를 선택합니다.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  <kbd>F5</kbd> 키를 눌러 응용 프로그램 작동하는지 확인합니다.  기본적으로 응용 프로그램은 구성이 필요 없는 메모리 내 리포지토리를 사용합니다.  따라서 웹 서버가 중지되면 모든 데이터가 손실됩니다.

1.  **Create Sample Polls**를 클릭하고 poll and vote를 클릭합니다.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>MongoDB 데이터베이스 만들기

데이터베이스에 대해 Azure에서 MongoLab 호스트 데이터베이스를 만들어 보겠습니다.

또는 Azure에서 실행되는 자체의 가상 컴퓨터를 만든 다음 MongoDB를 직접 설치하고 관리할 수도 있습니다.

다음 단계에 따라 MongoLab으로 무료 평가판을 만들 수 있습니다.

1.  [Azure 관리 포털][]에 로그인합니다.

1.  탐색 창 맨 아래에 있는 **새로 만들기**를 클릭합니다.

  	![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  **저장소**를 클릭한 다음 **MongoLab**을 클릭합니다.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  이름에는 데이터베이스 서비스에 사용할 이름을 입력합니다.

1.  데이터베이스 저장소를 찾을 영역/선호도 그룹을 선택합니다. Azure 응용 프로그램에서 데이터베이스를 사용하는 경우 응용 프로그램을 배포할 영역과 동일한 영역을 선택합니다.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  **구매**를 클릭합니다.

##<a name="configure-the-project"></a>프로젝트 구성

이 섹션에서는 우리가 방금 만든 MongoDB 데이터베이스를 사용하도록 응용 프로그램을 구성합니다.  Azure 포털에서 연결 설정을 가져오는 방법을 살펴보겠습니다.  그런 후 응용 프로그램을 로컬로 실행합니다.

1.  [Azure 관리 포털][]에서 **추가 기능**을 클릭한 다음 이전에 만든 MongoLab 서비스를 클릭합니다.

1.  **연결 정보**를 클릭합니다.  복사 단추를 사용하여 **MONGOLAB\_URI** 값을 클립보드에 추가할 수 있습니다.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  Visual Studio의 솔루션 탐색기에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **속성**를 선택합니다.  **디버그** 탭을 클릭합니다.

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  **디버그 서버 명령**, **환경**에서 응용 프로그램에 필요한 환경 변수 값을 설정합니다.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<MONGOLAB_URI 값>
        MONGODB_DATABASE=<데이터베이스 이름>

    이렇게 하면 **디버깅을 시작**할 때 환경 변수가 설정됩니다.  **디버깅하지 않고 시작**하는 경우 변수가 설정되게 하려면 **서버 명령 실행**에서도 같은 값을 설정합니다.

    또는 Windows 제어판을 사용하여 환경 변수를 정의할 수도 있습니다.  소스 코드/프로젝트 파일에 자격 증명을 저장하지 않으려는 경우 이 방법이 더 나은 옵션입니다.  응용 프로그램에서 새 환경 변수가 사용될 수 있게 하려면 Visual Studio를 다시 시작해야 합니다.

1.  MongoDB 리포지토리를 구현하는 코드는 **models/mongodb.py**에 있습니다.

1.  <kbd>F5</kbd> 키를 눌러 응용 프로그램을 실행합니다.  **Create Sample Polls**로 만든 설문 조사와 투표를 통해 제출된 데이터는 MongoDB에서 직렬화됩니다.

1.  **정보** 페이지로 이동하여 응용 프로그램이 **MongoDB** 리포지토리를 사용하고 있는지 확인합니다.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>MongoDB 데이터베이스 탐색

[RoboMongo][]와 같은 응용 프로그램을 사용하여 RoboMongo 데이터베이스에 대해 쿼리하고 편집할 수 있습니다.  이 섹션에서는 RoboMongo를 사용하여 설문 조사 응용 프로그램 데이터베이스의 내용을 확인합니다.

1.  새 연결을 만듭니다.  이전 섹션에서 검색한 **MONGOLAB\_URI**가 필요합니다.

    URI 형식은 `mongodb://<이름>:<암호>@<주소>:<포트>/<이름>`입니다.

    이 이름은 Azure로 서비스를 만들 때 입력한 이름과 일치합니다.  데이터베이스 이름과 사용자 이름 둘 다에 사용됩니다.

1.  연결 페이지에서 **이름**을 연결에 사용하려는 이름으로 설정합니다.  또한 **주소**와 **포트** 필드를 **MONGOLAB\_URI**의 *주소* 및 *포트*로 설정합니다.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  인증 페이지에서 **데이터베이스** 및 **사용자 이름**을 **MONGOLAB\_URI**의 *이름*으로 설정합니다.  또한 **암호**를 **MONGOLAB\_URI**의 *암호*로 설정합니다.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  저장한 후 데이터베이스에 연결합니다.  이제 설문 조사 컬렉션을 쿼리할 수 있습니다.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Azure 웹 사이트에 게시

PTVS에서는 웹 응용 프로그램을 Azure 웹 사이트에 쉽게 배포할 수 있습니다.

1.  **솔루션 탐색기**에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  **Microsoft Azure 웹 사이트**를 클릭합니다.

1.  **새로 만들기**를 클릭하여 새 사이트를 만듭니다.

1.  **사이트 이름** 및 **지역**을 선택한 후 **만들기**를 클릭합니다.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  다른 모든 기본값을 그대로 적용하고 **게시**를 클릭합니다.

1.  웹 브라우저에서 게시된 사이트가 자동으로 열립니다.  정보 페이지로 이동하면 **MongoDB** 리포지토리가 아니라 **메모리 내** 리포지토리가 사용된다는 사실을 확인할 수 있습니다.

    환경 변수가 Azure 웹 사이트에 설정되어 있지 않기 때문이며, **settings.py**에 지정된 기본값이 사용됩니다.

##<a name="configure-the-azure-website"></a>Azure 웹 사이트 구성

이 섹션에서는 사이트에 대한 환경 변수를 구성합니다.

1.  [Azure 관리 포털][]에서 이전 섹션에서 만든 사이트를 클릭합니다.

1.  최상위 메뉴에서 **구성**을 클릭합니다.

  	![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  아래로 스크롤하여 **앱 설정** 섹션으로 이동한 후 위 섹션에 설명된 대로 **REPOSITORY\_NAME**, **MONGODB\_HOST** 및 **MONGODB\_DATABASE** 값을 설정합니다.

  	![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  아래쪽 메뉴에서 **저장**을 클릭하고 **다시 시작**을 클릭한 후 **찾아보기**를 클릭합니다.

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  **MongoDB** 리포지토리를 사용하여 응용 프로그램이 예상대로 작동하는지 확인합니다.

    축하합니다.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>다음 단계

Python Tools for Visual Studio, Bottle 및 MongoDB에 대해 자세히 알아보려면 다음 링크를 참조하세요.

- [Python Tools for Visual Studio 설명서][]
  - [웹 프로젝트][]
  - [클라우드 서비스 프로젝트][]
  - [Microsoft Azure의 원격 디버깅][]
- [Bottle 설명서][]
- [MongoDB][]
- [PyMongo 설명서][]
- [PyMongo][]


<!--Link references-->
[Python 개발자 센터]: /ko-kr/develop/python/
[Azure 클라우드 서비스]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure 관리 포털]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 샘플 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32비트]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32비트]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 설명서]: http://pytools.codeplex.com/documentation
[Bottle 설명서]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[PyMongo 설명서]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Microsoft Azure의 원격 디버깅]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[웹 프로젝트]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[클라우드 서비스 프로젝트]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
