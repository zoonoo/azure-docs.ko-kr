<properties
	pageTitle="인증 시작(Windows Phone) | Microsoft Azure"
	description="모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Windows Phone 앱 사용자를 인증하는 방법을 알아봅니다."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>

# 모바일 서비스 앱에 인증 추가

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

##개요

이 항목은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

또한 다음 비디오에서 Nick Harris가 이 자습서를 데모합니다.

> [AZURE.VIDEO mobile-authorize-users-in-scripts-windows-phone]

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [기존 앱에 모바일 서비스 추가] 자습서도 완료해야 합니다.

>[AZURE.NOTE]이 자습서는 다양한 ID 공급자를 사용하여 모바일 서비스에서 관리하는 인증 흐름을 보여 줍니다. 이 방법은 간편하게 구성할 수 있으며 여러 공급자를 지원합니다. 클라이언트 관리 인증을 사용하면 앱에서 ID 공급자를 통해 유지 관리되는 추가적인 사용자 데이터에 액세스할 수 있게 됩니다. 서버 스크립트에서 **user.getIdentities()** 함수를 호출하여 모바일 서비스에서 동일한 사용자 데이터를 가져올 수 있습니다. 자세한 내용은 [이 게시물](http://go.microsoft.com/fwlink/p/?LinkId=506605)을 참조하십시오.

##<a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


&nbsp;&nbsp;3. Visual Studio에서 [기존 앱에 모바일 서비스 추가](mobile-services-windows-phone-get-started-data.md) 자습서를 완료할 때 만든 프로젝트를 엽니다.

&nbsp;&nbsp;4. F5 키를 눌러 이 빠른 시작 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>클라이언트에 권한 부여 토큰 저장

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여](mobile-services-javascript-backend-service-side-authorization.md)에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[기존 앱에 모바일 서비스 추가]: mobile-services-windows-phone-get-started-data.md
[Authorize users with scripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

<!---HONumber=AcomDC_0727_2016-->