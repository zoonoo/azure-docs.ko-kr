



템플릿 알림을 보낼 때 속성 집합만 제공하면 되지만 여기서는 최신 뉴스의 지역화된 버전을 포함하는 속성 집합을 보냅니다. 예:

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


이 섹션에서는 콘솔 앱을 사용하여 알림을 보내는 방법을 보여 줍니다.

지원되는 모든 장치에 백엔드가 브로드캐스트할 수 있으므로 포함된 코드는 Windows 스토어와 iOS 장치 둘 다에 브로드캐스트합니다.

### <a name="to-send-notifications-using-a-c-console-app"></a>C# 콘솔 응용 프로그램을 사용하여 알림을 보내려면
이전에 다음 코드를 사용하여 만든 콘솔 앱에서 `SendTemplateNotificationAsync` 메서드를 수정합니다. 이 경우 다른 로캘 및 플랫폼에 여러 알림을 보낼 필요가 없습니다.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

                    // Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


이 간단한 호출은 알림 허브에서 네이티브 페이로드를 생성하여 특정 태그에 가입된 모든 장치에 전달할 때 플랫폼과 상관없이 올바르게 지역화된 뉴스를 **모든** 장치에 전달합니다.

### <a name="sending-the-notification-with-mobile-services"></a>모바일 서비스로 알림 보내기
모바일 서비스 스케줄러에서 다음 스크립트를 사용할 수 있습니다.

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });


