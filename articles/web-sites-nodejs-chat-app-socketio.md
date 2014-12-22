<properties urlDisplayName="Website Using Socket.IO" pageTitle="Socket.io를 사용하는 Node.js 웹 사이트 - Azure 자습서" metaKeywords="Azure Node.js socket.io 자습서, Azure Node.js socket.io, Azure Node.js 자습서" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />




#Azure 웹 사이트에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드

Socket.IO는 WebSocket을 사용하여 node.js 서버와 클라이언트 간의 실시간 통신을 제공합니다. 또한 이전 브라우저에서 작동하는 다른 전송(예: 긴 폴링)으로의 대체를 지원합니다. 이 자습서는 Azure 웹 사이트에서 Socket.IO를 기반으로 하는 채팅 응용 프로그램 호스팅에 대해 안내합니다. Socket.IO에 대한 자세한 내용은 [http://socket.io/][socketio](영문)를 참조하세요.

> [WACOM.NOTE] 이 작업의 절차는 Azure 웹 사이트에 적용됩니다. 클라우드 서비스에 대해서는 <a href="http://www.windowsazure.com/ko-kr/develop/nodejs/tutorials/app-using-socketio/">Azure 클라우드 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드</a>를 참조하세요.


## <a id="Download"></a>채팅 예제 다운로드

이 프로젝트에서는 [Socket.IO
GitHub 리포지토리](영문)의 채팅 예제를 사용합니다. 다음 단계에 따라 예제를 다운로드하여
이전에 만든 프로젝트에 추가하세요.

1.  Socket.IO 프로젝트의 [ZIP 또는 GZ 보관 릴리스][릴리스](영문)를 다운로드합니다(이 문서에서는 버전 1.0.6 사용).


3.  보관 파일을 추출하고 **examples\\chat**
    새 위치(예:
    **\\node\\chat**)에 복사합니다.

## <a id="Modify"></a>App.js 수정 및 모듈 설치

Azure에 응용 프로그램을 배포하기 전에
몇 가지 사항을 수정해야 합니다.

1.  **index.js** 파일의 이름을 **app.js**로 바꿉니다. 그러면 Azure에서 이 파일을 Node.js 응용 프로그램으로 검색할 수 있습니다.

1.  메모장 또는 기타 텍스트 편집기에서 **app.js** 파일을 엽니다.

2.  app.js의 시작 부분에서 **Module dependencies** 섹션을 찾아 아래에 표시된 대로 `var io = require('../..')(server);`가 포함된 줄을 `var io = require('socket.io')(server);`로 변경합니다.

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

	> [WACOM.NOTE] 새 버전의 Socket.IO를 사용하여 이 문서의 단계를 수행할 수도 있지만 이 문서는 버전 1.0.6으로 테스트되었습니다.

## <a id="Publish"></a>Azure 웹 사이트 만들기

다음 단계에 따라 Azure 웹 사이트를 만들고, Git 게시를 사용하도록 설정한 다음, 웹 사이트에 대한 WebSocket 지원을 사용하도록 설정합니다.

> [WACOM.NOTE]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 다음을 참조하세요. <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure Free Trial</a>.

1. From the command-line, change directories to the **\\node\chat** directory and use the following command to create a new Azure Website and enable a Git repository for the website and the local directory. This will also create a Git remote named 'azure'.

		azure site create mysitename --git

	You must replace 'mysitename' with a unique name for your website.

2. Commit the existing files to the local repository by using the following commands:

		git add .
		git commit -m "Initial commit"

3. Push the files to the Azure Website repository with the following command:

		git push azure master

	You will receive status messages as modules are imported on the server. Once this process has completed, the application will be hosted on your Azure Website.

 	> [WACOM.NOTE] During module installation, you may notice errors that 'The imported project ... was not found'. These can safely be ignored.

4. Socket.IO uses WebSockets, which are not enabled by default on Azure. To enable web sockets, use the following command:

		azure site set -w

	If prompted, enter the name of the website.

	>[WACOM.NOTE]
	>The 'azure site set -w' command will only work with version 0.7.4 or higher of the Azure Cross-Platform Command-Line Interface. You can also enable WebSocket support using the Azure Management Portal.
	>
	>To enable WebSockets using the [Azure Management Portal](https://manage.windowsazure.com), select the Configure page for your website, select 'ON' for the Web Sockets entry, and then click Save.
	>	
	>![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. To view the website on Azure, use the following command to launch your web browser and navigate to the hosted website:

		azure site browse

Your application is now running on Azure, and can relay chat
messages between different clients using Socket.IO.

> [WACOM.NOTE] For simplicity, this sample is limited to chatting between users connected to the same instance. This means that if the cloud service creates two worker role instances, users will only be able to chat with others connected to the same worker role instance. To scale the application to work with multiple role instances, you could use a technology like Service Bus to share the Socket.IO store state across instances. For examples, see the Service Bus Queues and Topics usage samples in the <a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub repository</a>.

##Scale out

Socket.IO applications can be scaled out by using an __adapter__ to distribute messages and events between multiple application instances. While there are several adapters available, the [socket.io-redis](https://github.com/automattic/socket.io-redis) adapter can be easily used with the Azure Redis Cache feature.

> [WACOM.NOTE] An additional requirement for scaling out a Socket.IO solution is support for sticky sessions. Sticky sessions are enabled by default for Azure Websites through Azure Request Routing. For more information, see [Instance Affinity in Azure Web Sites](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)

###Create a Redis cache

Perform the steps in [Create a cache in Azure Redis Cache](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409) to create a new cache.

> [WACOM.NOTE] Save the __Host name__ and __Primary key__ for your cache, as these will be needed in the next steps.

###Add the redis and socket.io-redis modules

1. From a command-line, change to the __\\node\\chat__ directory and use the following command.

		npm install socket.io-redis@0.1.3 redis@0.11.0 --save

	> [WACOM.NOTE] The versions specified in this command are the versions used when testing this article.

2. Modify the __app.js__ file to add the following lines immediately after `var io = require('socket.io')(server);`

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));


	Replace __redishostname__ and __rediskey__ with the host name and key for your Redis cache.

	This will create a publish and subscribe client to the Redis cache created previously. The clients are then used with the adapter to configure Socket.IO to use the Redis cache for passing messages and events between instances of your application

	> [WACOM.NOTE] While the __socket.io-redis__ adapter can communicate directly to Redis, the current version (as of 7/14/2014) does not support the authentication required by Azure Redis cache. So the initial connection is created using the __redis__ module, then the client is passed to the __socket.io-redis__ adapter.
	> 
	> While Azure Redis Cache supports secure connections using port 6380, the modules used in this example do not support secure connections as of 7/14/2014. The above code uses the default, unsecure port of 6380.

3. Save the modified __app.js__

###Commit changes and redeploy

From the command-line in the __\\node\\chat__ directory, use the following commands to commit changes and redeploy the application.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Once the changes have been pushed to the server, you can scale your site across multiple instances by using the following command.

	azure site scale instances --instances #

Where __#__ is the number of instances to create. 

You can connect to your website from multiple browsers or computers to verify that messages are correctly sent to all clients.

##<a id="tshooting"></a>Troubleshooting

###Connection limits

Azure Websites is available in multiple SKUs, which determine the resources available to your site. This includes the number of allowed WebSocket connections. For more information, see the [Web Sites Pricing page][pricing].

###Messages aren't being sent using WebSockets

If client browsers keep falling back to long polling instead of using WebSockets, it may be because of one of the following.

* **Try limiting the transport to just WebSockets**

	In order for Socket.IO to use WebSockets as the messaging transport, both the server and client must support WebSockets. If one or the other does not, Socket.IO will negotiate another transport, such as long polling. The default list of transports used by Socket.IO is ` websocket, htmlfile, xhr-polling, jsonp-polling`. You can force it to only use WebSockets by adding the following code to the **app.js** file, after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [WACOM.NOTE] Note that older browsers that do not support WebSockets will not be able to connect to the site while the above code is active, as it restricts communication to WebSockets only.

* **Use SSL**

	WebSockets relies on some lesser used HTTP headers, such as the **Upgrade** header. Some intermediate network devices, such as web proxies, may remove these headers. To avoid this problem, you can establish the WebSocket connection over SSL.

	An easy way to accomplish this is to configure Socket.IO to `match origin protocol`. This instructs Socket.IO to secure WebSockets communication the same as the originating HTTP/HTTPS request for the web page. If a browser uses an HTTPS URL to visit your website, subsequent WebSocket communications through Socket.IO will be secured over SSL.

	To modify this example to enable this configuration, add the following code to the **app.js** file after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Verify web.config settings**

	Azure Websites that host Node.js applications use the **web.config** file to route incoming requests to the Node.js application. For WebSockets to function correctly with Node.js applications, the **web.config** must contain the following entry.

		<webSocket enabled="false"/>

	이는 WebSocket의 자체 구현을 포함하고 있어 Socket.IO와 같은 Node.js 관련 WebSocket 모듈과 충돌하는 IIS WebSocket 모듈을 사용하지 않도록 설정합니다. 이 줄이 없거나 'true'로 설정되어 있으면 응용 프로그램에서 WebSocket 전송이 작동하지 않는 원인이 될 수 있습니다.

	일반적으로 Node.js 응용 프로그램에는 **web.config** 파일이 포함되어 있지 않으므로 Azure 웹 사이트를 배포할 때 해당 웹 사이트에서 Node.js 응용 프로그램에 대해 이 파일을 자동으로 생성합니다. 이 파일은 서버에서 자동으로 생성되기 때문에 이 파일을 보려면 웹 사이트에 FTP 또는 FTPS URL을 사용해야 합니다. 사이트의 FTP 및 FTPS URL은 Azure 관리 포털에서 웹 사이트를 선택한 후 **대시보드** 링크를 선택하여 찾을 수 있습니다. 이러한 URL은 **간략 상태** 섹션에 표시됩니다.

	> [WACOM.NOTE] **web.config** 파일은 응용 프로그램에서 제공하지 않는 경우에만 Azure 웹 사이트에 의해 생성됩니다. 응용 프로그램 프로젝트의 루트에 **web.config** 파일을 제공한 경우에는 Azure 웹 사이트에서 이 파일이 사용됩니다.

	이 항목이 없거나 `true` 값으로 설정된 경우 Node.js 응용 프로그램의 루트에 **web.config**를 만들고 `false` 값을 지정해야 합니다.  참고로 다음은 **app.js**를 진입점으로 사용하는 응용 프로그램의 기본 **web.config**입니다.

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

	> [WACOM.NOTE] 응용 프로그램에서 **app.js**가 아닌 다른 진입점을 사용하는 경우 표시되는 **app.js**를 모두 올바른 진입점으로 바꿔야 합니다. 예를 들어 **app.js**를 **server.js**로 바꿉니다.

##다음 단계

이 자습서에서는 Azure 웹 사이트에 호스트되는 기본 채팅 응용 프로그램을 만드는 방법을 알아보았습니다. 이 응용 프로그램은 Azure 클라우드 서비스로 호스트할 수도 있습니다. 이를 수행하는 방법에 대한 단계는 [Azure 클라우드 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드][cloudservice]를 참조하세요.

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Socket.IO GitHub 리포지토리]: https://github.com/Automattic/socket.io
[릴리스]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /ko-kr/develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[가격]: /ko-kr/pricing/details/web-sites/
