<properties
	pageTitle="UWP(유니버설 Windows 플랫폼) 앱에 푸시 알림 추가 | Azure 모바일 앱"
	description="Azure 앱 서비스 모바일 앱 및 Azure 알림 허브를 사용하여 UWP(유니버설 Windows 플랫폼) 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다."
	services="app-service\mobile,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/15/2016"
	ms.author="glenga"/>

# Windows 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##개요

이 항목에서는 Azure 알림 허브를 포함한 Azure 앱 서비스의 모바일 앱을 사용하여 UWP(유니버설 Windows 플랫폼)에 푸시 알림을 보내는 방법을 보여 줍니다. 이 시나리오에서는 새 항목이 추가되는 경우 모바일 앱 백 엔드가 Windows 알림 서비스(WNS)와 함께 등록된 모든 Windows 앱에 푸시 알림을 보냅니다.

이 자습서는 모바일 앱 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 빠른 시작 자습서 [Windows 앱 만들기](app-service-mobile-windows-store-dotnet-get-started.md)를 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##<a name="create-hub"></a>알림 허브 만들기

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##푸시 알림에 대해 앱 등록

Azure에서 Windows 앱으로 푸시 알림을 보내기 전에 앱을 Windows 스토어에 제출해야 합니다. 그런 다음 서버 프로젝트를 구성하여 WNS와 통합할 수 있습니다.

1. Visual Studio 솔루션 탐색기에서 UWP 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어** > **스토어와 앱을 연결...**을 클릭합니다. 

    ![Windows 스토어에 응용 프로그램 연결](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. 마법사에서 **다음**을 클릭하고 Microsoft 계정으로 로그인하고 **새로운 앱 이름 예약**에서 앱 이름을 입력한 후 **예약**을 클릭합니다.

3. 앱을 성공적으로 등록한 후에 새로운 앱 이름을 선택하고 **다음** 및 **연결**을 차례로 클릭합니다. 이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.

7. [Windows 개발자 센터](https://dev.windows.com/ko-KR/overview)로 이동하여 Microsoft 계정을 사용해 로그인하고 **내 앱**에서 새 앱 등록을 클릭한 후 **서비스** > **푸시 알림**을 확장합니다.

8. **푸시 알림** 페이지에서 **Microsoft Azure 모바일 서비스** 아래의 **Live 서비스 사이트**를 클릭합니다.

9. 등록 페이지에서 **응용 프로그램 암호** 및 **패키지 SID**에 있는 값을 기록합니다. 이 값은 모바일 앱 백 엔드를 구성하는 데 사용합니다.

	![Windows 스토어에 응용 프로그램 연결](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] 클라이언트 암호와 패키지 SID는 중요한 보안 자격 증명입니다. 다른 사람과 공유하지 말고 앱과 함께 분산하지 마세요. **응용 프로그램 ID**는 Microsoft 계정 인증을 구성하는 암호와 함게 사용됩니다.

##푸시 알림을 전송하도록 백 엔드 구성

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>푸시 알림을 전송하도록 서버 업데이트

이제 앱에서 푸시 알림을 사용하도록 설정했으므로 푸시 알림을 전송하도록 앱 백 엔드를 업데이트해야 합니다. 백 엔드 프로젝트 type&mdash([.NET 백 엔드](#dotnet) 또는 [Node.js 백 엔드](#nodejs))와 일치하는 아래의 절차를 사용합니다.

### <a name="dotnet"></a>.NET 백 엔드 프로젝트

1. Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다. Microsoft.Azure.NotificationHubs를 검색한 다음 **설치**를 클릭합니다. 알림 허브 클라이언트 라이브러리를 설치합니다.

2. **컨트롤러**를 확장하고 TodoItemController.cs를 열고 다음 using 문을 추가합니다.

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

	    // Create the notification hub client.
	    NotificationHubClient hub = NotificationHubClient
	        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

	    // Define a WNS payload
	    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
	                            + item.Text + @"</text></binding></visual></toast>";
	    try
	    {
	        // Send the push notification.
	        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

	        // Write the success result to the logs.
	        config.Services.GetTraceWriter().Info(result.State.ToString());
	    }
	    catch (System.Exception ex)
	    {
	        // Write the failure result to the logs.
	        config.Services.GetTraceWriter()
	            .Error(ex.Message, null, "Push.SendAsync Error");
	    }

	이 코드는 새 항목을 삽입한 후에 알림 허브에 푸시 알림을 전송하도록 지시합니다.

4. 서버 프로젝트를 다시 게시합니다.

### <a name="nodejs"></a>Node.js 백 엔드 프로젝트

1. 아직 수행하지 않은 경우 [빠른 시작 프로젝트를 다운로드](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)하거나 [Azure 포털에서 온라인 편집기](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)를 사용합니다.

2. todoitem.js 파일의 기존 코드를 다음으로 바꿉니다.

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');

		var table = azureMobileApps.table();

		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK,
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');

	    // Define the WNS payload that contains the new item Text.
	    var payload = "<toast><visual><binding template=\ToastText01><text id="1">"
		                            + context.item.text + "</text></binding></visual></toast>";

	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a WNS native toast notification.
	                context.push.wns.sendToast(null, payload, function (error) {
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

	이는 새 todo 항목이 삽입된 경우 item.text가 포함된 WNS 알림 메시지를 보냅니다.

2. 로컬 컴퓨터에서 파일을 편집할 때 서버 프로젝트를 다시 게시합니다.

##<a id="update-app"></a>앱에 푸시 알림 추가

다음으로 앱은 시작에서 푸시 알림에 등록해야 합니다. 사용할 수 있는 인증이 있는 경우 푸시 알림을 등록하기 전에 사용자가 로그인해야 합니다. 자세한 내용은 빠른 시작 전체 샘플에서 [먼저 인증](https://github.com/Azure-Samples/app-service-mobile-windows-quickstart/blob/master/README.md#authenticate-first)을 참조하세요.

1. **App.xaml.cs** 프로젝트 파일을 열고 다음 `using` 문을 추가합니다.

		using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. 동일한 파일에서 다음 **InitNotificationsAsync** 메서드 정의를 **App** 클래스에 추가합니다.

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 앱 서비스 모바일 앱에 등록합니다.

3. **App.xaml.cs**의 **OnLaunched** 이벤트 처리기 맨 위에서 다음 예제와 같이 메서드 정의에 **비동기** 한정자를 추가하고 새 **InitNotificationsAsync** 메서드에 다음 호출을 추가합니다.

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    이제 응용 프로그램을 시작할 때마다 단기 ChannelURI가 등록됩니다.

4. UWP 앱 프로젝트를 다시 빌드합니다. 이제 앱에서 알림을 받을 수 있습니다.

##<a id="test"></a>앱에서 푸시 알림 테스트

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>추가 정보

* 템플릿은 유연성을 제공하여 플랫폼간 푸시 및 지역화된 푸시를 보냅니다. [Azure 모바일 앱에 관리된 클라이언트를 사용하는 방법](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)은 템플릿을 등록하는 방법을 보여 줍니다.
* 태그를 사용하면 푸시를 사용하여 여러 조각으로 나뉜 고객을 대상으로 할 수 있습니다. [Azure 모바일 앱에 대해 .NET 백 엔드 서버 SDK로 작업](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags)은 장치 설치에 태그를 추가하는 방법을 보여 줍니다.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
))) )

<!---HONumber=AcomDC_0608_2016-->