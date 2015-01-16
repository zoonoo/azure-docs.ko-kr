<properties pageTitle=".NET 런타임 모바일 서비스를 사용하여 푸시 알림 허브 시작" metaKeywords="" description="Microsoft Azure .Net 런타임 모바일 서비스 및 알림 허브를 사용하여 Windows phone 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc"  solutions="" writer="wesmc" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# 모바일 서비스 앱에 푸시 알림 추가

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

이 항목에서는 .NET 백 엔드와 함께 Azure 모바일 서비스를 사용하여 Windows Phone Silverlight 8 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 빠른 시작 프로젝트에 대한 Microsoft Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 알림 허브를 사용하여 푸시 알림을 전송합니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서도 사용 가능합니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [알림 등록을 위해 앱 업데이트](#update-app)
3. [푸시 알림을 전송하도록 서버 업데이트](#update-server)
4. [로컬 테스트에 푸시 알림 사용](#local-testing)
3. [푸시 알림을 받기 위한 데이터 삽입](#test)

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작] 또는 [데이터 작업 시작]를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다.

>[WACOM.NOTE]이 자습서는 Windows Phone 8.1 "Silverlight" 앱을 대상으로 합니다. 대신 Windows Phone 8.1 스토어 앱을 작성하는 경우 이 자습서의 [Windows 스토어 앱](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) 버전을 참조하세요. Windows Phone Silverlight 앱에 대한 자세한 내용과 Windows Phone 스토어 앱과의 비교 결과를 확인하려면 [Windows Phone Silverlight 8.1 앱]을 참조하세요. 

##<a id="update-app"></a> 알림 등록을 위해 앱 업데이트

앱에서 푸시 알림을 받을 수 있으려면 알림 채널을 등록해야 합니다.

1. Visual Studio에서 App.xaml.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.Phone.Notification;

2. 다음 `AcquirePushChannel` 메서드를 `App` 클래스에 추가합니다. 

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
                    System.Exception exception = null;
                    try
                    {
                        await MobileService.GetPush()
                            .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                    }
                    catch (System.Exception ex)
                    {
                        CurrentChannel.Close();
                        exception = ex;
                    }
                    if (exception != null)
                    {
                        Deployment.Current.Dispatcher.BeginInvoke(() =>
                        {
                            MessageBox.Show(exception.Message, 
                                            "Registering for Push Notifications",
                                            MessageBoxButton.OK);
                        });
                    }
            });
            CurrentChannel.ShellToastNotificationReceived += 
                new EventHandler<NotificationEventArgs>((o, args) =>
                {
                    string message = "";
                    foreach (string key in args.Collection.Keys)
                    {
                        message += key + " : " + args.Collection[key] + ", ";
                    }
                    Deployment.Current.Dispatcher.BeginInvoke(() =>
                    {
                        MessageBox.Show(message);
                    });
            });
        }

    이 코드는 앱의 채널 URI를 검색합니다(있는 경우). 채널 URI가 없는 경우 생성됩니다. 그런 다음 채널 URI가 열리고 알림 메시지에 바인딩됩니다. 채널 URI가 완전히 열리면 `ChannelUriUpdated` 메서드에 대한 처리기가 호출되고, 수신된 푸시 알림에 채널이 등록됩니다. 등록이 실패하면 이후 앱을 실행할 때 등록을 다시 시도하도록 채널이 닫힙니다. 앱이 실행 중에 푸시 알림을 받고 처리할 수 있도록 `ShellToastNotificationReceived` 처리기가 설정됩니다.
    
4. App.xaml.cs에서 `Application_Launching` 이벤트 처리기에 다음과 같은 새 `AcquirePushChannel` 메서드 호출을 추가합니다.

        AcquirePushChannel();

	이제 앱이 로드될 때마다 등록이 요청됩니다. 등록 상태가 유지되도록 앱에서 주기적으로 등록할 수도 있습니다. 

5. **F5** 키를 눌러 앱을 실행합니다. 등록 키가 포함된 팝업 대화 상자가 표시됩니다.
  
6. Visual Studio에서 Package.appxmanifest 파일을 열고 **응용 프로그램 UI** 탭에서 **알림 가능**이 **예**로 설정되어 있는지 확인합니다.

   	![][1]

   	이제 앱에서 알림 메시지를 표시할 수 있습니다. 

##<a id="update-server"></a> 푸시 알림을 전송하도록 서버 업데이트

1. Visual Studio 솔루션 탐색기에서 모바일 서비스 프로젝트의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 열고 다음 코드로 `PostTodoItem` 메서드 정의를 업데이트합니다.  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>" + item.Text + "</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    이 코드는 할 일 항목을 삽입한 후 삽입한 항목의 텍스트가 포함된 푸시 알림을 보냅니다. 오류 이벤트에서 코드는 관리 포털에서 모바일 서비스의 **로그** 탭에서 볼 수 있는 오류 로그 항목을 추가합니다.

2. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

3. **푸시** 탭을 클릭하고 **인증되지 않은 푸시 알림을 사용하도록 설정합니다.**를 선택한 다음 **저장**을 클릭합니다.

   	![][4]

	>[WACOM.NOTE]이 자습서에서는 인증되지 않은 모드로 MPNS를 사용합니다. 이 모드에서 MPNS는 장치 채널로 전송할 수 있는 알림 수를 제한합니다. 이 제한을 제거하려면 <strong>업로드</strong>를 클릭하고 인증서를 선택하여 인증서를 생성 및 업로드해야 합니다. 인증서 생성에 대한 자세한 내용은 <a href="http://msdn.microsoft.com/ko-kr/library/windowsphone/develop/ff941099(v=vs.105).aspx">Windows Phone의 푸시 알림을 보내도록 인증된 웹 서비스 설정</a>을 참조하세요.

이렇게 하면 푸시 알림을 보낼 수 있도록 모바일 서비스가 인증되지 않은 모드에서 MPNS에 연결됩니다.

##<a id="local-testing"></a> 로컬 테스트에 푸시 알림 사용

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]


##<a id="test"></a> 앱에서 푸시 알림 테스트

1. Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

    >[WACOM.NOTE] Windows Phone 에뮬레이터에서 테스트하는 경우 401 Unauthorized RegistrationAuthorizationException이 발생할 수 있습니다. 이는 Windows Phone 에뮬레이터가 해당 시계를 호스트 PC와 동기화하는 방법으로 인해 `RegisterNativeAsync()`를 호출하는 동안 발생할 수 있습니다. 이로 인해 보안 토큰이 거부될 수 있습니다. 이 문제를 해결하려면 테스트 전에 에뮬레이터의 시계를 수동으로 설정하면 됩니다.

5. 앱의 텍스트 상자에 "hello push"라는 텍스트를 입력하고 **저장**을 클릭한 후 시작 단추 또는 뒤로 단추를 바로 클릭하여 앱을 종료합니다.

   	![][2]

  	추가된 항목을 저장하기 위해 삽입 요청이 모바일 서비스로 전송됩니다. 장치가 **hello push**라는 내용의 알림 메시지를 받게 됩니다.

	![][5]

	>[WACOM.NOTE]계속 앱을 열어 두면 알림이 수신되지 않습니다. 앱이 활성 상태인 동안 알림 메시지를 수신하려면 [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx) 이벤트를 처리해야 합니다.

## <a name="next-steps">다음 단계</a>

이 자습서에서는 Windows Phone 앱에서 모바일 서비스와 알림 허브를 사용하여 푸시 알림을 보내도록 하기 위한 기본 사항을 알아보았습니다. 다음으로는 태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 보여 주는 다음 자습서인 [인증된 사용자에게 푸시 알림 보내기]를 완료할 수 있습니다.

<!--+ [Send push notifications to authenticated users]
	<br/>태그를 사용하는 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내도록 하는 방법을 알아봅니다.

+ [구독자에게 브로드캐스트 알림 보내기]
	<br/>사용자가 관심 있는 범주에 대한 푸시 알림을 등록하고 수신하게 하는 방법을 알아봅니다.
-->
다음의 모바일 서비스 및 알림 허브 항목에 대해서도 자세히 알아보세요.

* [데이터 작업 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장하고 쿼리하는 방법을 알아봅니다.

* [인증 시작]
  <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법을 알아봅니다.

* [알림 허브 정의]
  <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [알림 허브 응용 프로그램 디버깅](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>알림 허브 솔루션 문제를 해결하고 디버깅하기 위한 지침을 얻으세요. 

* [모바일 서비스 .NET 방법 개념 참조]
  <br/>모바일 서비스를 .NET과 함께 사용하는 방법에 대해 알아봅니다.

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
[5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png

<!-- URLs. -->
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users

[인증된 사용자에게 푸시 알림 보내기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/

[알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
[구독자에게 브로드캐스트 알림 보내기]: /ko-kr/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[구독자에게 템플릿 기반 알림 보내기]: /ko-kr/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/


[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-html-how-to-use-client-library
[Windows Phone Silverlight 8.1 앱]: http://msdn.microsoft.com/ko-kr/library/windowsphone/develop/dn642082(v=vs.105).aspx
[Azure 관리 포털]: https://manage.windowsazure.com/
