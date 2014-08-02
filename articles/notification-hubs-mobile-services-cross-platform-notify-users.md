<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

알림 허브를 통해 사용자에게 크로스 플랫폼 알림 보내기
=====================================================

[모바일 서비스](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/ "모바일 서비스") [ASP.NET](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet/ "ASP.NET")

이전 자습서인 [알림 허브를 통해 사용자에게 알림](/en-us/manage/services/notification-hubs/notify-users)에서는 인증된 특정 사용자가 등록한 모든 장치에 알림을 푸시하는 방법을 배웠습니다. 해당 자습서에서는 지원되는 각 클라이언트 플랫폼에 알림을 보내기 위해 여러 요청이 필요했습니다. 알림 허브는 특정 장치가 알림을 받는 방법을 지정할 수 있는 템플릿을 지원합니다. 이 경우 크로스 플랫폼 알림 전송이 간소화됩니다. 이 항목에서는 템플릿을 사용하여 단일 요청으로 모든 플랫폼을 대상으로 하는, 플랫폼을 알 수 없는 알림을 보내는 방법을 보여 줍니다. 템플릿에 대한 자세한 내용은 [Azure 알림 허브 개요](http://go.microsoft.com/fwlink/p/?LinkId=317339)를 참조하십시오.

**참고**

알림 허브를 사용하면 장치가 동일한 태그로 여러 템플릿을 등록할 수 있습니다. 이 경우 해당 태그를 대상으로 들어오는 메시지가 있으면 각 템플릿에 대해 하나씩 여러 개의 알림이 장치에 전달됩니다. 이런 방식으로 Windows 스토어 앱에 알림 메시지와 배지 둘 다로 표시하는 등 여러 시각적 알림에 동일한 메시지를 표시할 수 있습니다.

템플릿을 사용하여 크로스 플랫폼 알림을 보내려면 다음 단계를 따르십시오.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![][0]

2.  **API** 탭을 클릭한 후 API 테이블에서 **register\_notifications** 항목을 클릭합니다.

    ![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png)

3.  **스크립트** 탭을 클릭하고 `existingRegs` 값이 **false**일 때 새 등록을 만드는 **else** 블록을 찾은 후 다음 코드로 바꿉니다.

		else {
            // Create a new registration.
            var template;
            if (platform === 'win8') {                
                template = { text1: '$(message)' };              
                hub.wns.createToastText01Registration(request.body.channelUri, 
                [userId, installationId], template, registrationComplete);
            } else if (platform === 'ios') {
                template = '{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}';
                hub.apns.createTemplateRegistration(request.body.deviceToken, 
                [userId, installationId], template, registrationComplete);
            } else {
                response.send(500, 'Unknown client.');
            }
        }

    이 코드는 플랫폼 특정 메서드를 호출하여 기본 등록이 아니라 템플릿 등록을 만듭니다. 템플릿 등록은 기본 등록에서 파생되므로 기존 등록을 수정할 필요는 없습니다.

4.  **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   	![][2]

5.  **Todoitem**에서 **스크립트** 탭을 클릭하고 **Insert**를 선택한 후 기존 **insert** 함수를 다음 코드로 바꿉니다.

   	![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png) 
	
	**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

1.  insert 함수를 다음 코드로 바꿉니다.

         function insert(item, user, request) {
             var azure = require('azure');
             var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
             '<FULL_SAS_CONNECTION_STRING>');
            
             // Execute the request and send notifications.
             request.execute({
                 success: function() {
                     // Write the default response and send a notification 
                     // to the user on all devices by using the userId tag.
                     request.respond();
                     // Create a template-based payload.
                     var payload = '{ "message" : "New item added: ' + item.text + '" }';            
                     // Send a notification to the current user on all platforms. 
                     hub.send(user.userId, payload,  
                     function(error, outcome){
                         // Do something here with the outcome or error.
                     });     
                 }
             });
         }

    이 코드는 네이티브 페이로드를 지정할 필요 없이 동시에 모든 플랫폼에 알림을 보냅니다. 알림 허브는 등록된 템플릿에 지정된 대로 올바른 페이로드를 작성하고 제공된 *tag* 값을 가진 모든 장치에 전달합니다.

2.  *`<NOTIFICATION_HUB_NAME>`* 및 *`<FULL_SAS_CONNECTION_STRING>`*을 알림 허브의 값으로 바꾸도록 스크립트를 업데이트한 후 **저장**을 클릭합니다.

3.  장치 에뮬레이터를 중지하거나 장치에서 기존 클라이언트 앱을 제거합니다.

    그러면 새 설치 ID가 모바일 서비스 클라이언트에서 생성됩니다. 그렇지 않으면 서비스에서 기존 비템플릿 등록을 사용하려고 합니다.

4.  클라이언트 앱을 다시 배포하고 실행하여 등록에 성공했으며 새 등록 ID가 표시되는지 확인합니다.

5.  이전처럼 텍스트를 입력하고 새 항목을 추가합니다.

    삽입이 완료되고 나면 앱이 알림 허브로부터 푸시 알림을 받습니다.

6.  (옵션) 클라이언트 앱을 두 번째 장치에 배포한 후 앱을 실행하고 텍스트를 삽입합니다.

    각 장치에 알림이 표시됩니다.

다음 단계
---------

이 자습서를 마쳤습니다. 이제 다음 항목에서 알림 허브 및 템플릿에 대해 자세히 알아보십시오.

-   **알림 허브를 사용하여 속보 보내기([Windows 스토어 C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet)/[iOS](/en-us/manage/services/notification-hubs/breaking-news-ios))**
    다른 템플릿 사용 시나리오를 보여 줍니다.

-   **[Azure 알림 허브 개요](http://go.microsoft.com/fwlink/p/?LinkId=317339)**
    개요 항목에는 템플릿에 대한 세부 정보가 포함되어 있습니다.

-   **[Windows 스토어에 대한 알림 허브 방법](http://msdn.microsoft.com/en-us/library/windowsazure/jj927172.aspx)**
    템플릿 표현 언어 참조가 포함되어 있습니다.


<!-- Anchors. -->
[ASP.NET backend]: #aspnet
[Mobile Services backend]: #mobileservices

<!-- Images. -->
[0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
[2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
[3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
<!-- URLs. -->
[Push to users ASP.NET]: /en-us/manage/services/notification-hubs/notify-users-aspnet/
[Push to users Mobile Services]: /en-us/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Management Portal]: https://manage.windowsazure.com/
[Send cross-platform notifications to users with Notification Hubs]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Breaking news .NET]: /en-us/manage/services/notification-hubs/breaking-news-dotnet
[Breaking news iOS]: /en-us/manage/services/notification-hubs/breaking-news-ios
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notify users with Notification Hubs]: /en-us/manage/services/notification-hubs/notify-users 
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/en-us/library/windowsazure/jj927172.aspx
