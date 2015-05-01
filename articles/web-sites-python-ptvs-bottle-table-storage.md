<properties 
	pageTitle="Python Tools 2.1 for Visual Studio가 있는 Azure의 Bottle 및 Azure 테이블 저장소" 
	description="Python Tools for Visual Studio를 사용하여 Azure 테이블 저장소에 데이터를 저장하고 웹 사이트에 배포할 수 있는 Bottle 응용 프로그램을 만드는 방법에 대해 알아봅니다." 
	services="web-sites" 
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




# Python Tools 2.1 for Visual Studio가 있는 Azure의 Bottle 및 Azure 테이블 저장소 

이 자습서에서는 PTVS 샘플 템플릿 중 하나를 사용하여 간단한 설문 조사 응용 프로그램을 만들어 보겠습니다. 이 자습서는 [비디오](https://www.youtube.com/watch?v=GJXDGaEPy94)로도 제공됩니다.

설문 조사 응용 프로그램은 리포지토리의 추상화를 정의하므로 여러 다른 유형의 리포지토리(메모리 내, Azure 테이블 저장소, MongoDB) 간을 쉽게 전환할 수 있습니다.

Azure 저장소 계정을 만드는 방법, Azure 테이블 저장소를 사용하도록 응용 프로그램을 구성하는 방법 및 Azure 웹 사이트에 응용 프로그램을 게시하는 방법을 알아봅니다.

Bottle, Flask 및 Django 웹 프레임워크, MongoDB, Azure 테이블 저장소, MySQL 및 SQL 데이터베이스 서비스를 사용하여 PTVS로 Azure 웹 사이트를 개발하는 내용을 다루는 추가 문서에 대해서는 [Python 개발자 센터][]를 참조하세요.  이 문서는 Azure 웹 사이트를 중점적으로 다루지만 포함된 단계는 [Azure 클라우드 서비스][]를 개발할 때와 비슷합니다.

+ [필수 조건](#prerequisites)
+ [프로젝트 만들기](#create-the-project)
+ [Azure 저장소 계정 만들기](#create-an-azure-storage-account)
+ [프로젝트 구성](#configure-the-project)
+ [Azure 테이블 저장소 탐색](#explore-the-azure-table-storage)
+ [Azure 웹 사이트에 게시](#publish-to-an-azure-website)
+ [Azure 웹 사이트 구성](#configure-the-azure-website)
+ [다음 단계](#next-steps)

##<a name="prerequisites"></a>필수 조건

 - Visual Studio 2012 또는 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio 샘플 VSIX][]
 - [Azure SDK Tools for VS 2013][] 또는 [Azure SDK Tools for VS 2012][]
 - [Python 2.7 32비트][] 또는 [Python 3.4 32비트][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>프로젝트 만들기

이 섹션에서는 샘플 템플릿을 사용하여 Visual Studio 프로젝트를 만듭니다.  가상 환경을 만들고 필요한 패키지를 설치합니다.  그런 후 기본 메모리 내 리포지토리를 사용하여 로컬로 응용 프로그램을 실행합니다.

1.  Visual Studio에서 **파일**, **새 프로젝트**를 선택합니다.

1.  PTVS 샘플 VSIX의 프로젝트 테플릿은 **Python**, **샘플**에서 사용할 수 있습니다.  **Polls Bottle Web Project**를 선택하고 확인을 클릭하여 프로젝트를 만듭니다.

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  외부 패키지를 설치할지 묻는 메시지가 표시됩니다.  **가상 환경에 설치**를 선택합니다.

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  기본 해석기로 **Python 2.7** 또는 **Python 3.4**를 선택합니다.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  <kbd>F5</kbd> 키를 눌러 응용 프로그램이 작동하는지 확인합니다.  기본적으로 응용 프로그램은 구성이 필요하지 않은 메모리 내 리포지토리를 사용합니다.  따라서 웹 서버가 중지되면 모든 데이터가 손실됩니다.

1.  **Create Sample Polls**를 클릭하고 poll and vote를 클릭합니다.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Azure 저장소 계정 만들기

저장소 작업을 사용하려면 Azure 저장소 계정이 필요합니다. 다음 단계에 따라 저장소 계정을 만들 수 있습니다.

1.  [Azure 관리 포털][]에 로그인합니다.

1.  탐색 창 맨 아래에 있는 **새로 만들기**를 클릭합니다.

  	![New Button](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzurePlusNew.png)

1.  **데이터 서비스**, **저장소** 및 **빠른 생성**을 차례로 클릭합니다.

  	![Quick Create](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureStorageCreate.png)

1.  URL에서 저장소 계정의 URI에 사용할 하위 도메인 이름을 입력합니다.  이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 값은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URI 내의 호스트 이름이 됩니다.

1.  저장소를 찾을 영역/선호도 그룹을 선택합니다. Azure 응용 프로그램에서 저장소를 사용하는 경우 응용 프로그램을 배포할 영역과 동일한 영역을 선택합니다.

1.  선택적으로, 지역에서 복제를 사용하도록 설정할 수 있습니다.  지역에서 복제를 사용하면 Azure 저장소는 두 위치에 데이터를 유지합니다. Azure 저장소는 두 위치에서 데이터의 정상 복제본을 여러 개 지속적으로 유지합니다.

1.  **저장소 계정 만들기**를 클릭합니다.

##<a name="configure-the-project"></a>프로젝트 구성

이 섹션에서는 방금 만든 저장소 계정을 사용하도록 응용 프로그램을 구성합니다.  Azure 포털에서 연결 설정을 가져오는 방법을 알아봅니다.  그런 후 응용 프로그램을 로컬로 실행합니다.

1.  [Azure 관리 포털][]에서 이전 섹션에서 만든 저장소 계정을 클릭합니다.

1.  **액세스 키 관리**를 클릭합니다.

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  Visual Studio의 솔루션 탐색기에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.  **디버그** 탭을 클릭합니다.

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  **서버 명령 디버그**, **환경**에서 응용 프로그램에 필요한 환경 변수 값을 설정합니다.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    이렇게 하면 **디버깅 시작**을 사용할 때 환경 변수가 설정됩니다.  **디버깅하지 않고 시작**을 사용할 때 해당 변수를 설정하려면 **서버 명령 실행**에서도 동일한 값을 설정합니다.

    또는 Windows 제어판을 사용하여 환경 변수를 정의할 수 있습니다.  소스 코드/프로젝트 파일에 자격 증명을 저장하지 않으려는 경우에는 이 방법이 더 나은 옵션입니다.  응용 프로그램에서 새 환경 값이 사용되려면 Visual Studio를 다시 시작해야 합니다.

1.  Azure 테이블 저장소 리포지토리를 구현하는 코드는 **models/azuretablestorage.py**에 있습니다.  Python의 테이블 서비스를 사용하는 방법에 대한 자세한 내용은 [설명서]를 참조하세요.

1.  <kbd>F5</kbd> 키를 눌러 응용 프로그램을 실행합니다.  **Create Sample Polls**를 사용하여 만든 설문 조사와 투표를 통해 제출된 데이터는 Azure 테이블 저장소에서 serialize됩니다.

1.  **정보** 페이지로 가서 응용 프로그램이 **Azure 테이블 저장소** 리포지토리를 사용하고 있는지 확인합니다.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Azure 테이블 저장소 탐색

Visual Studio에서 서버 탐색기를 사용하여 저장소 테이블을 쉽게 보고 편집할 수 있습니다.  이 섹션에서는 서버 탐색기를 사용하여 설문 조사 응용 프로그램 테이블의 내용을 확인합니다.

> [AZURE.NOTE] 이를 위해서는 [Azure SDK for .NET][]의 일부로 사용할 수 있는 Microsoft Azure 도구가 설치되어 있어야 합니다.

1.  **서버 탐색기**를 엽니다.  **Azure**, **저장소**, 저장소 계정 및 **테이블**을 차례로 확장합니다.

  	![Server Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)

1.  **polls** 또는 **choices** 테이블을 두 번 클릭하여 문서 창에서 테이블 내용을 확인하고 add/remove/edit 엔터티를 확인할 수 있습니다.

  	![Table Query Results](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Azure 웹 사이트에 게시

PTVS는 Azure 웹 사이트에 웹 응용 프로그램을 배포하는 쉬운 방법을 제공합니다.

1.  **솔루션 탐색기**에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  **Microsoft Azure 웹 사이트**를 클릭합니다.

1.  **새로 만들기**를 클릭하여 새 사이트를 만듭니다.

1.  **사이트 이름** 및 **지역**을 선택하고 **만들기**를 클릭합니다.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonCreateWebSite.png)

1.  다른 모든 기본값을 그대로 적용하고 **게시**를 클릭합니다.

1.  웹 브라우저가 자동으로 게시된 사이트로 열립니다.  정보 페이지로 이동하면 **Azure 테이블 저장소** 리포지토리가 아니라 **메모리 내** 리포지토리를 사용한다는 것을 알 수 있습니다.

    환경 변수가 Azure 웹 사이트에 설정되어 있지 않아서 **settings.py**에 지정된 기본값을 사용하기 때문입니다.

##<a name="configure-the-azure-website"></a>Azure 웹 사이트 구성

이 섹션에서는 사이트에 대한 환경 변수를 구성합니다.

1.  [Azure 관리 포털][]에서 이전 섹션에서 만든 사이트를 클릭합니다.

1.  위쪽 메뉴에서 **구성**을 클릭합니다.

  	![Top Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteTopMenu.png)

1.  **앱 설정** 섹션으로 스크롤한 후 위 섹션에 설명된 대로 **REPOSITORY\_NAME**, **STORAGE\_NAME** 및 **STORAGE\_KEY** 값을 설정합니다.

  	![App Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. 아래쪽 메뉴에서 **저장**을 클릭하고 **다시 시작**을 클릭한 후 **찾아보기**를 클릭합니다.

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  **Azure 테이블 저장소** 리포지토리를 사용하여 예상한 대로 응용 프로그램이 작동하는지 확인합니다.

    축하합니다.

  	![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>다음 단계

Python Tools for Visual Studio, Bottle 및 Azure 테이블 저장소에 대해 자세히 알아보려면 다음 링크를 참조하세요.

- [Python Tools for Visual Studio 설명서][]
  - [웹 프로젝트][]
  - [클라우드 서비스 프로젝트][]
  - [Microsoft Azure의 원격 디버깅][]
- [Bottle 설명서][]
- [Azure 저장소][]
- [Azure SDK for Python][]
- [Python에서 테이블 저장소 서비스를 사용하는 방법][]


<!--Link references-->
[Python 개발자 센터]: /ko-kr/develop/python/
[Azure 클라우드 서비스]: ../cloud-services-python-ptvs/
[설명서]: ../storage-python-how-to-use-table-storage/
[Python에서 테이블 저장소 서비스를 사용하는 방법]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Azure 관리 포털]: https://manage.windowsazure.com
[.NET용 Azure SDK]: http://azure.microsoft.com/downloads/
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio 샘플 VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32비트]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32비트]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 설명서]: http://pytools.codeplex.com/documentation
[Bottle 설명서]: http://bottlepy.org/docs/dev/index.html
[Microsoft Azure의 원격 디버깅]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[웹 프로젝트]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[클라우드 서비스 프로젝트]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure 저장소]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK for Python]: https://github.com/Azure/azure-sdk-for-python



<!--HONumber=42-->
