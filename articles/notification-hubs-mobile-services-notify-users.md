<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="Notify Users" pageTitle="Notify Users of your mobile service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your mobile service by using Notification Hubs" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

알림 허브를 통해 사용자에게 알림
================================

[모바일 서비스](/en-us/manage/services/notification-hubs/notify-users "모바일 서비스") [ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

이 자습서에서는 Azure 알림 허브를 사용하여 특정 장치에서 특정 앱 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. Azure 모바일 서비스 백 엔드는 클라이언트를 인증하고 알림을 생성하는 데 사용됩니다. 이 자습서는 이전 **알림 허브 시작** 자습서에서 만든 알림 허브를 기반으로 합니다. 알림 등록 코드는 클라이언트에서 백 엔드 서비스로 이동되었습니다. 따라서 클라이언트가 서비스에서 인증된 후에만 등록이 완료됩니다. 또한 알림 허브 자격 증명이 클라이언트 앱에 분산되지 않습니다. 서비스는 등록 중에 요청되는 태그도 제어합니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

-   [알림을 등록하도록 모바일 서비스 업데이트](#register-notification)
-   [로그인하고 등록을 요청하도록 앱 업데이트](#update-app)
-   [알림을 보내도록 모바일 서비스 업데이트](#send-notifications)

필수 조건
---------

이 자습서를 시작하기 전에 먼저 다음 자습서를 완료해야 합니다.

-   **알림 허브 시작**([Windows 스토어 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/en-us/manage/services/notification-hubs/get-started-notification-hubs-android))

-   **모바일 서비스에서 인증 시작**([Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android/))

이 자습서는 **알림 허브 시작**에서 만든 앱과 알림 허브를 기반으로 합니다. 또한 **모바일 서비스에서 인증 시작**에서 구성한 인증된 모바일 서비스를 활용합니다.

**참고**

기본적으로 **모바일 서비스에서 인증 시작** 자습서에서는 Facebook 인증을 사용합니다. 두 개의 Windows 스토어 앱이 단일 Live Connect 등록을 공유할 수 없으므로 이 자습서에서는 Microsoft 계정 인증을 사용할 수 없습니다. Microsoft 계정 인증을 사용하려면 모바일 서비스와 알림 허브가 Live Connect에서 동일한 앱에 등록되어야 합니다.

알림 등록알림을 등록하도록 모바일 서비스 업데이트
-------------------------------------------------

알림 등록은 클라이언트가 서비스에서 인증된 후에만 완료되어야 하므로 등록은 모바일 서비스에 정의된 사용자 지정 API에 의해 수행됩니다. 이 사용자 지정 API가 인증된 클라이언트에 의해 호출되어 알림 등록이 요청됩니다. 이 섹션에서는 **모바일 서비스에서 인증 시작** 자습서를 완료할 때 정의한 인증된 모바일 서비스를 업데이트합니다.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인하고 **Service Bus**, 네임스페이스, **알림 허브**를 차례로 클릭한 후 알림 허브를 선택하고 **연결 정보**를 클릭합니다.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png)

2.  알림 허브 이름에 주의하고 **DefaultFullSharedAccessSignature**의 연결 문자열을 복사합니다.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png)

    이 연결 문자열과 알림 허브 이름을 함께 사용하여 알림을 등록하고 보냅니다.

3.  계속 관리 포털에 있는 상태에서 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png)

4.  **API** 탭을 클릭한 후 **사용자 지정 API 만들기**를 클릭합니다.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png)

    **Create a new custom API** 대화 상자가 표시됩니다.

5.  **API 이름**에 *register\_notifications*를 입력하고 **POST Permissions**에 대해 **Only Authenticated Users**를 선택한 후 확인 단추를 클릭합니다.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png)

	그러면 HTTP POST 메서드를 사용하여 호출하기 전에 사용자가 인증되어야 하는 API가 만들어집니다. 다른 HTTP 메서드는 구현하지 않을 것이므로 해당 메서드는 설정할 필요가 없습니다.

1.  API 테이블에서 새 **register\_notifications** 항목을 클릭합니다.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png)

2.  **스크립트** 탭을 클릭하고 기존 코드를 다음으로 바꿉니다.

         exports.post = function(request, response) {

             // Create a notification hub instance.
             var azure = require('azure');
             var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
                 '<FULL_SAS_CONNECTION_STRING>');
            
             // Get the registration info that we need from the request. 
             var platform = request.body.platform;
             var userId = request.user.userId;
             var installationId = request.header('X-ZUMO-INSTALLATION-ID');
                
             // Function called when registration is completed.
             var registrationComplete = function(error, registration) {
                 if (!error) {
                     // Return the registration.
                     response.send(200, registration);
                 } else {
                     response.send(500, 'Registration failed!');
                 }
             }
             // Function called to log errors.
             var logErrors = function(error) {
                 if (error) {
                     console.error(error)
                 }
             }
             // Get existing registrations.
             hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
                 var firstRegistration = true;
                 if (existingRegs.length > 0) {
                      for (var i = 0; i < existingRegs.length; i++) {
                         if (firstRegistration) {
                             // Update an existing registration.
                             if (platform === 'win8') {
                                 existingRegs[i].ChannelUri = request.body.channelUri;                        
                                 hub.updateRegistration(existingRegs[i], registrationComplete);                        
                             } else if (platform === 'ios') {
                                 existingRegs[i].DeviceToken = request.body.deviceToken;
                                 hub.updateRegistration(existingRegs[i], registrationComplete);
                             } else {
                                 response.send(500, 'Unknown client.');
                             }
                             firstRegistration = false;
                         } else {
                             // We shouldn't have any extra registrations; delete if we do.
                             hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
                         }
                     }
                 } else {
                     // Create a new registration.
                     if (platform === 'win8') {                
                         hub.wns.createNativeRegistration(request.body.channelUri, 
                         [userId, installationId], registrationComplete);
                     } else if (platform === 'ios') {
                         hub.apns.createNativeRegistration(request.body.deviceToken, 
                         [userId, installationId], registrationComplete);
                     } else {
                         response.send(500, 'Unknown client.');
                     }
                 }
             });
         }

    이 코드는 메시지 본문에서 플랫폼 및 장치 ID 정보를 가져옵니다. 이 데이터는 요청 헤더의 설치 ID 및 로그인한 사용자의 사용자 ID와 함께 등록을 업데이트하는 데 사용되거나(등록이 있는 경우) 새 등록을 만드는 데 사용됩니다. 이 등록은 사용자 ID 및 설치 ID로 태그가 지정됩니다.

3.  *`<NOTIFICATION_HUB_NAME>`* 및 *`<FULL_SAS_CONNECTION_STRING>`*을 알림 허브의 값으로 바꾸도록 스크립트를 업데이트한 후 **저장**을 클릭합니다.

    **참고**

    *`<FULL_SAS_CONNECTION_STRING>`*에 **DefaultFullSharedAccessSignature**가 사용되는지 확인합니다. 이 클레임을 통해 사용자 지정 API 메서드가 등록을 만들고 업데이트할 수 있습니다.

앱 업데이트로그인하고 등록을 요청하도록 앱 업데이트
---------------------------------------------------

이제 새 사용자 지정 API를 호출하여 알림에 대한 등록을 요청하도록 TodoList 앱을 업데이트해야 합니다.

1.  클라이언트 플랫폼에 따라 다음 **모바일 서비스를 사용하여 푸시 알림에 현재 사용자 등록** 버전 중 하나의 단계를 따릅니다.

    -   [Windows 스토어 C\# 버전](/en-us/manage/services/notification-hubs/register-users-mobile-services-dotnet)
    -   [iOS 버전](/en-us/manage/services/notification-hubs/register-users-ios)

2.  업데이트된 앱을 실행하고 Facebook을 사용하여 로그인한 후 알림에 할당된 등록 ID가 표시되는지 확인합니다.

알림 보내기알림을 보내도록 모바일 서비스 업데이트
-------------------------------------------------

마지막 단계는 모바일 서비스에서 알림을 보내는 코드를 추가하는 것입니다. 이 알림 코드는 **TodoItem** 테이블의 삽입 처리기에 등록된 서버 스크립트에 추가됩니다.

1.  관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png)

2.  **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

	![](./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png) 

	**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

1.  insert 함수를 다음 코드로 바꿉니다.

         function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
 		   // Create the payload for a Windows Store app.
		    var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
		            // Send to Windows Store apps.
		            hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		            // Send to iOS apps.
		            hub.apns.send(user.userId, {
		                alert: item.text
		            }, function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		        }
		    });
		}

    이 코드는 Windows 스토어 앱과 iOS 앱 둘 다에서 현재 로그인한 사용자에 대한 태그로 알림을 보내려고 시도합니다.

1.  *`<NOTIFICATION_HUB_NAME>`* 및 *`<FULL_SAS_CONNECTION_STRING>`*을 알림 허브의 값으로 바꾸도록 스크립트를 업데이트한 후 **저장**을 클릭합니다.

    그러면 새 삽입 스크립트가 등록됩니다. 이 스크립트에서는 알림 허브를 사용하여 푸시 알림(삽입된 텍스트)을 삽입 요청에 제공된 채널로 보냅니다.

    **참고**

    *`<FULL_SAS_CONNECTION_STRING>`*에 **DefaultFullSharedAccessSignature**가 사용되는지 확인합니다. 이 클레임은 알림을 등록된 클라이언트로 보낼 수 있는 기능을 비롯한 모든 권한을 삽입 스크립트에 제공합니다.

앱 테스트앱에서 푸시 알림 테스트
--------------------------------

이제 알림이 구성되었으므로 알림을 생성하기 위한 데이터를 삽입하여 앱을 테스트해야 합니다.

1.  앱을 실행합니다.

    시작 시 등록 알림이 다시 표시됩니다.

2.  이전처럼 텍스트를 입력하고 새 항목을 추가합니다.

    삽입이 완료되고 나면 앱이 알림 허브로부터 푸시 알림을 받습니다.

    **참고**

    알림이 전송되도록 요청된 플랫폼에 대한 등록이 없는 경우 백 엔드에서 오류가 발생합니다. 이 경우 이 오류를 무시해도 됩니다. 이 상황을 방지하기 위한 템플릿 사용 방법을 보려면 [알림 허브를 통해 사용자에게 플랫폼 간 알림 보내기](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)(영문)를 참조하십시오.

3.  (옵션) 클라이언트 앱을 두 번째 장치에 배포한 후 앱을 실행하고 텍스트를 삽입합니다.

    각 장치에 알림이 표시됩니다.

다음 단계
---------

이제 이 자습서를 완료했으므로 다음 자습서 완료를 고려하십시오.

-   **알림 허브를 사용하여 뉴스 속보 보내기([Windows 스토어 C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet)(영문)/[iOS](/en-us/manage/services/notification-hubs/breaking-news-ios)(영문))**
    이 플랫폼별 자습서는 태그를 사용하여 사용자가 관심 있는 알림 유형을 구독할 수 있도록 하는 방법을 보여 줍니다.

-   **[알림 허브를 통해 사용자에게 플랫폼 간 알림 보내기](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services)(영문)**
    이 자습서는 플랫폼별 템플릿을 사용하여 알림을 등록하도록 현재 **알림 허브를 통해 사용자에게 알림** 자습서를 확장합니다. 이를 통해 서버 쪽 코드의 단일 메서드에서 알림을 보낼 수 있습니다.

알림 허브에 대한 자세한 내용은 [Azure 알림 허브](http://msdn.microsoft.com/en-us/library/windowsazure/jj927170.aspx)를 참조하십시오.

