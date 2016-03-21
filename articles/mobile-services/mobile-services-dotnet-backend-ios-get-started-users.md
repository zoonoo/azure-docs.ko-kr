<properties
	pageTitle="기존 Azure 모바일 서비스 앱에 인증 추가(iOS) | .NET 백 엔드 | Microsoft Azure"
	description="모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 iOS 앱 사용자를 인증하는 방법을 알아봅니다."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/05/2016"
	ms.author="krisragh"/>

# 기존 Azure 모바일 서비스 앱에 인증 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 이 항목에 해당하는 모바일 앱 버전은 [iOS 앱에 인증 추가](../app-service-mobile/app-service-mobile-ios-get-started-users.md)를 참조하세요.
 
이 자습서에서는 지원되는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 이 자습서는 먼저 완료해야 하는 [모바일 서비스 퀵 스타트 자습서]를 기반으로 합니다.

##<a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

Xcode에서 프로젝트를 엽니다. **실행** 단추를 눌러 앱을 시작합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 예외가 발생하는지 확인합니다. 이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 _TodoItem_ 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>앱에 인증 토큰 저장

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여]에서는 사용자 ID 값을 사용하여 반환되는 데이터를 필터링합니다.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- URLs. -->
[모바일 서비스 사용자의 서비스 쪽 권한 부여]: mobile-services-dotnet-backend-service-side-authorization.md
[모바일 서비스 퀵 스타트 자습서]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0309_2016-->