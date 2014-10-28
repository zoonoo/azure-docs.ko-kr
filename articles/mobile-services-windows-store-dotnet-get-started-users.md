<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey"></tags>

# 모바일 서비스에서 인증 시작

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

<div class="dev-onpage-video-clear clearfix">

<div class="dev-onpage-left-content">

이 항목은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

오른쪽에 있는 클립을 클릭하여 이 자습서의 동영상 버전을 볼 수 있습니다.

</div>

<div class="dev-onpage-video-wrapper">

[자습서 보기][자습서 보기] [<span class="icon">동영상 재생</span>][<span class="icon">동영상 재생</span>] <span class="time">10:04</span>

</div>

</div>

> [WACOM.NOTE]모바일 서비스 JavaScript 클라이언트 라이브러리를 사용하는 경우에는 현재 Windows Phone Store 8.1 앱에서 인증이 지원되지 않습니다. JavaScript 클라이언트를 사용하는 범용 Windows 앱 프로젝트가 있는 경우에는 현재 Windows Phone에서 사용자를 인증할 수 없습니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성][인증을 위해 앱 등록 및 모바일 서비스 구성]
2.  [테이블 사용 권한을 인증된 사용자로 제한][테이블 사용 권한을 인증된 사용자로 제한]
3.  [앱에 인증 추가][앱에 인증 추가]
4.  [클라이언트에 인증 토큰 저장][클라이언트에 인증 토큰 저장]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료해야 합니다.

> [WACOM.NOTE]이 자습서에서는 모바일 서비스에서 다양한 ID 공급자를 사용하여 관리하는 인증 흐름을 보여 줍니다. 이 방법은 간편하게 구성할 수 있으며 여러 공급자를 지원합니다. 대신 Live Connect와 클라이언트 관리 인증을 함께 사용하고 Windows Phone 앱에서 Single Sign-On을 제공하려면 [Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On][Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On] 항목을 참조하십시오. 클라이언트 관리 인증을 사용하면 앱에서 ID 공급자를 통해 유지 관리되는 추가적인 사용자 데이터에 액세스할 수 있게 됩니다. 서버 스크립트에서 **user.getIdentities()** 함수를 호출하여 모바일 서비스에서 동일한 사용자 데이터를 가져올 수 있습니다. 자세한 내용은 [이 게시물][이 게시물]을 참조하십시오.

## <a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

1.  (옵션) [Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록][Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]의 단계를 완료합니다.

    <div class="dev-callout">

    **참고**
    이 단계는 Microsoft 계정 로그인 공급자에만 적용되기 때문에 선택 사항입니다. 모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록하는 경우 클라이언트에서 Single Sign-On 환경을 위해 Microsoft 계정 로그인 자격 증명을 다시 사용할 수 있습니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 Microsoft 계정 로그인 사용자에게 로그인 프롬프트가 표시됩니다. Microsoft 계정 ID 공급자를 사용하려는 경우 이 단계를 완료합니다.

    </div>

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  Visual Studio 2012 Express for Windows 8에서 [모바일 서비스 시작][1] 자습서를 완료할 때 만든 프로젝트를 엽니다.

2.  F5 키를 눌러 이 빠른 시작 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a>앱에 인증 추가

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app][mobile-services-windows-dotnet-authenticate-app]]

## <a name="tokens"></a>클라이언트에 권한 부여 토큰 저장

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token][mobile-services-windows-store-dotnet-authenticate-app-with-token]]

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여][모바일 서비스 사용자의 서비스 쪽 권한 부여]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다. .NET과 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 .NET 방법 개념 참조][모바일 서비스 .NET 방법 개념 참조]를 참조하십시오.

<!-- Anchors. --> <!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [자습서 보기]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services
  [<span class="icon">동영상 재생</span>]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services
  [인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
  [테이블 사용 권한을 인증된 사용자로 제한]: #permissions
  [앱에 인증 추가]: #add-authentication
  [클라이언트에 인증 토큰 저장]: #tokens
  [모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-get-started/
  [Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [이 게시물]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]: /ko-kr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /ko-kr/documentation/articles/mobile-services-windows-store-get-started
  [mobile-services-windows-dotnet-authenticate-app]: ../includes/mobile-services-windows-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [모바일 서비스 사용자의 서비스 쪽 권한 부여]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library
