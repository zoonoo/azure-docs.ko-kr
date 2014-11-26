<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# 알림 허브 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows 범용">Windows 범용</a><a href="/ko-KR/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/ko-KR/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ko-KR/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ko-KR/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ko-KR/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-KR/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

이 항목에서는 Azure 알림 허브를 사용하여 Windows Phone 8 또는 Windows Phone 8.1 Silverlight 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. Windows Phone 8.1(비 Silverlight)을 대상으로 하는 경우 [Windows 범용][1] 버전을 참조하세요.
이 자습서에서는 MPNS(Microsoft 푸시 알림 서비스)를 사용하여 푸시 알림을 받는 새 Windows Phone 8 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

> [AZURE.NOTE] 알림 허브 Windows Phone SDK에서는 Windows Phone 8.1 Silverlight 앱에서의 WNS 사용을 지원하지 않습니다. Windows Phone 8.1 Silverlight 앱에서 WNS(MPNS 대신)를 사용하려면 [Windows 범용에 대해 시작][1]에 표시된 대로 WNS 자격 증명을 설정해야 합니다. 그런 다음 [사용자에게 알림][사용자에게 알림] 자습서에 표시된 대로 백 엔드에서 등록하거나 [알림 허브 REST API][알림 허브 REST API]를 사용할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 단계를 단계별로 안내합니다.

1.  [알림 허브 만들기][알림 허브 만들기]
2.  [알림 허브에 앱 연결][알림 허브에 앱 연결]
3.  [백 엔드에서 알림 보내기][백 엔드에서 알림 보내기]

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 알림 허브를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다. 이 자습서를 사용하려면 다음이 필요합니다.

-   [Visual Studio 2012 Express for Windows Phone][Visual Studio 2012 Express for Windows Phone] 이상 버전

이 자습서를 완료해야 다른 모든 Windows Phone 8 앱용 알림 허브 자습서를 진행할 수 있습니다.

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-KR/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-KR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

## <a name="configure-hub"></a>알림 허브 만들기

1.  [Azure 관리 포털][Azure 관리 포털]에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2.  **앱 서비스**, **서비스 버스**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.

    ![][0]

3.  알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **Create a new Notification Hub**를 클릭합니다.

    ![][2]

4.  방금 만든 네임스페이스(일반적으로 ***알림 허브 이름*-ns**)를 클릭한 후 맨 위에 있는 **구성** 탭을 클릭합니다.

    ![][3]

5.  맨 위에 있는 **알림 허브** 탭을 클릭한 후 방금 만든 알림 허브를 클릭합니다.

    ![][4]

6.  맨 아래에 있는 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

    ![][5]

7.  **구성** 탭을 클릭한 후 **Windows Phone 알림 설정** 섹션에서 **인증되지 않은 푸시 알림 사용** 확인란을 클릭합니다.

    ![][6]

이제 Windows Phone 8 앱을 등록하고 알림을 보내는 데 필요한 연결 문자열이 있습니다.

<div class="dev-callout"><b>참고</b>
        <p>이 자습서에서는 인증되지 않은 모드로 MPNS를 사용합니다. MPNS 인증되지 않은 모드에는 각 채널로 보낼 수 있는 알림에 대한 제한이 있습니다. 알림 허브는 <a href="http://msdn.microsoft.com/ko-KR/library/windowsphone/develop/ff941099(v=vs.105).aspx">MPNS 인증된 모드</a>를 지원합니다. <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

## <a name="connecting-app"></a>알림 허브에 앱 연결

1.  Visual Studio에서 새 Windows Phone 8 응용 프로그램을 만듭니다.

    ![][7]

    Visual Studio 2013 업데이트 2 이상에서는 대신 Windows Phone Silverlight 응용 프로그램을 만듭니다.

    ![][8]

2.  Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.

    그러면 NuGet 패키지 관리 대화 상자가 표시됩니다.

3.  `WindowsAzure.Messaging.Managed`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.

    ![][9]

    그러면 [WindowsAzure.Messaging.Managed NuGet 패키지][WindowsAzure.Messaging.Managed NuGet 패키지]를 사용하여 Windows용 Azure 메시징 라이브러리에 대한 참조가 다운로드, 설치 및 추가됩니다.

4.  App.xaml.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5.  App.xaml.cs의 **Application\_Launching** 메서드 맨 위에 있는 다음 코드에서

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    허브 이름과 이전 섹션에서 얻은 **DefaultListenSharedAccessSignature**라는 연결 문자열을 삽입해야 합니다.
    이 코드는 MPNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 알림 허브에 등록합니다. 또한 이 코드는 응용 프로그램이 시작될 때마다 ChannelURI가 알림 허브에 등록되도록 보장합니다.

    > [WACOM.NOTE]이 자습서에서는 알림 메시지를 장치로 보냅니다. 타일 알림을 보내는 경우 채널에서 **BindToShellTile** 메서드를 대신 호출해야 합니다. 알림 메시지와 타일 알림을 둘 다 지원하려면 **BindToShellTile** 및 **BindToShellToast**를 둘 다 호출합니다.

6.  솔루션 탐색기에서 **속성**을 확장하고 WMAppManifest.xml 파일을 연 후 **기능** 탭을 클릭하고 **ID\_CAP\_PUSH\_NOTIFICATION** 기능이 선택되어 있는지 확인합니다.

    ![][10]

    이제 앱이 푸시 알림을 받을 수 있습니다.

7.  F5 키를 눌러 앱을 실행합니다.

    등록 메시지가 표시됩니다.

## <a name="send"></a>백 엔드에서 알림 보내기

[REST 인터페이스][REST 인터페이스]를 사용하는 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 응용 프로그램을 사용하여 알림을 보냅니다. 알림 허브와 통합된 Azure 모바일 서비스 백 엔드에서 알림을 보내는 방법에 대한 예는 **모바일 서비스에서 푸시 알림 시작**([.NET 백 엔드][.NET 백 엔드] | [JavaScript 백 엔드][.NET 백 엔드])을 참조하세요. REST API를 사용하여 알림을 보내는 방법에 대한 예는 **Java/PHP에서 알림 허브를 사용하는 방법**([Java][Java] | [PHP][PHP])을 참조하세요.

1.  솔루션을 마우스 오른쪽 단추로 클릭하고 **추가**, **새 프로젝트...**를 차례로 선택한 후 **Visual C#**에서 **Windows**, **콘솔 응용 프로그램**, **확인**을 차례로 클릭합니다.

    ![][11]

    그러면 새 Visual C# 콘솔 응용 프로그램이 솔루션에 추가됩니다. 별도의 솔루션에서 이 작업을 수행할 수도 있습니다.

2.  마우스 오른쪽 단추를 클릭하고 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

    그러면 패키지 관리자 콘솔이 표시됩니다.

3.  콘솔 창에서 **기본 프로젝트**를 새 콘솔 응용 프로그램 프로젝트로 설정한 후 콘솔 창에서 다음 명령을 실행합니다.

        Install-Package WindowsAzure.ServiceBus

    이 명령은 [WindowsAzure.ServiceBus NuGet 패키지][WindowsAzure.ServiceBus NuGet 패키지]를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다.

4.  Program.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

5.  **Program** 클래스에 다음 메서드를 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    "hub name" 자리 표시자를 포털의 **알림 허브** 탭에 나타나는 알림 허브의 이름으로 바꿔야 합니다. 또한 연결 문자열 자리 표시자를 "알림 허브 구성" 섹션에서 얻은 **DefaultFullSharedAccessSignature**라는 연결 문자열로 바꿉니다.

    > [WACOM.NOTE]**수신 대기** 권한이 아니라 **모든** 권한을 가진 연결 문자열을 사용해야 합니다. 수신 대기 권한 문자열은 알림을 보낼 권한이 없습니다.

6.  그런 다음 Main 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
         Console.ReadLine();

7.  Windows Phone 에뮬레이터를 실행하고 앱을 닫은 상태에서 콘솔 응용 프로그램 프로젝트를 기본 시작 프로젝트로 설정한 후 F5 키를 눌러 앱을 실행합니다.

    알림 메시지가 수신됩니다. 알림 배너를 누르면 앱이 로드됩니다.

MSDN의 [알림 카탈로그][알림 카탈로그] 및 [타일 카탈로그][타일 카탈로그] 항목에서 가능한 모든 페이로드를 찾을 수 있습니다.

## <a name="next-steps"> </a>다음 단계

이 간단한 예제에서는 모든 Windows Phone 8 장치로 알림을 브로드캐스트했습니다. 특정 사용자를 대상으로 하려면 [알림 허브를 사용하여 사용자에게 알림 푸시][알림 허브를 사용하여 사용자에게 알림 푸시](영문) 자습서를 참조하십시오. 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기][알림 허브를 사용하여 뉴스 속보 보내기](영문)를 참조하십시오. [알림 허브 지침][알림 허브 지침]에서 알림 허브 사용 방법을 자세히 알아보십시오.

 
 


  [Windows 범용]: /ko-KR/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows 범용"
  [1]: /ko-KR/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [사용자에게 알림]: /ko-KR/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
  [알림 허브 REST API]: http://msdn.microsoft.com/ko-KR/library/dn223264.aspx
  [알림 허브 만들기]: #configure-hub
  [알림 허브에 앱 연결]: #connecting-app
  [백 엔드에서 알림 보내기]: #send
  [Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
  [6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
  [7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
  [8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
  [9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
  [WindowsAzure.Messaging.Managed NuGet 패키지]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [10]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
  [REST 인터페이스]: http://msdn.microsoft.com/ko-KR/library/windowsazure/dn223264.aspx
  [.NET 백 엔드]: /ko-KR/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Java]: /ko-KR/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ko-KR/documentation/articles/notification-hubs-php-backend-how-to/
  [11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet 패키지]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [알림 카탈로그]: http://msdn.microsoft.com/ko-KR/library/windowsphone/develop/jj662938(v=vs.105).aspx
  [타일 카탈로그]: http://msdn.microsoft.com/ko-KR/library/windowsphone/develop/hh202948(v=vs.105).aspx
  [알림 허브를 사용하여 사용자에게 알림 푸시]: /ko-KR/manage/services/notification-hubs/notify-users-aspnet
  [알림 허브를 사용하여 뉴스 속보 보내기]: /ko-KR/manage/services/notification-hubs/breaking-news-dotnet
  [알림 허브 지침]: http://msdn.microsoft.com/ko-KR/library/jj927170.aspx
