<properties pageTitle="인증된 사용자에게 푸시 알림 보내기" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="특정 사용자에게 푸시 알림을 보내는 방법에 대해 알아봅니다. " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# 인증된 사용자에게 푸시 알림 보내기

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

이 항목에서는 등록된 모든 장치에서 인증된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 이전의 [푸시 알림][푸시 알림 시작] 자습서와 달리, 이 자습서에서는 먼저 사용자가 인증된 후에야 클라이언트가 푸시 알림을 받기 위해 알림 허브에 등록할 수 있도록 모바일 서비스를 변경합니다. 또한 할당된 사용자 ID를 기반으로 태그를 추가하도록 등록이 수정됩니다. 마지막에는 모든 등록 대신 인증된 사용자에게만 알림을 보내도록 서버 코드가 업데이트됩니다.

이 자습서에서는 다음 프로세스를 단계별로 안내합니다.

+ [등록에 인증이 필요하도록 서비스 업데이트]
+ [등록 전에 로그인하도록 앱 업데이트]
+ [앱 테스트]
 
이 자습서에서는 Windows Phone 8.0 및 Windows Phone 8.1("Silverlight") 앱이 지원됩니다. Windows Phone 8.1 스토어 앱의 경우 [이 항목의 Windows 스토어 버전](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users)을 참조하세요.

##필수 조건 

이 자습서를 시작하려면 먼저 다음 모바일 서비스 자습서를 완료해야 합니다.

+ [인증 시작]<br/>TodoList 샘플 앱에 로그인 요구 사항을 추가합니다.

+ [푸시 알림 시작]<br/>알림 허브를 사용하여 푸시 알림에 맞게 TodoList 샘플 앱을 구성합니다. 

두 자습서를 모두 완료한 다음에는 인증되지 않은 사용자가 모바일 서비스의 푸시 알림을 등록할 수 없도록 방지할 수 있습니다.

##<a name="register"></a>등록에 인증이 필요하도록 서비스 업데이트

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

##<a name="update-app"></a>등록 전에 로그인하도록 앱 업데이트

[WACOM.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../includes/mobile-services-windows-phone-push-notifications-app-users.md)]


##<a name="test"></a>앱 테스트

[WACOM.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"> </a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다. .NET과 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 .NET 방법 개념 참조]를 참조하세요.

<!-- Anchors. -->
[등록에 인증이 필요하도록 서비스 업데이트]: #register
[등록 전에 로그인하도록 앱 업데이트]: #update-app
[앱 테스트]: #test
[다음 단계]:#next-steps


<!-- URLs. -->
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/

[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=35.2-->
