<properties linkid="web-site-with-webmatrix" urlDisplayName="Web site with WebMatrix" pageTitle="Node.js web site with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure web site." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build and deploy a Node.js web site to Azure using WebMatrix" authors="" solutions="" manager="" editor="" />

WebMatrix를 사용하여 Node.js 웹 사이트 빌드 및 Azure에 배포
===========================================================

이 자습서에서는 WebMatrix를 사용하여 Node.js 응용 프로그램을 개발하고 Azure 웹 사이트에 배포하는 방법을 보여 줍니다. WebMatrix는 웹 사이트 개발에 필요한 모든 기능이 포함된 Microsoft의 무료 웹 개발 도구입니다. WebMatrix에는 코드 완성, 미리 빌드된 템플릿, Jade, LESS 및 CoffeeScript에 대한 편집기 지원 등 Node.js를 쉽게 사용할 수 있는 몇 가지 기능이 포함되어 있습니다. [Azure용 WebMatrix](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)(영문)에 대해 자세히 알아보십시오.

이 가이드를 완료하면 Azure에서 실행하는 Node.js 웹 사이트가 완성됩니다.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure 노드 웹 사이트](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Azure에 로그인
--------------

다음 단계에 따라 Azure 웹 사이트를 만듭니다.

**참고**

이 자습서를 완료하려면 Azure 웹 사이트 기능이 사용하도록 설정된 Azure 계정이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A7171371E)을 참조하십시오.

1.  WebMatrix를 시작합니다.
2.  이번에 처음으로 WebMatrix를 사용하는 경우 Azure에 로그인하라는 메시지가 표시됩니다. 또는 **로그인** 단추를 클릭하고 **계정 추가**를 선택할 수 있습니다. Microsoft 계정을 사용하여 **로그인**하도록 선택합니다.

    ![계정 추가](./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png)

3.  Azure 계정을 등록한 경우 Microsoft 계정을 사용하여 로그인할 수 있습니다.

    ![Azure에 로그인](./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png)

Azure용 기본 제공 템플릿을 사용하여 사이트 만들기
-------------------------------------------------

1.  시작 화면에서 **새로 만들기** 단추를 클릭하고 **템플릿 갤러리**를 선택하여 템플릿 갤러리에서 새 사이트를 만듭니다.

    ![템플릿 갤러리에서 새 사이트 만들기](./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png)

2.  **템플릿의 사이트** 대화 상자에서 **노드**를 선택한 후 **빠른 사이트**를 선택합니다. 마지막으로 **다음**을 클릭합니다. **빠른 사이트** 템플릿의 필수 조건?이 없는 경우 해당 요소를 설치하라는 메시지가 표시됩니다.

    ![빠른 템플릿 선택](./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png)

3.  Azure에 로그인하면 이제 로컬 사이트에 대한 Azure 웹 사이트를 만들 수 있는 옵션이 표시됩니다. 고유한 이름을 선택하고 사이트를 만들 데이터 센터를 선택합니다.

    ![Azure에서 사이트 만들기](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png)

4.  WebMatrix에서 웹 사이트 빌드를 완료하면 WebMatrix IDE가 표시됩니다.

    ![WebMatrix IDE](./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png)

Azure에 응용 프로그램 게시
--------------------------

1.  WebMatrix의 **홈** 리본에서 **게시**를 클릭하여 웹 사이트의 **게시 미리 보기** 대화 상자를 표시합니다.

    ![게시 미리 보기](./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png)

2.  **계속**을 클릭합니다. 게시가 완료되면 Azure의 웹 사이트 URL이 WebMatrix IDE 아래쪽에 표시됩니다.

    ![게시 완료](./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png)

3.  링크를 클릭하여 브라우저에서 웹 사이트를 엽니다.

    ![빠른 웹 사이트](./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png)

응용 프로그램 수정 및 다시 게시
-------------------------------

응용 프로그램을 쉽게 수정하고 다시 게시할 수 있습니다. 여기서는 **index.jade** 파일의 제목을 바꾸고 응용 프로그램을 다시 게시해 보겠습니다.

1.  WebMatrix에서 **파일**을 선택한 후 **views** 폴더를 확장합니다. **index.jade** 파일을 두 번 클릭하여 엽니다.

    ![index.jade를 표시하는 WebMatrix](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png)

2.  두 번째 줄을 다음과 같이 변경합니다.

         p Welcome to #{title} with WebMatrix on Azure!

3.  변경 내용을 저장한 후 게시 아이콘을 클릭합니다. 마지막으로, **게시 미리 보기** 대화 상자에서 **계속**을 클릭하고 업데이트가 게시될 때까지 기다립니다.

    ![게시 미리 보기](./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png)

4.  게시가 완료된 후 게시 프로세스가 완료되어 업데이트된 사이트를 표시할 때 반환되는 링크를 사용하십시오.

    ![Azure 노드 웹 사이트](./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png)

다음 단계
---------

Azure에 제공되는 Node.js 버전에 대한 정보 및 응용 프로그램에 사용할 버전 지정 방법에 대한 자세한 내용은 [Azure 응용 프로그램에서 Node.js 버전 지정](http://www.windowsazure.com/en-us/develop/nodejs/common-tasks/specifying-a-node-version/)(영문)을 참조하십시오.

응용 프로그램을 Azure에 배포한 후 문제가 발생하는 경우 [Azure 웹 사이트에서 Node.js 응용 프로그램 디버그 방법](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/Debug-Website/)(영문)에서 문제를 진단하는 방법에 대한 정보를 참조하십시오.

