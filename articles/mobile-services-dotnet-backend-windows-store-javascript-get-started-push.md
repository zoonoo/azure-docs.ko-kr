<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# 모바일 서비스에서 푸시 알림 시작

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][mobile-services-selector-get-started-push-legacy]]

이 항목에서는 .NET 백 엔드와 함께 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 빠른 시작 프로젝트에 대한 Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 알림 허브를 사용하여 .Net 백 엔드에서 푸시 알림을 전송합니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서도 사용 가능합니다.

> [WACOM.NOTE]이 항목에서는 Windows 스토어 앱용 WNS(Windows 알림 서비스)를 사용하여 푸시 알림을 수동으로 구성하는 방법을 설명합니다. Visual Studio 2013 도구를 사용하여 Windows 앱 프로젝트에서 동일한 푸시 알림을 자동으로 구성할 수 있습니다. 자세한 내용은 이 자습서의 [범용 Windows 앱 버전][범용 Windows 앱 버전]을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [WNS로 앱 등록 및 모바일 서비스 구성][WNS로 앱 등록 및 모바일 서비스 구성]
2.  [알림 등록을 위해 앱 업데이트][알림 등록을 위해 앱 업데이트]
3.  [푸시 알림을 전송하도록 서버 업데이트][푸시 알림을 전송하도록 서버 업데이트]
4.  [로컬 테스트에 푸시 알림 사용][로컬 테스트에 푸시 알림 사용]
5.  [푸시 알림을 받기 위한 데이터 삽입][푸시 알림을 받기 위한 데이터 삽입]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][모바일 서비스 시작하기] 또는 [데이터 시작하기][데이터 시작하기]를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 모바일 서비스가 연결되지 않은 경우 푸시 알림 추가 마법사에서 대신 연결해줍니다.

## <span id="register"></span></a> WNS로 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app][mobile-services-notification-hubs-register-windows-store-app]]

이제 모바일 서비스와 앱이 둘 다 WNS와 알림 허브를 사용하도록 구성되었습니다. 다음에는 알림 등록을 위해 Windows 스토어 앱을 업데이트합니다.

## <span id="update-app"></span></a> 알림 등록을 위해 앱 업데이트

앱에서 푸시 알림을 받을 수 있으려면 알림 채널을 등록해야 합니다.

1.  default.js 파일을 열고 **MobileServiceClient** 인스턴스를 만드는 코드 뒤에 다음 코드를 삽입합니다. 이 코드는 푸시 알림 채널을 만들고 푸시 알림을 등록합니다.

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);
            }, function (error) {
                var message = "Registration failed: " + error.message;
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.showAsync();
            });

    이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 푸시 알림에 대해 등록합니다. 등록이 실패하면 메시지 대화 상자에 오류 메시지가 표시됩니다.

2.  Visual Studio에서 Package.appxmanifest 파일을 열고 **응용 프로그램 UI** 탭에서 **알림 가능**이 **예**로 설정되어 있는지 확인합니다.

    ![][]

    이제 앱에서 알림 메시지를 표시할 수 있습니다. 이러한 알림은 다운로드한 빠른 시작 프로젝트에서 이미 사용되고 있습니다.

## <span id="update-server"></span></a> 푸시 알림을 전송하도록 서버 업데이트

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push][mobile-services-dotnet-backend-update-server-push]]

## <span id="local-testing"></span></a> 로컬 테스트에 푸시 알림 사용

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][mobile-services-dotnet-backend-configure-local-push]]

## <span id="test"></span></a>앱에서 푸시 알림 테스트

[WACOM.INCLUDE [mobile-services-windows-store-test-push][mobile-services-windows-store-test-push]]

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스와 알림 허브를 사용하여 푸시 알림을 보내도록 하기 위한 기본 사항을 알아보았습니다. 이후에는 다음 자습서 중 하나를 완료하는 것이 좋습니다.

-   [인증된 사용자에게 푸시 알림 보내기][인증된 사용자에게 푸시 알림 보내기]
    <br/>태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 알아보세요.

다음의 모바일 서비스 및 알림 허브 항목에 대해서도 자세히 알아보세요.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

-   [인증 시작][인증 시작]
    모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

-   [알림 허브 정의][알림 허브 정의]
    모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

-   [모바일 서비스 HTML/JavaScript 방법 개념 참조][모바일 서비스 HTML/JavaScript 방법 개념 참조]
    JavaScript 앱와 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보세요.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [범용 Windows 앱 버전]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push
  [WNS로 앱 등록 및 모바일 서비스 구성]: #register
  [알림 등록을 위해 앱 업데이트]: #update-app
  [푸시 알림을 전송하도록 서버 업데이트]: #update-server
  [로컬 테스트에 푸시 알림 사용]: #local-testing
  [푸시 알림을 받기 위한 데이터 삽입]: #test
  [모바일 서비스 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  []: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [인증된 사용자에게 푸시 알림 보내기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
  [모바일 서비스 HTML/JavaScript 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-html-how-to-use-client-library
