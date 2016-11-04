---
title: Azure의 Flask 및 Azure 테이블 저장소와 Python Tools 2.2 for Visual Studio
description: Python Tools for Visual Studio를 사용하여 Azure 테이블 저장소에 데이터를 저장하는 플라스크 웹앱을 만들고 Azure 앱 서비스 웹앱에 배포하는 방법에 대해 알아봅니다.
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo

---
# Azure의 Flask 및 Azure 테이블 저장소와 Python Tools 2.2 for Visual Studio
이 자습서에서는 PTVS 샘플 템플릿 중 하나를 사용하여 간단한 설문 조사 웹앱을 만들기 위해 [Python Tools for Visual Studio]를 사용해 보겠습니다. 이 자습서는 [비디오](https://www.youtube.com/watch?v=qUtZWtPwbTk)로도 제공됩니다.

설문 조사 웹앱은 리포지토리의 추상화를 정의하므로 여러 다른 유형의 리포지토리(메모리 내, Azure 테이블 저장소, MongoDB) 간을 쉽게 전환할 수 있습니다.

Azure 저장소 계정을 만드는 방법, Azure 테이블 저장소를 사용하도록 웹앱을 구성하는 방법 및 [Azure 앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)에 웹앱을 게시하는 방법을 알아봅니다.

Bottle, Flask 및 Django 웹 프레임워크, MongoDB, Azure 테이블 저장소, MySQL 및 SQL 데이터베이스 서비스를 사용하여 PTVS로 Azure 앱 서비스 웹앱을 개발하는 내용을 다루는 추가 문서에 대해서는 [Python 개발자 센터]를 참조하세요. 이 문서는 앱 서비스를 중점적으로 다루지만 포함된 단계는 [Azure 클라우드 서비스]를 개발할 때와 비슷합니다.

## 필수 조건
* Visual Studio 2015
* [Python Tools 2.2 for Visual Studio]
* [Python Tools 2.2 for Visual Studio Samples VSIX]
* [Azure SDK Tools for VS 2015]
* [Python 2.7 32비트] 또는 [Python 3.4 32비트]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## 프로젝트 만들기
이 섹션에서는 샘플 템플릿을 사용하여 Visual Studio 프로젝트를 만듭니다. 가상 환경을 만들고 필요한 패키지를 설치합니다. 그런 후 기본 메모리 내 리포지토리를 사용하여 로컬로 응용 프로그램을 실행합니다.

1. Visual Studio에서 **파일**, **새 프로젝트**를 선택합니다.
2. [Python Tools 2.2 for Visual Studio Samples VSIX]의 프로젝트 템플릿은 **Python**, **샘플**에서 사용할 수 있습니다. **Polls Flask Web Project**를 선택하고 확인을 클릭하여 프로젝트를 만듭니다.
   
     ![새 프로젝트 대화 상자](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)
3. 외부 패키지를 설치할지 묻는 메시지가 표시됩니다. **가상 환경에 설치**를 선택합니다.
   
     ![외부 패키지 대화 상자](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)
4. 기본 해석기로 **Python 2.7** 또는 **Python 3.4**를 선택합니다.
   
     ![가상 환경 추가 대화 상자](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)
5. `F5` 키를 눌러 응용 프로그램이 작동하는지 확인합니다. 기본적으로 응용 프로그램은 구성이 필요하지 않은 메모리 내 리포지토리를 사용합니다. 따라서 웹 서버가 중지되면 모든 데이터가 손실됩니다.
6. **Create Sample Polls**를 클릭하고 poll and vote를 클릭합니다.
   
     ![웹 브라우저](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## Azure 저장소 계정 만들기
저장소 작업을 사용하려면 Azure 저장소 계정이 필요합니다. 다음 단계에 따라 저장소 계정을 만들 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 포털의 왼쪽 위에서 **새로 만들기** 아이콘을 클릭한 다음 **데이터 + 저장소** > **저장소 계정**을 클릭합니다. **만들기**를 클릭하고 저장소 계정에 고유한 이름을 지정한 다음 이를 위한 새 [리소스 그룹](../resource-group-overview.md)을 만듭니다.
   
      ![빠른 생성](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)
   
    저장소 계정이 만들어지면 **알림** 단추가 녹색 **성공**으로 깜박이고 저장소 계정의 블레이드가 열려 새로 만든 리소스 그룹에 속한 것을 보여줍니다.
3. 저장소 계정 블레이드에서 **액세스 키** 부분을 클릭합니다. 계정 이름 및 key1을 기록해 둡니다.
   
      ![구성](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageKeys.png)
   
    다음 섹션에서 프로젝트를 구성하려면 이 정보가 필요합니다.

## 프로젝트 구성
이 섹션에서는 방금 만든 저장소 계정을 사용하도록 응용 프로그램을 구성합니다. Azure 포털에서 연결 설정을 가져오는 방법을 알아봅니다. 그런 후 응용 프로그램을 로컬로 실행합니다.

1. Visual Studio의 솔루션 탐색기에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. **디버그** 탭을 클릭합니다.
   
     ![프로젝트 디버그 설정](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)
2. **서버 명령 디버그**, **환경**에서 응용프로그램에 필요한 환경 변수 값을 설정합니다.
   
       REPOSITORY_NAME=azuretablestorage
       STORAGE_NAME=<storage account name>
       STORAGE_KEY=<primary access key>
   
   이렇게 하면 **디버깅 시작**을 사용할 때 환경 변수가 설정됩니다. **디버깅하지 않고 시작**을 사용할 때 해당 변수를 설정하려면 **서버 명령 실행**에서도 동일한 값을 설정합니다.
   
   또는 Windows 제어판을 사용하여 환경 변수를 정의할 수 있습니다. 소스 코드/프로젝트 파일에 자격 증명을 저장하지 않으려는 경우에는 이 방법이 더 나은 옵션입니다. 응용 프로그램에서 새 환경 값이 사용되려면 Visual Studio를 다시 시작해야 합니다.
3. Azure 테이블 저장소 리포지토리를 구현하는 코드는 **models/azuretablestorage.py**에 있습니다. Python의 테이블 서비스를 사용하는 방법에 대한 자세한 내용은 [설명서]를 참조하세요.
4. `F5` 키를 눌러 응용 프로그램을 실행합니다. **Create Sample Polls**를 사용하여 만든 설문 조사와 투표를 통해 제출된 데이터는 Azure 테이블 저장소에서 serialize됩니다.
   
   > [!NOTE]
   > Python 2.7 가상 환경에는 Visual Studio에서 예외 나누기가 발생할 수 있습니다. `F5` 키를 눌러 웹 프로젝트를 계속 로드합니다.
   > 
   > 
5. **정보** 페이지로 가서 응용 프로그램이 **Azure 테이블 저장소** 리포지토리를 사용하고 있는지 확인합니다.
   
     ![웹 브라우저](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## Azure 테이블 저장소 탐색
Visual Studio에서 클라우드 탐색기를 사용하여 저장소 테이블을 쉽게 보고 편집할 수 있습니다. 이 섹션에서는 서버 탐색기를 사용하여 설문 조사 응용 프로그램 테이블의 내용을 확인합니다.

> [!NOTE]
> 이를 위해서는 [Azure SDK for .NET]의 일부로 사용할 수 있는 Microsoft Azure 도구가 설치되어 있어야 합니다.
> 
> 

1. **클라우드 탐색기**를 엽니다. **저장소 계정**, 사용자의 저장소 계정 및 **테이블**을 차례로 확장합니다.
   
     ![Cloud Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)
2. **polls** 또는 **choices** 테이블을 두 번 클릭하여 문서 창에서 테이블 내용을 확인하고 add/remove/edit 엔터티를 확인할 수 있습니다.
   
     ![테이블 쿼리 결과](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## Azure 앱 서비스에 웹앱 게시
Azure .NET SDK를 통해 Azure 앱 서비스에 웹앱을 쉽게 배포할 수 있습니다.

1. **솔루션 탐색기**에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
   
     ![웹 게시 대화 상자](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)
2. **Microsoft Azure 웹앱**을 클릭합니다.
3. **새로 만들기**를 클릭하여 새 웹앱을 만듭니다.
4. 다음 필드에 입력하고 **만들기**를 클릭합니다.
   
   * **웹앱 이름**
   * **앱 서비스 계획**
   * **리소스 그룹**
   * **지역**
   * **데이터베이스 서버**를 **데이터베이스 없음**으로 그대로 설정합니다.
5. 다른 모든 기본값을 그대로 적용하고 **게시**를 클릭합니다.
6. 웹 브라우저가 자동으로 게시된 웹앱으로 열립니다. 정보 페이지로 이동하면 **Azure 테이블 저장소** 리포지토리가 아니라 **메모리 내** 리포지토리를 사용한다는 것을 알 수 있습니다.
   
   환경 변수가 Azure 앱 서비스의 웹앱 인스턴스에 설정되어 있지 않아서 **settings.py**에 지정된 기본값을 사용하기 때문입니다.

## 웹앱 인스턴스 구성
이 섹션에서는 웹앱 인스턴스에 대한 환경 변수를 구성합니다.

1. [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **앱 서비스** > 사용자의 웹앱 이름을 클릭하여 웹앱 블레이드를 엽니다.
2. 웹앱 블레이드에서 **모든 설정**을 클릭한 다음 **응용 프로그램 설정**을 클릭합니다.
3. **앱 설정** 섹션으로 스크롤한 후 위의 **프로젝트 구성** 섹션에 설명된 대로 **REPOSITORY\_NAME**, **STORAGE\_NAME** 및 **STORAGE\_KEY** 값을 설정합니다.
   
     ![앱 설정](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)
4. **Save**를 클릭합니다. 변경 내용이 적용되었다는 알림을 받은 후에 웹앱 주 블레이드에서 **찾아보기**를 클릭합니다.
5. **Azure 테이블 저장소** 리포지토리를 사용하여 예상한 대로 웹앱이 작동하는지 확인합니다.
   
   축하합니다.
   
     ![웹 브라우저](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## 다음 단계
Python Tools for Visual Studio, Flask 및 Azure 테이블 저장소에 대해 자세히 알아보려면 다음 링크를 참조하세요.

* [Python Tools for Visual Studio 설명서]
  * [웹 프로젝트]
  * [클라우드 서비스 프로젝트]
  * [Microsoft Azure의 원격 디버깅]
* [Flask 설명서]
* [Azure 저장소]
* [Python용 Azure SDK]
* [Python에서 테이블 저장소 서비스를 사용하는 방법]

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

<!--Link references-->
[Python 개발자 센터]: /develop/python/
[Azure 클라우드 서비스]: ../cloud-services-python-ptvs.md
[설명서]: ../storage-python-how-to-use-table-storage.md
[Python에서 테이블 저장소 서비스를 사용하는 방법]: ../storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[Azure SDK for .NET]: http://azure.microsoft.com/downloads/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 for Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?linkid=518003
[Python 2.7 32비트]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32비트]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 설명서]: http://aka.ms/ptvsdocs
[Flask 설명서]: http://flask.pocoo.org/
[Microsoft Azure의 원격 디버깅]: http://go.microsoft.com/fwlink/?LinkId=624026
[웹 프로젝트]: http://go.microsoft.com/fwlink/?LinkId=624027
[클라우드 서비스 프로젝트]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure 저장소]: http://azure.microsoft.com/documentation/services/storage/
[Python용 Azure SDK]: https://github.com/Azure/azure-sdk-for-python


<!---HONumber=AcomDC_0713_2016-->