---
title: Microsoft ID 플랫폼 UWP 시작 | Azure
description: UWP(유니버설 Windows 플랫폼) 애플리케이션이 Microsoft ID 플랫폼 엔드포인트에서 액세스 토큰이 필요한 API를 어떻게 호출하는지 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b7e59e39adbb485738ca66b7ad8e5ba8293ddc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784986"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>유니버설 Windows 플랫폼 애플리케이션(XAML)에서 Microsoft Graph API 호출

> [!div renderon="docs"]

이 가이드에서는 네이티브 UWP(유니버설 Windows 플랫폼) 애플리케이션이 액세스 토큰을 요청한 다음, Microsoft Graph API를 호출하는 방법을 설명합니다. 이 가이드는 Microsoft ID 플랫폼 엔드포인트의 액세스 토큰이 필요한 다른 API에도 적용됩니다.

이 가이드의 끝에서 애플리케이션은 개인 계정을 사용하여 보호된 API를 호출합니다. outlook.com, live.com 및 기타를 예로 듭니다. 또한 애플리케이션은 Azure AD(Azure Active Directory)가 있는 회사 또는 조직의 회사 및 학교 계정을 호출합니다.

>[!NOTE]
> 이 가이드는 유니버설 Windows 플랫폼 개발을 설치한 Visual Studio 2017을 필요로 합니다. 유니버설 Windows 플랫폼 앱을 개발하기 위해 Visual Studio를 다운로드하고 구성하는 지침은 [설정](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)을 참조하세요.

## <a name="how-this-guide-works"></a>이 가이드의 작동 방식

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

이 가이드에서는 Microsoft ID 플랫폼 엔드포인트의 토큰을 수락하는 Microsoft Graph API 또는 Web API를 쿼리하는 샘플 UWP 애플리케이션을 만듭니다. 이 시나리오에서는 토큰은 권한 부여 헤더를 통해 HTTP 요청에 추가됩니다. MSAL(Microsoft 인증 라이브러리)에서는 토큰 획득 및 갱신을 처리합니다.

## <a name="nuget-packages"></a>NuGet 패키지

이 가이드에서는 다음 NuGet 패키지를 사용합니다.

|라이브러리|설명|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft 인증 라이브러리|

## <a name="set-up-your-project"></a>프로젝트 설정

이 섹션에서는 *Microsoft에 로그인*과 Windows Desktop .NET 응용 프로그램(XAML)을 통합하는 단계별 지침을 제공합니다. 그런 다음, Microsoft Graph API와 같은 토큰을 필요로 하는 웹 API를 쿼리할 수 있습니다.

이 가이드는 Graph API, 로그 아웃 단추 및 호출의 결과를 표시하는 텍스트 상자를 쿼리하는 단추를 표시하는 애플리케이션을 만듭니다.

> [!NOTE]
> 이 샘플의 Visual Studio 프로젝트를 다운로드하고 싶으세요? [프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)하고, 실행하기 전에 코드 샘플을 구성하려면 [응용 프로그램 등록](#register-your-application "응용 프로그램 등록 단계")의 단계로 건너뜁니다.

### <a name="create-your-application"></a>애플리케이션 만들기

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
2. **템플릿**에서 **Visual C#** 을 선택합니다.
3. **비어 있는 앱(유니버설 Windows)** 을 선택합니다.
4. 앱 이름을 지정하고, **확인**을 선택합니다.
5. 메시지가 표시되면 **대상** 및 **최소** 버전에 대한 모든 버전을 선택하고, **확인**을 선택합니다.

    >![최소 및 대상 버전](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>프로젝트에 Microsoft 인증 라이브러리 추가
1. Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.
2. **패키지 관리자 콘솔** 창에서 다음 명령을 복사 및 붙여넣기합니다.

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> 이 명령은 [Microsoft 인증 라이브러리](https://aka.ms/msal-net)를 설치합니다. MSAL은 Microsoft ID 플랫폼으로 보호되는 API에 액세스하는 사용자 토큰을 획득하고, 캐싱하고, 새로 고칩니다.

## <a name="create-your-applications-ui"></a>애플리케이션 UI 만들기

**MainPage.xaml** 파일은 프로젝트 템플릿의 일부로 자동으로 생성됩니다. 이 파일을 연 다음, 지침을 따릅니다.

* 애플리케이션의 **그리드** 노드를 다음 코드로 바꿉니다.

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>MSAL을 사용하여 Microsoft Graph API에 대한 토큰 가져오기

이 섹션에서는 MSAL을 사용하여 Microsoft Graph API에 대한 토큰을 가져오는 방법을 보여줍니다.

1.  **MainPage.xaml.cs**에서 클래스에 MSAL에 대한 참조를 추가합니다.

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. <code>MainPage</code> 클래스의 코드를 다음 코드로 바꿉니다.

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    ```

### <a name="more-information"></a>자세한 정보

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

`AcquireTokenInteractive` 메서드를 호출하면 사용자에게 로그인하라는 창이 표시됩니다. 처음으로 보호된 리소스에 액세스할 때 애플리케이션에서는 사용자가 일반적으로 대화형으로 로그인하도록 요청합니다. 토큰을 획득하는 자동 작업에 실패한 경우 로그인해야 할 수도 있습니다. 사용자의 암호가 만료된 경우를 예로 듭니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`AcquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `AcquireTokenInteractive`가 처음으로 실행되고 사용자에게 자격 증명을 묻는 메시지가 표시되면 자동으로 토큰을 획득하기 때문에 `AcquireTokenSilent` 메서드를 사용하여 후속 호출을 위한 토큰을 요청해야 합니다. MSAL은 토큰 캐시 및 갱신을 처리합니다.

결국 `AcquireTokenSilent` 메서드가 실패합니다. 사용자가 로그아웃했거나 다른 디바이스에서 해당 암호를 변경하면 실패할 수 있습니다. MSAL이 대화형 작업을 요구해 이 문제를 해결할 수 있다고 감지하면 `MsalUiRequiredException` 예외를 발생합니다. 애플리케이션에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

* 즉시 `AcquireTokenInteractive`에 대해 호출할 수 있습니다. 이 호출로 인해 사용자에게 로그인하라는 메시지가 표시됩니다. 일반적으로 이 패턴은 사용자가 사용할 수 있는 오프라인 콘텐츠가 없는 온라인 응용 프로그램에서 사용됩니다. 이 설정 안내에서 생성하는 샘플은 패턴을 따릅니다. 샘플을 처음으로 실행할 때 작업에 표시됩니다.
  * 이 애플리케이션을 사용한 사용자가 없기 때문에 `accounts.FirstOrDefault()`에는 null 값이 포함되며 `MsalUiRequiredException` 예외가 throw됩니다.
  * 그런 다음, 샘플의 코드는 `AcquireTokenInteractive`를 호출하여 예외를 처리합니다. 이 호출로 인해 사용자에게 로그인하라는 메시지가 표시됩니다.

* 또는 대신 대화형 로그인이 필요한 사용자에게 시각적 표시를 제공합니다. 그런 다음, 로그인할 적절한 시기를 선택할 수 있습니다. 또는 애플리케이션이 나중에 `AcquireTokenSilent`를 다시 시도할 수 있습니다. 사용자가 중단 없이 다른 응용 프로그램 기능을 사용할 수 있는 경우에 이 패턴이 종종 사용됩니다. 예제로 응용 프로그램에서 오프라인 콘텐츠를 사용할 수 있는 경우입니다. 이 경우에 사용자는 보호된 리소스에 액세스하거나, 오래된 정보를 새로 고치기 위해 로그인하는 시점을 결정할 수 있습니다. 또는 네트워크가 일시적으로 사용할 수 없게 된 후에 복원된 경우 응용 프로그램이 `AcquireTokenSilent`를 다시 시도하도록 결정할 수 있습니다.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>방금 가져온 토큰을 사용하여 Microsoft Graph API를 호출합니다.

* **MainPage.xaml.cs**에 다음과 같은 새 메서드를 추가합니다. 이 메서드는 `Authorization` 헤더를 사용하여 Graph API에 대한 `GET` 요청을 수행하는 데 사용됩니다.

    ```csharp
    /// <summary>
    /// Perform an HTTP GET request to a URL using an HTTP Authorization header
    /// </summary>
    /// <param name="url">The URL</param>
    /// <param name="token">The token</param>
    /// <returns>String containing the results of the GET operation</returns>
    public async Task<string> GetHttpContentWithToken(string url, string token)
    {
        var httpClient = new System.Net.Http.HttpClient();
        System.Net.Http.HttpResponseMessage response;
        try
        {
            var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
            // Add the token in Authorization header
            request.Headers.Authorization = 
              new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
            response = await httpClient.SendAsync(request);
            var content = await response.Content.ReadAsStringAsync();
            return content;
        }
        catch (Exception ex)
        {
            return ex.ToString();
        }
    }
    ```

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 샘플 응용 프로그램에서 `GetHttpContentWithToken` 메서드는 토큰이 필요한 보호되는 리소스에 대한 HTTP `GET` 요청을 실행하는 데 사용됩니다. 그런 다음, 메서드는 호출자에게 콘텐츠를 반환합니다. 이 메서드는 **HTTP 인증** 헤더에 획득된 토큰을 추가합니다. 이 샘플에서 리소스는 사용자 프로필 정보를 표시하는 Microsoft Graph API **me** 엔드포인트입니다.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>사용자를 로그아웃하는 메서드 추가

* 사용자를 로그아웃하려면 **MainPage.xaml.cs**에 다음 메서드를 추가합니다.

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> MSAL.NET은 비동기 메서드를 사용하여 토큰을 획득하거나 계정을 조작하므로 UI 스레드에서 UI 작업, 즉, `Dispatcher.RunAsync`를 수행하고 예방 조치로 `ConfigureAwait(false)`를 호출해야 합니다.

### <a name="more-information-on-sign-out"></a>로그아웃에 대한 자세한 정보

`SignOutButton_Click` 메서드는 MSAL 사용자 캐시에서 사용자를 제거합니다. 이 메서드는 현재 사용자를 잊도록 MSAL에 효과적으로 지시합니다. 그런 다음, 대화형이 되도록 하는 경우에만 토큰을 획득하는 후속 요청이 성공합니다.
이 샘플의 애플리케이션은 단일 사용자를 지원합니다. 그러나 MSAL은 둘 이상의 계정이 동시에 로그인할 수 있는 시나리오를 지원합니다. 사용자 한 명이 여러 계정을 가질 수 있는 이메일 응용 프로그램을 예로 듭니다.

## <a name="display-basic-token-information"></a>기본 토큰 정보 표시

* 토큰에 대한 기본 정보를 표시하려면 **MainPage.xaml.cs** 파일에 다음 메서드를 추가합니다.

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>자세한 정보

**OpenID Connect**를 통해 획득된 ID 토큰에는 사용자와 관련된 정보의 일부가 포함됩니다. `DisplayBasicTokenInfo`는 토큰에 포함된 기본 정보를 표시합니다. 사용자의 표시 이름 및 ID, 토큰 만료 날짜 및 액세스 토큰 자체를 나타내는 문자열을 예로 듭니다. **Microsoft Graph API 호출** 단추를 여러 번 선택하는 경우 동일한 토큰이 후속 요청에 다시 사용된 것을 볼 수 있습니다. MSAL이 토큰 갱신 시점이라고 판단한 경우에는 만료 날짜가 연장된 것도 확인할 수 있습니다.

## <a name="register-your-application"></a>애플리케이션 등록

이제 Microsoft 애플리케이션 등록 포털에서 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 Azure AD 테넌트에 있는 경우 페이지 상단 메뉴의 오른쪽 위 모서리에서 `Directory + Subscription`을 선택하고, 포털 세션을 원하는 Azure AD 테넌트로 전환합니다.
1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **새 등록**을 선택합니다.
   - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `UWP-App-calling-MSGraph`)을 입력합니다.
   - **지원되는 계정 유형** 섹션에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정(얘: Skype, Xbox, Outlook.com)** 을 선택합니다.
   - **등록**을 선택하여 애플리케이션을 만듭니다.
1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. Visual Studio로 돌아가서 **MainPage.xaml.cs** 파일을 열고, ClientId 값을 방금 등록한 애플리케이션 ID로 바꿉니다.
1. 앱의 페이지 목록에서 **인증**을 선택합니다.
   1. **리디렉션 URI** 섹션의 리디렉션 URI 목록에서 다음을 수행합니다.
   1. **TYPE** 열에서 **공용 클라이언트(모바일 및 데스크톱)** 를 선택합니다.
   1. **리디렉션 URI** 열에 `urn:ietf:wg:oauth:2.0:oob`를 입력합니다.
1. **저장**을 선택합니다.
1. 앱의 페이지 목록에서 **API 권한**을 선택합니다.
   - **권한 추가** 단추를 클릭한 다음,
   - **Microsoft API** 탭을 선택합니다.
   - *일반적으로 사용되는 Microsoft API* 섹션에서 **Microsoft Graph**를 클릭합니다.
   - **위임된 권한** 섹션에서 적절한 권한, 즉, **User.Read**를 선택합니다. 필요한 경우 검색 상자를 사용합니다.
   - **권한 추가** 단추를 선택합니다.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>페더레이션된 도메인에서 통합된 인증을 사용하도록 설정(선택 사항)

페더레이션된 Azure AD 도메인과 함께 사용할 경우 Windows 통합 인증을 사용하려면 애플리케이션 매니페스트에서 추가 기능을 사용하도록 설정해야 합니다.

1. **Package.appxmanifest**를 두 번 클릭합니다.
2. **기능** 탭을 선택하고 다음 설정이 사용하도록 설정되어 있는지 확인합니다.

    - 엔터프라이즈 인증
    - 개인 네트워크(클라이언트 및 서버)
    - 공유 사용자 인증서

> [!IMPORTANT]
> [Windows 통합 인증](https://aka.ms/msal-net-iwa)은 이 샘플에 대해 기본적으로 구성되지 않습니다. *엔터프라이즈 인증* 또는 *공유 사용자 인증서* 기능을 요청하는 응용 프로그램은 Windows 스토어를 통해 더 높은 수준의 확인이 필요합니다. 또한 일부 개발자가 더 높은 수준의 확인을 수행하려고 합니다. 페더레이션된 Azure AD 도메인과 함께 사용할 Windows 통합 인증이 필요한 경우에만 이 설정을 사용하세요.

## <a name="test-your-code"></a>코드 테스트

응용 프로그램을 테스트하려면 F5 키를 선택하여 Visual Studio에서 프로젝트를 실행합니다. 아래와 같이 주 창이 표시됩니다.

![애플리케이션의 사용자 인터페이스](./media/tutorial-v2-windows-uwp/testapp-ui.png)

테스트할 준비가 되면 **Call Microsoft Graph API**를 선택합니다. live.com 또는 outlook.com 같은 Azure AD 조직 계정이나 Microsoft 계정을 사용하여 로그인합니다. 처음인 경우 로그인하라는 창이 표시됩니다.

![로그인 페이지](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>동의

응용 프로그램에 처음으로 로그인하면 다음과 유사한 동의 화면이 표시됩니다. 액세스하려면 **예**를 선택하여 명시적으로 동의합니다.

![액세스 동의 화면](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>예상 결과

**API 호출 결과** 화면에 Microsoft Graph API 호출에서 반환된 사용자 프로필 정보가 표시됩니다.

![API 호출 결과 화면](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

**토큰 정보** 상자에 `AcquireTokenInteractive` 또는 `AcquireTokenSilent`를 통해 획득한 토큰에 대한 기본 정보가 표시됩니다.

|자산  |형식  |설명 |
|---------|---------|---------|
|**사용자 이름** |<span>user@domain.com</span> |사용자를 식별하는 사용자 이름입니다.|
|**토큰 만료** |DateTime |토큰이 만료되는 시간입니다. MSAL은 필요에 따라 토큰을 갱신하여 만료 날짜를 연장합니다.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 애플리케이션 등록 포털에서 등록된 모든 애플리케이션에서 기본적으로 자동 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. Microsoft Graph API는 *Calendars.Read* 범위가 있어야만 사용자 일정을 나열할 수 있습니다.

애플리케이션의 컨텍스트에서 사용자 일정에 액세스하려면 애플리케이션 등록 정보에 *Calendars.Read* 위임 권한을 추가합니다. 그런 다음, `acquireTokenSilent` 호출에 *Calendars.Read* 범위를 추가합니다.

> [!NOTE]
> 범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

## <a name="known-issues"></a>알려진 문제

### <a name="issue-1"></a>문제 1

페더레이션된 Azure AD 도메인의 애플리케이션에 로그인할 때 다음 오류 메시지 중 하나가 수신됩니다.

* 요청에서 유효한 클라이언트 인증서를 찾을 수 없습니다.
* 사용자의 인증서 저장소에서 유효한 인증서를 찾을 수 없습니다.
* 다른 인증 방법을 다시 선택하십시오.

**원인:** 엔터프라이즈 및 인증서 기능을 사용할 수 없습니다.

**해결 방법:** [페더레이션된 도메인에서 통합된 인증](#enable-integrated-authentication-on-federated-domains-optional)의 단계를 따릅니다.

### <a name="issue-2"></a>문제 2

[페더레이션된 도메인에서 통합 인증](#enable-integrated-authentication-on-federated-domains-optional)을 활성화하고 Windows 10 컴퓨터에서 Windows Hello를 사용하여 다단계 인증이 구성된 환경에 로그인하려고 합니다. 인증서 목록이 표시됩니다. 그러나 사용자의 PIN을 사용하도록 선택한 경우 PIN 창이 표시되지 않습니다.

**원인:** 이 문제는 Windows 10 데스크톱에서 실행되는 UWP 애플리케이션의 웹 인증 broker에 대해 알려진 제한 사항입니다. Windows 10 Mobile에서 제대로 작동합니다.

**해결 방법:** **기타 옵션으로 로그인**을 선택합니다. 그런 다음, **사용자 이름과 암호로 로그인**을 선택합니다. **암호 제공**을 선택합니다. 그런 다음, 전화 인증 프로세스로 진행합니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
