<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="인증 시작(Android) | 모바일 개발자 센터" metaKeywords="" description="모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Android 앱 사용자를 인증하는 방법에 대해 알아봅니다." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal" />

# 모바일 서비스 앱에 인증 추가

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>이 항목에서는 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">동영상 재생</span></a><span class="time">10:42</span></div>
</div> 

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증을 위해 앱 등록 및 모바일 서비스 구성]
2. [테이블 사용 권한을 인증된 사용자로 제한]
3. [앱에 인증 추가]
4. [클라이언트에 권한 부여 토큰 저장]
5. [만료된 토큰 새로 고침]

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다. 

>[AZURE.NOTE] 완성된 앱의 소스 코드를 참조하려는 경우 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithAuth/Android" target="_blank">여기</a>로 이동하세요.

이 자습서를 완료하려면 Eclipse 및 Android 4.2 이상 버전이 필요합니다. 

<h2><a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>사용 권한을 인증된 사용자로 제한</h2>

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

3. Eclipse에서 [모바일 서비스 시작] 자습서를 완료했을 때 생성된 프로젝트를 엽니다. 

4. **실행** 메뉴에서 **실행**을 클릭하여 앱을 시작하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 

	 이러한 현상은 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 하지만 _TodoItem_ 테이블이 현재 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

<h2><a name="add-authentication"></a>앱에 인증 추가</h2>

[WACOM.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>클라이언트에 권한 부여 토큰 저장

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>토큰 캐시 새로 고침

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 



## <a name="next-steps"></a>다음 단계

다음 자습서인 [스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다. 

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[클라이언트에 권한 부여 토큰 저장]: #cache-tokens
[만료된 토큰 새로 고침]: #refresh-tokens
[다음 단계]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect를 사용한 Windows 스토어 앱에 대한 Single Sign-On]: /ko-kr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started-android
[데이터 작업 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-android
[인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-android
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-android
[스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-android

[Azure 관리 포털]: https://manage.windowsazure.com/
