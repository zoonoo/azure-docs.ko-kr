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
	ms.date="11/25/2015" 
	ms.author="glenga"/>

# Windows 런타임 8.1 범용 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##개요

이 항목은 Azure 앱 서비스 모바일 앱 및 Azure 알림 허브를 사용하여 Windows 런타임 8.1 범용 앱에 푸시 알림을 보내는 방법을 보여줍니다. 이 시나리오에서는 새 항목이 추가되는 경우 모바일 앱 백 엔드가 Windows 알림 서비스(WNS)와 함께 등록된 모든 Windows 앱에 푸시 알림을 보냅니다.

이 자습서는 앱 서비스 모바일 앱 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 빠른 시작 자습서 [Windows 앱 만들기](../app-service-mobile-windows-store-dotnet-get-started.md)를 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 [Microsoft 스토어 계정](http://go.microsoft.com/fwlink/p/?LinkId=280045)
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* [빠른 시작 자습서](../app-service-mobile-windows-store-dotnet-get-started.md)를 완료합니다.  


##<a name="create-hub"></a>알림 허브 만들기

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##푸시 알림에 대해 앱 등록

Azure에서 Windows 앱으로 푸시 알림을 보내기 전에 앱을 Windows 스토어에 제출해야 합니다. 그런 다음 서버 프로젝트를 구성하여 WNS와 통합할 수 있습니다.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##푸시 알림을 전송하도록 백 엔드 구성

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>푸시 알림을 전송하도록 서버 업데이트

이제 앱에서 푸시 알림을 사용하도록 설정했으므로 푸시 알림을 전송하도록 앱 백 엔드를 업데이트해야 합니다.

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


## <a name="publish-the-service"></a>Azure에 모바일 백 엔드 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>앱에 푸시 알림 추가

1. 공유 **App.xaml.cs** 프로젝트 파일을 열고 다음 `using` 문을 추가합니다.

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. 동일한 파일에서 다음 **InitNotificationsAsync** 메서드 정의를 **App** 클래스에 추가합니다.
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 앱 서비스 모바일 앱에 등록합니다.
    
3. **App.xaml.cs**의 **OnLaunched** 이벤트 처리기 맨 위에서 다음 예제와 같이 메서드 정의에 **async** 한정자를 추가하고 새 **InitNotificationsAsync** 메서드에 다음 호출을 추가합니다.

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    이제 응용 프로그램을 시작할 때마다 단기 ChannelURI가 등록됩니다.

4. 솔루션 탐색기에서 Windows 스토어 앱의 **Package.appxmanifest**를 두 번 클릭하고 **알림**에서 **알림 가능**을 **예**로 설정합니다.

    **파일** 메뉴에서 **모두 저장**을 클릭합니다.

5. Windows Phone 스토어 앱 프로젝트에서 이전 단계를 반복합니다.

이제 앱에서 알림을 받을 수 있습니다.

##<a id="test"></a>앱에서 푸시 알림 테스트

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

##다음 단계

마지막으로, 이 자습서의 [특정 사용자에게 플랫폼 간 알림 보내기](app-service-mobile-windows-store-dotnet-push-notifications-to-users.md)에서는 모든 장치 플랫폼에서 인증된 특정 사용자에 속하는 모든 장치 등록에 푸시 알림을 전송하는 방법에 대해 설명합니다.

##<a id="more"></a>추가

* 템플릿은 유연성을 제공하여 플랫폼간 푸시 및 지역화된 푸시를 보냅니다. [Azure 모바일 앱에 관리된 클라이언트를 사용하는 방법](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)은 템플릿을 등록하는 방법을 보여줍니다.
* 태그를 사용하면 푸시를 사용하여 여러 조각으로 나뉜 고객을 대상으로 할 수 있습니다. [Azure 모바일 앱에 대해 .NET 백 엔드 서버 SDK로 작업](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags)은 장치 설치에 태그를 추가하는 방법을 보여줍니다.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_1203_2015-->