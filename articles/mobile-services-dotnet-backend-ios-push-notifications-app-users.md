<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# 인증된 사용자에게 푸시 알림 보내기

<div class="dev-center-tutorial-selector sublanding">
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="Windows 스토어 C#">Windows 스토어 C#</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="Windows 스토어 JavaScript" >Windows 스토어 JavaScript</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS" class="current">iOS</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a></div>

이 항목에서는 등록된 모든 iOS 장치에서 인증된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 이전의 [푸시 알림][푸시 알림] 자습서와 달리 이 자습서에서는 클라이언트가 푸시 알림을 위해 알림 허브에 등록할 수 있기 전에 사용자 인증을 요구하도록 모바일 서비스를 변경합니다. 또한 할당된 사용자 ID를 기반으로 태그를 추가하도록 등록이 수정됩니다. 마지막에는 모든 등록 대신 인증된 사용자에게만 알림을 보내도록 서버 코드가 업데이트됩니다.

이 자습서에서는 다음 프로세스를 단계별로 안내합니다.

+ [등록에 인증이 필요하도록 서비스 업데이트][등록에 인증이 필요하도록 서비스 업데이트]
+ [등록 전에 로그인하도록 앱 업데이트][등록 전에 로그인하도록 앱 업데이트]
+ [앱 테스트][앱 테스트]

이 자습서에서는 Windows 스토어 및 Windows Phone 스토어 앱이 모두 지원됩니다.

## 필수 조건

이 자습서를 시작하려면 먼저 다음 모바일 서비스 자습서를 완료해야 합니다.

+ [인증 시작][인증 시작]<br/>TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

+ [푸시 알림 시작][푸시 알림]<br/>알림 허브를 사용해서 푸시 알림에 맞게 TodoList 샘플 앱을 구성합니다.

두 자습서를 모두 완료한 다음에는 인증되지 않은 사용자가 모바일 서비스의 푸시 알림을 등록할 수 없도록 방지할 수 있습니다.

## <a name="register"></a> 등록에 인증이 필요하도록 서비스 업데이트

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

## <a name="update-app"></a> 등록 전에 로그인하도록 앱 업데이트

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

## <a name="test"></a> 앱 테스트

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]


<!-- Anchors. --> 
<!-- URLs. -->

[Windows 스토어 C#]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "Windows 스토어 C#"
[Windows 스토어 JavaScript]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "Windows 스토어 JavaScript"
[Windows Phone]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
[iOS]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
[Android]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
[.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/ ".NET 백 엔드"
[JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/ "JavaScript 백 엔드"
[푸시 알림]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[등록에 인증이 필요하도록 서비스 업데이트]: #register
[등록 전에 로그인하도록 앱 업데이트]: #update-app
[앱 테스트]: #test
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
[mobile-services-ios-push-notifications-app-users-login]: ../includes/mobile-services-ios-push-notifications-app-users-login.md
[mobile-services-ios-push-notifications-app-users-test-app]: ../includes/mobile-services-ios-push-notifications-app-users-test-app.md
