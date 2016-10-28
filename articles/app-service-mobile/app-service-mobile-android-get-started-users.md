<properties
	pageTitle="모바일 앱을 사용하여 Android에 인증 추가 | Azure 앱 서비스"
	description="Azure 앱 서비스에서 모바일 앱을 사용하여 Google, Facebook, Twitter, Microsoft를 비롯한 다양한 ID 공급자를 통해 Android 앱의 사용자를 인증하는 방법을 알아봅니다."
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="ricksal"/>

# Android 앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## 요약

이 자습서에서는 지원되는 ID 공급자를 사용하여 Android의 할 일 모음 빠른 시작 프로젝트에 인증을 추가합니다. 이 자습서는 [모바일 앱 시작] 자습서를 기반으로 하며 이를 먼저 완료해야 합니다.

##<a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Android Studio에서 [모바일 앱 시작] 자습서를 완료했을 때 만든 프로젝트를 열고 **실행** 메뉴에서 **앱 실행**을 클릭하며 앱이 시작된 후에 상태 코드 401(인증되지 않음)을 통해 처리되지 않은 예외가 발생하는지 확인합니다.

	 이는 앱이 인증되지 않은 사용자로 백 엔드에 액세스하려고 시도하지만 _할 일 모음_ 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음으로 앱을 업데이트하여 모바일 앱 백 엔드에서 리소스를 요청하기 전에 사용자를 인증하도록 합니다.

## 앱에 인증 추가

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>클라이언트에 인증 토큰 캐시

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##다음 단계

이 기본 인증 자습서를 완료했으므로 다음 자습서 중 하나를 계속하는 것을 고려해보세요.

+ [Android 앱에 푸시 알림 추가](app-service-mobile-android-get-started-push.md) 앱에 푸시 알림 지원을 추가하고 모바일 앱 백 엔드를 구성하여 푸시 알림을 보내는 Azure 알림 허브를 사용하는 방법을 알아봅니다.

+ [Android 앱에 오프라인 동기화 사용](app-service-mobile-android-get-started-offline-data.md) 모바일 앱 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[모바일 앱 시작]: app-service-mobile-android-get-started.md

<!---HONumber=AcomDC_0720_2016-->