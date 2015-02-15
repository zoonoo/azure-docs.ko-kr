
이 섹션에서는 .NET 콘솔 앱 및 다른 위치에서 알림을 보내는 방법을 보여 줍니다.
모바일 서비스를 사용하는 경우 [푸시 시작](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/) 자습서를 참조하세요. Java 또는 PHP를 사용하려는 경우 [Java/PHP에서 알림 허브를 사용하는 방법](/ko-kr/documentation/articles/notification-hubs-java-backend-how-to/)을 참조하세요. [알림 허브 REST 인터페이스]를 사용하여 아무 백 엔드에서나 알림을 보낼 수 있습니다.

다음 코드는 Windows 스토어, Windows Phone, iOS 및 Android 장치에 알림을 보냅니다. 

[알림 허브 시작][get-started].을 완료했을 때 콘솔 응용 프로그램을 만들었으면 1~3단계를 건너뛰세요.

1. Visual Studio에서 다음과 같이 새로운 Visual C# 콘솔 응용 프로그램을 만듭니다. 

  	![][13]

2. Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭한 후 콘솔 창에서 다음을 입력하고 **Enter** 키를 누릅니다.

        Install-Package WindowsAzure.ServiceBus
 	
	이 코드는 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 패키지</a>를 사용하여 Azure 서비스 버스 SDK에 참조를 추가합니다. 

3. Program.cs 파일을 열고 다음 `using` 문을 추가합니다.

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
	
	> [AZURE.NOTE] 이 백 엔드 코드는 Windows 스토어, Windows Phone, iOS 및 Android 클라이언트를 지원합니다. 알림 허브가 특정 클라이언트 플랫폼에 맞게 아직 구성되어 있지 않으면 전송 메서드에서 오류 응답을 반환합니다. 

6. 위 코드에서 `<hub name>`과 `<connection string with full access>` 자리 표시자를 알림 허브 이름과 앞서 얻었던 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿉니다.

7. **Main** 메서드에 다음 코드를 추가합니다.

         SendNotificationAsync();
		 Console.ReadLine();

<!-- Anchors -->
[콘솔 앱에서]: #console
[모바일 서비스에서]: #mobile-services
[앱 실행 및 알림 생성]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: /ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[알림 허브를 사용하여 사용자에게 알림 보내기]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[모바일 서비스 시작](영문): /ko-kr/develop/mobile/tutorials/get-started/#create-new-service
[Azure 관리 포털]: https://manage.windowsazure.com/
[wns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[알림 허브 지침]: http://msdn.microsoft.com/ko-kr/library/jj927170.aspx
[Windows 스토어용 알림 허브 방법]: http://msdn.microsoft.com/ko-kr/library/jj927172.aspx
[알림 허브 REST 인터페이스]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn223264.aspx

<!--HONumber=42-->
