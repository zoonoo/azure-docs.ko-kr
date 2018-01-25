---
title: "Azure Active Directory v2.0 .NET 네이티브 앱 | Microsoft Docs"
description: "개인 Microsoft 계정과 회사 또는 학교 계정 둘 다로 사용자를 로그인하는 .NET 네이티브 앱을 빌드하는 방법입니다."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88679e7dd71011f767cbe4de295c284516375d20
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2018
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Windows 데스크톱 앱에 로그인 추가
v2.0 끝점에서는 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다를 지원하는 인증을 데스크톱 앱에 빠르게 추가할 수 있습니다.  또한 앱이 백 엔드 웹 API 그리고 [Microsoft Graph](https://graph.microsoft.io) 및 [Office 365 통합 API](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) 중 일부와 안전하게 통신할 수 있도록 합니다.

> [!NOTE]
> v2.0 끝점에서는 일부 Azure AD(Active Directory) 시나리오 및 기능만 지원합니다.  v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
> 
> 

[장치에서 실행되는 .NET 네이티브 앱](active-directory-v2-flows.md#mobile-and-native-apps)의 경우 Azure AD는 Microsoft Identity 인증 라이브러리 또는 MSAL을 제공합니다.  MSAL의 유일한 용도는 앱이 쉽게 웹 서비스 호출을 위한 토큰을 가져오도록 하는 것입니다.  액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 여기서는 다음과 같은 작업을 수행하는 .NET WPF To-Do List 앱을 빌드하겠습니다.

* [OAuth 2.0 인증 프로토콜](active-directory-v2-protocols.md)을 사용하여 사용자를 로그인하고 액세스 토큰을 가져옵니다.
* OAuth 2.0으로 보안된 백 엔드 To-Do List 웹 서비스를 안전하게 호출합니다.
* 사용자가 로그아웃합니다.

## <a name="download-sample-code"></a>샘플 코드 다운로드
이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet)에서 유지 관리됩니다.  자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) 하거나 구조를 복제할 수 있습니다.

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

전체 앱은 이 자습서 마지막 부분에서도 제공됩니다.

## <a name="register-an-app"></a>앱 등록
[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 새 앱을 만들거나 다음 [세부 단계](active-directory-v2-app-registration.md)를 따릅니다.  다음을 수행해야 합니다.

* 곧 필요하게 되므로 앱에 할당된 **응용 프로그램 ID** 를 적어둡니다.
* 앱용 **Mobile** 플랫폼을 추가합니다.

## <a name="install--configure-msal"></a>MSAL 설치 및 구성
이제 앱을 Microsoft에 등록했으며, MSAL을 설치하고 ID 관련 코드를 작성할 수 있습니다.  MSAL에서 v2.0 끝점을 전달할 수 있도록 하려면, 앱 등록에 관한 일부 정보를 제공해야 합니다.

* 패키지 관리자 콘솔을 사용하여 MSAL을 TodoListClient 프로젝트에 추가하여 시작합니다.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* TodoListClient 프로젝트에서 `app.config`를 엽니다.  앱 등록 포털에 입력한 값을 반영하도록 `<appSettings>` 섹션의 요소 값을 바꿉니다.  코드는 MSAL을 사용할 때마다 이러한 값을 참조합니다.
  
  * `ida:ClientId` 는 포털에서 복사한 앱의 **응용 프로그램 ID** 입니다.
* TodoList 서비스 프로젝트에서 프로젝트의 루트에 있는 `web.config` 를 엽니다.  
  
  * `ida:Audience` 값을 포털과 동일한 **응용 프로그램 ID** 로 바꿉니다.

## <a name="use-msal-to-get-tokens"></a>MSAL을 사용하여 토큰 가져오기
MSAL의 기본 원칙은 앱에 액세스 토큰이 필요할 때마다 `app.AcquireToken(...)`을 호출하기만 하면 MSAL이 나머지 작업을 수행한다는 것입니다.  

* `TodoListClient` 프로젝트에서 `MainWindow.xaml.cs`를 열고 `OnInitialized(...)` 메서드를 찾습니다.  첫 번째 단계는 앱의 `PublicClientApplication` - 네이티브 응용 프로그램을 나타내는 MSAL의 기본 클래스를 초기화하는 것입니다.  이 클래스에서 Azure AD와 통신하는 데 필요한 좌표를 MSAL에 전달하고 토큰 캐시 방법을 알려줍니다.

```csharp
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* 앱이 시작되면 사용자가 앱에 이미 로그인했는지 여부를 확인하려고 합니다.  그러나 로그인 UI는 아직 호출하지 않고 사용자가 "로그인"을 클릭하여 호출하도록 합니다.  또한 `OnInitialized(...)` 메서드에서

```csharp
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* 사용자가 로그인하지 않았으며 "로그인" 단추를 클릭하는 경우 로그인 UI를 호출하고 사용자가 해당 자격 증명을 입력하도록 합니다.  로그인 단추 처리기를 구현합니다.

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* 사용자가 로그인하면 MSAL이 자동으로 토큰을 받아서 캐시하며, `GetTodoList()` 메서드를 호출할 수 있습니다.  `GetTodoList()` 메서드만 구현하면 사용자 작업을 가져올 수 있습니다.

```csharp
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>실행
축하합니다! 이제 OAuth 2.0을 사용하여 사용자를 인증하고 웹 API를 안전하게 호출하는 기능이 있는 .NET WPF 앱이 작동합니다.  두 프로젝트를 실행하고, 개인 Microsoft 계정이나 회사 또는 학교 계정으로 로그인합니다.  작업을 사용자의 할 일 모음에 추가합니다.   다른 사용자의 할 일 목록을 보려면 로그아웃하고 해당 사용자로 다시 로그인합니다.  앱을 닫았다가 다시 실행합니다.  사용자의 세션이 원래 상태로 유지됩니다. 이는 앱이 로컬 파일의 토큰을 캐시하기 때문입니다.

MSAL은 개인 및 회사 계정을 사용하여 공통 ID 기능을 앱에 쉽게 통합합니다.  또한 캐시 관리, OAuth 프로토콜 지원, 사용자에게 로그인 UI 제공, 만료된 토큰 새로 고침 등의 모든 귀찮은 작업을 관리해줍니다.  실제로 알아두어야 할 모든 항목은 단일 API 호출, `app.AcquireTokenAsync(...)`입니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>다음 단계
이제 좀 더 고급 항목으로 이동할 수 있습니다.  다음 작업을 시도할 수 있습니다.

* [v2.0 끝점을 사용하여 TodoListService Web API 보안 유지](active-directory-v2-devquickstarts-dotnet-api.md)

추가 리소스는 다음을 확인해보세요.  

* [개발자 가이드 v2.0 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "msal" 태그 >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>당사 제품에 대한 보안 업데이트 가져오기
[이 페이지](https://technet.microsoft.com/security/dd252948) 를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.

