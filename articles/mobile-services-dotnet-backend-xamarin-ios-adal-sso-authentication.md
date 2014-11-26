<properties linkid="develop-mobile-tutorials-sso-with-adal-xamarin-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Xamarin.iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Xamarin.iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender" />

# Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증

<div class="dev-center-tutorial-selector sublanding">
<a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows 스토어 C#" >Windows 스토어 C#</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
</div>

이 자습서에서는 Active Directory 인증 라이브러리를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다.

사용자를 인증하려면 먼저 AAD(Azure Active Directory)에 응용 프로그램을 등록해야 합니다. 이 작업은 다음 두 단계로 수행됩니다. 먼저, 모바일 서비스를 등록하고 그에 대한 사용 권한을 표시합니다. 두 번째로 Xamarin.iOS 앱을 등록하여 해당 사용 권한에 대한 액세스를 부여해야 합니다.

> [WACOM.NOTE] 이 자습서는 모바일 서비스를 통해 Xamarin.iOS 앱에 대한 Single Sign-On Azure Active Directory 인증을 수행할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 모바일 서비스를 처음 사용하는 경우 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료하는 것이 좋습니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [Azure Active Directory에 모바일 서비스 등록][Azure Active Directory에 모바일 서비스 등록]
2.  [Azure Active Directory에 앱 등록][Azure Active Directory에 앱 등록]
3.  [인증을 요구하도록 모바일 서비스 구성][인증을 요구하도록 모바일 서비스 구성]
4.  [클라이언트 앱에 인증 코드 추가][클라이언트 앱에 인증 코드 추가]
5.  [인증을 사용하여 클라이언트 테스트][인증을 사용하여 클라이언트 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

-   XCode 4.5 및 iOS 6.0(또는 이후 버전)
-   [Xamarin 확장][Xamarin 확장]이 포함된 Visual Studio 또는 OS X의 [Xamarin Studio][Xamarin Studio]
-   [모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작] 자습서 완료
-   Microsoft Azure 모바일 서비스 SDK
-   [iOS용 Active Directory 인증 라이브러리를 위한 Xamarin 바인딩][iOS용 Active Directory 인증 라이브러리를 위한 Xamarin 바인딩].

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>인증을 요구하도록 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>클라이언트 앱에 인증 코드 추가

1.  Active Directory 인증 라이브러리를 위한 Xamarin 바인딩을 사용자의 Xamarin.iOS 프로젝트에 추가합니다. Visual Studio 2013에서는 **참조**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다. 그런 후 바인딩 라이브러리로 이동하고 **추가**를 클릭합니다. 또한 ADAL 소스에서 스토리보드를 추가해야 합니다.

2.  QSTodoService 클래스에 다음을 추가합니다.

        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

        public async Task Authenticate()
        {
            var aadAuthority = @"<INSERT-AUTHORITY-HERE>";
            var aadResource = @"<INSERT-RESOURCE-URI-HERE>";
            var aadClientId = @"<INSERT-CLIENT-ID-HERE>";
            var aadRedirect = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError error;
            var aadContext = ADAuthenticationContext.AuthenticationContextWithAuthority(aadAuthority, out error);
            if (error != null)
            {
                throw new InvalidOperationException("Error creating the AAD context: " + error.ErrorDetails);
            }

            var tcs = new TaskCompletionSource<string>();
            aadContext.AcquireTokenWithResource(aadResource, aadClientId, new NSUrl(aadRedirect), result =>
                {
                    if (result.Status == ADAuthenticationResultStatus.SUCCEEDED)
                    {
                        tcs.SetResult(result.AccessToken);
                    }
                    else
                    {
                        string errorDetails;
                        if (result.Status == ADAuthenticationResultStatus.USER_CANCELLED)
                        {
                            errorDetails = "Authentication cancelled by user";
                        }
                        else
                        {
                            errorDetails = result.Error.ErrorDetails;
                        }

                        tcs.SetException(new InvalidOperationException("Error during authentication: " + errorDetails));
                    }
                });
            string accessToken = await tcs.Task;

            var token = new JObject(new JProperty("access_token", accessToken));

            try
            {
                user = await this.client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, token);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

3.  `AuthenticateAsync` 메서드에 대한 코드에서 응용 프로그램을 제공한 테넌트의 이름으로 **INSERT-AUTHORITY-HERE**를 바꾸고, 형식은 <https://login.windows.net/tenant-name.onmicrosoft.com>입니다. 이 값은 [Azure 관리 포털][Azure 관리 포털]에서 Azure Active Directory의 도메인 탭에서 복사할 수 있습니다.

4.  위의 `AuthenticateAsync` 메서드에 대한 코드에서 **INSERT-RESOURCE-URI-HERE**를 모바일 서비스에 대한 **앱 ID URI**로 바꿉니다. [Azure Active Directory에 등록하는 방법][Azure Active Directory에 등록하는 방법] 항목을 따르면 앱 ID URI가 <https://todolist.azure-mobile.net/login/aad>와 유사합니다.

5.  위의 `AuthenticateAsync` 메서드에 대한 코드에서 **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 응용 프로그램에서 복사한 클라이언트 ID로 바꿉니다.

6.  위의 `AuthenticateAsync` 메서드에 대한 코드에서 **INSERT-REDIRECT-URI-HERE**를 모바일 서비스에 대한 /login/done 끝점으로 바꿉니다. 이 항목은 <https://todolist.azure-mobile.net/login/done>과 유사합니다.

7.  QSTodoListViewController에서 RefreshAsync() 호출 바로 전에 다음 코드를 추가하여 **ViewDidLoad**를 수정합니다.

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>인증을 사용하여 클라이언트 테스트

1.  실행 메뉴에서 실행을 클릭하여 앱을 시작합니다.
2.  Azure Active Directory에 대한 로그인 메시지가 표시됩니다.
3.  앱이 인증하며 todo 항목을 반환합니다.

   ![][0]

 


  [모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [Azure Active Directory에 모바일 서비스 등록]: #register-mobile-service-aad
  [Azure Active Directory에 앱 등록]: #register-app-aad
  [인증을 요구하도록 모바일 서비스 구성]: #require-authentication
  [클라이언트 앱에 인증 코드 추가]: #add-authentication-code
  [인증을 사용하여 클라이언트 테스트]: #test-client
  [Xamarin 확장]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [데이터 시작]: /ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [iOS용 Active Directory 인증 라이브러리를 위한 Xamarin 바인딩]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [Azure Active Directory에 등록하는 방법]: /ko-kr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [0]: ./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png
