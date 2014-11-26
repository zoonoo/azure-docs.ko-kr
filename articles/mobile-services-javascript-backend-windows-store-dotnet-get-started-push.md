<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# 모바일 서비스에서 푸시 알림 시작

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 범용 Windows 앱에 푸시 알림을 보내는 방법을 보여 줍니다.
이 자습서에서는 빠른 시작 프로젝트에 대한 Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 알림 허브를 사용하여 푸시 알림을 전송합니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서도 사용 가능합니다.

> [WACOM.NOTE]이 항목에서는 Windows 스토어 프로젝트에서 모바일 서비스를 통해 푸시 알림을 수동으로 구성할 때 사용하는 방법을 보여 줍니다. Visual Studio 2013을 사용하여 Windows 스토어 앱 프로젝트에 동일한 푸시 알림을 추가할 수 있습니다. 자세한 내용은 이 자습서의 [범용 Windows 앱 버전][범용 Windows 앱 버전]을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [WNS로 앱 등록 및 모바일 서비스 구성][WNS로 앱 등록 및 모바일 서비스 구성]
2.  [알림 등록을 위해 앱 업데이트][알림 등록을 위해 앱 업데이트]
3.  [푸시 알림을 전송하도록 서버 스크립트 업데이트][푸시 알림을 전송하도록 서버 스크립트 업데이트]
4.  [푸시 알림을 받기 위한 데이터 삽입][푸시 알림을 받기 위한 데이터 삽입]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][모바일 서비스 시작하기] 또는 [데이터 시작하기][데이터 시작하기]를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 모바일 서비스가 연결되지 않은 경우 푸시 알림 추가 마법사에서 대신 연결해줍니다.

> [WACOM.NOTE]이 항목은 Windows Phone 8.1 앱을 지원합니다. Windows Phone 8 또는 Windows Phone Silverlight 8.1 앱에 푸시 알림을 추가하려면 [모바일 서비스에서 푸시 알림 시작][모바일 서비스에서 푸시 알림 시작]을 참조하세요.

## <span id="register"></span></a> WNS로 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

이제 모바일 서비스와 앱이 둘 다 WNS와 알림 허브를 사용하도록 구성되었습니다. 다음에는 알림 등록을 위해 범용 Windows 스토어 앱을 업데이트합니다.

## <span id="update-app"></span></a> 알림 등록을 위해 앱 업데이트

앱에서 푸시 알림을 받을 수 있으려면 알림 채널을 등록해야 합니다.

1.  Visual Studio에서 App.xaml.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  **App** 클래스에 다음 메서드를 추가합니다.

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel =
                await PushNotificationChannelManager
                    .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
        }

    이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 푸시 알림에 대해 등록합니다.

3.  App.xaml.cs에서 **OnLaunched** 이벤트 처리기의 맨 위에 다음과 같은 새 **InitNotificationsAsync** 메서드 호출을 추가합니다.

        InitNotificationsAsync();

    이제 페이지가 로드될 때마다 등록이 요청됩니다. 등록 상태가 유지되도록 앱에서 주기적으로 등록할 수도 있습니다.

4.  **F5** 키를 눌러 앱을 실행합니다. 등록 키가 포함된 팝업 대화 상자가 표시됩니다.

5.  (옵션) 관리 포털에서 생성한 빠른 시작 프로젝트를 사용하지 않는 경우 Package.appxmanifest 파일을 열고 **응용 프로그램 UI** 탭에서 **알림 가능**이 **예**로 설정되어 있는지 확인합니다.

    ![][0]

    이제 앱에서 알림 메시지를 표시할 수 있습니다. 이러한 알림은 다운로드한 빠른 시작 프로젝트에서 이미 사용되고 있습니다.

## <span id="update-scripts"></span></a> 푸시 알림을 전송하도록 스크립트 업데이트

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>앱에서 푸시 알림 테스트

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스와 알림 허브를 사용하여 푸시 알림을 보내도록 하기 위한 기본 사항을 알아보았습니다. 이후에는 다음 자습서 중 하나를 완료하는 것이 좋습니다.

-   [인증된 사용자에게 푸시 알림 보내기][인증된 사용자에게 푸시 알림 보내기]
    태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 알아보세요.

-   [구독자에게 브로드캐스트 알림 보내기][구독자에게 브로드캐스트 알림 보내기]
    관심이 있는 범주에 대해 푸시 알림을 등록하고 받아보는 방법에 대해 알아보세요.

-   [구독자에게 템플릿 기반 알림 보내기][구독자에게 템플릿 기반 알림 보내기]
    백 엔드에 플랫폼 전용 페이로드를 마련할 필요 없이 템플릿을 사용하여 모바일 서비스에서 푸시 알림을 보내는 방법에 대해 알아보세요.

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

-   [인증 시작][인증 시작]
    모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

-   [알림 허브 정의][알림 허브 정의]
    모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

-   [모바일 서비스 .NET 방법 개념 참조][모바일 서비스 .NET 방법 개념 참조]
    .NET과 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보세요.

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]
    모바일 서비스에서 비즈니스 논리를 구현하는 방법에 대해 자세히 알아봅니다.



  [범용 Windows 앱 버전]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [WNS로 앱 등록 및 모바일 서비스 구성]: #register
  [알림 등록을 위해 앱 업데이트]: #update-app
  [푸시 알림을 전송하도록 서버 스크립트 업데이트]: #update-scripts
  [푸시 알림을 받기 위한 데이터 삽입]: #test
  [모바일 서비스 시작하기]: /ko-kr/documentation/articles/mobile-services-windows-store-get-started
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [모바일 서비스에서 푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [인증된 사용자에게 푸시 알림 보내기]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [구독자에게 브로드캐스트 알림 보내기]: /ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
  [구독자에게 템플릿 기반 알림 보내기]: /ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
  [알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
  [모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
