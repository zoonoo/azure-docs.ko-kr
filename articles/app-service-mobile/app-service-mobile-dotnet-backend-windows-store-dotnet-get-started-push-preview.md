<properties 
	pageTitle="Azure 앱 서비스를 사용하여 Windows 범용 앱에 푸시 알림 추가" 
	description="Azure 앱 서비스를 사용하여 Windows 범용 앱에 푸시 알림을 전송하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="yuaxu"/>

# Windows 범용 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

이 항목에서는 Azure 앱 서비스를 사용하여 .NET 백 엔드에서 Windows 범용 앱에 푸시 알림을 전송하는 방법을 보여 줍니다. 완료하면 레코드 삽입 시 .NET 백 엔드에서 등록된 모든 Windows 범용 앱에 푸시 알림을 전송하게 됩니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [푸시 알림에 대해 앱 등록](#register)
2. [구성](#configure)
3. [푸시 알림을 전송하도록 서비스 업데이트](#update-service)
4. [앱에 푸시 알림 추가](#add-push)
5. [앱에서 푸시 알림 테스트](#test)

이 자습서는 앱 서비스 모바일 앱 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [앱 서비스 모바일 앱 시작]을 완료해야 합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 [Microsoft 스토어 계정](http://go.microsoft.com/fwlink/p/?LinkId=280045)
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>.

##<a id="register"></a>푸시 알림에 대해 앱 등록

Azure 앱 서비스를 사용하여 Windows 범용 앱에 푸시 알림을 전송하려면 앱을 Windows 스토어에 제출해야 합니다. 그런 다음 WNS와 통합되도록 모바일 앱 푸시 알림 서비스 자격 증명을 구성해야 합니다.

1. 앱을 아직 등록하지 않은 경우 Windows 스토어 앱용 개발자 센터의 <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">앱 제출 페이지</a>로 이동하여 Microsoft 계정으로 로그온한 후 **앱 이름**을 클릭합니다.

    ![][0]

2. **앱 이름**에 앱의 이름을 입력하고 **앱 이름 예약**을 클릭한 후 **저장**을 클릭합니다.

    ![][1]

    이렇게 하면 앱을 새로 Windows 스토어에 등록하게 됩니다.

4. 솔루션 탐색기에서 Windows 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어**를 클릭한 후 **응용 프로그램을 스토어에 연결...**을 클릭합니다.

    ![][3]

    **응용 프로그램을 Windows 스토어에 연결** 마법사가 나타납니다.

5. 마법사에서 **로그인**을 클릭한 후 Microsoft 계정으로 로그인합니다.

6. 2단계에서 등록한 앱을 클릭하고 **다음**을 클릭한 후 **연결**을 클릭합니다.

    ![][4]

    이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.

7. (옵션) Windows Phone 스토어 앱 프로젝트에 대해 4~6단계를 반복합니다.

7. 새 앱의 Windows 개발자 센터 페이지로 돌아가서 **Services**를 클릭합니다.

    ![][5]

8. **서비스** 페이지에서 **Microsoft Azure 모바일 서비스** 아래의 **Live 서비스 사이트**를 클릭합니다.

    ![][17]

9. **앱 설정** 탭에서 **클라이언트 암호** 및 **패키지 SID(보안 식별자)**를 적어둡니다.

    ![][6]

    > [AZURE.NOTE]**보안 정보** 클라이언트 암호와 패키지 SID는 중요한 보안 자격 증명입니다. 다른 사람과 공유하지 말고 앱과 함께 분산하지 마세요.

##<a id="configure"></a>푸시 요청을 전송하도록 모바일 앱 구성

1. [Azure Preview 포털]에 로그온하고 **찾아보기**, **모바일 앱**을 선택한 다음 앱을 클릭합니다. 푸시 알림 서비스를 클릭합니다.

2. Windows 알림 서비스에서 **클라이언트 암호** 및 **패키지 SID(보안 식별자)**를 입력하고 저장합니다.

이제 앱 서비스 모바일 앱이 WNS와 작동하도록 구성되었습니다.

<!-- URLs. -->
[Azure Preview 포털]: https://portal.azure.com/

##<a id="update-service"></a>푸시 알림을 전송하도록 서비스 업데이트

이제 앱에서 푸시 알림을 사용하도록 설정했으므로 푸시 알림을 전송하도록 앱 백 엔드를 업데이트해야 합니다.

1. Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

2. **Microsoft.Azure.NotificationHubs**를 검색하고 솔루션에 포함된 모든 프로젝트에 대해 **설치**를 클릭합니다.

3. Visual Studio 솔루션 탐색기에서 모바일 백 엔드 프로젝트의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 엽니다. 파일 맨 위에 다음 `using` 문을 추가합니다.

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

4. `PostTodoItem` 메서드의 **InsertAsync** 호출 뒤에 다음 조각을 추가합니다.

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // windows payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + item.Text + @"</text></binding></visual></toast>";

        await Hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

    이 코드는 이 모바일 앱과 연결된 알림 허브에 todo 항목 삽입 후 푸시 알림을 전송하도록 지시합니다.


## <a name="publish-the-service"></a>Azure에 모바일 백 엔드 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>앱에 푸시 알림 추가

1. Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다. 

    그러면 NuGet 패키지 관리 대화 상자가 표시됩니다.

2. 관리된 항목용 앱 서비스 모바일 앱 클라이언트 SDK를 검색하고 **설치**를 클릭한 다음 솔루션에 포함된 모든 프로젝트를 선택하고 사용 약관에 동의합니다.

    그러면 Windows용 Azure 모바일 푸시 라이브러리가 다운로드 및 설치되고 해당 참조가 모든 프로젝트에 추가됩니다.

3. **App.xaml.cs** 프로젝트 파일을 열고 다음 `using` 문을 추가합니다.

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.MobileServices;

    범용 프로젝트에서는 이 파일이 `<project_name>.Shared` 폴더에 위치해 있습니다.

4. 동일한 파일에서 다음 **InitNotificationsAsync** 메서드 정의를 **App** 클래스에 추가합니다.
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            
            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 앱 서비스 모바일 앱에 등록합니다.
    
5. **App.xaml.cs**에서 **OnLaunched** 이벤트 처리기의 맨 위에 다음과 같은 새 **InitNotificationsAsync** 메서드 호출을 추가합니다.

        InitNotificationsAsync();

    이제 응용 프로그램을 시작할 때마다 단기 ChannelURI가 등록됩니다.

6. 솔루션 탐색기에서 Windows 스토어 앱의 **Package.appxmanifest**를 두 번 클릭하고 **알림**에서 **알림 가능**을 **예**로 설정합니다.

    **파일** 메뉴에서 **모두 저장**을 클릭합니다.

7. (옵션) Windows Phone 스토어 앱 프로젝트에서 이전 단계를 반복합니다.

8. **F5** 키를 눌러 앱을 실행합니다.

이제 앱에서 알림을 받을 수 있습니다.

##<a id="test"></a>앱에서 푸시 알림 테스트

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-windows-universal-test-push-preview](../../includes/app-service-mobile-dotnet-backend-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

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

<!---HONumber=62-->