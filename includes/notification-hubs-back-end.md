이 섹션에서는 알림을 보내는 두 가지 방법을 보여 줍니다.

* [콘솔 응용 프로그램에서 보냄](#console)
* [모바일 서비스에서 보냄](#mobile-services)

두 백엔드 모두 Windows 스토어와 iOS 장치에 알림을 보냅니다. [알림 허브 REST 인터페이스][1]를 사용하여 아무 백엔드에서 알림을 보낼 수 있습니다.

<h3><a name="console"></a>C#으로 콘솔 응용 프로그램에서 알림을 보내려면</h3>


[알림 허브 시작](/ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/)을 완료했을 때 콘솔 응용 프로그램을 만들었으면 1~3단계를 건너뛰십시오.

1.  Visual Studio에서 다음과 같이 새로운 Visual C# 콘솔 응용 프로그램을 만듭니다.
    
       ![][13]

2.  Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로
    클릭한 후 콘솔 창에서 다음을 입력하고 **Enter** 키를 누릅니다.
    
        Install-Package WindowsAzure.ServiceBus
    
    이 코드는 [WindowsAzure.ServiceBus NuGet 패키지][2]를 사용하여 Azure 서비스 버스 SDK에 참조를 추가합니다.

3.  Program.cs 파일을 열고 다음 `using` 문을 추가합니다.
    
        using Microsoft.ServiceBus.Notifications;

4.  `Program` 클래스에서 다음 메서드를 추가하거나 이미 있으면 바꿉니다.
    
        private static async void SendNotificationAsync()
         {
             // 알림 허브를 정의합니다.
             NotificationHubClient hub = 
                 NotificationHubClient.CreateClientFromConnectionString(
                     "<connection  string with full access>", "<hub  name>");
        	
             // 뉴스 속보 범주 배열을 만듭니다.
             var categories = new string[] { "World", "Politics", "Business", 
                 "Technology", "Science", "Sports"};
        	
             foreach (var category in categories)
             {
                 try
                 {
                     // Windows 스토어 알림을 정의합니다.
                     var wnsToast = "<toast><visual><binding  template=\"ToastText01\">" 
                         + "<text  id=\"1\">Breaking " + category + " News!" 
                         + "</text></binding></visual></toast>";
                     // 템플릿을 사용하여 WNS 알림을 보냅니다.            
                     await hub.SendWindowsNativeNotificationAsync(wnsToast, category);
        
                     // Windows Phone 알림을 정의합니다.
                     var mpnsToast =
                         "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                         "<wp :Notification xmlns:wp=\"WPNotification\">" +
                             "<wp :Toast>" +
                                 "<wp :Text1>Breaking " + category + " News!</wp:Text1>" +
                             "</wp:Toast> " +
                         "</wp:Notification>";
                     // 템플릿을 사용하여 MPNS 알림을 보냅니다.            
                     await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);
        
                     // iOS 경고를 정의합니다.
                     var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                     // 템플릿을 사용하여 APNS 알림을 보냅니다.
                     await hub.SendAppleNativeNotificationAsync(alert, category);
                 }
                 catch (ArgumentException)
                 {
                     // 알림 허브가 iOS, Windows 스토어 또는 Windows Phone 플랫폼에 
                     // 등록되어 있지 않으면 예외가 발생합니다. 
                 }
            }
         }
    
    이 코드는 문자열 배열에 있는 6개의 각 태그에 대한 알림을 Windows 스토어, Windows Phone 및 iOS 장치에 보냅니다. 태그를 사용하면 등록된 범주의 알림만 장치에서 받습니다.
	<div class="dev-callout">

    **참고**

    이 백엔드 코드는 Windows 스토어, Windows Phone 및 iOS 클라이언트를 지원합니다. 알림 허브가 특정 클라이언트 플랫폼에 맞게 아직 구성되어 있지 않으면 전송 메서드에서 오류 응답을 반환합니다.
    </div>
<p></p>
5.  위 코드에서 `<hub  name>`과 `<connection string with full access>` 자리 표시자를 알림 허브 이름과 앞서 얻었던 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿉니다.

6.  **Main** 메서드에 다음 코드를 추가합니다.
    
          SendNotificationAsync();
          Console.ReadLine();

이제 [앱 실행 및 알림 생성](#test-app)을 계속할 수 있습니다.

### <a name="mobile-services"></a>모바일 서비스에서 알림을 보내려면

모바일 서비스를 사용하여 알림을 보내려면 다음을 수행하십시오.

1.  [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started/#create-new-service) 자습서를 완료하여 모바일 서비스를 만듭니다.

2.  [Azure 관리 포털][3]에 로그온하여 모바일 서비스를 클릭한 후 해당 모바일 서비스를 클릭합니다.

3.  **Scheduler** 탭을 클릭한 후 **Create**를 클릭합니다.
    
       ![][15]

4.  **Create new job**에서 이름을 입력하고 **On demand**를 선택한 후 허용할 확인란을 클릭합니다.
    
       ![][16]

5.  작업이 만들어지면 만든 작업 이름을 클릭한 후 **Script** 탭에서 다음 스크립트를 예약된 작업 함수에 삽입합니다.
    
        var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService(
                 '<hub  name>', 
                 '<connection  string with full access>');
        
        	 // 뉴스 속보 범주 배열을 만듭니다.
             var categories = ['World', 'Politics', 'Business', 'Technology', 'Science', 'Sports'];
             for (var i = 0; i < categories.length; i++) {
                 // WNS 알림을 보냅니다.
                 notificationHubService.wns.sendToastText01(categories[i], {
                     text1: 'Breaking ' + categories[i] + ' News!'
                 }, sendComplete);
                 // MPNS 알림을 보냅니다.
                 notificationHubService.mpns.sendToast(categories[i], {
                     text1: 'Breaking ' + categories[i] + ' News!'
                 }, sendComplete);
                 // APNS 알림을 보냅니다.
                 notificationHubService.apns.send(categories[i], {
                     alert: 'Breaking ' + categories[i] + ' News!'
                 }, sendComplete);
             }
    
    이 코드는 문자열 배열에 있는 6개의 각 태그에 대한 알림을 Windows 스토어, Windows Phone 및 iOS 장치에 보냅니다. 태그를 사용하면 등록된 범주의 알림만 장치에서 받습니다.

6.  위 코드에서 `<hub  name>`과 `<connection string with full access>` 자리 표시자를 알림 허브 이름과 앞서 얻었던 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿉니다.

7.  예약된 작업 함수 뒤에 다음 도우미 함수를 추가한 후 **Save**를 클릭합니다.
    
         function sendComplete(error) {
    
        if (error) {
                 // iOS, Windows 스토어 또는 Windows Phone 플랫폼에 등록된 
                 // 장치가 없으면 예외가 발생합니다. 대신 템플릿 알림을 
                 // 사용하십시오.
                 //console.warn("Notification failed:" + error);
             }
         }

	
	<strong>참고</strong>

	이 코드는 Windows 스토어, Windows Phone 및 iOS 클라이언트를 지원합니다. 특정 플랫폼이 등록되어 있지 않으면 전송 메서드에서 오류 응답을 반환합니다. 이를 방지하려면 여러 플랫폼에 단일 알림을 보내도록 템플릿을 등록하십시오. 예를 보려면 [알림 허브를 사용하여 지역 뉴스 속보 방송](/en-us/manage/services/notification-hubs/breaking-news-localized-dotnet/)을 참조하십시오.



이제 [앱 실행 및 알림 생성](#test-app)을 계속할 수 있습니다.

<!-- Anchors -->

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png
<!-- URLs. -->



[1]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn223264.aspx
[2]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[3]: https://manage.windowsazure.com/