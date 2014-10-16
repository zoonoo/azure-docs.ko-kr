이 섹션에서는 .NET 콘솔 앱 및 기타에서 알림을 보내는 방법을 보여 줍니다.
모바일 서비스를 사용하는 경우 [푸시 시작][] 자습서를 참조하세요. Java 또는 PHP를 사용하려는 경우 [Java/PHP에서 알림 허브를 사용하는 방법][]을 참조하세요. [알림 허브 REST 인터페이스][]를 사용하여 아무 백엔드에서 알림을 보낼 수 있습니다.

다음 코드는 Windows 스토어, Windows Phone, iOS 및 Android 장치에 알림을 보냅니다.

[알림 허브 시작][]을 완료했을 때 콘솔 응용 프로그램을 만들었으면 1~3단계를 건너뛰세요.

1.  Visual Studio에서 다음과 같이 새로운 Visual C\# 콘솔 응용 프로그램을 만듭니다.

    ![][]

2.  Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭한 후 콘솔 창에서 다음을 입력하고 **Enter** 키를 누릅니다.

        Install-Package WindowsAzure.ServiceBus

    이 코드는 [WindowsAzure.ServiceBus NuGet 패키지][]를 사용하여 Azure 서비스 버스 SDK에 참조를 추가합니다.

3.  Program.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

4.  `Program` 클래스에서 다음 메서드를 추가하거나 이미 있으면 바꿉니다.

        private static async void SendNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
                "Technology", "Science", "Sports"};

            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

                    // Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
         }

    이 코드는 문자열 배열에 있는 6개의 각 태그에 대한 알림을 Windows 스토어, Windows Phone 및 iOS 장치에 보냅니다. 태그를 사용하면 등록된 범주의 알림만 장치에서 받습니다.

    <div class="dev-callout"><strong>참고</strong> 
    <p>이 백엔드 코드는 Windows 스토어, Windows Phone, iOS 및 Android 클라이언트를 지원합니다. 알림 허브가 특정 클라이언트 플랫폼에 맞게 아직 구성되어 있지 않으면 전송 메서드에서 오류 응답을 반환합니다. </p>
</div>

5.  위의 코드에서 `<hub name>` 및 `<connection string with full access>` 자리 표시자를 알림 허브 이름과 앞에서 얻은 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다.

6.  **Main** 메서드에 다음 코드를 추가합니다.

         SendNotificationAsync();
         Console.ReadLine();



  [푸시 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Java/PHP에서 알림 허브를 사용하는 방법]: /ko-kr/documentation/articles/notification-hubs-java-backend-how-to/
  [알림 허브 REST 인터페이스]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
  [알림 허브 시작]: /ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  []: ./media/notification-hubs-back-end/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet 패키지]: http://nuget.org/packages/WindowsAzure.ServiceBus/
