<properties 
	pageTitle="Socket.io를 사용하는 Node.js 웹 사이트 - Azure 자습서" 
	description="Azure에서 호스팅되는 node.js 웹 사이트에서 socket.io를 사용하는 방법을 보여 주는 자습서입니다." 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>




#Azure 웹 사이트에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드

Socket.IO는 WebSocket을 사용하여 node.js 서버와 클라이언트 간의 실시간 통신을 제공합니다. 또한 이전 브라우저에서 작동하는 다른 전송(예: 긴 폴링)으로의 대체를 지원합니다. 이 자습서는 Azure 웹 사이트에서 Socket.IO를 기반으로 하는 채팅 응용 프로그램 호스팅에 대해 안내합니다. Socket.IO에 대한 자세한 내용은 [http://socket.io/][socketio]를 참조하세요.

> [AZURE.NOTE] 이 작업의 절차는 Azure 웹 사이트에 적용됩니다. 클라우드 서비스에 대해서는 <a href="http://azure.microsoft.com/develop/nodejs/tutorials/app-using-socketio/">Azure 클라우드 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드</a>를 참조하세요.


## <a id="Download"></a>채팅 예제 다운로드

이 프로젝트에서는 [Socket.IO
GitHub 리포지토리]의 채팅 예제를 사용합니다. 다음 단계에 따라 예제를 다운로드하여
이전에 만든 프로젝트에 추가하세요.

1.  Socket.IO 프로젝트의 [ZIP 또는 GZ 보관 릴리스][release]를 다운로드합니다(이 문서에서는 버전 1.0.6 사용).


3.  보관 파일을 추출하고 **examples\\chat** 디렉터리를
    새 위치(예: 
    **\\node\\chat**)에 복사합니다.

## <a id="Modify"></a>App.js 수정 및 모듈 설치

Azure에 응용 프로그램을 배포하기 전에
몇 가지 사항을 수정해야 합니다.

1.  **index.js** 파일의 이름을 **app.js**로 바꿉니다. 그러면 Azure에서 이 파일을 Node.js 응용 프로그램으로 검색할 수 있습니다.

1.  메모장 또는 기타 텍스트 편집기에서 **app.js** 파일을 엽니다.

2.  app.js의 시작 부분에서 **Module dependencies** 섹션을 찾아 아래 표시된 대로 `var io = require('../..')(server);`가 포함된 줄을 `var io = require('socket.io')(server);`로 변경합니다.

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;


app.js의 변경 내용을 저장한 후 다음 단계에 따라
필요한 모듈을 설치합니다.

1.  명령줄에서 **\\node\\chat**로 디렉터리를 변경하고 다음 명령을 사용하여 이 응용 프로그램에 필요한 모듈을 설치합니다.

        npm install

    그러면 package.json 파일에 나열된 모듈이 설치됩니다. 명령이
    완료되면 다음과 유사한 출력이
    표시됩니다.

	    express@3.4.8 node_modules\express
		├── methods@0.1.0
		├── merge-descriptors@0.0.1
		├── debug@0.8.1
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── fresh@0.2.0
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── mkdirp@0.3.5
		├── commander@1.3.2 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  이 예제는 원래 Socket.IO GitHub 리포지토리의 일부이고
    상대 경로로 Socket.IO 라이브러리를 직접 참조하며
    package.json 파일에서 Socket.IO가 참조되지 않기 때문에
    다음 명령을 실행하여 모듈을 설치해야 합니다.

        npm install socket.io@1.0.6 -save

	> [AZURE.NOTE] 새 버전의 Socket.IO를 사용하여 이 문서의 단계를 수행할 수도 있지만 이 문서는 버전 1.0.6으로 테스트되었습니다.

## <a id="Publish"></a>Azure 웹 사이트 만들기

다음 단계에 따라 Azure 웹 사이트를 만들고, Git 게시를 사용하도록 설정한 다음, 웹 사이트에 대한 WebSocket 지원을 사용하도록 설정합니다.

> [AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure 무료 평가판</a>을 참조하세요.

1. 명령줄에서 **\\node\chat**로 디렉터리를 변경하고 다음 명령을 사용하여 새 Azure 웹 사이트를 만들고 해당 웹 사이트 및 로컬 디렉터리에 대해 Git 리포지토리를 사용하도록 설정합니다. 그렇게 하면  'azure'라는 Git 원격이 만들어집니다.

		azure site create mysitename --git

	 'mysitename'을 웹 사이트의 고유한 이름으로 바꿔야 합니다.

2. 다음 명령을 사용하여 기존 파일을 로컬 리포지토리로 커밋합니다.

		git add .
		git commit -m "Initial commit"

3. 다음 명령으로 파일을 Azure 웹 사이트 리포지토리로 푸시합니다.

		git push azure master

	서버에서 모듈을 가져올 때 상태 메시지가 표시됩니다. 이 프로세스가 완료되면 응용 프로그램이 Azure 웹 사이트에서 호스트됩니다.

 	> [AZURE.NOTE] 설치하는 동안  'The imported project ... was not found' 오류가 발생할 수도 있습니다. 이 오류는 무시해도 됩니다.

4. Socket.IO는 Azure에서 기본적으로 사용되지 않는 WebSocket을 사용합니다. WebSocket을 사용하도록 설정하려면 다음 명령을 사용하십시오.

		azure site set -w

	메시지가 표시되면 웹 사이트 이름을 입력합니다.

	>[AZURE.NOTE]
	> 'azure site set -w' 명령은 버전 0.7.4 이상의 Azure 크로스 플랫폼 명령줄 인터페이스에서만 작동합니다. 또한 Azure 관리 포털에서 WebSocket 지원을 사용하도록 설정할 수도 있습니다.
	>
	>[Azure 관리 포털](https://manage.windowsazure.com)에서 WebSocket을 사용하도록 설정하려면 웹 사이트의 구성 페이지를 선택하고, WebSocket 항목에 대해 '설정'을 선택한 후 저장을 클릭합니다.
	>	
	>![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. Azure에서 웹 사이트를 보려면 다음 명령을 사용하여 웹 브라우저를 시작하고 호스트되는 웹 사이트로 이동합니다.

		azure site browse

이제 응용 프로그램이 Azure에서 실행되고 있으며
Socket.IO를 사용하여 서로 다른 클라이언트 간에 채팅 메시지를 릴레이할 수 있습니다.

> [AZURE.NOTE] 간단히 하기 위해 샘플은 같은 인스턴스에 연결된 사용자 간 채팅으로 제한됩니다. 즉 클라우드 서비스에서 작업자 역할 인스턴스를 두 개 만드는 경우 사용자는 같은 작업자 역할 인스턴스에 연결된 사람들과만 채팅할 수 있습니다. 응용 프로그램을 여러 역할 인스턴스와 작업하도록 조정하려면 서비스 버스 같은 기술을 사용하여 인스턴스 간에 Socket.IO 저장소 상태를 공유할 수 있습니다. 예를 들어, <a href="https://github.com/WindowsAzure/azure-sdk-for-node">Node.js에 대한 Azure SDK GitHub 리포지토리</a>의 서비스 버스 큐 및 토픽 사용 샘플을 참조하세요.

##확장

__adapter__를 사용하여 여러 응용 프로그램 인스턴스 간에 메시지 및 이벤트를 배포하는 방식으로 Socket.IO 응용 프로그램을 확장할 수 있습니다. 사용 가능한 여러 어댑터가 있지만 Azure Redis 캐시 기능에 사용하기 편리한 어댑터는 [socket.io-redis](https://github.com/automattic/socket.io-redis) 어댑터입니다.

> [AZURE.NOTE] Socket.IO 솔루션 확장에 대한 추가 요구 사항은 고정 세션의 지원입니다. 고정 세션은 Azure 요청 라우팅을 통해 Azure 웹 사이트에서 기본적으로 사용하도록 설정됩니다. 자세한 내용은 [Azure 웹 사이트의 인스턴스 선호도](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)를 참조하세요.

###Redis 캐시 만들기

[Azure Redis 캐시에서 캐시 만들기](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409)의 단계를 수행하여 새 캐시를 만듭니다.

> [AZURE.NOTE] 캐시의 __Host name__ 및 __Primary key__를 저장합니다. 이러한 정보는 다음 단계에서 필요합니다.

###Redis 및 socket.io-redis 모듈 추가

1. 명령줄에서 __\\node\\chat__ 디렉터리로 변경하여 다음 명령을 사용합니다.

		npm install socket.io-redis@0.1.3 redis@0.11.0 --save

	> [AZURE.NOTE] 이 명령에 지정된 버전은 이 문서를 테스트할 때 사용된 버전입니다.

2. __app.js__ 파일을 수정하여 다음 줄을 `var io = require('socket.io')(server);` 바로 뒤에 추가합니다.

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));


	__redishostname__ 및 __rediskey__를 Redis 캐시의 호스트 이름 및 키로 바꿉니다.

	그러면 이전에 만든 Redis 캐시에 대한 게시 및 구독 클라이언트가 만들어집니다. 이 클라이언트는 어댑터에서 응용 프로그램의 인스턴스 간에 메시지 및 이벤트를 전달하는 데 Redis 캐시를 사용하도록 Socket.IO를 구성하는 데 사용됩니다.

	> [AZURE.NOTE] __socket.io-redis__ 어댑터는 Redis와 직접 통신할 수 있지만 현재 버전(2014년 7월 14일자)에서는 Azure Redis 캐시에 필요한 인증을 지원하지 않습니다. 따라서 __redis__ 모듈을 통해 초기 연결이 만들어진 다음 __socket.io-redis__ 어댑터로 클라이언트가 전달됩니다.
	> 
	> Azure Redis 캐시는 포트 6380을 사용한 보안 연결을 지원하지만 이 예제에 사용된 모듈에서는 2014년 7월 14일 현재 보안 연결을 지원하지 않습니다. 위 코드에서는 기본적으로 비보안 포트 6380을 사용합니다.

3. 수정한 __app.js__를 저장합니다.

###변경 내용 커밋 및 다시 배포

__\\node\\chat__ 디렉터리의 명령줄에서 다음 명령을 사용하여 변경 내용을 커밋하고 응용 프로그램을 다시 배포합니다.

	git add .
	git commit -m "implementing scale out"
	git push azure master

변경 내용이 서버에 푸시되고 나면 다음 명령을 사용하여 여러 인스턴스 간에 사이트를 확장할 수 있습니다.

	azure site scale instances --instances #

여기서 __#__은 만들 인스턴스 수입니다. 

여러 브라우저 또는 컴퓨터에서 웹 사이트에 연결하여 메시지가 모든 클라이언트에 올바르게 전송되었는지 확인할 수 있습니다.

##<a id="tshooting"></a>문제 해결

###연결 제한

Azure 웹 사이트는 여러 SKU에서 사용할 수 있으며, 이러한 SKU에 따라 사이트에 사용 가능한 리소스가 결정됩니다. 여기에는 허용되는 WebSocket 연결 수가 포함됩니다. 자세한 내용은 [웹 사이트 가격 책정 페이지][pricing]를 참조하세요.

###메시지가 WebSocket을 사용하여 전송되지 않음

클라이언트 브라우저에서 WebSocket을 사용하는 대신 긴 폴링으로의 대체를 유지하는 경우 다음 중 하나가 원인일 수 있습니다.

* **전송을 WebSocket으로만 제한**

	Socket.IO에서 WebSocket을 메시징 전송으로 사용하려면 서버와 클라이언트가 모두 WebSocket을 지원해야 합니다. 둘 중 하나가 지원하지 않으면 Socket.IO에서 긴 폴링과 같은 다른 전송을 협상합니다. Socket.IO에서 사용하는 기본 전송 목록은 ` websocket, htmlfile, xhr-polling, jsonp-polling`입니다. **app.js** 파일에서 `, nicknames = {};`가 포함된 줄 뒤에 다음 코드를 추가하여 Socket.IO에서 WebSocket만 사용하도록 강제로 설정할 수 있습니다.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [AZURE.NOTE] 위 코드가 활성화되어 있는 동안에는 통신이 WebSocket으로만 제한되므로 WebSocket을 지원하지 않는 이전 브라우저에서 사이트에 연결할 수 없게 됩니다.

* **SSL 사용**

	WebSocket은 **Upgrade** 헤더와 같은 보다 적은 수의 사용된 HTTP 헤더를 기반으로 합니다. 웹 프록시와 같은 일부 중간 네트워크 장치는 이러한 헤더를 제거할 수 있습니다. 이 문제를 방지하기 위해 SSL을 통해 WebSocket 연결을 설정할 수 있습니다.

	이 작업을 수행하는 간단한 방법은  `match origin protocol`로 Socket.IO를 구성하는 것입니다. 이 명령은 Socket.IO에 웹 페이지에 대한 시작 HTTP/HTTPS 요청과 동일하게 WebSockets 통신의 보안을 유지하도록 지시합니다. 브라우저에서 HTTPS URL을 사용하여 웹 사이트를 방문한 경우 Socket.IO를 통한 이후의 WebSocket 통신은 SSL을 통해 보안이 유지됩니다.

	이 구성을 사용하도록 이 예제를 수정하려면 **app.js** 파일에서 `, nicknames = {};`이 포함된 줄 뒤에 다음 코드를 추가합니다.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **web.config 설정 확인**

	Node.js 응용 프로그램을 호스트하는 Azure 웹 사이트에서는 **web.config** 파일을 사용하여 들어오는 요청을 Node.js 응용 프로그램으로 라우팅합니다. Node.js 응용 프로그램에서 WebSocket이 올바르게 작동하려면 **web.config**에 다음 항목이 포함되어야 합니다.

		<webSocket enabled="false"/>

	그러면 WebSocket의 자체 구현을 포함하고 Socket.IO와 같은 Node.js 관련 WebSocket 모듈과 충돌하는 IIS WebSocket 모듈이 사용하지 않도록 설정됩니다. 이 줄이 없거나  `true`로 설정되면 WebSocket 전송이 사용자 응용 프로그램에 작동하지 않습니다.

	일반적으로 Node.js 응용 프로그램에는 **web.config** 파일이 포함되어 있지 않으므로 Azure 웹 사이트를 배포할 때 해당 웹 사이트에서 Node.js 응용 프로그램에 대해 이 파일을 자동으로 생성합니다. 이 파일은 서버에서 자동으로 생성되기 때문에 이 파일을 보려면 웹 사이트에 FTP 또는 FTPS URL을 사용해야 합니다. 사이트의 FTP 및 FTPS URL은 Azure 관리 포털에서 웹 사이트를 선택한 후 **대시보드** 링크를 선택하여 찾을 수 있습니다. 이러한 URL은 **빠른 보기** 섹션에 표시됩니다.

	> [AZURE.NOTE] **web.config** 파일은 응용 프로그램에서 제공하지 않는 경우에만 Azure 웹 사이트에 의해 생성됩니다. 응용 프로그램 프로젝트의 루트에 **web.config** 파일을 제공한 경우에는 Azure 웹 사이트에서 이 파일이 사용됩니다.

	이 항목이 없거나  `true` 값으로 설정된 경우 Node.js 응용 프로그램의 루트에 **web.config**를 만들고  `false` 값을 지정해야 합니다.  참고로 다음은 **app.js**를 진입점으로 사용하는 응용 프로그램의 기본 **web.config**입니다.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
		     This configuration file is required if iisnode is used to run node processes behind
		     IIS or IIS Express.  For more information, visit:
		
		     https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
		-->
		
		<configuration>
		  <system.webServer>
		    <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
		    <webSocket enabled="false" />
		    <handlers>
		      <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
		      <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
		    </handlers>
		    <rewrite>
		      <rules>
		        <!-- Do not interfere with requests for node-inspector debugging -->
		        <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
		          <match url="^app.js\/debug[\/]?" />
		        </rule>
		
		        <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
		        <rule name="StaticContent">
		          <action type="Rewrite" url="public{REQUEST_URI}"/>
		        </rule>
		
		        <!-- All other URLs are mapped to the node.js site entry point -->
		        <rule name="DynamicContent">
		          <conditions>
		            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
		          </conditions>
		          <action type="Rewrite" url="app.js"/>
		        </rule>
		      </rules>
		    </rewrite>
		    <!--
		      You can control how Node is hosted within IIS using the following options:
		        * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
		        * node_env: will be propagated to node as NODE_ENV environment variable
		        * debuggingEnabled - controls whether the built-in debugger is enabled
		
		      See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
		    -->
		    <!--<iisnode watchedFiles="web.config;*.js"/>-->
		  </system.webServer>
		</configuration>

	> [AZURE.NOTE] 응용 프로그램에서 **app.js**가 아닌 다른 진입점을 사용하는 경우 표시되는 **app.js**를 모두 올바른 진입점으로 바꿔야 합니다. 예를 들어 **app.js**를 **server.js**로 바꿉니다.

##다음 단계

이 자습서에서는 Azure 웹 사이트에 호스트되는 기본 채팅 응용 프로그램을 만드는 방법을 알아보았습니다. 이 응용 프로그램은 Azure 클라우드 서비스로 호스트할 수도 있습니다. 이를 수행하는 방법에 대한 단계는 [Azure 클라우드 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드][cloudservice]를 참조하세요.

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Socket.IO GitHub repository]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /ko-kr/develop/nodejs/tutorials/app-using-socketio/
	
[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /ko-kr/pricing/details/web-sites/

<!--HONumber=42-->
