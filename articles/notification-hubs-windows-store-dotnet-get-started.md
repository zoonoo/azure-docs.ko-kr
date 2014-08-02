<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

알림 허브 시작
==============

[Windows 스토어 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows 스토어 C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

이 항목은 Azure 알림 허브를 사용하여 Windows 스토어 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 WNS(Windows 푸시 알림 서비스)를 사용하여 푸시 알림을 받는 새 Windows 스토어 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [푸시 알림에 대해 앱 등록](#register)
2.  [알림 허브 구성](#configure-hub)
3.  [알림 허브에 앱 연결](#connecting-app)
4.  [백 엔드에서 알림 보내기](#send)

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 알림 허브를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다. 이 자습서를 이용하려면 다음 사항이 필요합니다.

-   Microsoft Visual Studio 2012 Express for Windows 8
-   활성 Windows 스토어 계정

이 자습서를 완료해야 다른 모든 Windows 스토어 앱용 알림 허브 자습서를 진행할 수 있습니다.

**참고**

이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F)을 참조하십시오.

앱 등록Windows 스토어에 앱 등록
-------------------------------

모바일 서비스에서 Windows 스토어 앱으로 푸시 알림을 보내려면 앱을 Windows 스토어에 제출해야 합니다. 그런 다음 WNS와 통합되도록 알림 허브를 구성해야 합니다.

1.  앱을 아직 등록하지 않은 경우 Windows 스토어 앱용 개발자 센터의 [앱 제출 페이지](http://go.microsoft.com/fwlink/p/?LinkID=266582)로 이동하여 Microsoft 계정으로 로그온한 후 **앱 이름**을 클릭합니다.

	![][0]

2.  **앱 이름**에 앱의 이름을 입력하고 **앱 이름 예약**을 클릭한 후 **저장**을 클릭합니다.

	![][1]

	이렇게 하면 앱을 새로 Windows 스토어에 등록하게 됩니다.

3.  Visual Studio 2012 Express for Windows 8에서 **새 응용 프로그램** 템플릿을 사용하여 새 Visual C\# Windows 스토어 프로젝트를 만듭니다.

	![][2]

4.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어**를 클릭한 후 **응용 프로그램을 스토어에 연결...**을 클릭합니다.

	![][3]

	**응용 프로그램을 Windows 스토어에 연결** 마법사가 나타납니다.

5.  마법사에서 **로그인**을 클릭한 후 Microsoft 계정으로 로그인합니다.

6.  2단계에서 등록한 앱을 클릭하고 **다음**을 클릭한 후 **연결**을 클릭합니다.

	![][4]

	이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.    

7.  새 앱의 Windows 개발자 센터 페이지로 돌아가서 **서비스**를 클릭합니다.

	![][5] 

8.  **서비스** 페이지에서 **Azure 모바일 서비스** 아래의 **Live 서비스 사이트**를 클릭합니다.

	![][17]

9.  **Authenticating your service**를 클릭하고 **Client secret** 및 **Package security identifier (SID)**를 적어둡니다.

	![][6]

	<div class="dev-callout"\><b>보안 정보</b>

    <p>클라이언트 암호 및 패키지 SID는 중요한 보안 자격 증명입니다. 다른 사람과 공유하지 말고 앱과 함께 분산하지 마십시오.</p>
    </div>

알림 허브 구성알림 허브 구성
----------------------------

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하고 화면 맨 아래에 있는 **새로 만들기**를 클릭합니다.

2.  **앱 서비스**, **Service Bus**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.

	![][7]

3.  알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **Create a new Notification Hub**를 클릭합니다.

	![][8]

4.  방금 만든 네임스페이스(일반적으로 ***알림 허브 이름*-ns**)를 클릭한 후 맨 위에 있는 **구성** 탭을 클릭합니다.

	![][9]

5.  맨 위에 있는 **알림 허브** 탭을 선택한 후 방금 만든 알림 허브를 클릭합니다.

	![][10]

6.  맨 위에 있는 **구성** 탭을 선택하고 이전 섹션의 WNS에서 얻은 **클라이언트 암호** 및 **패키지 SID** 값을 입력한 후 **저장**을 클릭합니다.

	![][11]

7.  맨 위에 있는 **대시보드** 탭을 선택한 후 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

	![][12]

이제 알림 허브가 WNS와 작동하도록 구성되었으며 앱을 등록하고 알림을 보내기 위한 연결 문자열이 있습니다.

앱 연결알림 허브에 앱 연결
--------------------------

1.  [WindowsAzure.Messaging.Managed NuGet 패키지](http://nuget.org/packages/WindowsAzure.Messaging.Managed/)를 사용하여 Windows 스토어용 Azure 메시징 라이브러리에 대한 참조를 추가합니다. Visual Studio 주 메뉴에서 **도구**를 클릭하고 **라이브러리 패키지 관리자**를 클릭한 다음 **패키지 관리자 콘솔**을 클릭합니다. 그런 다음 콘솔 창에서 다음을 입력하고

         Install-Package WindowsAzure.Messaging.Managed

    **Enter** 키를 누릅니다.

2.  App.xaml.cs 파일을 열고 다음 `using` 문을 추가합니다.

         using Windows.Networking.PushNotifications;
         using Microsoft.WindowsAzure.Messaging;
         using Windows.UI.Popups;

3.  App.xaml.cs의 `App` 클래스에 다음 코드를 추가합니다. "hub name" 자리 표시자를 포털의 **알림 허브** 탭에 나타나는 알림 허브의 이름으로 바꿔야 합니다(예: 이전 예제의 **mynotificationhub2**).

         private async void InitNotificationsAsync()
         {
             var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
                
             var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
             var result = await hub.RegisterNativeAsync(channel.Uri);
                
             // Displays the registration ID so you know it was successful
             if (result.RegistrationId != null)
             {
                 var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
             }

         }

    허브 이름과 이전 섹션에서 얻은 **DefaultListenSharedAccessSignature**라는 연결 문자열을 삽입해야 합니다. 이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 알림 허브에 등록합니다.

4.  App.xaml.cs에서 **OnLaunched** 이벤트 처리기의 맨 위에 다음과 같은 새 **InitNotificationsAsync** 메서드 호출을 추가합니다.

         InitNotificationsAsync();

    이 코드는 응용 프로그램이 시작될 때마다 ChannelURI가 알림 허브에 등록되도록 보장합니다.

5.  **F5** 키를 눌러 앱을 실행합니다. 등록 키가 포함된 팝업 대화 상자가 표시됩니다.

	![][19]

알림 보내기백 엔드에서 알림 보내기
----------------------------------

[REST 인터페이스](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx)를 사용하여 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 응용 프로그램 및 노드 스크립트를 사용하는 모바일 서비스를 통해 알림을 보냅니다.

.NET 앱을 사용하여 알림을 보내려면

1.  Visual Studio에 이전 솔루션을 계속 열어둔 상태로 솔루션 탐색기에서 **Package.appxmanifest**를 두 번 클릭하여 Visual Studio 편집기에서 엽니다.

2.  **모든 이미지 자산**까지 아래로 스크롤하고 **배지 로고**를 클릭합니다. **알림**에서 **알림 가능**을 **예**로 설정합니다.

	![][18]

	**파일** 메뉴에서 **모두 저장**을 클릭합니다.

3.  이제 새 Visual C\# 콘솔 응용 프로그램을 만듭니다.

	![][13]

4.  [WindowsAzure.ServiceBus NuGet 패키지](http://nuget.org/packages/WindowsAzure.ServiceBus/)를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다. Visual Studio 주 메뉴에서 **도구**를 클릭하고 **라이브러리 패키지 관리자**를 클릭한 다음 **패키지 관리자 콘솔**을 클릭합니다. 그런 다음 콘솔 창에서 다음을 입력하고

         Install-Package WindowsAzure.ServiceBus

    **Enter** 키를 누릅니다.

5.  Program.cs 파일을 열고 다음 `using` 문을 추가합니다.

         using Microsoft.ServiceBus.Notifications;

6.  `Program` 클래스에 다음 메서드를 추가합니다. "hub name" 자리 표시자를 포털의 **알림 허브** 탭에 나타나는 알림 허브의 이름으로 바꿔야 합니다.

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
             await hub.SendWindowsNativeNotificationAsync(toast);
         }

	허브 이름과 "알림 허브 구성" 섹션에서 얻은 **DefaultFullSharedAccessSignature**라는 연결 문자열을 삽입해야 합니다. 이는 **수신 대기** 권한이 아니라 **모든** 권한을 가진 연결 문자열입니다.

7.  그런 다음 `Main` 메서드에 다음 줄을 추가합니다.

          SendNotificationAsync();
          Console.ReadLine();

8.  **F5** 키를 눌러 앱을 실행합니다. 그러면 알림 메시지가 수신되어야 합니다.

	![][14]

MSDN의 [알림 카탈로그](http://msdn.microsoft.com/en-us/library/windows/apps/hh761494.aspx), [타일 카탈로그](http://msdn.microsoft.com/en-us/library/windows/apps/hh761491.aspx) 및 [배지 개요](http://msdn.microsoft.com/en-us/library/windows/apps/hh779719.aspx)에서 가능한 모든 페이로드를 찾을 수 있습니다.

모바일 서비스를 사용하여 알림을 보내려면 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started/#create-new-service)(영문)을 따른 후 다음을 수행하십시오.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하고 모바일 서비스를 클릭합니다.

2.  맨 위에 있는 **스케줄러** 탭을 선택합니다.

	![][15]

3.  새 예약된 작업을 만들고 이름을 삽입한 후 **요청 시**를 클릭합니다.

	![][16]

4.  작업이 만들어졌으면 작업 이름을 클릭합니다. 그런 다음 위쪽 막대에서 **스크립트** 탭을 클릭합니다.

5.  스케줄러 함수 내에 다음 스크립트를 삽입합니다. 자리 표시자를 알림 허브 이름과 앞에서 얻은 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다. 작업을 마쳤으면 아래쪽 막대에서 **저장**을 클릭합니다.

        var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
         notificationHubService.wns.sendToastText01(
             null,
             {
                 text1: 'Hello from Mobile Services!!!'
             },
             function(error) {
                 if (!error) {
                     console.warn("Notification successful");
                 }
         });

6.  아래쪽 막대에서 **한 번 실행**을 클릭합니다. 그러면 알림 메시지가 수신되어야 합니다.

다음 단계
---------

이 간단한 예제에서는 모든 Windows 장치로 브로드캐스트 알림을 보냈습니다. 특정 사용자를 대상으로 하려면 [알림 허브를 사용하여 사용자에게 알림 푸시](/en-us/manage/services/notification-hubs/notify-users-aspnet)(영문) 자습서를 참조하십시오. 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기](/en-us/manage/services/notification-hubs/breaking-news-dotnet)(영문)를 참조하십시오. 알림 허브 사용 방법에 대해 자세히 알아보려면 [알림 허브 지침](http://msdn.microsoft.com/en-us/library/jj927170.aspx) 및 [Windows 스토어용 알림 허브 방법](http://msdn.microsoft.com/en-us/library/jj927172.aspx)을 참조하십시오.

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png