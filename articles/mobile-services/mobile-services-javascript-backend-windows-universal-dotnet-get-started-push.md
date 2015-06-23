<properties 
	pageTitle="JavaScript 백 엔드 모바일 서비스를 사용하여 푸시 알림 시작" 
	description="Azure 모바일 서비스와 알림 허브를 사용하여 범용 Windows 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="glenga"/>


# 모바일 서비스 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

이 항목에서는 JavaScript 백 엔드와 함께 Azure 모바일 서비스를 사용하여 범용 Windows 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 범용 Windows 앱 프로젝트에서 Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 TodoList 테이블에 레코드를 삽입할 때마다 모바일 서비스가 JavaScript 백 엔드에서 등록된 모든 Windows 스토어 및 Windows Phone 스토어 앱으로 푸시 알림을 보냅니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서도 사용 가능합니다.

>[AZURE.NOTE]이 항목에서는 Visual Studio 2013 업데이트 3의 도구를 사용하여 모바일 서비스에서 범용 Windows 앱으로의 푸시 알림에 대한 지원을 추가하는 방법을 보여 줍니다. 같은 단계를 사용하여 모바일 서비스에서 Windows 스토어 또는 Windows Phone 스토어 8.1 앱으로의 푸시 알림을 추가할 수 있습니다. Windows Phone 8 또는 Windows Phone Silverlight 8.1 앱으로의 푸시 알림을 추가하려면 [모바일 서비스에서 푸시 알림 시작](mobile-services-javascript-backend-windows-phone-get-started-push.md) 버전을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [푸시 알림에 대해 앱 등록](#register)
2. [푸시 알림을 전송하도록 서비스 업데이트](#update-service)
3. [앱에서 푸시 알림 테스트](#test)

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 [Microsoft 스토어 계정](http://go.microsoft.com/fwlink/p/?LinkId=280045)
* [Visual Studio 2013 Express for Windows](http://go.microsoft.com/fwlink/?LinkId=257546) 업데이트 3 이상 버전

## <a id="register"></a>푸시 알림에 대해 앱 등록

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p><code>\Services\MobileServices\your_service_name</code> 프로젝트 폴더로 이동하여 생성된 push.register.cs 코드 파일을 열고 장치의 채널 URL을 알림 허브에 등록하는 <strong>UploadChannel</strong> 메서드를 검사합니다.</p></li> 
<li><p>공유되는 App.xaml.cs 코드 파일을 열고 새 <strong>UploadChannel</strong> 메서드 호출이 <strong>OnLaunched</strong> 이벤트 처리기에 추가되었음을 확인합니다.</p> <p>따라서 앱을 시작할 때마다 장치 등록을 시도합니다.</p></li>
<li><p>이전 단계를 반복하여 Windows Phone 스토어 앱 프로젝트에 푸시 알림을 추가한 다음 공유되는 App.xaml.cs 파일에서 추가 <strong>UploadChannel</strong> 호출과 나머지 <code>#if...#endif</code> 조건부 래퍼를 제거합니다.</p> <p>이제 두 프로젝트가 모두 단일 <strong>UploadChannel</strong> 호출을 공유할 수 있습니다.</p>
<p><code>#if...#endif</code> 래핑 <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> 정의를 앱의 두 버전에서 모두 사용되는 래핑 해제된 단일 정의로 통합하여 생성되는 코드를 간소화할 수도 있습니다.</p></li>
</ol>

이제 앱에서 푸시 알림을 사용하도록 설정했으므로 모바일 서비스가 푸시 알림을 보내도록 업데이트해야 합니다.

## <a id="update-service"></a>푸시 알림을 전송하도록 서비스 업데이트

다음 단계에서는 TodoItem 테이블에 등록된 삽입 스크립트를 업데이트합니다. 모든 서버 스크립트 또는 백 엔드 서비스의 모든 위치에서도 비슷한 코드를 구현할 수 있습니다.

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]


## <a id="test"></a> 앱에서 푸시 알림 테스트

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스 및 알림 허브를 사용하여 푸시 알림을 보내도록 설정하기 위한 기본 사항에 대해 설명했습니다. 이후에는 다음 자습서 중 하나를 완료하는 것이 좋습니다.

+ [인증된 사용자에게 푸시 알림 보내기](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) <br/>태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법에 대해 알아봅니다.

+ [구독자에게 브로드캐스트 알림 보내기](../notification-hubs-windows-store-dotnet-send-breaking-news.md) <br/>관심이 있는 범주에 대해 푸시 알림을 등록하고 받아보는 방법에 대해 알아봅니다.

+ [구독자에게 플랫폼을 알 수 없는 알림 보내기](../notification-hubs-aspnet-cross-platform-notify-users.md) <br/>백 엔드에 플랫폼 전용 페이로드를 마련할 필요 없이 템플릿을 사용하여 모바일 서비스에서 푸시 알림을 보내는 방법에 대해 알아봅니다.

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [Azure 알림 허브-진단 지침](../notification-hubs-diagnosing.md) <br/>프로그램 푸시 알림 문제를 해결하는 방법에 대해 알아봅니다.

* [인증 시작] <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [알림 허브 정의] <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [Azure 모바일 서비스용 .NET 클라이언트를 사용하는 방법] <br/>C# Windows 앱에서 모바일 서비스를 사용하는 방법에 대해 자세히 알아봅니다.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md
[인증 시작]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md

[Send push notifications to authenticated users]: mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md

[알림 허브 정의]: ../notification-hubs-overview.md

[Azure 모바일 서비스용 .NET 클라이언트를 사용하는 방법]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54--> 