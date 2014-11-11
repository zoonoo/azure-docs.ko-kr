<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 모바일 서비스를 사용하여 푸시 알림에 현재 사용자 등록

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-KR/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows 스토어 C#" class="current">Windows 스토어 C#</a><a href="/ko-KR/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS">iOS</a>
</div>

이 항목에서는 Azure 모바일 서비스에 의해 등록이 수행될 때 Azure 알림 허브를 사용하여 푸시 알림 등록을 요청하는 방법을 보여 줍니다. 이 항목은 [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림] 자습서를 확장합니다. 이미 해당 자습서의 필수 단계를 완료하여 인증된 모바일 서비스를 만든 상태여야 합니다. 사용자 알림 시나리오에 대한 자세한 내용은 [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림]을 참조하십시오.

1.  Visual Studio 2012 Express for Windows 8에서 필수 자습서 [인증 시작][인증 시작]을 완료할 때 만든 프로젝트를 엽니다.

2.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어**를 클릭한 후 **응용 프로그램을 스토어에 연결...**을 클릭합니다.

    ![][0]

    **응용 프로그램을 Windows 스토어에 연결** 마법사가 나타납니다.

3.  마법사에서 **로그인**을 클릭한 후 Microsoft 계정에 로그인합니다.

4.  [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림]에서 등록한 앱을 선택하고 **다음**을 클릭한 후 **연결**을 클릭합니다.

    ![][1]

    이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.

    <div class="dev-callout"><b>참고</b>
<p>여기서는 이 모바일 서비스 앱과 함께 알림 허브 자습서 앱의 Windows 스토어 등록을 재사용합니다. 이로 인해 알림 허브 자습서 앱이 알림을 받지 못할 수 있습니다.</p>
</div>

5.  솔루션 탐색기에서 Package.appxmanifest 프로젝트 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.

6.  **모든 이미지 자산**까지 아래로 스크롤하고 **배지 로고**를 클릭합니다. **알림**에서 **알림 가능**을 **예**로 설정합니다.

    ![][2]

    그러면 이 모바일 서비스 자습서 앱이 알림 메시지를 받을 수 있습니다.

7.  Visual Studio에서 MainPage.xaml.cs 파일을 열고 **NotificationRequest** 및 **RegistrationResult** 클래스를 정의하는 다음 코드를 추가합니다.

        public class NotificationRequest
        {
            public string channelUri { get; set; }
            public string platform { get; set; }
        }

        public class RegistrationResult
        {
            public string RegistrationId { get; set; }
            public string ExpirationTime { get; set; }
        }

    이러한 클래스는 각각 사용자 지정 API가 호출될 때 반환되는 요청 본문 및 등록 ID를 저장합니다.

8.  **MainPage** 클래스에 다음 메서드를 추가합니다.

        private async System.Threading.Tasks.Task RegisterNotification()
        {
            string message;

            // Get a channel for push notifications.
            var channel =
                await Windows.Networking.PushNotifications
                .PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Create the body of the request.
            var body = new NotificationRequest 
            {
                channelUri = channel.Uri, 
                platform = "win8" 
            }; 

            try
            {
                // Call the custom API POST method with the supplied body.
                var result = await App.MobileService
                    .InvokeApiAsync<NotificationRequest, 
                    RegistrationResult>("register_notifications", body,
                    System.Net.Http.HttpMethod.Post, null);

                // Set the response, which is the ID of the registration.
                message = string.Format("Registration ID: {0}", result.RegistrationId);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    이 메서드는 푸시 알림에 대한 채널을 만들어 장치 유형과 함께, 알림 허브에서 등록을 만드는 사용자 지정 API 메서드로 보냅니다. 이 사용자 지정 API는 [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림]에서 정의되었습니다.

9.  다음 코드 줄을 **Authenticate** 메서드가 호출된 직후 **OnNavigatedTo** 메서드에 추가합니다.

        await RegisterNotification();

    <div class="dev-callout"><b>참고</b>
<p>이제 페이지가 로드될 때마다 등록이 요청됩니다. 등록 상태가 유지되도록 앱에서 주기적으로 등록할 수도 있습니다.</p>
</div>

클라이언트 앱이 업데이트되었으므로 [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림]으로 돌아가서 알림 허브를 사용하여 알림을 보내도록 모바일 서비스를 업데이트합니다.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [알림 허브를 통해 사용자에게 알림]: /ko-KR/manage/services/notification-hubs/notify-users
  [인증 시작]: /ko-KR/develop/mobile/tutorials/get-started-with-users-dotnet/
  [0]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
  [1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
  [2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png
