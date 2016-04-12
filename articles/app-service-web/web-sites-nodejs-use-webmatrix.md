<properties 
	pageTitle="WebMatrix를 사용하여 Node.js 웹앱 빌드 및 Azure에 배포" 
	description="이 자습서에서는 WebMatrix를 사용하여 Node.js 응용 프로그램을 개발하고 Azure 앱 서비스 웹앱에 배포하는 방법을 설명합니다." 
	services="app-service\web" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/04/2016"
	ms.author="robmcm"/>


# WebMatrix를 사용하여 Node.js 웹앱 빌드 및 Azure에 배포

이 자습서에서는 WebMatrix를 사용하여 Node.js 응용 프로그램을 개발하고 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹앱에 배포하는 방법을 보여 줍니다. WebMatrix는 웹 사이트 또는 웹앱 개발에 필요한 모든 기능이 포함된 Microsoft의 무료 웹 개발 도구입니다. WebMatrix에는 코드 완성, 미리 빌드된 템플릿, Jade, LESS 및 CoffeeScript에 대한 편집기 지원 등 Node.js를 쉽게 사용할 수 있는 몇 가지 기능이 포함되어 있습니다. [WebMatrix](https://www.microsoft.com/web/webmatrix/)에 대해 자세히 알아봅니다.

이 가이드를 완료하면 Azure 앱 서비스에서 실행되는 Node.js 웹앱이 완성됩니다.
 
아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![Azure 노드 웹 사이트][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## Azure에 로그인

Azure 앱 서비스에서 웹앱을 만들려면 다음 단계를 따르세요.

1. WebMatrix를 시작합니다.
2. 이번에 처음으로 WebMatrix를 사용하는 경우 Azure에 로그인하라는 메시지가 표시됩니다. 또는 **로그인** 단추를 클릭하고 **계정 추가**를 선택할 수 있습니다. Microsoft 계정을 사용하여 **로그인**하도록 선택합니다.

	![계정 추가][addaccount]

3. Azure 계정을 등록한 경우 Microsoft 계정을 사용하여 로그인할 수 있습니다.

	![Azure에 로그인][signin]


## Azure용 기본 제공 템플릿을 사용하여 사이트 만들기

1. 시작 화면에서 **새로 만들기** 단추를 클릭하고 **템플릿 갤러리**를 선택하여 템플릿 갤러리에서 새 사이트를 만듭니다.

	![템플릿 갤러리에서 새 사이트 만들기][sitefromtemplate]

2. **템플릿의 사이트** 대화 상자에서 **노드**를 선택한 후 **빠른 사이트**를 선택합니다. 마지막으로 **다음**을 클릭합니다. **빠른 사이트** 템플릿의 필수 조건  
이 없는 경우 해당 요소를 설치하라는 메시지가 표시됩니다.

	![빠른 템플릿 선택][webmatrix-templates]

3. Azure에 로그인한 경우 로컬 사이트에 대한 앱 서비스 웹앱을 만들 수 있는 옵션이 제공됩니다. 고유한 이름을 선택하고 앱 서비스 웹앱을 만들 데이터 센터를 선택합니다.

	![Azure에서 사이트 만들기][nodesitefromtemplateazure]
	
4. WebMatrix에서 로컬 사이트를 빌드하고 앱 서비스 웹앱을 만들고 나면 WebMatrix IDE가 표시됩니다.

	![WebMatrix IDE][webmatrix-ide]

##Azure에 응용 프로그램 게시

1. WebMatrix의 **홈** 리본에서 **게시**를 클릭하여 사이트의 **게시 미리 보기** 대화 상자를 표시합니다.

	![게시 미리 보기][webmatrix-node-publishpreview]

2. **계속**을 클릭합니다. 게시가 완료되면 앱 서비스 웹앱의 URL이 WebMatrix IDE 아래쪽에 표시됩니다.

	![게시 완료][webmatrix-publish-complete]

3. 링크를 클릭하여 브라우저에서 앱 서비스 웹앱을 엽니다.

	![빠른 웹앱][webmatrix-node-express-site]

##응용 프로그램 수정 및 다시 게시

응용 프로그램을 쉽게 수정하고 다시 게시할 수 있습니다. 여기서는 **index.jade** 파일의 제목을 바꾸고 응용 프로그램을 다시 게시해 보겠습니다.

1. WebMatrix에서 **파일**을 선택한 후 **views** 폴더를 확장합니다. **index.jade** 파일을 두 번 클릭하여 엽니다.

	![index.jade를 표시하는 WebMatrix][webmatrix-modify-index]

2. 단락 줄을 다음으로 변경합니다.

		p Welcome to #{title} with WebMatrix on Azure!

3. 변경 내용을 저장한 후 게시 아이콘을 클릭합니다. 마지막으로, **게시 미리 보기** 대화 상자에서 **계속**을 클릭하고 업데이트가 게시될 때까지 기다립니다.

	![게시 미리 보기][webmatrix-republish]

4. 게시가 완료되면 게시 프로세스가 완료될 때 반환된 링크를 사용하여 업데이트된 앱 서비스 웹앱을 확인합니다.

	![Azure 노드 웹앱][webmatrix-node-completed]

##다음 단계

Azure에 제공되는 Node.js 버전에 대한 정보 및 응용 프로그램에 사용할 버전 지정 방법에 대한 자세한 내용은 [Azure 응용 프로그램에서 Node.js 버전 지정](../nodejs-specify-node-version-azure-apps.md)(영문)을 참조하십시오.

응용 프로그램을 Azure에 배포한 후 문제가 발생한 경우 [Azure 웹 사이트에서 Node.js 웹앱을 디버그하는 방법](web-sites-nodejs-debug.md)에서 문제를 진단하는 방법에 대한 정보를 참조하세요.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 

<!---HONumber=AcomDC_0309_2016-->