<properties
	pageTitle="Azure 앱 서비스에서 Node.js 웹 앱 만들기"
	description="Azure에서 Node.js 웹 앱을 빌드 및 배포하는 방법에 대해 알아봅니다."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Azure 앱 서비스에서 Node.js 웹 앱 빌드 및 만들기

이 자습서에서는 [Node][nodejs.org] 응용 프로그램을 만들고 [Git]를 사용하여 [Azure 앱 서비스 웹 앱](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포하는 방법을 설명합니다. 이 자습서의 지침은 Node를 실행할 수 있는 모든 운영 체제에 적용될 수 있습니다.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

!['Hello World' 메시지를 표시하는 브라우저][helloworld-completed]

##웹 앱 만들기 및 Git 게시 설정

웹 앱 및 Git 게시를 설정하려면 다음 단계를 따르세요.

> [AZURE.NOTE]
> 이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [MSDN 구독자 혜택을 활성화](/ko-kr/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)하거나 [무료 평가판을 등록](/ko-kr/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있습니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 포털의 왼쪽 위에서 **+ 새로 만들기** 아이콘을 클릭합니다.

3. **웹 + 모바일**, **웹 앱**을 차례로 클릭합니다.

    ![][portal-quick-create]

4. **URL** 값을 입력합니다.

5. 앱 서비스 계획을 선택하거나 새로 만듭니다. 새 계획을 만드는 경우 가격 책정 계층, 위치 및 기타 옵션을 선택합니다.

    ![][portal-quick-create2]

6. **만들기**를 클릭합니다.

7. 상태가 **실행**으로 변경되면, 포털에서 웹 앱에 대한 블레이드를 자동으로 엽니다. **찾아보기**를 클릭하여 블레이드를 찾을 수도 있습니다.

	![][go-to-dashboard]

8. **배포**를 클릭합니다. 이 부분의 블레이드를 보려면 스크롤해야 할 수 있습니다.

	![][deployment-part]

9. **소스 선택**, **로컬 Git 리포지토리**를 차례로 클릭합니다. **확인**을 클릭합니다.

	![][setup-git-publishing]


10. **배포 자격 증명** 부분을 클릭합니다(아래 빨간색의 밑줄). 사용자 이름 및 암호를 만듭니다. **Save**를 클릭합니다. 이전에 웹 앱에 대한 게시를 사용하도록 설정한 경우, 이 단계를 수행하지 않아도 됩니다.

	![][deployment-credentials]


11. 게시하려면 Git 원격 리포지토리에 푸시합니다. 리포지토리의 URL을 찾고 **모든 설정**, **속성**을 차례로 클릭합니다. URL은 "GIT URL"에 나열됩니다.

	![][git-url]

##로컬에서 응용 프로그램 빌드 및 테스트

이 섹션에서는 **nodejs.org**의 'hello world' 예가 포함된 [server.js] 파일을 만들겠습니다. 이 예는 원본 예에서 수정된 것으로, Azure 웹 앱에서 실행되는 경우의 수신 포트로 process.env.PORT가 추가되었습니다.

1. 텍스트 편집기를 사용하여 **server.js**라는 새 파일을 **helloworld** 디렉터리에 만듭니다. **helloworld** 디렉터리가 없으면 디렉터리를 만듭니다.

2. 다음을 **server.js** 파일의 내용으로 추가하고 저장합니다.

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. 명령줄을 열고 다음 명령을 사용하여 웹 앱을 로컬에서 시작합니다.

        node server.js

4. 웹 브라우저를 열고 http://localhost:1337으로 이동합니다. 아래 스크린샷에서처럼 "Hello World"라는 웹 페이지가 나타납니다.

    !['Hello World' 메시지를 표시하는 브라우저][helloworld-localhost]

##응용 프로그램 게시

1. 명령줄에서 **helloworld** 디렉터리로 이동한 후 다음 명령을 입력하여 로컬 Git 리포지토리를 초기화합니다.

		git init

	> [AZURE.NOTE]**Git 명령을 사용할 수 없는 경우**
	[Git](http://git-scm.com/%20target="_blank)는 Azure 웹 사이트를 배포하는 데 사용할 수 있는 분산된 버전 제어 시스템입니다. 플랫폼별 설치 지침은 [Git 다운로드 페이지](http://git-scm.com/download%20target="_blank")(영문)를 참조하십시오.

2. 다음 명령을 사용하여 리포지토리에 파일을 추가합니다.

		git add .
		git commit -m "initial commit"

3. 다음 명령을 사용하여 이전에 만든 웹 앱으로 업데이트를 푸시하는 Git 원격을 추가합니다.

		git remote add azure [URL for remote repository]


4. 다음 명령을 사용하여 변경 내용을 Azure에 푸시합니다.

		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다. 다음과 유사하게 출력되어야 합니다.

		Counting objects: 3, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 374 bytes, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '5ebbe250c9'.
		remote: Preparing files for deployment.
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		 * [new branch]      master -> master


5. 앱을 보려면 관리 포털 내 **웹 앱** 부분에서 **찾아보기** 단추를 클릭합니다.

##응용 프로그램의 변경 내용 게시

1. 텍스트 편집기에서 **server.js** 파일을 열고 'Hello World\n'를 'Hello Azure\n'로 변경합니다. 파일을 저장합니다.
2. 명령줄에서 **helloworld** 디렉터리로 이동한 후 다음 명령을 실행합니다.

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	이전에 만든 암호를 입력하라는 메시지가 나타납니다.

3. **찾아보기** 단추를 클릭하여 앱으로 이동한 후 업데이트가 적용되었는지 확인합니다.

	!['Hello Azure'를 표시하는 웹 페이지][helloworld-completed]

4. **배포**에서 선택하여 이전 배포로 되돌릴 수 있습니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

##다음 단계

이 문서의 단계에 따라 Azure 포털을 사용하여 웹앱을 만들 수 있지만 [Azure 명령줄 인터페이스](../xplat-cli.md)를 사용하여 동일한 작업을 수행할 수도 있습니다.

Node.js는 응용 프로그램에 사용될 수 있는 풍부한 모듈 에코시스템을 제공합니다. 웹 앱 모듈 사용하는 방법을 알아보려면 [Azure 응용 프로그램에서 Node.js 모듈 사용](../nodejs-use-node-modules-azure-apps.md)을 참조하세요.

Azure에 제공되는 Node.js 버전에 대한 정보 및 응용 프로그램에 사용할 버전 지정 방법에 대한 자세한 내용은 [Azure 응용 프로그램에서 Node.js 버전 지정](../nodejs-specify-node-version-azure-apps.md)(영문)을 참조하십시오.

응용 프로그램을 Azure에 배포한 후 문제가 발생하는 경우 [Azure 웹 사이트에서 Node.js 응용 프로그램 디버그 방법](web-sites-nodejs-debug.md)(영문)에서 문제를 진단하는 방법에 대한 정보를 참조하십시오.


##추가 리소스

* [Azure PowerShell](../install-configure-powershell.md)
* [Azure 명령줄 인터페이스](../xplat-cli.md)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.


[nodejs.org]: http://nodejs.org
[server.js]: http://nodejs.org
[Git]: http://git-scm.com


[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png

[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png


[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png

[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png

[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png

[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png


[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
 

<!---HONumber=August15_HO6-->