
이 섹션에서는 .NET 콘솔 앱에서 태그가 지정된 템플릿 알림으로 속보를 보내는 방법을 보여 줍니다.

모바일 앱을 사용하는 경우 [모바일 앱에 대한 푸시 알림 추가](../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) 자습서를 참조하고 맨 위에 있는 플랫폼을 선택합니다.

Java 또는 PHP를 사용하려는 경우 [Java/PHP에서 알림 허브를 사용하는 방법](../articles/notification-hubs/notification-hubs-java-backend-how-to.md)을 참조하세요. [알림 허브 REST 인터페이스](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)를 사용하여 모든 백엔드에서 알림을 보낼 수 있습니다.

[알림 허브 시작][get-started]을 완료했을 때 알림을 보내는 콘솔 앱을 만들었으면 1-3단계를 건너뜁니다.

1. Visual Studio에서 다음과 같이 새로운 Visual C# 콘솔 응용프로그램을 만듭니다. 

   	![][13]

2. Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭한 후 콘솔 창에서 다음을 입력하고 **Enter** 키를 누릅니다.

        Install-Package Microsoft.Azure.NotificationHubs
 	
	그러면 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet 패키지</a>를 사용하는 Azure 알림 허브 SDK에 대한 참조가 추가됩니다.

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

6. 위의 코드에서 `<hub name>` 및 `<connection string with full access>` 자리 표시자를 알림 허브 이름과 알림 허브의 대시보드에서 얻은 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다.

7. **Main** 메서드에 다음 코드를 추가합니다.

         SendTemplateNotificationAsync();
		 Console.ReadLine();

8. 콘솔 앱을 시작합니다.

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[Use Notification Hubs to send notifications to users]: ../articles/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!---HONumber=AcomDC_0330_2016-->