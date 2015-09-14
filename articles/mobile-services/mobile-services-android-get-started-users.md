<properties
	pageTitle="인증 시작(Android) | Microsoft Azure"
	description="모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Android 앱 사용자를 인증하는 방법을 알아봅니다."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="ricksal"/>

# 모바일 서비스 Android 앱에 인증 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## 요약

이 항목은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

> [AZURE.VIDEO android-getting-started-with-authentication-in-windows-azure-mobile-services]

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.


##필수 조건

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## 인증을 위해 앱 등록 및 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## 사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1. Android Studio에서 [모바일 서비스 시작] 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

2. **실행** 메뉴에서 **앱 실행**을 클릭하여 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

	 이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 _TodoItem_ 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## 앱에 인증 추가

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>클라이언트에 인증 토큰 캐시

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../../includes/mobile-services-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>토큰 캐시 새로 고침

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../../includes/mobile-services-android-authenticate-app-refresh-token.md)]



## <a name="next-steps"></a>다음 단계

다음 자습서인 [스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[모바일 서비스 시작]: /develop/mobile/tutorials/get-started-android
[Add Mobile Services to an existing app]: /develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-android
[스크립트를 통해 사용자 권한 부여]: /develop/mobile/tutorials/authorize-users-in-scripts-android

[Azure Management Portal]: https://manage.windowsazure.com/

<!---HONumber=September15_HO1-->