이 섹션에서는 새 항목이 추가될 때마다 푸시 알림을 보내도록 기존 모바일 앱 백 엔드 프로젝트의 코드를 업데이트합니다. 클라이언트는 템플릿 등록을 사용하여 푸시 알림에 등록되므로 모든 클라이언트 플랫폼에 단일 푸시 알림 메시지를 보낼 수 있습니다. 각 클라이언트 템플릿 등록에는 *messageParam* 매개 변수가 포함됩니다. 알림이 전송되면 삽입되는 항목의 텍스트인 문자열이 *messageParam*에 포함됩니다. 알림 허브에서 템플릿을 사용하는 방법에 대한 자세한 내용은 [템플릿](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)을 참조하세요.

백 엔드 프로젝트 type&mdash([.NET 백 엔드](#dotnet) 또는 [Node.js 백 엔드](#nodejs))와 일치하는 아래의 절차를 선택합니다.

### <a name="dotnet"></a>.NET 백 엔드 프로젝트
1. Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭한 후 `Microsoft.Azure.NotificationHubs`를 검색한 다음 **설치**를 클릭합니다. 백 엔드에서 알림을 보내기 위한 알림 허브 라이브러리를 설치합니다.
2. 서버 프로젝트에서 **컨트롤러** > **TodoItemController.cs**를 열고 다음 using 문을 추가합니다.
   
        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. **PostTodoItem** 메서드에서 **InsertAsync**에 대한 호출 뒤에 다음 코드를 추가합니다.
   
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
   
    이는 새 항목이 삽입된 경우 item.Text가 포함된 템플릿 알림을 보냅니다.
4. 서버 프로젝트를 다시 게시합니다.

### <a name="nodejs"></a>Node.js 백 엔드 프로젝트
1. 아직 수행하지 않은 경우 [빠른 시작 백 엔드 프로젝트를 다운로드](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)하거나 [Azure 포털에서 온라인 편집기](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)를 사용합니다.
2. todoitem.js의 기존 코드를 다음으로 바꿉니다.
   
        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
   
        var table = azureMobileApps.table();
   
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
   
        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  
   
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });
   
        module.exports = table;  
   
    이는 새 항목이 삽입된 경우 item.Text가 포함된 템플릿 알림을 보냅니다.
3. 로컬 컴퓨터에서 파일을 편집할 때 서버 프로젝트를 다시 게시합니다.

<!---HONumber=AcomDC_0629_2016-->