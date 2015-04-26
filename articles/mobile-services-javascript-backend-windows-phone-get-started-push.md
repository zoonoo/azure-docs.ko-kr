<properties 
	pageTitle="푸시 알림 시작(Windows 스토어) | 모바일 개발자 센터" 
	description="Azure 모바일 서비스와 알림 허브를 사용하여 Windows 스토어 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>


# 모바일 서비스 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 Windows Phone Silverlight 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 퀵 스타트 프로젝트에 대한 Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 알림 허브를 사용하여 푸시 알림을 전송합니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서 도 사용 가능합니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [알림 등록을 위해 앱 업데이트](#update-app)
2. [푸시 알림을 전송하도록 서버 스크립트 업데이트](#update-scripts)
3. [푸시 알림을 받기 위한 데이터 삽입](#test)

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작] 또는 [데이터 시작]을 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 모바일 서비스가 연결되지 않은 경우 푸시 알림 추가 마법사에서 대신 연결해줍니다. 

>[AZURE.NOTE]Windows Phone 8.1 스토어 앱으로 푸시 알림을 보내려면 이 자습서의 [Windows 스토어 앱](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) 버전을 따릅니다.

##<a id="update-app"></a> 알림 등록을 위해 앱 업데이트

앱에서 푸시 알림을 받을 수 있으려면 알림 채널을 등록해야 합니다.

1. Visual Studio에서 App.xaml.cs 파일을 열고 다음  `using` 문을 추가합니다.

        using Microsoft.Phone.Notification;

3. App.xaml.cs에 다음을 추가합니다.
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                    // Register for notifications using the new channel
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    이 코드는 Windows Phone 8.x "Silverlight"에서 사용되는 MPNS(Microsoft 푸시 알림 서비스)에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 푸시 알림에 대해 등록합니다.

	>[AZURE.NOTE]이 자습서에서 모바일 서비스는 알림 메시지를 장치에 보냅니다. 타일 알림을 보내는 경우 채널에서 **BindToShellTile** 메서드를 대신 호출해야 합니다.

4. App.xaml.cs에서 **Application_Launching** 이벤트 처리기의 맨 위에 다음과 같은 새 **AcquirePushChannel** 메서드 호출을 추가합니다.

        AcquirePushChannel();

	이제 페이지가 로드될 때마다 등록이 요청됩니다. 등록 상태가 유지되도록 앱에서 주기적으로 등록할 수도 있습니다. 

5. **F5** 키를 눌러 앱을 실행합니다. 등록 키가 포함된 팝업 대화 상자가 표시됩니다.
  
6.	솔루션 탐색기에서 **속성**을 확장하고 WMAppManifest.xml 파일을 연 후 **기능** 탭을 클릭하고 **ID___CAP___PUSH_NOTIFICATION** 기능이 선택되었는지 확인합니다.

   	![][1]

   	이제 앱에서 알림 메시지를 표시할 수 있습니다. 

##<a id="update-scripts"></a> 푸시 알림을 전송하도록 서버 스크립트 업데이트

마지막으로 TodoItem 테이블에서 삽입 작업에 등록된 스크립트를 업데이트하여 알림을 보내야 합니다.

1. **TodoItem**을 클릭하고 **스크립트**를 클릭한 후 **삽입**을 선택합니다. 

   	![][10]

2. 삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

		function insert(item, user, request) {
		// Define a payload for the Windows Phone toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
		    '</wp:Text2></wp:Toast></wp:Notification>';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.mpns.send(null, payload, 'toast', 22, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });      
		}

	삽입이 성공한 후 이 삽입 스크립트는 모든 Windows Phone 앱 등록에 푸시 알림(삽입된 항목의 텍스트와 함께)을 전송합니다.

3. **푸시** 탭을 클릭하고 **인증되지 않은 푸시 알림을 사용하도록 설정합니다.**를 선택한 다음 **저장**을 클릭합니다.

	>[AZURE.NOTE]이전 모바일 서비스를 사용하여 이 자습서를 완료하는 경우 **푸시** 탭의 맨 아래에 **향상된 푸시 설정**이라는 링크가 표시될 수 있습니다. 이 링크를 클릭하면 알림 허브와 통합되도록 모바일 서비스가 업그레이드됩니다. 이 변경 작업은 되돌릴 수 없습니다. 프로덕션 모바일 서비스에서 향상된 푸시 알림을 설정하는 방법에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">이 지침</a>을 참조하세요.

	![][11]

	이렇게 하면 푸시 알림을 보낼 수 있도록 모바일 서비스가 인증되지 않은 모드에서 MPNS에 연결됩니다.

	>[AZURE.NOTE]이 자습서에서는 인증되지 않은 모드로 MPNS를 사용합니다. 이 모드에서 MPNS는 장치 채널로 전송할 수 있는 알림 수를 제한합니다. 이 제한을 제거하려면 **업로드**를 클릭하고 인증서를 선택하여 인증서를 생성 및 업로드해야 합니다. 인증서 생성에 대한 자세한 내용은 [Windows Phone의 푸시 알림을 보내도록 인증된 웹 서비스 설정]을 참조하세요.

##<a id="test"></a> 앱에서 푸시 알림 테스트

1. Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

    >[AZURE.NOTE] Windows Phone 에뮬레이터에서 테스트하는 경우 401 Unauthorized RegistrationAuthorizationException이 발생할 수 있습니다. 이는 Windows Phone 에뮬레이터가 해당 시계를 호스트 PC와 동기화하는 방법으로 인해  `RegisterNativeAsync()`를 호출하는 동안 발생할 수 있습니다. 이로 인해 보안 토큰이 거부될 수 있습니다. 이 문제를 해결하려면 테스트 전에 에뮬레이터의 시계를 수동으로 설정하면 됩니다.

5. 앱의 텍스트 상자에 "hello push"라는 텍스트를 입력하고 **저장**을 클릭한 후 시작 단추 또는 뒤로 단추를 바로 클릭하여 앱을 종료합니다.

   	![][4]

  	추가된 항목을 저장하기 위해 삽입 요청이 모바일 서비스로 전송됩니다. 장치가 **hello push**라는 내용의 알림 메시지를 받게 됩니다.

	![][5]

	>[AZURE.NOTE]계속 앱을 열어 두면 알림이 수신되지 않습니다. 앱이 활성 상태인 동안 알림 메시지를 수신하려면 [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx) 이벤트를 처리해야 합니다.



## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스 및 알림 허브를 사용하여 푸시 알림을 보내도록 설정하기 위한 기본 사항에 대해 설명했습니다. 이후에는 다음 자습서 중 하나를 완료하는 것이 좋습니다.

+ [인증된 사용자에게 푸시 알림 보내기]
	<br/>태그를 사용하여 모바일 서비스의 푸시 알림을 인증된 사용자에게만 보내는 방법에 대해 알아봅니다.

+ [구독자에게 브로드캐스트 알림 보내기]
	<br/>사용자가 관심 있어 하는 범주에 대해 푸시 알림을 등록하고 수신하는 방법을 설명합니다.

<!---+ [구독자에게 템플릿 기반 알림 보내기]
	<br/>백 엔드에 플랫폼 전용 페이로드를 마련할 필요 없이 템플릿을 사용하여 모바일 서비스에서 푸시 알림을 보내는 방법에 대해 알아봅니다.
-->
다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [데이터 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* [인증 시작](영문)
  <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [알림 허브 정의]
  <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [모바일 서비스 .NET 방법 개념 참조]
  <br/>모바일 서비스를 .NET과 함께 사용하는 방법에 대해 알아봅니다.

* [모바일 서비스 서버 스크립트 참조]
  <br/>모바일 서비스에서 비즈니스 논리를 구현하는 방법에 대해 자세히 알아봅니다.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-data
[인증 시작]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-users

[Windows Phone의 푸시 알림을 보내도록 인증된 웹 서비스 설정]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[인증된 사용자에게 푸시 알림 보내기]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/

[알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
[구독자에게 브로드캐스트 알림 보내기]: /ko-kr/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[구독자에게 템플릿 기반 알림 보내기]: /ko-kr/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/



<!--HONumber=42-->
