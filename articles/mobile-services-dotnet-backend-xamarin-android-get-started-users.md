<properties linkid="mobile-services-dotnet-backend-xamarin-android-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin Android apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin Android apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# 모바일 서비스에서 인증 시작

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows 스토어 C#">Windows 스토어 C#</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="iOS">iOS</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android" class="current">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a></div>

이 항목은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성][인증을 위해 앱 등록 및 모바일 서비스 구성]
2.  [테이블 사용 권한을 인증된 사용자로 제한][테이블 사용 권한을 인증된 사용자로 제한]
3.  [앱에 인증 추가][앱에 인증 추가]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료해야 합니다.

## <a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 장치 또는 시뮬레이터에서 실행합니다. 앱 시작 후 상태 코드가 401(권한이 부여되지 않음)인 처리되지 않은 예외가 발생했는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a>앱에 인증 추가

1.  **TodoActivity** 클래스에 다음 속성을 추가합니다.

            private MobileServiceUser user;

2.  **TodoActivity** 클래스에 다음 메서드를 추가합니다.

            private async Task Authenticate()
            {
                try
                {
                    user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
            }

    인증 프로세스를 처리하는 새 메서드가 만들어집니다. 사용자는 Facebook 로그인을 사용하여 인증됩니다. 인증된 사용자의 ID를 표시하는 대화 상자가 나타납니다.

    <div class="dev-callout"><b>참고</b>
<p>Facebook 이외의 ID 공급자를 사용하는 경우 위의 <strong>LoginAsync</strong>에 전달된 값을 <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> 또는 <i>WindowsAzureActiveDirectory</i> 중 하나로 변경합니다.</p>
</div>

3.  **OnCreate** 메서드에서 `MobileServiceClient` 개체를 인스턴스화하는 코드 다음에 다음 코드 줄을 추가합니다.

        // Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

    이 호출은 인증 프로세스를 시작하고 비동기적으로 기다립니다.

4.  **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작하고 원하는 ID 공급자에 로그인합니다.

    로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

<!-- ## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.
-->
 


  [인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
  [테이블 사용 권한을 인증된 사용자로 제한]: #permissions
  [앱에 인증 추가]: #add-authentication
  [모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/
