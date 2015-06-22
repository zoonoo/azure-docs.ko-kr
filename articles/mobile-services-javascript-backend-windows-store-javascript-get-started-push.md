<properties 
	pageTitle="모바일 서비스 앱에 푸시 알림 추가(Windows 스토어) | 모바일 개발자 센터" 
	description="Azure 모바일 서비스와 알림 허브를 사용하여 Windows 스토어 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="glenga"/>


# 모바일 서비스 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 빠른 시작 프로젝트에 대한 Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 알림 허브를 사용하여 푸시 알림을 전송합니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서도 사용 가능합니다.

>[AZURE.NOTE]이 항목에서는 Windows 스토어 앱용 WNS(Windows 알림 서비스)를 사용하여 푸시 알림을 수동으로 구성하는 방법을 설명합니다. Visual Studio 2013 도구를 사용하여 Windows 앱 프로젝트에서 동일한 푸시 알림을 자동으로 구성할 수 있습니다. 자세한 내용은 이 자습서의 [범용 Windows 앱 버전](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md)을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [WNS로 앱 등록 및 모바일 서비스 구성](#register)
2. [알림 등록을 위해 앱 업데이트](#update-app)
3. [푸시 알림을 전송하도록 서버 스크립트 업데이트](#update-scripts)
3. [푸시 알림을 받기 위한 데이터 삽입](#test)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기] 또는 [데이터 시작하기]를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 모바일 서비스가 연결되지 않은 경우 푸시 알림 추가 마법사에서 대신 연결해줍니다.

## <a id="register"></a> WNS로 앱 등록 및 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

이제 모바일 서비스와 앱이 둘 다 WNS와 알림 허브를 사용하도록 구성되었습니다. 다음에는 알림 등록을 위해 Windows 스토어 앱을 업데이트합니다.

## <a id="update-app"></a> 알림 등록을 위해 앱 업데이트

앱에서 푸시 알림을 받을 수 있으려면 알림 채널을 등록해야 합니다.

1. default.js 파일을 열고 **MobileServiceClient** 인스턴스를 만드는 코드 뒤에 다음 코드를 삽입합니다.

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);                    
            });      

	이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 푸시 알림에 대해 등록합니다.

2. **F5** 키를 눌러 앱을 실행합니다. 등록 키가 포함된 팝업 대화 상자가 표시됩니다.

6. Package.appxmanifest 파일을 열고 **Application UI** 탭에서 **Toast capable**이 **Yes**로 설정되어 있는지 확인합니다.

   	![][2]

   	이제 앱에서 알림 메시지를 표시할 수 있습니다.

## <a id="update-scripts"></a> 푸시 알림을 전송하도록 서버 스크립트 업데이트

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <a id="test"></a> 앱에서 푸시 알림 테스트

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스 및 알림 허브를 사용하여 푸시 알림을 보내도록 설정하기 위한 기본 사항에 대해 설명했습니다. 다음으로는 태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 보여 주는 다음 자습서인 [인증된 사용자에게 푸시 알림 보내기]를 완료할 수 있습니다.

<!---+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [데이터 시작] <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법을 자세히 알아봅니다.

* [인증 시작] <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [알림 허브 정의] <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [알림 허브 응용 프로그램 디버깅](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>알림 허브 솔루션 문제를 해결하고 디버깅하기 위한 지침을 얻습니다.

* [모바일 서비스 .NET 방법 개념 참조] <br/>.NET과 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

* [모바일 서비스 서버 스크립트 참조] <br/>모바일 서비스에서 비즈니스 논리를 구현하는 방법에 대해 자세히 알아봅니다.

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[모바일 서비스 시작하기]: mobile-services-windows-store-get-started.md
[데이터 시작]: mobile-services-windows-store-javascript-get-started-data.md
[데이터 시작하기]: mobile-services-windows-store-javascript-get-started-data.md
[인증 시작]: mobile-services-windows-store-javascript-get-started-users.md

[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[모바일 서비스 .NET 방법 개념 참조]: mobile-services-windows-dotnet-how-to-use-client-library.md


[인증된 사용자에게 푸시 알림 보내기]: mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users.md

[알림 허브 정의]: notification-hubs/notification-hubs-overview.md
[Send broadcast notifications to subscribers]: notification-hubs-windows-store-javascript-send-breaking-news.md
[Send template-based notifications to subscribers]: notification-hubs-windows-store-javascript-send-localized-breaking-news.md

<!--HONumber=54-->