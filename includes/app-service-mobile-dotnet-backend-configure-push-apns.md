
+ **.NET 백 엔드(C#)**:  

	백 엔드의 Visual Studio 프로젝트에서 **Controllers** > **TodoItemController.cs**를 엽니다. 파일 맨 위에 다음 `using` 문을 추가합니다.


        using Microsoft.Azure.Mobile.Server.Config;
        using Microsoft.Azure.NotificationHubs;


	`PostTodoItem` 메서드를 다음 코드로 바꿉니다.

      
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
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

            // iOS payload
            var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendAppleNativeNotificationAsync(appleNotificationPayload);

                // Write the success result to the logs.
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                // Write the failure result to the logs.
                config.Services.GetTraceWriter()
                    .Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }


+ **Node.js 백 엔드**:
	
	todoitem.js 테이블 스크립트를 다음 코드로 바꿉니다.


        var azureMobileApps = require('azure-mobile-apps'),
            promises = require('azure-mobile-apps/src/utilities/promises'),
            logger = require('azure-mobile-apps/src/logger');
        
        var table = azureMobileApps.table();
        
        // When adding record, send a push notification via APNS
        // For this to work, you must have an APNS Hub already configured
        table.insert(function (context) {
            // For details of the Notification Hubs JavaScript SDK, 
            // see http://aka.ms/nodejshubs
            logger.info('Running TodoItem.insert');
            
			// Create a payload that contains the new item Text.
            var payload = "{"aps":{"alert":"" + context.item.text + ""}}";
            
            // Execute the insert.  The insert returns the results as a Promise,
            // Do the push as a post-execute action within the promise flow.
            return context.execute()
                .then(function (results) {
                    // Only do the push if configured
                    if (context.push) {
                        context.push.apns.send(null, payload, function (error) {
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

<!---HONumber=AcomDC_1210_2015-->