<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure Javascript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Live Connect Single Sign-On으로 Windows 스토어 앱 인증

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ko-kr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows 스토어 C#">Windows 스토어 C#</a><a href="/ko-kr/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows 스토어 JavaScript" class="current">Windows 스토어 JavaScript</a><a href="/ko-kr/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone" class="current">Windows Phone</a>
</div>

이 항목에서는 Windows 스토어 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 Live Connect를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. Live Connect에 의해 인증되고 나면 로그인한 사용자는 이름과 함께 환영의 메시지를 보게 되고 사용자 ID 값이 표시됩니다.

> [WACOM.NOTE]이 자습서에서는 Windows 스토어 앱용 Live Connect에서 제공되는 Single Sign-On 환경을 사용할 때 얻는 이점을 보여 줍니다. 이 환경은 모바일 서비스를 사용해 이미 로그온한 사용자를 더 쉽게 인증할 수 있습니다. 여러 인증 공급자를 지원하는, 보다 일반화된 인증 환경을 알아보려면 [인증 시작][인증 시작] 항목을 참조하십시오.

이 자습서에서는 Live Connect 인증을 사용하도록 설정하는 다음 기본 단계를 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성][인증을 위해 앱 등록 및 모바일 서비스 구성]
2.  [테이블 사용 권한을 인증된 사용자로 제한][테이블 사용 권한을 인증된 사용자로 제한]
3.  [앱에 인증 추가][앱에 인증 추가]

이 자습서를 사용하려면 다음이 필요합니다.

-   [Windows용 Live SDK][Windows용 Live SDK]
-   Microsoft Visual Studio 2012 Express for Windows 8 RC 이상 버전

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료해야 합니다.

> [WACOM.NOTE]이 자습서에서는 JavaScript 백 엔드 모바일 서비스를 사용합니다. Live Connect 또는 다른 인증 클라이언트를 사용한 클라이언트 관리 인증은 아직 .NET 백 엔드 모바일 서비스에서 지원되지 않습니다.

## <a name="register"></a>Windows 스토어에 앱 등록

사용자를 인증할 수 있으려면 Windows 스토어에 앱을 제출해야 합니다. 그런 다음 Live Connect와 모바일 서비스를 통합하도록 클라이언트 암호를 등록해야 합니다.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Visual Studio 2012 Express for Windows 8에서 [모바일 서비스 시작][1] 자습서를 완료할 때 만든 프로젝트를 엽니다.

2.  F5 키를 눌러 이 빠른 시작 기반 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a>앱에 인증 추가

1.  [Windows용 Live SDK][Windows용 Live SDK]를 다운로드하여 설치합니다.

2.  Visual Studio의 **Project** 메뉴에서 **참조 추가**를 클릭한 후 **Windows**를 확장하여 **확장**을 클릭하고 **Live SDK**를 선택한 다음 **확인**을 클릭합니다.

    ![][0]

    Live SDK에 대한 참조가 프로젝트에 추가됩니다.

3.  default.html 프로젝트 파일을 열고 \<head\> 요소에 다음 \<script\> 요소를 추가합니다.

        <script src="///LiveSDKHTML/js/wl.js"></script>

    이 요소는 default.html 파일에서 Microsoft IntelliSense를 사용하도록 설정합니다.

4.  default.js 프로젝트 파일을 열고 파일의 맨 위에 다음 주석을 추가합니다.

        /// <reference path="///LiveSDKHTML/js/wl.js" />

    이 주석은 default.js 파일에서 Microsoft IntelliSense를 사용하도록 설정합니다.

5.  **app.OnActivated** 메서드 오버로드에서 **refreshTodoItems** 메서드에 대한 호출을 다음 코드로 바꿉니다.

        var session = null;   

        var logout = function () {
            return new WinJS.Promise(function (complete) {
                WL.getLoginStatus().then(function () {
                    if (WL.canLogout()) {
                        WL.logout(complete);                            
                    }
                    else {
                        complete();
                    }
                });
            });
        };                  

        var login = function () {
            return new WinJS.Promise(function (complete) {                    
                WL.login({ scope: "wl.basic"}).then(function (result) {
                    session = result.session;

                    WinJS.Promise.join([
                        WL.api({ path: "me", method: "GET" }),
                        client.login(result.session.authentication_token)
                    ]).done(function (results) {
                        var profile = results[0];
                        var mobileServicesUser = results[1];
                        refreshTodoItems();
                        var title = "Welcome " + profile.first_name + "!";
                        var message = "You are now logged in as: " + mobileServicesUser.userId;
                        var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                        dialog.showAsync().done(complete);                                
                    });                       
                }, function (error) {                        
                    session = null;
                    var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                    dialog.showAsync().done(complete);                        
                });
            });
        }

        var authenticate = function () {
            // Force a logout to make it easier to test with multiple Microsoft Accounts
            logout().then(login).then(function () {
                if (session === null) {
                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        WL.init({
            redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
        });           

        authenticate();

    이 코드는 Live Connect 클라이언트를 초기화하고, 로그아웃을 실행하고, Live Connect에 새 로그인 요청을 보내고, 반환된 인증 토큰을 모바일 서비스로 보낸 다음 로그인한 사용자에 대한 정보를 표시합니다. 이 코드는 가능한 경우 로그아웃을 실행하며 응용 프로그램이 실행될 때마다 사용자에게 자격 증명을 요구하는 메시지가 표시되도록 합니다. 인증이 올바르게 작동하는지 확인하기 위해 다양한 Microsoft 계정을 사용하여 응용 프로그램을 테스트하기 쉬워집니다. 이 메커니즘은 로그인한 사용자에게 연결된 Microsoft 계정이 없는 경우에만 작동합니다.

    > [WACOM.NOTE]앱이 실행될 때마다 Live Connection 인증 토큰 또는 모바일 서비스 권한 부여 토큰을 요청해야 하는 것은 아닙니다. 이 방법은 비효율적일 뿐 아니라 많은 고객이 동시에 앱을 시작하려고 할 경우 사용 관련 문제가 발생할 수도 있습니다. 보다 나은 접근 방법은 토큰을 캐시하고 **LoginWithMicrosoftAccountAsync**를 호출하기 전에 캐시된 모바일 서비스의 사용을 시도하는 것입니다. 이 토큰을 캐시하는 방법을 예를 보려면 [인증 시작][2]을 참조하십시오.

6.  이전 단계의 *\<\< INSERT REDIRECT DOMAIN HERE \>\>* 문자열을 Live Connect에서 앱을 구성할 때 **<https://_service-name_.azure-mobile.net/>** 형식으로 지정한 리디렉션 도메인으로 업데이트합니다.

7.  F5 키를 눌러 앱을 실행하고 Microsoft 계정으로 Live Connect에 로그인합니다.

    로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [스크립트를 통해 사용자 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다. 인증을 위해 다른 ID 공급자를 사용하는 방법에 대한 자세한 내용은 [인증 시작][3]을 참조하십시오.



  [인증 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
  [테이블 사용 권한을 인증된 사용자로 제한]: #permissions
  [앱에 인증 추가]: #add-authentication
  [Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started
  [1]: /ko-kr/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png
  [2]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/#tokens
  [스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-js
  [3]: /ko-kr/develop/mobile/tutorials/get-started-with-users-js
