<properties 
	pageTitle="Windows 스토어 클라이언트를 사용하여 특정 사용자에게 x-plat 알림 전송"
	description="특정 사용자의 모든 장치에 푸시 알림을 전송하는 방법을 알아봅니다."
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/17/2015"
	ms.author="yuaxu"/>

# 특정 사용자에게 크로스 플랫폼 알림 전송

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../includes/app-service-mobile-selector-push-users-preview.md)]

이 항목에서는 모바일 백 엔드에서 특정 사용자의 모든 등록된 장치에 알림을 전송하는 방법을 보여 줍니다. 등록 시 클라이언트 응용 프로그램이 자유롭게 페이로드 형식과 변수 자리 표시자를 지정할 수 있게 하는 [템플릿] 개념을 소개했습니다. 이 경우 해당 자리 표시자가 포함된 모든 플랫폼에 전송이 수행되어 크로스 플랫폼 알림을 사용할 수 있습니다.

> [AZURE.NOTE]크로스 플랫폼 클라이언트에서 푸시가 작동하려면 사용하도록 설정할 각 플랫폼에 대해 이 자습서를 완료해야 합니다. 동일한 모바일 백 엔드를 공유하는 클라이언트의 경우 [모바일 백 엔드 업데이트](#backend)를 한 번만 수행하면 됩니다.
 
##필수 조건 

이 자습서를 시작하기 전에 작업할 각 클라이언트 플랫폼에 대해 다음과 같은 앱 서비스 자습서를 완료해야 합니다.

+ [인증 시작]<br/>TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

+ [푸시 알림 시작]<br/>푸시 알림에 맞게 TodoList 샘플 앱을 구성합니다.

##<a name="client"></a>플랫폼 간 푸시를 처리할 템플릿을 등록하도록 클라이언트를 업데이트합니다.

1. 대신 **MainPage.cs**에서 **InitNotificationAsync**를 수행하여 사용자 인증 작업을 합니다. **App.xmal.cs**에서 **InitNotificationAsync** 메서드 정의 및 호출을 삭제하고 **MainPage.cs**의 **MainPage** 클래스에 다음 코드를 추가합니다.

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
 
            // building templates for wns
            var toastTemplate = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";
            JObject templateBody = new JObject();
            templateBody["body"] = toastTemplate;
 
            JObject wnsToastHeaders = new JObject();
            wnsToastHeaders["X-WNS-Type"] = "wns/toast";
            templateBody["headers"] = wnsToastHeaders;
 
            JObject templates = new JObject();
            templates["testTemplate"] = templateBody;
 
            await App.MobileService.GetPush().RegisterAsync(channel.Uri, templates);
        }

    또한 일부 using 문을 **MainPage.cs**로 전송하려고 합니다.

2. 이 메서드는 **ButtonLogin_Click**에서 **AuthenticateAsync** 호출 바로 뒤에 사용합니다.

        await AuthenticateAsync();
        InitNotificationAsync();

이제 앱이 사용자 로그인 정보로 사용자 장치를 등록하도록 설정되었습니다.

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

            // get the current user id and create tag to identify user
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // build dictionary for template
            var notification = new Dictionary<string, string>{{"message", item.Text}};

            try
            {
            	await Hub.Push.SendTemplateNotificationAsync(notification, userTag);
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
[인증 시작]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[푸시 알림 시작]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview.md
[템플릿]: https://msdn.microsoft.com/ko-kr/library/dn530748.aspx

<!--HONumber=54-->