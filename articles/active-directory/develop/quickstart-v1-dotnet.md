---
title: .NET Desktop(WPF) 앱에서 사용자 로그인 및 Microsoft Graph API 호출 | Microsoft Docs
description: 로그인을 위해 Azure AD와 통합되고 OAuth 2.0을 사용하여 Azure AD로 보호되는 API를 호출하는 .NET Windows Desktop 애플리케이션을 빌드하는 방법을 알아보세요.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2d9639c21e201db1df5145caf1345d4f0879af6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121952"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>빠른 시작: .NET Desktop(WPF) 앱에서 사용자 로그인 및 Microsoft Graph API 호출

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

보호된 리소스에 액세스해야 하는 .NET 네이티브 클라이언트의 경우 Azure AD(Azure Active Directory)에서는 Active Directory 인증 라이브러리(ADAL)를 제공합니다. ADAL을 사용하면 앱에서 쉽게 액세스 토큰을 가져올 수 있습니다. 

이 빠른 시작에서는 다음과 같은 .NET WPF 할 일 목록 애플리케이션을 빌드하는 방법을 알아보겠습니다.

* OAuth 2.0 인증 프로토콜을 사용하여 Azure AD Graph API를 호출하기 위한 액세스 토큰을 가져옵니다.
* 지정된 별칭을 가진 사용자를 디렉터리에서 검색합니다.
* 사용자를 로그아웃합니다.

완전하게 작동하는 응용 프로그램을 빌드하려면 다음 작업이 필요합니다.

1. Azure AD에 애플리케이션을 등록합니다.
2. ADAL을 설치 및 구성합니다.
3. ADAL을 사용하여 Azure AD에서 토큰을 가져옵니다.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음과 같은 필수 구성 요소를 완료하세요.

* [앱 기본 사항 다운로드](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) 또는 [완성된 샘플 다운로드](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)
* 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트 보유 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>1단계: DirectorySearcher 애플리케이션 등록

앱에서 토큰을 가져올 수 있게 하려면 Azure AD 테넌트에 앱을 등록하고 Azure AD Graph API에 액세스할 수 있는 사용 권한을 부여해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 위쪽 표시줄에서 계정을 선택하고 **디렉터리** 목록에서 응용 프로그램을 등록하려는 Active Directory 테넌트를 선택합니다.
3. 왼쪽 탐색 창에서 **모든 서비스**를 선택하고 **Azure Active Directory**를 선택합니다.
4. **앱 등록**에서 **새 등록**을 선택합니다.
5. 프롬프트에 따라 새 클라이언트 애플리케이션을 만듭니다.
    * **이름**은 애플리케이션 이름이고 최종 사용자에게 애플리케이션을 설명합니다.
    * **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
    * **리디렉션 URI**는 Azure AD가 토큰 응답을 반환하는 데 사용하는 구성표 및 문자열의 조합입니다. 애플리케이션과 관련되고(예: `http://DirectorySearcher`) 이전 리디렉션 URI 정보를 바탕으로 한 값을 입력합니다. 또한 드롭다운에서 **공용 클라이언트(모바일 및 데스크톱)** 를 선택합니다. 

6. 등록을 완료하면 AAD는 앱에 고유한 애플리케이션 ID를 할당합니다. 이 값은 다음 섹션에서 필요하므로 애플리케이션 페이지에서 복사해 둡니다.
7. **API 사용 권한** 페이지에서 **사용 권한 추가**를 선택합니다. **API 선택** 안에서 ***Microsoft Graph***를 선택합니다.
8. **위임된 권한**에서 사용 권한 **User.Read**를 선택한 다음, **추가**를 눌러 저장합니다. 이 권한은 Azure AD Graph API에서 사용자를 쿼리하도록 애플리케이션을 설정합니다.

## <a name="step-2-install-and-configure-adal"></a>2단계: ADAL 설치 및 구성

Azure AD에서 애플리케이션이 있으므로 ADAL을 설치하고 ID 관련 코드를 작성할 수 있습니다. ADAL이 Azura AD와 통신할 수 있게 하려면, 앱 등록에 관한 일부 정보를 제공해야 합니다.

1. 먼저 패키지 관리자 콘솔을 사용하는 `DirectorySearcher` 프로젝트에 ADAL을 추가합니다.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. `DirectorySearcher` 프로젝트에서 `app.config`를 엽니다.
1. Azure Portal에 입력한 값을 반영하도록 `<appSettings>` 섹션의 요소 값을 바꿉니다. 코드는 ADAL을 사용할 때마다 이러한 값을 참조합니다.
   * `ida:Tenant`는 Azure AD 테넌트의 도메인입니다(예: contoso.onmicrosoft.com).
   * `ida:ClientId`는 포털에서 복사한 애플리케이션의 클라이언트 ID여야 합니다.
   * `ida:RedirectUri`는 포털에 등록한 리디렉션 URL입니다.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>3단계: ADAL을 사용하여 Azure AD에서 토큰 가져오기

ADAL에서 확인되는 기본 원칙은 액세스 토큰이 필요할 때마다 앱이 `authContext.AcquireTokenAsync(...)`을 호출하고 나머지 작업은 ADAL이 수행한다는 것입니다.

1. `DirectorySearcher` 프로젝트에서 `MainWindow.xaml.cs`를 엽니다.
1. `MainWindow()` 메서드를 찾습니다. 
1. ADAL의 기본 클래스인 앱의 `AuthenticationContext`를 초기화합니다. `AuthenticationContext`는 Azure AD와 통신하는 데 필요한 좌표를 ADAL에 전달하고 토큰 캐시 방법을 알려주는 위치입니다.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. 사용자가 앱의 UI에서 **검색** 단추를 클릭할 때 호출되는 `Search(...)` 메서드를 찾습니다. 이 메서드는 Azure AD Graph API에 해당 UPN이 지정된 검색어로 시작하는 사용자를 쿼리하라는 GET 요청을 만듭니다.
1. Graph API를 쿼리하기 위해 ADAL이 들어오는 요청의 `Authorization` 헤더에 access_token을 포함합니다.

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    앱이 `AcquireTokenAsync(...)`을 호출하여 토큰을 요청하면 ADAL은 사용자에게 자격 증명을 요구하지 않고 토큰을 반환하려고 합니다.
    * ADAL은 사용자가 토큰을 가져오기 위해 로그인해야 한다고 판단할 경우 로그인 대화 상자를 표시하고, 사용자의 자격 증명을 수집하고, 인증 성공 시 토큰을 반환합니다. 
    * 어떤 이유로든 ADAL이 토큰을 반환할 수 없는 경우 `AdalException`을 발생합니다.

1. `AuthenticationResult` 개체에는 사용자 앱에 필요할 수 있는 정보를 수집하는 데 사용할 수 있는 `UserInfo` 개체가 포함되어 있습니다. DirectorySearcher에서 `UserInfo`는 사용자 ID를 사용하여 앱 UI를 사용자 지정하는 데 사용됩니다.
1. 사용자가 **로그아웃** 단추를 선택한 경우 `AcquireTokenAsync(...)`에 대한 다음 호출 시 사용자에게 로그인하라는 메시지가 표시되어야 합니다. 토큰 캐시를 정리하여 ADAL에서 이 작업을 쉽게 수행할 수 있습니다.

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    사용자가 **로그아웃** 단추를 클릭하지 않으면 다음에 DirectorySearcher를 실행할 때 사용자 세션이 유지되어야 합니다. 앱이 시작되면 ADAL의 토큰 캐시에서 기존 토큰이 있는지 확인하고 그에 따라 UI를 업데이트할 수 있습니다.

1. `CheckForCachedToken()` 메서드에서 `AcquireTokenAsync(...)`를 다시 호출합니다. 이번에는 `PromptBehavior.Never` 매개 변수를 전달합니다. `PromptBehavior.Never` 는 사용자가 로그인하라는 메시지를 표시하지 않도록 ADAL에 지시하고, 대신 토큰을 반환할 수 없는 경우 ADAL에서는 예외를 발생해야 합니다.

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without signing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

축하합니다! 이제 사용자를 인증하고 OAuth 2.0을 사용하여 Web API를 안전하게 호출하고, 사용자에 대한 기본 정보를 가져올 수 있는 .NET WPF 응용 프로그램이 작동하게 되었습니다. 아직 일부 사용자로 테넌트를 채우지 않은 경우 지금 할 수 있습니다. DirectorySearcher 앱을 실행하고 해당 사용자 중 하나로 로그인합니다. 해당 UPN에 따라 다른 사용자를 검색합니다. 앱을 닫았다가 다시 실행합니다. 사용자의 세션을 그대로 유지하는 방법을 알아두세요. 로그아웃했다가 다른 사용자로 다시 로그인합니다.

ADAL을 통해 응용 프로그램에 이러한 일반적인 ID 기능을 쉽게 통합할 수 있습니다. 또한 캐시 관리, OAuth 프로토콜 지원, 사용자에게 로그인 UI 제공, 만료된 토큰 새로 고침을 비롯한 귀찮은 작업을 관리해줍니다. 실제로 알아두어야 할 모든 항목은 단일 API 호출, `authContext.AcquireTokenAsync(...)`입니다.

참조용으로 [GitHub의](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip) 완성된 샘플(사용자 구성 값 제외)을 참조하세요.

## <a name="next-steps"></a>다음 단계

OAuth 2.0 전달자 액세스 토큰을 사용하여 웹 API를 보호하는 방법을 알아보세요.
> [!div class="nextstepaction"]
> [Azure AD를 사용하여 .NET Web API 보안 유지 >>](quickstart-v1-dotnet-webapi.md)
