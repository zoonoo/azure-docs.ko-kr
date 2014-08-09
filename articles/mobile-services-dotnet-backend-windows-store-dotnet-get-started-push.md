<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

모바일 서비스에서 푸시 알림 시작
================================

[Windows 스토어 C\#](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push "Windows 스토어 C#")[Windows 스토어 JavaScript](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push "Windows 스토어 JavaScript")

[.NET 백 엔드](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push ".NET 백 엔드") | [JavaScript 백 엔드](/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/ "JavaScript 백 엔드")

이 항목에서는 .NET 백 엔드와 함께 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 빠른 시작 프로젝트에 대한 Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 알림 허브를 사용하여 .Net 백 엔드에서 푸시 알림을 전송합니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서도 사용 가능합니다.

> [WACOM.NOTE]모바일 서비스와 알림 허브의 통합은 현재 사전 검토 단계이며 Windows 플랫폼에서만 이용 가능합니다. 그동안에는 **알림 허브 시작**([iOS](/ko-kr/documentation/articles/notification-hubs-ios-get-started)/[Android](/ko-kr/documentation/articles/notification-hubs-android-get-started))에서 설명한 것처럼 연결된 알림 허브를 사용하여 .NET 백 엔드 서비스에서 iOS 및 Android 장치로 푸시 알림을 보낼 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [WNS로 앱 등록 및 모바일 서비스 구성](#register)
2.  [알림 등록을 위해 앱 업데이트](#update-app)
3.  [푸시 알림을 전송하도록 서버 업데이트](#update-server)
4.  [푸시 알림을 받기 위한 데이터 삽입](#test)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started) 또는 [데이터 시작하기](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data)를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 모바일 서비스가 연결되지 않은 경우 푸시 알림 추가 마법사에서 대신 연결해줍니다.

WNS로 앱 등록 및 모바일 서비스 구성
-----------------------------------

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app](../includes/mobile-services-javascript-backend-register-windows-store-app.md)]

이제 모바일 서비스와 앱이 둘 다 WNS와 알림 허브를 사용하도록 구성되었습니다. 다음에는 알림 등록을 위해 Windows 스토어 앱을 업데이트합니다.

알림 등록을 위해 앱 업데이트
----------------------------

앱에서 푸시 알림을 받을 수 있으려면 알림 채널을 등록해야 합니다.

1.  Visual Studio에서 App.xaml.cs 파일을 열고 다음 `using` 문을 추가합니다.

         using Windows.Networking.PushNotifications;
         using Windows.UI.Popups;

2.  푸시 알림 채널을 만들고 푸시 알림을 등록하기 위해 다음 `InitNotificationAsync` 메서드를 **App** 클래스에 추가합니다.

         private async void InitNotificationsAsync()
         {
             // Request a push notification channel.
             var channel = await PushNotificationChannelManager
                 .CreatePushNotificationChannelForApplicationAsync();

             // Register for notifications using the new channel
             System.Exception exception = null;
             try
             {
                 await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
             }
             catch (System.Exception ex)
             {
                 exception = ex;
             }
             if (exception != null)
             {
                 var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
             }
         }

    이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 푸시 알림에 대해 등록합니다.

3.  App.xaml.cs에서 **OnLaunched** 이벤트 처리기의 맨 위에 다음과 같은 새 **InitNotificationsAsync** 메서드 호출을 추가합니다.

         InitNotificationsAsync();

    이제 페이지가 로드될 때마다 등록이 요청됩니다. 등록 상태가 유지되도록 앱에서 주기적으로 등록할 수도 있습니다.

4.  Visual Studio에서 Package.appxmanifest 파일을 열고 **응용 프로그램 UI** 탭에서 **알림 가능**이 **예**로 설정되어 있는지 확인합니다. 파일을 저장합니다.

  ![][1]

  이제 앱에서 알림 메시지를 표시할 수 있습니다. 

푸시 알림을 전송하도록 서버 업데이트
------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

앱에서 푸시 알림 테스트
-----------------------

1.  Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2.  앱에서 **Insert a TodoItem**에 텍스트를 입력하고 **Save**를 클릭합니다.

  ![][2]

  삽입 작업이 완료되면 앱이 WNS에서 푸시 알림을 받습니다.

  ![][3]

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

-   [데이터 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data)
  <br/>.Net 런타임 모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보십시오.

-   [인증 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users)
  <br/>.Net 런타임 모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아보십시오.

-   [모바일 서비스 서버 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

-   [모바일 서비스 .NET 방법 개념 참조](/ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
  <br/>.NET과 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보십시오.


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png