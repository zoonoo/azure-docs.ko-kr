---
title: "UWP(유니버설 Windows 플랫폼) 앱에 인증 추가 | Microsoft Docs"
description: "Azure 앱 서비스 모바일 앱을 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 사용해서 UWP(유지버설 Windows 플랫폼) 앱 사용자를 인증하는 방법을 알아봅니다."
services: app-service\mobile
documentationcenter: windows
author: adrianhall
manager: erikre
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 17ced2fb19b2beb057b67a0aff2f67fb2bfe49e9


---
# <a name="add-authentication-to-your-windows-app"></a>Windows 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

이 항목에서는 모바일 앱에 클라우드 기반 인증을 추가하는 방법을 보여줍니다. 이 자습서에서는 Azure 앱 서비스가 지원하는 ID 공급자를 사용하여 모바일 앱용 UWP(유니버설 Windows 플랫폼) 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 앱 백 엔드에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 모바일 앱 퀵 스타트를 기반으로 합니다. 먼저 [모바일 앱 시작](app-service-mobile-windows-store-dotnet-get-started.md)자습서를 완료해야 합니다.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

이제 백 엔드에 대한 익명 액세스가 비활성화되었는지 확인할 수 있습니다. 시작 프로젝트로 설정된 UWP 앱 프로젝트를 사용하여 앱을 배포하고 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 이 예외는 앱이 인증되지 않은 사용자로 모바일 앱 코드에 액세스하려고 시도하는데 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 앱 서비스에서 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="a-nameadd-authenticationaadd-authentication-to-the-app"></a><a name="add-authentication"></a>앱에 인증 추가
1. UWP 프로젝트 파일 MainPage.cs에서 MainPage 클래스에 다음 코드 조각을 추가합니다.
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    이 코드는 Facebook 로그인으로 사용자를 인증합니다. Facebook 이외의 ID 공급자를 사용하는 경우 위의 **MobileServiceAuthenticationProvider** 값을 공급자에 대한 값으로 변경합니다.
2. 기존 **OnNavigatedTo** 메서드 재정의에서 **ButtonRefresh_Click** 메서드(또는 **InitLocalStoreAsync** 메서드)에 대한 호출을 주석 처리하거나 삭제합니다. 그러면 사용자가 인증되기 전에 데이터가 로드되지 않습니다. 다음으로, 인증을 트리거하는 앱에 **로그인** 단추를 추가합니다.
3. MainPage 클래스에 다음 코드 조각을 추가합니다.
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. MainPage.xaml 프로젝트 파일을 열고 **저장** 단추를 정의하는 요소를 찾아서 다음 코드로 바꿉니다.
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. F5 키를 눌러 앱을 실행하고 **로그인** 단추를 클릭한 다음 선택한 ID 공급자로 앱에 로그인합니다. 성공적으로 로그인되고 나면 앱이 오류 없이 실행되고 백 엔드를 쿼리하여 데이터를 업데이트할 수 있습니다.

## <a name="a-nametokensastore-the-authentication-token-on-the-client"></a><a name="tokens"></a>클라이언트에 인증 토큰 저장
이전 예제에서는 앱이 시작될 때마다 클라이언트가 ID 공급자와 앱 서비스 둘 다에 접근해야 하는 표준 로그인을 보여 주었습니다. 이 방법은 비효율적일 뿐 아니라 많은 고객이 동시에 앱을 시작하려고 할 경우 사용 관련 문제가 발생할 수도 있습니다. 더 나은 접근 방법은 앱 서비스에서 반환된 권한 부여 토큰을 캐시한 다음 공급자 기반 로그인을 사용하기 전에 이 토큰을 먼저 사용하는 것입니다.

> [!NOTE]
> 클라이언트 관리 인증 또는 서비스 관리 인증을 사용하는지에 관계없이 앱 서비스에서 발급된 토큰을 캐시할 수 있습니다. 이 자습서에서는 서비스 관리 인증을 사용합니다.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>다음 단계
이 기본 인증 자습서를 완료했으므로 다음 자습서 중 하나를 계속하는 것을 고려해보세요.

* [앱에 푸시 알림 추가](app-service-mobile-windows-store-dotnet-get-started-push.md)  
   앱에 푸시 알림 지원을 추가하고 모바일 앱 백 엔드를 구성하여 푸시 알림을 보내는 Azure 알림 허브를 사용하는 방법을 알아봅니다.
* [앱에 오프라인 동기화 사용](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
   모바일 앱 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다.

<!-- URLs. -->
[모바일 앱 시작]: app-service-mobile-windows-store-dotnet-get-started.md




<!--HONumber=Nov16_HO3-->


