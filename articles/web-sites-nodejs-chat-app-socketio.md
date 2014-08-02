<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build a Node.js Chat Application with Socket.IO on an Azure Web Site" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

Azure 웹 사이트에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드
========================================================================

Socket.IO는 node.js 서버와 클라이언트 간의 실시간 통신을 제공합니다. 이 자습서는 Azure 웹 사이트에서 Socket.IO를 기반으로 하는 채팅 응용 프로그램 호스팅에 대해 안내합니다. Socket.IO에 대한 자세한 내용은 <http://socket.io/>(영문)를 참조하십시오.

**참고**

이 작업의 절차는 Azure 웹 사이트에 적용됩니다. 클라우드 서비스에 대해서는 [Azure 클라우드 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드](http://www.windowsazure.com/en-us/develop/nodejs/tutorials/app-using-socketio/)를 참조하십시오.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![채팅 응용 프로그램을 표시하는 브라우저](./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png)

채팅 예제 다운로드
------------------

이 프로젝트에서는 [Socket.IO GitHub 리포지토리](https://github.com/LearnBoost/socket.io/tree/0.9.14)(영문)의 채팅 예제를 사용합니다. 다음 단계에 따라 예제를 다운로드하여 이전에 만든 프로젝트에 추가하십시오.

1.  **복제** 단추를 눌러 리포지토리의 로컬 복사본을 만듭니다. **ZIP** 단추를 눌러 프로젝트를 다운로드할 수도 있습니다.

    ![ZIP 다운로드 아이콘이 강조 표시된 https://github.com/LearnBoost/socket.io/tree/master/examples/chat을 표시하는 브라우저 창](./media/web-sites-nodejs-chat-app-socketio/socketio-2.png)

2.  **examples\\chat** 디렉터리를 찾을 때까지 로컬 리포지토리의 디렉터리 구조를 탐색합니다. 이 디렉터리의 내용을 별도의 디렉터리(예: **\\node\\chat**)로 복사합니다.

App.js 수정 및 모듈 설치
------------------------

Azure에 응용 프로그램을 배포하기 전에 몇 가지 사항을 수정해야 합니다. app.js 파일에 대해 다음 단계를 수행합니다.

1.  메모장 또는 기타 텍스트 편집기에서 app.js 파일을 엽니다.

2.  app.js의 시작 부분에서 **Module dependencies** 섹션을 찾아 아래와 같이 **sio = require('..//..//lib//socket.io')**가 포함된 줄을 **sio = require('socket.io')**로 변경합니다.

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  응용 프로그램이 올바른 포트에서 수신 대기하도록 메모장 또는 원하는 편집기에서 app.js를 연 후 아래와 같이 다음 줄에서 **3000**을 **process.env.PORT**로 변경합니다.

        //app.listen(3000, function () {            //Original
        app.listen(process.env.PORT, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

app.js의 변경 내용을 저장한 후 다음 단계에 따라 필요한 모듈을 설치하십시오.

1.  명령줄에서 **\\node\\chat**로 디렉터리를 변경하고 다음 명령을 사용하여 이 응용 프로그램에 필요한 모듈을 설치합니다.

         npm install

    그러면 package.json 파일에 나열된 모듈이 설치됩니다. 명령을 완료한 후 다음과 유사한 출력이 표시됩니다.

    ![npm 설치 명령의 출력](./media/web-sites-nodejs-chat-app-socketio/socketio-7.png)

2.  이 예제는 원래 Socket.IO GitHub 리포지토리의 일부이고, 상대 경로에서 Socket.IO 라이브러리를 직접 참조하며 package.json 파일에서 Socket.IO가 참조되지 않기 때문에 다음 명령을 실행하여 모듈을 설치해야 합니다.

        npm install socket.io -save

Azure 웹 사이트 만들기 및 Git 게시 사용
---------------------------------------

다음 단계에 따라 Azure 웹 사이트를 만들고 해당 웹 사이트에 대해 Git 게시를 사용하도록 설정하십시오.

**참고**

이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A7171371E)을 참조하십시오.

1.  명령줄에서 **\\node\\chat**로 디렉터리를 변경하고 다음 명령을 사용하여 새 Azure 웹 사이트를 만들고 해당 웹 사이트 및 로컬 디렉터리에 대해 Git 리포지토리를 사용하도록 설정합니다. 그렇게 하면 'azure'라는 Git 원격이 만들어집니다.

         azure site create mysitename --git

    'mysitename'을 웹 사이트의 고유한 이름으로 바꿔야 합니다.

2.  다음 명령을 사용하여 기존 파일을 로컬 리포지토리로 커밋합니다.

         git add .
         git commit -m "Initial commit"

3.  다음 명령으로 파일을 Azure 웹 사이트 리포지토리로 푸시합니다.

         git push azure master

    서버에서 모듈을 가져올 때 상태 메시지가 표시됩니다. 이 프로세스가 완료되면 응용 프로그램이 Azure 웹 사이트에 호스트됩니다.



    <b>참고</b>
    <p>모듈이 설치되는 동안 'The imported project ... was not found' 오류가 발생할 수 있습니다. 이 오류는 무시해도 됩니다.</p>
    </div>

1.  Socket.IO는 Azure에서 기본적으로 사용되지 않는 WebSocket을 사용합니다. WebSocket을 사용하도록 설정하려면 다음 명령을 사용하십시오.

         azure site set -w

    메시지가 표시되면 웹 사이트 이름을 입력하십시오.

    > [WACOM.NOTE] 'azure site set -w' 명령은 버전 0.7.4 이상의 Azure 크로스 플랫폼 명령줄 인터페이스에서만 작동합니다. 또한 Azure 관리 포털에서 WebSocket 지원을 사용하도록 설정할 수도 있습니다.
    >
    > [Azure 관리 포털](https://manage.windowsazure.com)에서 WebSocket을 사용하도록 설정하려면 웹 사이트의 구성 페이지를 선택하고, WebSocket 항목에 대해 '설정'을 선택한 후 저장을 클릭합니다.
    >
    > ![WebSocket](./media/web-sites-nodejs-chat-app-socketio/websockets.png)

2.  Azure에서 웹 사이트를 보려면 다음 명령을 사용하여 웹 브라우저를 시작하고 호스트되는 웹 사이트로 이동합니다.

         azure site browse

현재 응용 프로그램이 Azure에서 실행되고 있으며 Socket.IO를 사용하여 다른 클라이언트 간에 채팅 메시지를 릴레이할 수 있습니다.

**참고**

간단히 하기 위해 샘플은 같은 인스턴스에 연결된 사용자 간 채팅으로 제한됩니다. 즉 클라우드 서비스에서 작업자 역할 인스턴스를 두 개 만드는 경우 사용자는 같은 작업자 역할 인스턴스에 연결된 사람들과만 채팅할 수 있습니다. 응용 프로그램을 여러 역할 인스턴스와 작업하도록 조정하려면 서비스 버스 같은 기술을 사용하여 인스턴스 간에 Socket.IO 저장소 상태를 공유할 수 있습니다. 예를 들어, [Node.js에 대한 Azure SDK GitHub 리포지토리](https://github.com/WindowsAzure/azure-sdk-for-node)(영문)의 서비스 버스 큐 및 토픽 사용 샘플을 참조하십시오.

다음 단계
---------

이 자습서에서는 Azure 웹 사이트에 호스트되는 기본 채팅 응용 프로그램을 만드는 방법을 알아보았습니다. 이 응용 프로그램은 Azure 클라우드 서비스로 호스트할 수도 있습니다. 이를 수행하는 방법에 대한 단계는 [Azure 클라우드 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드](/en-us/develop/nodejs/tutorials/app-using-socketio/)를 참조하십시오.

