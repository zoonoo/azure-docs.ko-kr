<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" writer="wesmc" manager="" editor="" />

푸시 알림 모바일 서비스 시작
============================

[Windows 스토어 C\#](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push "Windows 스토어 C#") [Windows 스토어 JavaScript](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push "Windows 스토어 JavaScript") [Windows Phone](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push "Windows Phone")

[.NET 백 엔드](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push ".NET 백 엔드") | [JavaScript 백 엔드](/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/ "JavaScript 백 엔드")

이 항목에서는 Windows Azure .Net 런타임 모바일 서비스를 사용하여 Windows Phone 8 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 빠른 시작 프로젝트에 대한 Windows Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 .Net 런타임 모바일 서비스에서 알림 허브를 사용하여 푸시 알림을 전송합니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서도 사용 가능합니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [알림 등록을 위해 앱 업데이트](#update-app)
2.  [푸시 알림을 전송하도록 서버 업데이트](#update-server)
3.  [푸시 알림을 받기 위한 데이터 삽입](#test)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기](/ko-kr/documentation/articles/mobile-services-windows-store-get-started) 또는 [데이터 시작하기](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다.

알림 등록을 위해 앱 업데이트
----------------------------

앱에서 푸시 알림을 받을 수 있으려면 알림 채널을 등록해야 합니다.

1.  Visual Studio에서 App.xaml.cs 파일을 열고 다음 `using` 문을 추가합니다.

         using Microsoft.Phone.Notification;

2.  `App` 클래스에 다음과 같은 `AcquirePushChannel` 메서드를 추가합니다.

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

3.  App.xaml.cs에서 `Application_Launching` 이벤트 처리기에 다음과 같은 새 `AcquirePushChannel` 메서드 호출을 추가합니다.

         AcquirePushChannel();

    이제 앱이 로드될 때마다 등록이 요청됩니다. 등록 상태가 유지되도록 앱에서 주기적으로 등록할 수도 있습니다.

4.  **F5** 키를 눌러 앱을 실행합니다. 등록 키가 포함된 팝업 대화 상자가 표시됩니다.

5.  Visual Studio에서 Package.appxmanifest 파일을 열고 **응용 프로그램 UI** 탭에서 **알림 가능**이 **예**로 설정되어 있는지 확인합니다.

  ![][1]

  이제 앱에서 알림 메시지를 표시할 수 있습니다. 

푸시 알림을 전송하도록 서버 업데이트
------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

1.  [Windows Azure 관리 포털](%20https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

2.  **푸시** 탭을 클릭하고 **인증되지 않은 푸시 알림을 사용하도록 설정합니다.**를 선택한 다음 **저장**을 클릭합니다.

![](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png)

> [WACOM.NOTE]이 자습서에서는 인증되지 않은 모드로 MPNS를 사용합니다. 이 모드에서 MPNS는 장치 채널로 전송할 수 있는 알림 수를 제한합니다. 이 제한을 제거하려면 **업로드**를 클릭하고 인증서를 선택하여 인증서를 생성 및 업로드해야 합니다. 인증서 생성에 대한 자세한 내용은 [Windows Phone의 푸시 알림을 보내도록 인증된 웹 서비스 설정](http://msdn.microsoft.com/ko-kr/library/windowsphone/develop/ff941099(v=vs.105).aspx)을 참조하십시오.

이렇게 하면 푸시 알림을 보낼 수 있도록 모바일 서비스가 인증되지 않은 모드에서 MPNS에 연결됩니다.

앱에서 푸시 알림 테스트
-----------------------

1.  Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2.  앱에서 "hello push" 텍스트를 텍스트 상자에 입력한 후 **저장**을 클릭합니다.

  ![][2]

  추가된 항목을 저장하기 위해 삽입 요청이 모바일 서비스로 전송됩니다. 응용 프로그램이 **hello push**라는 내용의 알림 메시지를 받게 됩니다.

다음 단계
---------

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보십시오.

-   [알림 허브 시작하기](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
  <br/>Windows 스토어 앱에서 알림 허브를 활용하는 방법에 대해 알아보십시오.

-   [구독자에게 알림 보내기](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
  <br/>관심이 있는 범주에 대해 푸시 알림을 등록하고 받아보는 방법에 대해 알아보십시오.

-   [사용자에게 알림 보내기](/en-us/manage/services/notification-hubs/notify-users/)
  <br/>모바일 서비스에서 장치와 상관없이 특정 사용자에게 푸시 알림을 보내는 방법에 대해 알아보십시오.

-   [사용자에게 플랫폼 간 알림 보내기](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
  <br/>백 엔드에 플랫폼 전용 페이로드를 마련할 필요 없이 템플릿을 사용하여 모바일 서비스에서 푸시 알림을 보내는 방법에 대해 알아보십시오.

다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [데이터 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)
  <br/>.Net 런타임 모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보십시오.

-   [인증 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users)
  <br/>.Net 런타임 모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아보십시오.

-   [모바일 서비스 서버 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

-   [모바일 서비스 .NET 방법 개념 참조](/ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
  <br/>.NET과 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보십시오.


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
