<properties 
	pageTitle="Windows 런타임 8.1 범용 앱에 푸시 알림 추가 | Azure 모바일 앱" 
	description="Azure 앱 서비스 모바일 앱 및 Azure 알림 허브를 사용하여 Windows 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." 
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
	ms.date="08/14/2015" 
	ms.author="glenga"/>

# Windows 런타임 8.1 범용 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##개요

이 항목은 Azure 앱 서비스 모바일 앱 및 Azure 알림 허브를 사용하여 Windows 런타임 8.1 범용 앱에 푸시 알림을 보내는 방법을 보여줍니다. 이 시나리오에서는 새 항목이 추가되는 경우 모바일 앱 백 엔드가 Windows 알림 서비스(WNS)와 함께 등록된 모든 Windows 앱에 푸시 알림을 보냅니다.

이 자습서는 앱 서비스 모바일 앱 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 빠른 시작 자습서 [Windows 앱 만들기](../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)를 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 [Microsoft 스토어 계정](http://go.microsoft.com/fwlink/p/?LinkId=280045)
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* [빠른 시작 자습서](../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)를 완료합니다.  



##<a name="create-gateway"></a>알림 허브 만들기

푸시 알림을 처리하는 새 알림 허브를 만들려면 다음 단계를 수행합니다. 동일한 리소스 그룹에 이미 허브가 있는 경우 이 섹션을 완료할 필요가 없습니다.

1. [Azure 포털]을 방문합니다. **모두 찾아보기** > **모바일 앱** > 방금 만든 백 엔드를 클릭합니다. **설정** > **모바일** > **푸시**를 클릭합니다. 

2. 알림 허브를 만들려면 워크플로를 따릅니다. 현재 리소스 그룹에 해당 사항이 없으면 새 네임스페이스를 만들어야 합니다. 설정을 모두 구성했으면 **만들기**를 클릭합니다.

다음으로, 앱을 푸시할 수 있도록 이 알림 허브를 사용합니다.

##푸시 알림에 대해 앱 등록

Azure에서 Windows 앱으로 푸시 알림을 보내기 전에 앱을 Windows 스토어에 제출해야 합니다. 그런 다음 서버 프로젝트를 구성하여 WNS와 통합할 수 있습니다.

1. Visual Studio 솔루션 탐색기에서 Windows 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어** > **앱을 스토어에 연결...**을 클릭합니다. 

    ![][3]
    
2. 마법사에서 **다음**을 클릭하고 Microsoft 계정으로 로그인하며 **새로운 앱 이름 예약**에서 앱 이름을 입력한 다음 **예약**을 클릭합니다.

3. 앱 등록을 성공적으로 만든 후에 새로운 앱 이름을 선택하고 **다음**, **연결**을 차례로 클릭합니다. 이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.

7. Windows 스토어 앱에서 이전에 만든 동일한 등록을 사용하여 Windows Phone 스토어 앱 프로젝트에서 1 및 3단계를 반복합니다.

7. [Windows 개발자 센터](https://dev.windows.com/en-us/overview)로 이동하고 Microsoft 계정을 사용하여 로그인합니다. **내 앱**에서 새 앱 등록을 클릭한 다음 **서비스** > **푸시 알림**을 확장합니다.

8. **푸시 알림** 페이지에서 **Microsoft Azure 모바일 서비스** 아래의 **Live 서비스 사이트**를 클릭합니다.

9. **앱 설정** 탭에서 **클라이언트 암호** 및 **패키지 SID**의 값을 적어둡니다.

    ![][6]

    > [AZURE.IMPORTANT]클라이언트 암호와 패키지 SID는 중요한 보안 자격 증명입니다. 다른 사람과 공유하지 말고 앱과 함께 분산하지 마세요.

##푸시 요청을 전송하도록 모바일 앱 구성

1. [Azure 포털]에 로그온하고 **찾아보기** > **모바일 앱**> 사용자 앱 > **푸시 알림 서비스**를 선택합니다.

2. **Windows 알림 서비스**에서 라이브 서비스 사이트에서 가져온 **보안 키**(클라이언트 암호) 및 **패키지 SID**를 입력한 다음 **저장**을 클릭합니다.

이제 모바일 앱 백 엔드가 WNS와 작동하도록 구성되었습니다.

##<a id="update-service"></a>푸시 알림을 전송하도록 서버 업데이트

이제 앱에서 푸시 알림을 사용하도록 설정했으므로 푸시 알림을 전송하도록 앱 백 엔드를 업데이트해야 합니다.

1. Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다. `Microsoft.Azure.NotificationHubs`를 검색한 다음 **설치**를 클릭합니다. 알림 허브 클라이언트 라이브러리를 설치합니다.

3. 서버 프로젝트에서 **컨트롤러** > **TodoItemController.cs**를 열고 다음 using 문을 추가합니다.

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. **PostTodoItem** 메서드에서 **InsertAsync**를 호출한 후에 다음 코드를 추가합니다.

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

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
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


## <a name="publish-the-service"></a>Azure에 모바일 백 엔드 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>앱에 푸시 알림 추가

1. Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다. 

    그러면 NuGet 패키지 관리 대화 상자가 표시됩니다.

2. 관리된 항목용 앱 서비스 모바일 앱 클라이언트 SDK를 검색하고 **설치**를 클릭한 다음 솔루션에 포함된 모든 클라이언트 프로젝트를 선택하고 사용 약관에 동의합니다.

    그러면 Windows용 Azure 모바일 푸시 라이브러리가 다운로드 및 설치되고 해당 참조가 모든 클라이언트 프로젝트에 추가됩니다.

3. 공유 **App.xaml.cs** 프로젝트 파일을 열고 다음 `using` 문을 추가합니다.

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

4. 동일한 파일에서 다음 **InitNotificationsAsync** 메서드 정의를 **App** 클래스에 추가합니다.
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 앱 서비스 모바일 앱에 등록합니다.
    
5. **App.xaml.cs**의 **OnLaunched** 이벤트 처리기 맨 위에서 다음 예제와 같이 메서드 정의에 **async** 한정자를 추가하고 새 **InitNotificationsAsync** 메서드에 다음 호출을 추가합니다.

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    이제 응용 프로그램을 시작할 때마다 단기 ChannelURI가 등록됩니다.

6. 솔루션 탐색기에서 Windows 스토어 앱의 **Package.appxmanifest**를 두 번 클릭하고 **알림**에서 **알림 가능**을 **예**로 설정합니다.

    **파일** 메뉴에서 **모두 저장**을 클릭합니다.

7. Windows Phone 스토어 앱 프로젝트에서 이전 단계를 반복합니다.

이제 앱에서 알림을 받을 수 있습니다.

##<a id="test"></a>앱에서 푸시 알림 테스트

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push-preview](../../includes/app-service-mobile-windows-universal-test-push-preview.md)]

<!-- Anchors. -->
<!-- URLs. -->
[Azure 포털]: https://portal.azure.com/
<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-submit-win8-app.png
[1]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/notification-hub-associate-win8-app.png
[4]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-select-app-name.png
[5]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit-app.png
[6]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=Oct15_HO3-->