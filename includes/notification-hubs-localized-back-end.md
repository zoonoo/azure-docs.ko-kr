

백엔드 앱에서 이제 네이티브 페이로드 대신 템플릿 알림을 보내도록 전환해야 합니다. 이렇게 하면 다른 플랫폼에 여러 페이로드를 보내지 않아도 되므로 백엔드 코드가 간단해집니다.

템플릿 알림을 보낼 때 속성 집합만 제공하면 되지만 여기서는 최신 뉴스의 지역화된 버전을 포함하는 속성 집합을 보냅니다. 예:

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


이 섹션에서는 알림을 보내는 두 가지 방법을 보여 줍니다.

- 콘솔 응용 프로그램 사용
- 모바일 서비스 스크립트 사용

지원되는 모든 장치에 백엔드가 브로드캐스트할 수 있으므로 포함된 코드는 Windows 스토어와 iOS 장치 둘 다에 브로드캐스트합니다.



## C# 콘솔 응용 프로그램을 사용하여 알림을 보내려면 ##

단일 템플릿 알림을 보내 *SendNotificationAsync* 메서드를 간단히 수정합니다.

	var hub = NotificationHubClient.CreateClientFromConnectionString("<connection string>", "<hub name>");
    var notification = new Dictionary<string, string>() {
							{"News_English", "World News in English!"},
                            {"News_French", "World News in French!"},
                            {"News_Mandarin", "World News in Mandarin!"}};
    await hub.SendTemplateNotificationAsync(notification, "World");

이 간단한 호출은 알림 허브에서 올바른 네이티브 페이로드를 생성하여 특정 태그에 가입된 모든 장치에 전달할 때 플랫폼과 상관없이 올바르게 지역화된 뉴스를 **모든** 장치에 전달합니다.

### 모바일 서비스

모바일 서비스 스케줄러에서 스크립트를 다음으로 덮어쓰세요.

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
	
이 경우 다른 로캘 및 플랫폼에 여러 알림을 보낼 필요가 없습니다.

<!---HONumber=July15_HO3-->