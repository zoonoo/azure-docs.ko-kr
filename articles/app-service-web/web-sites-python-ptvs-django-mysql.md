---
title: "Azure의 Django 및 MySQL과 Python Tools 2.2 for Visual Studio"
description: "Python Tools for Visual Studio를 사용하여 MySQL 데이터베이스 인스턴스에 데이터를 저장하고 Azure 앱 서비스 웹앱에 배포할 수 있는 Django 웹앱을 만드는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: c60a50b5-8b5e-4818-a442-16362273dabb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: get-started-article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 1c29cfc0a5b6361a7f526c37d5421ee4be3fe2c1


---
# <a name="django-and-mysql-on-azure-with-python-tools-22-for-visual-studio"></a>Azure의 Django 및 MySQL과 Python Tools 2.2 for Visual Studio
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

이 자습서에서는 PTVS 샘플 템플릿 중 하나를 사용하여 간단한 설문 조사 웹앱을 만들기 위해 [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python)를 사용합니다. Azure에서 호스트된 MySQL 서비스를 사용하는 방법, MySQL을 사용하도록 웹앱을 구성하는 방법 및 [Azure 앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)에 웹앱을 게시하는 방법을 알아봅니다.

> [!NOTE]
> 이 자습서에 포함된 정보는 다음 비디오에서 제공됩니다.
> 
> [PTVS 2.1: MySQL을 사용하는 Django 앱][video]
> 
> 

Bottle, Flask 및 Django 웹 프레임워크, Azure 테이블 저장소, MySQL 및 SQL 데이터베이스 서비스를 사용하여 PTVS로 Azure 앱 서비스 웹앱을 개발하는 내용을 다루는 추가 문서에 대해서는 [Python 개발자 센터] 를 참조하세요. 이 문서는 앱 서비스를 중점적으로 다루지만 포함된 단계는 [Azure 클라우드 서비스]를 개발할 때와 비슷합니다.

## <a name="prerequisites"></a>필수 조건
* Visual Studio 2015
* [Python 2.7 32비트] 또는 [Python 3.4 32비트]
* [Python Tools 2.2 for Visual Studio]
* [Python Tools 2.2 for Visual Studio Samples VSIX]
* [Azure SDK Tools for VS 2015]
* Django 1.9 이상

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](https://azure.microsoft.com/try/app-service/)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드 및 약정은 필요하지 않습니다.
> 
> 

## <a name="create-the-project"></a>프로젝트 만들기
이 섹션에서는 샘플 템플릿을 사용하여 Visual Studio 프로젝트를 만듭니다. 가상 환경을 만들고 필요한 패키지를 설치합니다. sqlite를 사용하여 로컬 데이터베이스를 만듭니다. 그런 후 응용 프로그램을 로컬로 실행합니다.

1. Visual Studio에서 **파일**, **새 프로젝트**를 선택합니다.
2. [Python Tools 2.2 for Visual Studio Samples VSIX]의 프로젝트 템플릿은 **Python**, **샘플**에서 사용할 수 있습니다. **Polls Django Web Project** 를 선택하고 확인을 클릭하여 프로젝트를 만듭니다.
   
    ![새 프로젝트 대화 상자](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)
3. 외부 패키지를 설치할지 묻는 메시지가 표시됩니다. **가상 환경에 설치**를 선택합니다.
   
    ![외부 패키지 대화 상자](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)
4. 기본 해석기로 **Python 2.7** 또는 **Python 3.4**를 선택합니다.
   
    ![가상 환경 추가 대화 상자](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)
5. **솔루션 탐색기**에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **Python**을 선택한 다음 **Django Migrate**를 선택합니다.  그런 다음 **Django Create Superuser**를 선택합니다.
6. Django 관리 콘솔이 열리고 프로젝트 폴더에 sqlite 데이터베이스를 만듭니다. 프롬프트에 따라 사용자를 만듭니다.
7. `F5`키를 눌러 응용 프로그램이 작동하는지 확인합니다.
8. 위쪽의 탐색 모음에서 **로그인** 을 클릭합니다.
   
    ![Django 탐색 모음](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)
9. 데이터베이스를 동기화할 때 만든 사용자의 자격 증명을 입력합니다.
   
    ![로그인 형식](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)
10. **Create Sample Polls**를 클릭합니다.
    
     ![Create Sample Polls](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)
11. poll and vote를 클릭합니다.
    
     ![샘플 설문 조사 투표](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-mysql-database"></a>MySQL 데이터베이스 만들기
데이터베이스에 대해 Azure에서 ClearDB MySQL 호스트 데이터베이스를 만듭니다.

또는 Azure에서 실행되는 자체의 가상 컴퓨터를 만든 다음 MySQL을 직접 설치하고 관리할 수도 있습니다.

다음 단계에 따라 무료로 데이터베이스를 만들 수 있습니다.

1. [Azure 포털]에 로그인합니다.
2. 탐색 창 맨 위에서 **새로 만들기**를 클릭한 다음 **데이터 + 저장소**를 클릭한 다음 **MySQL 데이터베이스**를 클릭합니다.
3. 새 리소스 그룹을 만들어 새 MySQL 데이터베이스를 구성하고 적절한 위치를 선택합니다.
4. MySQL 데이터베이스를 만든 후 데이터베이스 블레이드에서 **속성** 을 클릭합니다.
5. 복사 단추를 사용하여 **CONNECTION STRING** 값을 클립보드에 저장합니다.

## <a name="configure-the-project"></a>프로젝트 구성
이 섹션에서는 방금 만든 MySQL 데이터베이스를 사용하도록 웹앱을 구성합니다. Django에서 MySQL 데이터베이스를 사용하는 데 필요한 추가 Python 패키지도 설치합니다. 그런 다음 웹앱을 로컬로 실행합니다.

1. Visual Studio에서 **ProjectName**폴더의 *settings.py* 를 엽니다. 일시적으로 연결 문자열을 편집기에 붙여 넣습니다. 연결 문자열은 다음 형식입니다.
   
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
2. 솔루션 탐색기의 **Python Environments**에서 가상 환경을 마우스 오른쪽 단추로 클릭하고 **Install Python Package**를 선택합니다.
3. **pip**를 사용하여 패키지 `mysqlclient`를 설치합니다.
   
    ![설치 패키지 대화 상자](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)
4. **솔루션 탐색기**에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **Python**을 선택한 다음 **Django Migrate**를 선택합니다.  그런 다음 **Django Create Superuser**를 선택합니다.
   
    이렇게 하면 이전 섹션에서 만든 MySQL 데이터베이스에 대한 테이블이 만들어집니다. 프롬프트에 따라 사용자를 만듭니다. 이 사용자가 이 문서의 첫 번째 섹션에서 만든 sqlite 데이터베이스의 사용자와 일치할 필요는 없습니다.
5. `F5`키를 눌러 응용 프로그램을 실행합니다. **Create Sample Polls** 를 사용하여 만든 설문 조사와 투표를 통해 제출된 데이터는 MySQL 데이터베이스에서 serialize됩니다.

## <a name="publish-the-web-app-to-azure-app-service"></a>Azure 앱 서비스에 웹앱 게시
Azure .NET SDK를 통해 Azure 앱 서비스에 웹앱을 쉽게 배포할 수 있습니다.

1. **솔루션 탐색기**에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
   
    ![웹 게시 대화 상자](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)
2. **Microsoft Azure 앱 서비스**를 클릭합니다.
3. **새로 만들기** 를 클릭하여 새 웹앱을 만듭니다.
4. 다음 필드에 입력하고 **만들기**를 클릭합니다.
   
   * **웹앱 이름**
   * **앱 서비스 계획**
   * **리소스 그룹**
   * **지역**
   * **데이터베이스 서버**를 **데이터베이스 없음**으로 그대로 설정합니다.
5. 다른 모든 기본값을 그대로 적용하고 **게시**를 클릭합니다.
6. 웹 브라우저가 자동으로 게시된 웹앱으로 열립니다. Azure에 호스트된 **MySQL** 데이터베이스를 사용하여 예상한 대로 웹앱이 작동하는지 확인합니다.
   
    ![웹 브라우저](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)
   
    축하합니다. Azure에 MySQL 기반 웹앱을 성공적으로 게시했습니다.

## <a name="next-steps"></a>다음 단계
Python Tools for Visual Studio, Django 및 MySQL에 대해 자세히 알아보려면 다음 링크를 참조하세요.

* [Python Tools for Visual Studio 설명서]
  * [웹 프로젝트]
  * [클라우드 서비스 프로젝트]
  * [Microsoft Azure의 원격 디버깅]
* [Django 설명서]
* [MySQL]

자세한 내용은 [Python 개발자 센터](/develop/python/)를 참조하세요.

<!--Link references-->

[Python 개발자 센터]: /develop/python/
[Azure 클라우드 서비스]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->

[Azure 포털]: https://portal.azure.com
[Python Tools for Visual Studio]: https://www.visualstudio.com/vs/python/
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 for Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32비트]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32비트]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio 설명서]: http://aka.ms/ptvsdocs
[Microsoft Azure의 원격 디버깅]: http://go.microsoft.com/fwlink/?LinkId=624026
[웹 프로젝트]: http://go.microsoft.com/fwlink/?LinkId=624027
[클라우드 서비스 프로젝트]: http://go.microsoft.com/fwlink/?LinkId=624028
[Django 설명서]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[video]: http://youtu.be/oKCApIrS0Lo



<!--HONumber=Jan17_HO3-->


