<properties urlDisplayName="Web App with Express" pageTitle="Express를 사용한 웹 앱(Node.js) - Azure 자습서" metaKeywords="Azure Node.js hello world 자습서, Azure Node.js hello world, Azure Node.js 시작 자습서, Azure Node.js 자습서, Azure Node.js Express 자습서" description="클라우드 서비스 자습서를 기반으로 웹 앱을 빌드하고 Express 모듈 사용 방법을 보여 주는 자습서입니다." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Azure 클라우드 서비스에서 Express를 사용하여 Node.js 웹 응용 프로그램 빌드

Node.js에는 핵심 런타임에 최소한의 기능이 들어 있습니다. 개발자는 Node.js 응용 프로그램을 개발할 때 추가 기능을 제공하기 위해 종종 타사 모듈을 사용합니다. 이 자습서에서는Node.js 웹 응용 프로그램을 만들기 위한 MVC 프레임워크를 제공하는 [Express][] 모듈을 사용하여 새 응용 프로그램을 만들어 봅니다.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![A web browser displaying Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##클라우드 서비스 프로젝트 만들기

다음 단계에 따라 'expressapp'라는 이름의 새 클라우드 서비스 프로젝트를 만듭니다.

1. **시작 메뉴** 또는 **시작 화면**에서 **Azure PowerShell**을 검색합니다. 마지막으로, **Azure PowerShell**을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.

	![Azure PowerShell icon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2. 디렉터리를 **c:\\node** 디렉터리로 변경한 후 다음 명령을 입력하여 **expressapp**이라는 새 솔루션과 **WebRole1**이라는 웹 역할을 만듭니다.

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node\expressapp> Add-AzureNodeWebRole
		PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

	> [WACOM.NOTE] 기본적으로 **Add-AzureNodeWebRole**은 이전 버전의 Node.js를 사용합니다. 위의 **Set-AzureServiceProjectRole** 문은 Azure에 Node의 v0.10.21을 사용하도록 지시합니다. 

##Express 설치

1. 다음 명령을 실행하여 Express 생성기를 설치합니다.

		PS C:\node\expressapp> npm install express-generator -g

	npm 명령의 출력이 아래 결과와 비슷하게 표시되어야 합니다. 

	![Windows PowerShell displaying the output of the npm install express command.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. 디렉터리를 **WebRole1** 디렉터리로 변경하고 express 명령을 사용하여 새 응용 프로그램을 만듭니다.

        PS C:\node\expressapp\WebRole1> express

	이전 응용 프로그램을 덮어쓸지 묻습니다. **y** 또는 **yes**를 입력하여 계속합니다. Express에서 응용 프로그램 빌드를 위해 app.js 파일과 폴더 구조를 생성합니다.

	![The output of the express command](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  package.json 파일에 정의된 추가 종속성을 설치하려면 다음 명령을 입력합니다.

        PS C:\node\expressapp\WebRole1> npm install

	![The output of the npm install command](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  다음 명령을 사용하여 **bin/www** 파일을 **server.js**에 복사합니다. 이는 클라우드 서비스에서 이 응용 프로그램의 진입점을 찾을 수 있도록 하기 위한 것입니다.

		PS C:\node\expressapp\WebRole1> copy bin/www server.js

	이 명령을 완료하면 WebRole1 디렉터리에 **server.js** 파일이 생성됩니다.

7.  **server.js**를 수정하여 다음 줄에서 '.' 문자 중 하나를 제거합니다.

		var app = require('../app');

	수정을 완료하면 줄이 다음과 같이 표시됩니다.

		var app = require('./app');

	이 변경 작업은 필요한 앱 파일과 동일한 디렉터리로 파일(이전의 **bin/www**)을 이동했기 때문에 필요합니다. 이렇게 변경한 후 **server.js** 파일을 저장합니다.

8.  다음 명령을 사용하여 Azure 에뮬레이터에서 응용 프로그램을 실행합니다.

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![A web page containing welcome to express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## 뷰 수정

이제 "Welcome to Express in Azure" 메시지를 표시하도록 뷰를
수정합니다.

1.  다음 명령을 입력하여 index.jade 파일을 엽니다.

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![The contents of the index.jade file.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade는 Express 응용 프로그램에서 사용하는 기본 뷰 엔진입니다. Jade 뷰 엔진에 대한 자세한 내용은 [http://jade-lang.com][](영문)을 참조하세요.

2.  **in Azure**를 추가하여 텍스트의 마지막 줄을 수정합니다.

	![The index.jade file, the last line reads: p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  파일을 저장하고 메모장을 종료합니다.

4.  브라우저를 새로 고치면 변경 내용이 표시됩니다.

	![A browser window, the page contains Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

응용 프로그램을 테스트한 후 **Stop-AzureEmulator** cmdlet을 사용하여 에뮬레이터를 중지합니다.

##Azure에 응용 프로그램 게시

Azure PowerShell 창에서 **Publish-AzureServiceProject** cmdlet을 사용하여 응용 프로그램을 클라우드 서비스에 배포합니다.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

배포 작업을 완료하면 브라우저가 열리고 웹 페이지가 표시됩니다.

![A web browser displaying the Express page. The URL indicates it is now hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)


  [Node.js 웹 응용 프로그램]: http://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/getting-started/
  [Express](영문): http://expressjs.com/
  [http://jade-lang.com](영문): http://jade-lang.com


<!--HONumber=35.2-->
