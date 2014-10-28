<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin iOS apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# 모바일 서비스에서 인증 시작

<div class="dev-center-tutorial-selector sublanding">
  <a href="/ko--kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows 스토어 C#">Windows 스토어 C#</a>
<a href="/ko--kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a>
<a href="/ko--kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/ko--kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS">iOS</a>
<a href="/ko--kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
<a href="/ko--kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/ko--kr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko--kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/ko--kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a></div>

이 항목은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성][인증을 위해 앱 등록 및 모바일 서비스 구성]
2.  [테이블 사용 권한을 인증된 사용자로 제한][테이블 사용 권한을 인증된 사용자로 제한]
3.  [앱에 인증 추가][앱에 인증 추가]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료해야 합니다.

## <a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

1.  Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 장치 또는 시뮬레이터에서 실행합니다. 앱 시작 후 상태 코드가 401(권한이 부여되지 않음)인 처리되지 않은 예외가 발생했는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a>앱에 인증 추가

이 섹션에서는 데이터 표시 전에 로그인 화면을 표시하도록 앱을 수정합니다. 앱이 시작될 때 앱은 모바일 서비스에 연결하지 않으며, 데이터를 표시하지 않습니다. 사용자가 새로 고침 제스처를 처음 수행한 다음 로그인 화면이 나타나고, 로그인이 성공하면 todo 항목 목록이 표시됩니다.

1.  클라이언트 프로젝트에서 **QSTodoService.cs** 파일을 열고 다음 선언을 QSTodoService에 추가합니다.

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  다음 정의를 포함하여 새 메서드 **Authenticate**를 **QSTodoService**에 추가합니다.

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout"><b>참고</b>
<p>Facebook 이외의 ID 공급자를 사용하는 경우 위의 <strong>LoginAsync</strong>에 전달된 값을 <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> 또는 <i>WindowsAzureActiveDirectory</i> 중 하나로 변경합니다.</p>
</div>

3.  **QSTodoListViewController.cs**를 엽니다. **ViewDidLoad**의 메서드 정의를 수정해서 끝 부근의 **RefreshAsync()** 호출을 제거합니다.

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;

            todoService.BusyUpdate += (bool busy) => {
                if (busy)
                    activityIndicator.StartAnimating ();
                else 
                    activityIndicator.StopAnimating ();
            };

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();

            AddRefreshControl ();
        }

4.  **User** 속성이 null인 경우 인증을 수행하고 로그인 화면을 표시하도록 **RefreshAsync** 메서드를 수정합니다. 다음 코드의 메서드 정의 상단에서 다음을 수행합니다.

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5.  **실행** 단추를 눌러 프로젝트를 빌드하고 iPhone 시뮬레이터에서 앱을 시작합니다. 앱이 데이터를 표시하지 않는지 확인합니다.

    항목 목록을 아래로 끌어서 새로 고침 제스처를 수행하고 로그인 화면이 나타나도록 합니다. 유효한 자격 증명을 성공적으로 입력한 후에는 앱이 todo 항목 목록을 표시하고 사용자가 데이터를 업데이트할 수 있습니다.


<!-- ## <a name="next-steps"> </a>Next steps  
In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.
-->

<!-- Anchors. --> 
<!-- URLs. -->

  [Windows 스토어 C#]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows 스토어 C#"
  [Windows 스토어 JavaScript]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows 스토어 JavaScript"
  [Windows Phone]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone"
  [iOS]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS"
  [Android]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "Android"
  [Xamarin.iOS]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users "Xamarin.iOS"
  [Xamarin.Android]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users "Xamarin.Android"
  [.NET 백 엔드]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/ ".NET 백 엔드"
  [JavaScript 백 엔드]: /ko--kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/ "JavaScript 백 엔드"
  [인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
  [테이블 사용 권한을 인증된 사용자로 제한]: #permissions
  [앱에 인증 추가]: #add-authentication
  [모바일 서비스 시작]: /ko--kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
