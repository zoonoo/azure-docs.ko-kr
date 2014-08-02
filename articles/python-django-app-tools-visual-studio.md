<properties linkid="develop-python-django-with-visual-studio" urlDisplayName="Django with Visual Studio" pageTitle="Django with Visual Studio (Python) - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to build a Django web application hosted in an Azure virtual machine." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools for Visual Studio 1.5" authors="" solutions="" manager="" editor="" />

Python Tools for Visual Studio 1.5를 사용하여 Django 응용 프로그램 만들기
=========================================================================

**참고:** 이 자습서는 [Youtube 동영상](http://www.youtube.com/watch?v=UsLti4KlgAY)(영문)에도 캡슐화되어 있습니다.

**참고:** PTVS 2.0 Beta를 대상으로 한 [최신의 더 방대한 자습서](../web-sites-python-create-deploy-django-app/)(영문)도 현재 제공됩니다.

사용 가능한 도구를 사용할 경우 Azure용으로 개발하는 작업을 쉽게 수행할 수 있습니다. 이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 가이드를 완료하면 클라우드에서 Django 응용 프로그램을 실행할 수 있게 됩니다.

다음 내용을 배웁니다.

-   기본 Django 응용 프로그램을 만드는 방법
-   Django 테스트 서버를 사용하여 로컬로 Django 응용 프로그램을 실행 및 디버그하는 방법
-   계산 에뮬레이터에서 로컬로 Django 응용 프로그램을 실행하는 방법
-   응용 프로그램을 Azure에 게시 및 다시 게시하는 방법

이 자습서의 지침에 따라 간단한 Hello World 웹 응용 프로그램을 빌드할 수 있습니다. 응용 프로그램은 Azure에서 실행될 때 전용 VM(가상 컴퓨터)에서 자체 호스트되는 웹 역할의 인스턴스에 호스트됩니다.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

개발 환경 설정
--------------

Azure 응용 프로그램 개발을 시작하려면 먼저 도구를 가져오고 개발 환경을 설정해야 합니다. Python용 Azure SDK 다운로드 및 설치에 대한 자세한 내용은 [Python 설치 방법](../python-how-to-install/)(영문)을 참조하십시오.

**참고:** 이 자습서에는 Python 2.7 및 Django 1.4가 필요합니다. 이러한 버전은 현재 Python용 Azure SDK에 포함되어 있습니다.

**참고:** 계산 에뮬레이터 및/또는 Azure에 배포하려면 처음 사용자용 Visual Studio가 필요합니다(통합 셸은 지원되지 않음).

새 Django 응용 프로그램 만들기
------------------------------

새 Django 응용 프로그램을 만들려면 먼저 Visual Studio를 시작한 후 **파일-\>새 프로젝트**를 사용하여 새 프로젝트를 만듭니다. Python 탭(최상위 또는 다른 언어 영역에서 사용 가능)을 찾아 Django 응용 프로그램 템플릿을 선택합니다.

![새 Python 프로젝트 템플릿](./media/python-django-app-tools-visual-studio/ptvs-dj-NewProject.png)

**확인**을 클릭하면 첫 번째 Django 앱이 만들어집니다.

![첫 번째 Django 프로젝트가 열려 있는 Visual Studio](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstProject.png)

이제 첫 번째 Django 앱 개발을 시작합니다. 모듈 노드를 마우스 오른쪽 단추로 클릭하고 **새 Django 앱 추가...**를 선택하여 프로젝트 내에 새 앱을 설정합니다.

![새 앱 추가 메뉴 항목](./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewApp.png)

그러면 응용 프로그램의 새 이름을 입력할 수 있습니다.

![새 앱 추가 이름 프롬프트](./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewAppPrompt.png)

응용 프로그램의 이름을 입력하고 **확인**을 클릭하면 새 앱이 프로젝트에 추가됩니다.

![새 앱이 추가된 솔루션 탐색기](./media/python-django-app-tools-visual-studio/ptvs-dj-MyFirstApp.png)

이제 응용 프로그램이 등록되도록 **settings.py**를 업데이트합니다. 그러면 Django가 앱 템플릿 디렉터리에 추가된 템플릿 파일을 자동으로 검색합니다. INSTALLED\_APPS 섹션에 응용 프로그램 이름을 추가합니다.

    'DjangoApplication.MyFirstApp',

![INSTALLED\_APPS의 settings.py에 앱 추가](./media/python-django-app-tools-visual-studio/ptvs-dj-InstallApp.png)

이제 간단한 템플릿 파일을 반환할 수 있도록 앱의 **views.py**에 코드를 추가합니다.

     from django.http import HttpResponse
    from django.template.loader import render_to_string
    def home(request):
        return HttpResponse(render_to_string(
                                            'index.html',
                                            {'content': 'Hello World'}
                                            ))

![INSTALLED\_APPS의 settings.py에 앱 추가](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstView.png)

그런 다음 이 뷰를 방문할 때 렌더링되는 간단한 템플릿 파일을 추가합니다. 이렇게 하려면 템플릿 폴더를 마우스 오른쪽 단추로 클릭하고 **"새 항목 추가"**를 선택하면 됩니다.

![템플릿 폴더에 새 항목 추가](./media/python-django-app-tools-visual-studio/ptvs-dj-AddFirstTemplate.png)

이제 템플릿 목록에서 "Django HTML 템플릿"을 선택하고 파일 이름으로 **index.html**을 입력할 수 있습니다.

![템플릿 폴더에 새 항목 추가](./media/python-django-app-tools-visual-studio/ptvs-dj-NewDjangoTemplate.png)

그러면 템플릿이 프로젝트에 추가되고 열립니다. 여기서 템플릿 태그를 강조 표시하는 구문의 시작 부분을 볼 수 있습니다.

![솔루션 탐색기에 추가된 템플릿](./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateAdded.png)

계속해서 이때 템플릿 업데이트를 시작하여 렌더링된 HTML을 변경할 수 있으며, 이렇게 하는 동안 멋진 IntelliSense를 얻게 됩니다.

![Django 필터에 대한 템플릿 IntelliSense](./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateIntellisense.png)

대문자화 여부는 이 자습서의 결과를 변화시키지 않으므로 그대로 두어도 괜찮습니다. 마지막으로, **urls.py**의 url 패턴에 뷰를 등록하기만 하면 됩니다. 다음을 **urlpatterns**에 추가합니다.

    url(r'^$', 'DjangoApplication.MyFirstApp.views.home', name='home'),

![URL 등록](./media/python-django-app-tools-visual-studio/ptvs-dj-RegisterUrl.png)

테스트 서버에서 로컬로 응용 프로그램 실행
-----------------------------------------

이때 Django 응용 프로그램을 처음으로 만든 것입니다. 이제 **F5 키를 눌러** 로컬로 Django 응용 프로그램을 실행할 수 있습니다.

![브라우저의 Django Hello World 및 테스트 서버](./media/python-django-app-tools-visual-studio/ptvs-dj-DjangoHelloWorldTestServer.png)

그러면 Django의 **manage.py**를 실행하는 Python 인터프리터가 시작되어 테스트 서버가 실행됩니다. 테스트 서버가 시작되고 나면 웹 브라우저도 시작되어 웹 사이트가 표시됩니다. F5 키로 시작했기 때문에 웹 사이트가 디버거에서 시작되므로 Python 코드의 모든 부분(예: 뷰 코드) 또는 템플릿 파일 자체에서 중단점을 설정할 수도 있습니다.

![템플릿 중단점에서 중지된 디버거](./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateBreakpoint.png)

이제 **중지 단추를 클릭**하고 Azure 계산 에뮬레이터에서의 실행으로 이동할 수 있습니다.

에뮬레이터에서 로컬로 응용 프로그램 실행
----------------------------------------

계산 에뮬레이터 내에서 실행하려면 Django 프로젝트용 솔루션에 Azure 배포 프로젝트를 추가하기만 하면 됩니다.

**참고:** 계산 에뮬레이터 및/또는 Azure에 배포하려면 처음 사용자용 Visual Studio가 필요합니다(통합 셸은 지원되지 않음).

이렇게 하려면 솔루션 탐색기에서 Django 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **"Azure 클라우드 서비스 프로젝트 추가"**를 선택하면 됩니다.

![배포 프로젝트 추가](./media/python-django-app-tools-visual-studio/ptvs-dj-AddDeploymentProject.png)

이 명령을 실행하고 나면 솔루션 탐색기에 새로 추가된 프로젝트를 볼 수 있습니다.

![배포 프로젝트 추가 후](./media/python-django-app-tools-visual-studio/ptvs-dj-AfterDeployProjAdded.png)

이 새 프로젝트는 이제 솔루션에서 시작 프로젝트로도 표시됩니다. 이때 **관리자 권한으로 Visual Studio를 다시 시작**해야 계산 에뮬레이터에서 실행할 수 있지만, 이렇게 하고 나서는 **F5 키를 누르기만 하면** 앱이 계산 에뮬레이터에서 실행 및 배포됩니다.

![배포 프로젝트 추가 후](./media/python-django-app-tools-visual-studio/ptvs-dj-ComputeEmulator.png)

이제 동일한 웹 페이지를 보고 있지만 URL이 약간 다르다는 사실을 알 수 있습니다. 또한 Django 테스트 서버에서 실행되고 있는 python.exe도 없다는 사실을 알 수 있습니다. 대신, Visual Studio 내에서 작업할 때 자동으로 포함 및 설정되는 FastCGI 게이트웨이를 사용하여 IIS를 통해 Django를 실행하고 있습니다.

계산 에뮬레이터에서 실행할 경우 응용 프로그램에 대해 신속하게 반복할 수 있습니다. Visual Studio로 돌아가 파일을 업데이트하고 웹 브라우저를 새로 고치기만 하면 되기 때문입니다. 그러면 결과가 즉시 표시됩니다!

Azure에 응용 프로그램 배포
--------------------------

이제 Azure에 프로젝트를 배포할 준비가 되었습니다. 이렇게 하려면 솔루션 탐색기에서 Azure 배포 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시를 선택**하기만 하면 됩니다.

![패키지 앱 메뉴](./media/python-django-app-tools-visual-studio/ptvs-dj-publish1.png)

게시를 선택하고 나면 Azure에 로그인하라는 메시지가 표시됩니다. 여기서 기존 자격 증명을 가져오거나 새 자격 증명을 설정할 수 있습니다.

![패키지 구독](./media/python-django-app-tools-visual-studio/ptvs-dj-publish2.png)

자격 증명을 선택하고 나면 Azure 게시 설정 화면이 표시됩니다. 배포를 진행할 방법에 대한 다양한 옵션을 선택하거나 그냥 **게시를 누를** 수 있습니다.

![패키지 설정](./media/python-django-app-tools-visual-studio/ptvs-dj-publish3.png)

이제 응용 프로그램이 설정 및 배포될 때까지 기다립니다.

![패키지 배포](./media/python-django-app-tools-visual-studio/ptvs-dj-publish4.png)

모두 설정되면 DNS 이름 아래의 링크를 클릭하여 클라우드에서 실행되는 웹 사이트를 볼 수 있습니다.

![클라우드의 Django 앱](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png)

