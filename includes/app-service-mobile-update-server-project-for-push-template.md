
1. Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭한 후 `Microsoft.Azure.NotificationHubs`를 검색한 다음 **설치**를 클릭합니다. 백 엔드에서 알림을 보내기 위한 알림 허브 라이브러리를 설치합니다.

3. 서버 프로젝트에서 **컨트롤러** > **TodoItemController.cs**를 열고 다음 using 문을 추가합니다.

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. **PostTodoItem** 메서드에서 **InsertAsync**에 대한 호출 뒤에 다음 코드를 추가합니다.

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    이 코드는 알림 허브가 "messageParam"을 포함하는 모든 템플릿 등록에 템플릿 알림을 보내도록 지시합니다. 문자열은 "messageParam"을 사용하여 등록된 각 PNS에서 messageParam 대신 삽입됩니다. 이를 통해 APNS, GCM, WNS 또는 기타 모든 PNS에 알림을 보낼 수 있습니다.

	알림 허브를 사용하는 템플릿에 대한 자세한 내용은 [템플릿](notification-hubs-templates.md)을 참조하세요.

<!---HONumber=AcomDC_1203_2015-->