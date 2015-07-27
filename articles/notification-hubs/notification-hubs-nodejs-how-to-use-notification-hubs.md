<properties
	pageTitle="Node.js에서 알림 허브를 사용하는 방법"
	description="알림 허브를 사용하여 Node.js 응용 프로그램에 푸시 알림을 보내는 방법에 대해 알아봅니다."
	services="notification-hubs"
	documentationCenter="nodejs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="wesmc"/>

# Node.js에서 알림 허브를 사용하는 방법
<div class="dev-center-tutorial-selector sublanding">
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js" class="current">Node.js</a>
</div>

##개요

이 가이드에서는 Node.js 응용 프로그램에서 알림 허브를 사용하는 방법을 보여 줍니다. 여기서 다루는 시나리오에는 **Android, iOS, Windows Phone 및 Windows 스토어 응용 프로그램에 알림 보내기**가 포함됩니다. 알림 허브에 대한 자세한 내용은 [다음 단계](#next) 섹션을 참조하십시오.

##알림 허브 정의

Azure 알림 허브는 모바일 서비스에 푸시 알림을 보내는 사용하기 쉽고 확장 가능한 다중 플랫폼 인프라를 제공합니다. 자세한 내용은 [Azure 알림 허브](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx)를 참조하십시오.

##Node.js 응용 프로그램 만들기

빈 Node.js 응용 프로그램을 만듭니다. Node.js 응용 프로그램을 만드는 방법에 대한 지침은 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포하기][nodejswebsite], [Node.js 클라우드 서비스][Node.js Cloud Service](Windows PowerShell 사용) 또는 [WebMatrix를 사용하는 웹 사이트]를 참조하십시오.

##알림 허브를 사용하도록 응용 프로그램 구성

Azure 알림 허브를 사용하려면 Node.js Azure 패키지를다운로드하여 사용해야 합니다. 이 패키지에는 REST 서비스와 통신하는 편의 라이브러리 집합이 포함되어 있습니다.

### NPM(Node Package Manager)을 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac), **Bash**(Unix) 등과 같은 명령줄 인터페이스를 사용하여 샘플 응용 프로그램을 만든 폴더로 이동합니다.

2.  명령 창에 **npm install azure**를 입력합니다. 그러면 다음과 같이 출력됩니다.

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  **ls** 또는 **dir** 명령을 수동으로 실행하여 **node_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 알림 허브에 액세스하는 데 필요한 라이브러리가 들어 있는 **azure** 패키지를 찾습니다.

### 모듈 가져오기

텍스트 편집기를 사용하여 다음을 응용 프로그램의 **server.js** 파일 맨 위에 추가합니다.

    var azure = require('azure');

### Azure 알림 허브 연결 설정

**NotificationHubService** 개체를 사용하면 허브 알림으로 작업할 수 있습니다. 다음 코드는 **hubname** 알림 허브에 대한 **NotificationHubService** 개체를 만듭니다. 이 코드를 **server.js** 파일의 위쪽, Azure 모듈을 가져오기 위한 문 뒤에 추가하십시오.

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

다음 단계를 수행하여 Azure 관리 포털에서 **connectionstring** 연결 값을 가져올 수 있습니다.

1. Azure 관리 포털에서 **서비스 버스**를 선택한 다음 알림 허브를 포함하는 네임스페이스를 선택합니다.

2. **알림 허브**를 선택한 다음 사용할 허브를 선택합니다.

3. **간략 상태** 섹션에서 **연결 문자열 보기**를 선택하고 연결 문자열 값을 복사합니다.

> [AZURE.NOTE]Azure PowerShell에서 제공하는 **Get-AzureSbNamespace** cmdlet 또는 Azure CLI(Azure 명령줄 인터페이스)의 **azure sb namespace show** 명령을 사용하여 연결 문자열을 검색할 수도 있습니다.

</div>

##알림을 보내는 방법

**NotificationHubService** 개체는 특정 장치 및 응용 프로그램에 알림을 보내는 다음 개체 인스턴스를 표시합니다.

* **Android** - **notificationHubService.gcm**에서 제공되는 **GcmService** 개체를 사용합니다.
* **iOS** - **notificationHubService.apns**에서 액세스할 수 있는 **ApnsService** 개체를 사용합니다.
* **Windows Phone** - **notificationHubService.mpns**에서 제공되는 **MpnsService** 개체를 사용합니다.
* **Windows 스토어 응용 프로그램** - **notificationHubService.wns**에서 제공되는 **WnsService** 개체를 사용합니다.

### Android 응용 프로그램 알림을 보내는 방법

**GcmService** 개체는 Android 응용 프로그램에 알림을 보내는 데 사용할 수 있는 **send** 메서드를 제공합니다. **send** 메서드는 다음 매개 변수를 수락합니다.

* Tags - 태그 식별자. 태그를 제공하지 않은 경우 모든 클라이언트에게 알림이 전송됩니다.
* Payload - 메시지의 JSON 또는 문자열 페이로드
* Callback - 콜백 함수

페이로드 형식에 대한 자세한 내용은 [GCM 서버 구현](http://developer.android.com/google/gcm/server.html#payload)(영문)의 페이로드 섹션을 참조하십시오.

다음 코드는 **NotificationHubService**에 의해 표시되는 **GcmService** 인스턴스를 사용하여 모든 클라이언트에 메시지를 보냅니다.

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### iOS 응용 프로그램 알림을 보내는 방법

**ApnsService** 개체는 iOS 응용 프로그램에 알림을 보내는 데 사용할 수 있는 **send** 메서드를 제공합니다. **send** 메서드는 다음 매개 변수를 수락합니다.

* Tags - 태그 식별자. 태그를 제공하지 않은 경우 모든 클라이언트에게 알림이 전송됩니다.
* Payload - 메시지의 JSON 또는 문자열 페이로드
* Callback - 콜백 함수

페이로드 형식에 대한 자세한 내용은 [로컬 및 푸시 알림 프로그래밍 가이드](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)(영문)의 알림 페이로드 섹션을 참조하십시오.

다음 코드는 **NotificationHubService**에 의해 표시되는 **ApnsService** 인스턴스를 사용하여 모든 클라이언트에 경고 메시지를 보냅니다.

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Windows Phone 알림을 보내는 방법

**MpnsService** 개체는 Windows Phone 응용 프로그램에 알림을 보내는 데 사용할 수 있는 **send** 메서드를 제공합니다. **send** 메서드는 다음 매개 변수를 수락합니다.

* Tags - 태그 식별자. 태그를 제공하지 않은 경우 모든 클라이언트에게 알림이 전송됩니다.
* Payload - 메시지의 XML 페이로드
* TargetName - 알림 메시지의 '알림(toast)'이고 타일 알림의 '토큰'입니다.
* NotificationClass - 알림 우선 순위. 유효한 값은 [서버의 푸시 알림](http://msdn.microsoft.com/library/hh221551.aspx)(영문)의 HTTP 헤더 요소 섹션을 참조하십시오.
* Options - 선택적 요청 헤더
* Callback - 콜백 함수

유효한 TargetName, NotificationClass 및 헤더 옵션 목록은 [서버의 푸시 알림](http://msdn.microsoft.com/library/hh221551.aspx)(영문)을 참조하십시오.

다음 코드는 **NotificationHubService**에 의해 표시되는 **MpnsService** 인스턴스를 사용하여 알림 경고를 보냅니다.

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Windows 스토어 응용 프로그램 알림을 보내는 방법

**WnsService** 개체는 Windows 스토어 응용 프로그램에 알림을 보내는 데 사용할 수 있는 **send** 메서드를 제공합니다. **send** 메서드는 다음 매개 변수를 수락합니다.

* Tags - 태그 식별자. 태그를 제공하지 않은 경우 모든 클라이언트에게 알림이 전송됩니다.
* Payload - XML 메시지 페이로드
* Type - 알림 유형
* Options - 선택적 요청 헤더
* Callback - 콜백 함수

유효한 유형 및 요청 헤더 목록은 [푸시 알림 서비스 요청 및 응답 헤더](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx)를 참조하십시오.

다음 코드는 **NotificationHubService**에 의해 표시되는 **WnsService** 인스턴스를 사용하여 알림 경고를 보냅니다.

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## 다음 단계

이제 알림 허브 사용에 대한 기본 사항을 익혔으므로 다음 링크를 따라 이동하여 자세한 내용을 확인할 수 있습니다.

-   MSDN 참조: [Azure 알림 허브][]를 참조하세요.
-   GitHub에서 [Azure SDK for Node] 리포지토리를 방문하십시오.

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Management Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [WebMatrix를 사용하는 웹 사이트]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
 

<!---HONumber=July15_HO3-->