<properties pageTitle="인증 시작(Windows 스토어) | 모바일 개발자 센터" description="모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Windows 스토어 앱 사용자를 인증하는 방법에 대해 알아봅니다." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"/>

# 모바일 서비스 앱에 인증 추가 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>이 항목은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.</p>
<p>오른쪽에 있는 클립을 클릭하여 이 자습서의 동영상 버전을 볼 수 있습니다.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">비디오 재생</span></a> <span class="time">오전 10:04</span></div>
</div> 

>[AZURE.NOTE]모바일 서비스 JavaScript 클라이언트 라이브러리를 사용하는 경우에는 현재 Windows Phone 스토어 8.1 앱에서 인증이 지원되지 않습니다. JavaScript 클라이언트를 사용하는 범용 Windows 앱 프로젝트가 있는 경우에는 현재 Windows Phone에서 사용자를 인증할 수 없습니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증을 위해 앱 등록 및 모바일 서비스 구성]
2. [테이블 사용 권한을 인증된 사용자로 제한]
3. [앱에 인증 추가]
5. [클라이언트에 인증 토큰 저장]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다. 

>[AZURE.NOTE]이 자습서는 다양한 ID 공급자를 사용하여 모바일 서비스에서 관리하는 인증 흐름을 보여 줍니다. 이 방법은 간편하게 구성할 수 있으며 여러 공급자를 지원합니다. 대신 Live Connect와 클라이언트 관리 인증을 사용하고 Windows Phone 앱에서 Single Sign-On 환경을 제공하려면 [Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On] 항목을 참조하세요. 클라이언트 관리 인증을 사용하는 경우 앱은 ID 공급자가 유지 관리하는 추가 사용자 데이터에 액세스할 수 있습니다. 서버 스크립트에서 **user.getIdentities()** 함수를 호출하면 모바일 서비스에서 와 같은 사용자 데이터를 가져올 수 있습니다. 자세한 내용은 [이 게시물](http://go.microsoft.com/fwlink/p/?LinkId=506605)(영문)을 참조하세요.

##<a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(선택 사항) <a href="/ko-kr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록</a>의 단계를 완료합니다.</p>

	<p>이 단계는 Microsoft 계정 로그인 공급자에만 적용되기 때문에 선택 사항입니다. 모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록하는 경우 클라이언트에서 Single Sign-On 환경을 위해 Microsoft 계정 로그인 자격 증명을 다시 사용할 수 있습니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 Microsoft 계정 로그인 사용자에게 로그인 프롬프트가 표시됩니다. Microsoft 계정 ID 공급자를 사용하려는 경우 이 단계를 완료합니다.</p>
    
</li>
</ol>

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Visual Studio 2012 Express for Windows 8에서 <a href="/ko-kr/documentation/articles/mobile-services-windows-store-get-started">모바일 서비스 시작</a> 자습서를 완료할 때 만든 프로젝트를 엽니다.</p></li> 
<li><p>F5 키를 눌러 이 퀵 스타트 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.</p>
   
   	<p>이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 <em>TodoItem</em> 테이블에서 이제 인증을 요구하기 때문에 발생합니다.</p></li>
</ol>

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[AZURE.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>클라이언트에 권한 부여 토큰 저장

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다. .NET과 함께 모바일 서비스를 사용하는 방법에 대한 자세한 내용은 [모바일 서비스 .NET 방법 개념 참조]를 참조하세요.

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[클라이언트에 인증 토큰 저장]: #tokens
[다음 단계]:#next-steps


<!-- URLs. -->
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[모바일 서비스 시작](영문): /ko-kr/documentation/articles/mobile-services-windows-store-get-started/
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[인증 시작](영문): /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/
[스크립트를 통해 사용자 권한 부여]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
[JavaScript 및 HTML]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/

[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library
[Microsoft 인증을 위해 Windows 스토어 앱 패키지 등록]: /ko-kr/develop/mobile/how-to-guides/register-windows-store-app-package


<!--HONumber=42-->
