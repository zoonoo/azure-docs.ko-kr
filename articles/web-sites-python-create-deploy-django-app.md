<properties linkid="develop-python-web-site-with-django" urlDisplayName="Websites with Django" pageTitle="Python Websites with Django - Azure tutorial" metaKeywords="Azure django, django website" description="A tutorial that introduces you to running a Python website on Azure." metaCanonical="" services="web-sites" documentationCenter="Python" title="Creating Websites with Django" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Django를 사용하여 웹 사이트 만들기

이 자습서에서는 Azure 웹 사이트에서 Python을 실행하는 방법을 설명합니다. Azure 웹 사이트는 제한된 무료 호스팅 및 신속한 배포 기능을 제공하며, 이제 Python도 사용할 수 있습니다! 앱이 증가하면 유료 호스팅으로 전환하여 다른 모든 Azure 서비스와 통합할 수도 있습니다.

이 자습서에서는 Django 웹 프레임워크를 사용하여 빌드된 응용 프로그램을 배포하는 방법을 보여 줍니다. 이 자습서는 Django를 포함하여 응용 프로그램 및 필수 라이브러리를 배포하는 단계를 안내해 줍니다. Git 리포지토리에 모든 항목을 넣으면 빠르고 간편하게 웹 사이트에 업데이트를 푸시할 수 있습니다. 마지막으로 Python 응용 프로그램을 실행하도록 Azure를 통해 새로 만든 사이트를 구성합니다.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

이 자습서에서는 Python 2.7 및 Django 1.4를 사용합니다. 이러한 프로그램을 직접 구할 수도 있고, [http://www.windowsazure.com/ko-kr/develop/python/](http://www.windowsazure.com/ko-kr/develop/python/) 에서 Windows Installer 링크를 사용하여 빠르고 쉽게 설치할 수도 있습니다.

**참고**: 이제 Azure 웹 사이트에는 Python(2.7.3 또는 3.4.0 중에서 선택) 및 wfastcgi 처리기가 미리 설치되어 있습니다. 그러나 Django와 같은 웹 프레임워크는 포함되어 있지 않습니다. 원하는 경우 다른 Python 인터프리터도 계속 사용할 수 있습니다. Git 리포지토리에 원하는 인터프리터를 포함하고 이미 설치된 Python 2.7 인터프리터 대신 해당 인터프리터를 사용하도록 웹 사이트를 구성하면 됩니다.

> [WACOM.NOTE] 이제 웹 사이트의 구성 탭을 열고 **Python 버전** 설정을 변경하여 Azure 웹 사이트 포털에서 사용하려는 Python의 버전을 선택할 수 있습니다.

또한 Azure에 사이트를 푸시하는 배포 옵션을 설치해야 합니다. 다양한 배포 도구를 사용할 수 있지만 이 자습서에서는 Git를 사용합니다. [msysgit][msysgit]를 사용하는 것이 좋습니다.

**참고**: 현재 Python 프로젝트에서는 TFS 게시를 사용할 수 없습니다.

Python, Django 및 Git를 설치하면 필요한 모든 구성 요소를 충족하게 됩니다.

## 포털에서 웹 사이트 만들기

앱을 만드는 첫 번째 단계는 Azure 관리 포털을 통해 웹 사이트를 만드는 것입니다. 그렇게 하려면 포털에 로그인하여 왼쪽 아래 모서리에서 새로 만들기 단추를 클릭해야 합니다. 창이 나타납니다. **빠른 생성**을 클릭하고 URL을 입력하고 **웹 사이트 만들기**를 선택합니다.

![][0]

사이트가 빠르게 설정됩니다. 그런 다음 Git를 통해 게시를 손쉽게 진행합니다. **소스 제어에서 배포 설정**을 선택하면 됩니다.

![][1]

**배포 설정** 대화 상자에서 아래로 스크롤하여 **로컬 Git** 옵션을 선택합니다. 오른쪽 화살표를 클릭하여 계속합니다.

![][2]

Git 게시를 설정한 후 리포지토리가 만들어지고 있다고 알리는 페이지가 잠시 나타납니다. 리포지토리가 준비되면 배포 탭으로 이동됩니다. 배포 탭에는 연결하는 방법에 대한 지침이 포함되어 있습니다.

![][3]

## 웹 사이트 개발

Azure에서 Git 리포지토리를 만들었으므로 로컬 컴퓨터에서 웹 사이트로 리포지토리를 채웁니다. 첫 번째 단계는 제공된 URL을 사용하여 기존의 빈 사이트를 복제하는 것입니다.

![][4]

여기에서 웹 사이트에 대한 참여를 설정할 수 있습니다. 다음과 같은 몇 가지 작업을 수행해야 합니다.

1.  웹 사이트를 실행하기 위해 사용할 Django 라이브러리 및 기타 라이브러리를 포함합니다.
2.  Django 응용 프로그램 코드를 포함합니다.

먼저, Django 라이브러리를 포함합니다. 그렇게 하려면 site-packages라는 새로운 디렉터리를 만들고 다음 명령을 사용하여 설치된 버전의 Django를 복사하십시오.

    mkdir site-packages
    cd site-packages
    xcopy /s C:\Python27\lib\site-packages\* .

이러한 명령은 Django를 포함하여 site-packages에 있는 모든 라이브러리를 복사합니다. 웹 사이트에 사용되지 않는 라이브러리가 있는 경우 제거하십시오.

![][5]

그런 다음, 첫 번째 Django 응용 프로그램을 만듭니다. 이를 위해 명령줄에서 다른 Django 응용 프로그램을 만들거나 [Python Tools for Visual Studio][Python Tools for Visual Studio](영문)를 참조하여 프로젝트를 만들 수 있습니다. 두 옵션 모두가 아래에 나와 있습니다.

**옵션 1:**
명령줄에서 새 프로젝트를 만들려면 다음 명령을 실행하십시오. 이 명령은 DjangoApplication 폴더에 Django 응용 프로그램을 만듭니다.

     C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication

![][6]

**옵션 2:**
또한 Python Tools for Visual Studio를 사용하여 새로운 사이트를 만들 수도 있습니다. Python Tools for Visual Studio가 설치된 Visual Studio를 시작하고 **파일**-\>**새 프로젝트**를 선택합니다. **기타 언어**에서 Python 프로젝트로 이동하여 **Django 응용 프로그램**을 선택합니다. 프로젝트 이름으로 **DjangoApplication**을 입력하고 명령줄에서 Django 응용 프로그램을 만들 때와 같은 정확한 디렉터리 구조를 가져올 수 있도록 **솔루션용 디렉터리 만들기**가 선택 취소되어 있는지 확인하십시오. 이 옵션은 Visual Studio 솔루션 및 프로젝트 파일을 설정하여 템플릿 디버깅 및 IntelliSense를 포함하는 뛰어난 로컬 개발 환경을 제공합니다.

![][7]

이제 방금 추가한 모든 파일을 추가하고 사이트를 Git로 푸시하면 됩니다. 그렇게 하려면 다음 명령을 실행하십시오.

    git add DjangoApplication site-packages
    git commit -m "Initial site"
    git remote add azure https://dinov@pythonwebsite.scm.azurewebsites.net/PythonWebSite.git
    git push azure master

첫 번째 명령은 추적되지 않는 파일이 추적되도록 추가합니다. 두 번째 명령은 리포지토리에 방금 추가한 파일을 커밋합니다. 세 번째 명령은 리포지토리에 대한 원격을 *azure* 이름으로 추가합니다. 마지막 명령은 변경 내용을 적용하여 원격 리포지토리로 푸시합니다. 또한 마지막 명령은 배포도 시작합니다. 그렇게 하면 다음과 유사한 결과가 나타나야 합니다.

![][8]

푸시를 수행하면 Azure 포털이 새로 고쳐지고 활성 배포가 표시됩니다.

![][9]

## 웹 사이트 구성

이제 Django 프로젝트에 대해 인식하도록 웹 사이트를 구성하고 wfastcgi 처리기를 사용해야 합니다. 이를 위해 화면 맨 위에 있는 구성을 클릭한 후 **앱 설정** 및 **처리기 매핑** 섹션이 있는 페이지 아래쪽으로 스크롤하십시오.

여기에서 지정한 모든 설정은 실제 요청 중에 환경 변수로 전환됩니다. 즉, 이 설정을 사용하여 DJANGO\_SETTINGS\_MODULE 환경 변수와 PYTHONPATH 및 WSGI\_HANDLER를 구성할 수 있습니다. 응용 프로그램에 다른 구성 값이 있는 경우 여기에서 이러한 값을 할당하고 환경 외부에서 선택할 수 있습니다. 때로는 웹 사이트의 파일 경로를 설정할 수 있습니다. 예를 들어 PYTHONPATH에 대해 이 작업을 수행할 수 있습니다. Azure 웹 사이트로 실행할 때 웹 사이트는 \*\*D:\\home\\site\\wwwroot\*\*에서 사용되므로, 디스크의 파일에 대한 전체 경로가 필요한 어떤 위치에서든 사용할 수 있습니다.

Django 응용 프로그램을 설정하려면 환경 변수를 3개 만들어야 합니다. 첫 번째는 Django 응용 프로그램의 모듈 이름을 제공하는 DJANGO\_SETTINGS\_MODULE이며 구성에 사용됩니다. 두 번째는 설정 모듈이 사용되는 패키지를 지정하는 PYTHONPATH 환경 변수입니다. 세 번째는 WSGI\_HANDLER입니다. 이 변수의 이름은 모듈/패키지 이름과 사용할 모듈의 특성 순으로 지정됩니다(예: mypackage.mymodule.handler). 괄호를 추가하여 특성을 호출해야 한다는 것을 나타냅니다. 따라서 이러한 변수에 대해 다음과 같이 설정합니다.

    DJANGO_SETTINGS_MODULE    DjangoApplication.settings
    PYTHONPATH                D:\home\site\wwwroot\DjangoApplication;D:\home\site\wwwroot\site-packages
    WSGI_HANDLER              django.core.handlers.wsgi.WSGIHandler()

![][10]

이제 처리기 매핑을 구성해야 합니다. 그렇게 하려면 Python 인터프리터 경로 및 wfastcgi.py 스크립트를 사용하여 모든 확장에 대한 처리기를 등록합니다.

    EXTENSION                 *
    SCRIPT PROCESSOR PATH     D:\python27\python.exe
    ADDITIONAL ARGUMENTS      D:\python27\scripts\wfastcgi.py

![][11]

여기서 맨 아래에 있는 **저장** 단추를 클릭할 수 있습니다.

마지막으로 대시보드로 다시 돌아갑니다. 왼쪽 아래에 있는 **사이트 URL**로 이동합니다. 링크를 클릭하여 다음과 유사한 새 Django 사이트를 엽니다.

![][12]

## 다음 단계

여기에서 기존 도구를 사용하여 Django 응용 프로그램의 개발을 계속할 수 있습니다. 개발에 [Python Tools for Visual Studio][Python Tools for Visual Studio](영문)를 사용하는 경우 [VisualGit][VisualGit]를 설치하여 Visual Studio에서 소스 제어 통합을 가져올 수 있습니다.

앱이 사용하는 언어가 Python 및 Django 외의 언어일 수도 있습니다. [http://www.windowsazure.com/ko-kr/develop/python/](http://www.windowsazure.com/ko-kr/develop/python/) (영문)에서 설치 관리자를 사용하여 Python을 설치한 경우 PIP가 이미 설치되어 있습니다. PIP를 사용하여 새로운 종속성을 빠르게 추가할 수 있습니다. 예를 들어 자연어 툴킷 및 모든 종속성을 설치하려면 다음을 입력하십시오.

    pip install nltk

그런 다음 C:\\Python27\\Lib\\site-packages에서 로컬 site-packages 디렉터리로 파일을 복사하여 site-packages 디렉터리를 업데이트해야 합니다.

파일을 복사한 후 **git status** 명령을 실행하여 새로 추가된 파일을 확인하고 **git add**와 **git commit**을 순서대로 사용하여 리포지토리의 변경 내용을 커밋합니다. 마지막으로 업데이트된 웹 사이트를 Azure로 배포하는 **git push**를 수행할 수 있습니다.

이제 DjangoApplication 디렉터리로 이동할 수 있으며, 일반적으로 새 응용 프로그램을 Django 프로젝트에 추가할 때 manage.py를 사용할 수 있습니다.

  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [0]: http://www.windowsazure.com/ko-kr/develop/python/
  [msysgit]: http://code.google.com/p/msysgit/
  [0]: ./media/web-sites-python-create-deploy-django-app/django-ws-003.png
  [1]: ./media/web-sites-python-create-deploy-django-app/django-ws-004.png
  [2]: ./media/web-sites-python-create-deploy-django-app/django-ws-005.png
  [3]: ./media/web-sites-python-create-deploy-django-app/django-ws-006.png
  [4]: ./media/web-sites-python-create-deploy-django-app/django-ws-007.png
  [5]: ./media/web-sites-python-create-deploy-django-app/django-ws-008.png
  [Python Tools for Visual Studio]: http://pytools.codeplex.com/
  [6]: ./media/web-sites-python-create-deploy-django-app/django-ws-010.png
  [7]: ./media/web-sites-python-create-deploy-django-app/django-ws-011.png
  [8]: ./media/web-sites-python-create-deploy-django-app/django-ws-013.png
  [9]: ./media/web-sites-python-create-deploy-django-app/django-ws-014.png
  [10]: ./media/web-sites-python-create-deploy-django-app/django-ws-015.png
  [11]: ./media/web-sites-python-create-deploy-django-app/django-ws-016.png
  [12]: ./media/web-sites-python-create-deploy-django-app/django-ws-017.png
  [VisualGit]: http://code.google.com/p/visualgit/
