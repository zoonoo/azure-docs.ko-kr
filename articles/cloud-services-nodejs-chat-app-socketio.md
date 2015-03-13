<properties 
	pageTitle="Socket.io를 사용하는 Node.js 응용 프로그램 - Azure 자습서" 
	description="Azure에서 호스트되는 node.js 응용 프로그램에서 socket.io를 사용하는 방법을 보여 주는 자습서입니다." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>





# Azure 클라우드 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드

Socket.IO는 node.js 서버와 클라이언트 간에
실시간 커뮤니케이션을 제공합니다. 이 자습서에서는 Azur에서 socket IO 기반의 채팅 응용 프로그램을
호스트하는 방법을 보여줍니다. Blob에 대한 자세한 내용은
Socket.IO에서 <a href="http://socket.io/">http://socket.io/</a>를 참조하세요.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![A browser window displaying the service hosted on Azure][completed-app]  

## 클라우드 서비스 프로젝트 만들기

다음은 Socket.IO 응용 프로그램을 호스트하는 클라우드 서비스 프로젝트를 만드는 단계입니다.

1. **시작 메뉴** 또는 **시작 화면**에서 **Azure PowerShell**을 검색합니다. 마지막으로, **Azure PowerShell**을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.

	![Azure PowerShell icon][powershell-menu]

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]



2. 디렉터리를 **c:\\node** 디렉터리로 변경한 후 다음 명령을 입력하여 **chatapp**이라는 새 솔루션과 **WorkerRole1**이라는 작업자 역할을 만듭니다.

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	다음과 같은 응답이 표시됩니다.

	![The output of the new-azureservice and add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## 채팅 예제 다운로드

이 프로젝트에서는 [Socket.IO
GitHub 리포지토리]의 채팅 예제를 사용합니다. 다음 단계에 따라 예제를 다운로드하여
이전에 만든 프로젝트에 추가하세요.

1.  **복제** 단추를 눌러 리포지토리의 로컬 복사본을 만듭니다. **ZIP** 단추를 눌러 프로젝트를 다운로드할 수도 있습니다.

    ![A browser window viewing https://github.com/LearnBoost/socket.io/tree/master/examples/chat, with the ZIP download icon highlighted][chat-example-view]

3.  **examples\\chat** 디렉터리를 찾을 때까지 로컬 리포지토리의 디렉터리 구조를 탐색합니다.
    디렉터리를 삭제합니다. 이 디텍터리의 내용을
    앞에서 만든 **C:\\node\\chatapp\\WorkerRole1** 디렉터리로 복사합니다.

    ![Explorer, displaying the contents of the examples\\chat directory extracted from the archive][chat-contents]

    위 스크린샷에서 강조 표시된 항목은 **examples\\chat** 디렉터리에서 복사한 파일입니다.

4.  **C:\\node\\chatapp\\WorkerRole1** 디렉터리에서 **server.js** 파일을 삭제한 다음 **app.js** 파일의 이름을 **server.js**로 변경합니다. 그러면 이전에 **Add-azurenodeworkerrole** cmdlet으로 만든 기본 **server.js** 파일이 제거되고 채팅 예제의 응용 프로그램 파일로 바뀝니다.

### Server.js 수정 및 모듈 설치

Azure 에뮬레이터 응용 프로그램을 테스트하기 전에
몇 가지 사항을 수정해야 합니다. server.js 파일에서 다음 단계를
따르십시오.

1.  메모장 또는 기타 텍스트 편집기에서 server.js 파일을 엽니다.

2.  server.js의 시작 부분에서 **모듈 종속성** 섹션을 찾아 아래와 같이 **sio = require('..//..//lib//socket.io')**가 포함된 줄을 **sio = require('socket.io')**로 변경합니다.

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  응용 프로그램이 올바른 포트에서 수신하도록 하려면,
    메모장이나 즐겨찾는 편집기에서 server.js를 열고
    다음 줄을 **3000**와 **process.env.port**로 아래와 같이 바꿉니다.

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

server.js에 변경 내용을 저장한 후, 다음 단계에 따라
필요한 모듈을 설치하고 Azur 에뮬레이터에서
테스트합니다.

1.  **Azure PowerShell**을 사용하여 디렉터리를 **C:\\node\\chatapp\\WorkerRole1** 디렉터리를 변경하고 다음 명령을 사용하여 이 응용 프로그램에서 필요한 모듈을 설치합니다.

        PS C:\node\chatapp\WorkerRole1> npm install

    그러면 package.json 파일에 나열된 모듈이 설치됩니다. 명령이
    완료되면 다음과 유사한 출력이
    표시됩니다.

    ![The output of the npm install command][The-output-of-the-npm-install-command]

4.  이 예제는 원래 Socket.IO GitHub 리포지토리의 일부이고
    상대 경로로 Socket.IO 라이브러리를 직접 참조하며
    package.json 파일에서 Socket.IO가 참조되지 않기 때문에
    다음 명령을 실행하여 모듈을 설치해야 합니다.

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### 테스트 및 배포

1.  다음 명령을 실행하여 에뮬레이터를 시작합니다.

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  브라우저 창이 열리면 애칭을 입력하고 Enter 키를 누릅니다.
    이렇게 하면 특정 애칭으로 메시지를 게시할 수 있습니다. 다중 사용자 기능을 테스트하려면
    동일한 URL을 사용하여 브라우저의 추가 윈도우를 열고
    다른 애칭을 입력합니다.

    ![Two browser windows displaying chat messages from User1 and User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  응용 프로그램을 테스트한 후 다음 명령을 수행하여
    에뮬레이터를 중지합니다.

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Azure에 응용 프로그램을 배포하려면,
    **Publish-AzureServiceProject** cmdlet을 사용합니다. 예를 들면 다음과 같습니다.

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	> [AZURE.IMPORTANT] 고유한 이름을 사용해야 합니다. 그렇지 않으면 게시 프로세스가 실패합니다. 배포가 완료되면 브라우저가 열리고 배포된 서비스로 이동합니다.
	> 
	> 제공한 구독 이름이 가져온 게시 프로필에 존재하지 않는다는 내용의 오류를 받게 되는 경우, Azure를 배포하기 전에 구독에 대한 게시 프로필을 다운로드하고 가져와야 합니다. [Azure 클라우드 서비스에 Node.js 응용 프로그램 빌드 및 배포](https://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/getting-started/)(영문)에서 **Azure에 응용 프로그램 배포** 섹션을 참조하세요.

    ![A browser window displaying the service hosted on Azure][completed-app]

	> [AZURE.NOTE] 제공한 구독 이름이 가져온 게시 프로필에 존재하지 않는다는 내용의 오류를 받게 되는 경우, Azure를 배포하기 전에 구독에 대한 게시 프로필을 다운로드하고 가져와야 합니다. [Azure 클라우드 서비스에 Node.js 응용 프로그램 빌드 및 배포](https://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/getting-started/)(영문)에서 **Azure에 응용 프로그램 배포** 섹션을 참조하세요.

이제 응용 프로그램이 Azure에서 실행되고 있으며
Socket.IO를 사용하여 서로 다른 클라이언트 간에 채팅 메시지를 릴레이할 수 있습니다.

> [AZURE.NOTE] 간단히 하기 위해 샘플은 같은 인스턴스에 연결된 사용자 간 채팅으로 제한됩니다. 즉 클라우드 서비스에서 작업자 역할 인스턴스를 두 개 만드는 경우 사용자는 같은 작업자 역할 인스턴스에 연결된 사람들과만 채팅할 수 있습니다. 응용 프로그램을 여러 역할 인스턴스와 작업하도록 조정하려면 서비스 버스 같은 기술을 사용하여 인스턴스 간에 Socket.IO 저장소 상태를 공유할 수 있습니다. 예를 들어, [Node.js에 대한 Azure SDK GitHub 리포지토리](https://github.com/WindowsAzure/azure-sdk-for-node)의 서비스 버스 큐 및 토픽 사용 샘플을 참조하세요.

## 다음 단계

이 자습서에서는 Azure 클라우드 서비스에 호스팅된 기본 채팅 응용 프로그램을 만드는 방법을 학습했습니다. 이 응용 프로그램을 Azure 웹 사이트에 호스트하는 방법은 [Azure 웹 사이트에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드][chatwebsite]를 참조하세요.

  [chatwebsite]: /ko-kr/develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://azure.microsoft.com/support/sla/
  [Azure SDK for Node.js GitHub 리포지토리]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Node.js용 Azure SDK]: https://www.windowsazure.com/ko-kr/develop/nodejs/
  [Node.js 웹 응용 프로그램]: https://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/getting-started/
  [Socket.IO GitHub 리포지토리]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure 고려 사항]: #windowsazureconsiderations
  [작업자 역할에서 채팅 예제 호스트]: #hostingthechatexampleinawebrole
  [요약 및 다음 단계]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  

<!--HONumber=45--> 
