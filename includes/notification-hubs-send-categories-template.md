
이 섹션에서는 .NET 콘솔 앱에서 태그가 지정된 템플릿 알림으로 속보를 보내는 방법을 보여 줍니다.

Mobile Apps를 사용하는 경우 [Mobile Apps에 대한 푸시 알림 추가] 자습서를 참조하고 맨 위에 있는 플랫폼을 선택합니다.

Java 또는 PHP를 사용하려는 경우 [Java/PHP에서 Notification Hubs를 사용하는 방법]을 참조하세요. [Notification Hubs REST 인터페이스]를 사용하여 아무 백 엔드에서나 알림을 보낼 수 있습니다.

[Notification Hubs 시작]을 완료했을 때 알림을 보내는 콘솔 앱을 만들었으면 1-3단계를 건너뜁니다.

1. Visual Studio에서 다음과 같이 새로운 Visual C# 콘솔 응용프로그램을 만듭니다.
   
       ![][13]
2. Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭한 후 콘솔 창에서 다음을 입력하고 **Enter** 키를 누릅니다.
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    그러면 [Microsoft.Azure.Notification Hubs NuGet 패키지]를 사용하는 Azure 알림 허브 SDK에 대한 참조가 추가됩니다.
3. Program.cs 파일을 열고 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.NotificationHubs;
4. `Program` 클래스에서 다음 메서드를 추가하거나 이미 있으면 바꿉니다.
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Sending the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    이 코드는 문자열 배열에 있는 6개의 각 태그에 대한 템플릿 알림을 보냅니다. 태그를 사용하면 등록된 범주의 알림만 장치에서 받습니다.
5. 위의 코드에서 `<hub name>` 및 `<connection string with full access>` 자리 표시자를 알림 허브 이름과 알림 허브의 대시보드에서 얻은 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다.
6. **Main** 메서드에 다음 코드를 추가합니다.
   
         SendTemplateNotificationAsync();
         Console.ReadLine();
7. 콘솔 앱을 시작합니다.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Notification Hubs 시작]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST 인터페이스]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Mobile Apps에 대한 푸시 알림 추가]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[Java/PHP에서 Notification Hubs를 사용하는 방법]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet 패키지]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
