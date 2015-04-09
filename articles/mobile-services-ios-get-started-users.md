<properties
	pageTitle="기존 Azure 모바일 서비스 앱에 인증 추가(iOS) | 모바일 개발자 센터"
	description="모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 iOS 앱 사용자를 인증하는 방법을 알아봅니다."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="2/16/2015"
	ms.author="krisragh"/>

# 기존 Azure 모바일 서비스 앱에 인증 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

이 자습서에서는 지원되는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다.

이 자습서는 먼저 완료해야 하는 [모바일 서비스 퀵 스타트 자습서]를 기반으로 합니다.

##<a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

Xcode에서 프로젝트를 엽니다. **실행** 단추를 눌러 앱을 시작합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 예외가 발생하는지 확인합니다. 이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 _TodoItem_ 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>앱에 인증 토큰 저장

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여]에서는 사용자 ID 값을 사용하여 반환되는 데이터를 필터링합니다.

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[다음 단계]:#next-steps
[앱에 인증 토큰 저장]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[모바일 서비스 사용자의 서비스 쪽 권한 부여]: mobile-services-javascript-backend-service-side-authorization.md
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[모바일 서비스 퀵 스타트 자습서]: /develop/mobile/tutorials/get-started-ios
[데이터 시작]: /develop/mobile/tutorials/get-started-with-data-ios
[인증 시작]: /develop/mobile/tutorials/get-started-with-users-ios
[푸시 알림 시작]: /develop/mobile/tutorials/get-started-with-push-ios
[스크립트를 통해 사용자 권한 부여]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure 관리 포털]: https://manage.windowsazure.com/

<!--HONumber=49-->