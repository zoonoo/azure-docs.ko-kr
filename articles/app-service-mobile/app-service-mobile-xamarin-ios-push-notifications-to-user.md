<properties 
	pageTitle="Xamarin iOS 클라이언트를 사용하여 특정 사용자에게 푸시 알림 전송" 
	description="사용자의 모든 장치에 푸시 알림을 전송하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/01/2015"
	ms.author="yuaxu"/>

# 특정 사용자에게 크로스 플랫폼 알림 전송

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

이 항목에서는 모바일 백 엔드에서 특정 사용자의 모든 등록된 장치에 알림을 전송하는 방법을 보여 줍니다. 등록 시 클라이언트 응용 프로그램이 자유롭게 페이로드 형식과 변수 자리 표시자를 지정할 수 있게 하는 [템플릿]을 소개합니다. 서버에서 템플릿 알림을 보내면 알림 허브는 자리 표시자가 포함 된 모든 플랫폼에 플랫폼 간 알림 사용을 지시합니다.

> [AZURE.NOTE]크로스 플랫폼 클라이언트에서 푸시가 작동하려면 사용하도록 설정할 각 플랫폼에 대해 이 자습서를 완료해야 합니다. 동일한 모바일 백 엔드를 공유하는 클라이언트의 경우 [모바일 백 엔드 업데이트](#backend)를 한 번만 수행하면 됩니다.
 
##필수 조건 

이 자습서를 시작하기 전에 작업할 각 클라이언트 플랫폼에 대해 다음과 같은 앱 서비스 자습서를 완료해야 합니다.

+ [인증 시작]<br/>TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

+ [푸시 알림 시작]<br/>푸시 알림에 맞게 TodoList 샘플 앱을 구성합니다.

##<a name="client"></a>플랫폼 간 푸시를 처리할 템플릿을 등록하도록 클라이언트를 업데이트합니다.

1. APNS 등록 조각을 **AppDelegate.cs**의 **FinishedLaunching**에서 **QSTodoListViewController.cs**의 **RefreshAsync** 작업 정의로 이동합니다. 인증이 완료된 후 등록이 수행되어야 합니다.

        ...
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound, 
                new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        ...



##<a name="backend"></a>특정 사용자에게 알림을 전송하도록 서비스 백 엔드 업데이트

1. Visual Studio에서 `PostTodoItem` 메서드 정의를 다음 코드로 업데이트합니다.  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create given user tag
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // Sending the message so that all template registrations that contain "messageParam"
            // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string,string> templateParams = new Dictionary<string,string>();
            templateParams["messageParam"] = item.Text + " was added to the list.";

            try
            {
                await Hub.SendTemplateNotificationAsync(templateParams, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>앱 테스트

모바일 백 엔드 프로젝트를 다시 게시하고 설정한 클라이언트 앱을 실행합니다. 항목 삽입 시 백 엔드에서 사용자가 로그인한 모든 클라이언트 앱에 알림을 전송합니다.

<!-- URLs. -->
[인증 시작]: app-service-mobile-xamarin-ios-get-started-users.md
[푸시 알림 시작]: app-service-mobile-xamarin-ios-get-started-push.md
[템플릿]: ../notification-hubs/notification-hubs-templates.md

<!---HONumber=AcomDC_1203_2015--->