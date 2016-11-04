---
title: Xamarin iOS 앱용 모바일 서비스에서 인증 시작 | Microsoft Docs
description: 모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Xamarin iOS 앱 사용자를 인증하는 방법을 알아봅니다.
services: mobile-services
documentationcenter: xamarin
author: lindydonna
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: donnam

---
# 모바일 서비스 앱에 인증 추가
[!INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> 이 항목에 해당하는 모바일 앱 버전은 [Xamarin.iOS 앱에 인증 추가](../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md)를 참조하세요.
> 
> 

이 항목은 앱에서 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증을 위해 앱 등록 및 모바일 서비스 구성]
2. [테이블 사용 권한을 인증된 사용자로 제한]
3. [앱에 인증 추가]

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다.

## <a name="register"></a>인증을 위해 앱 등록 및 모바일 서비스 구성
[!INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[!INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;&nbsp;6. Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 장치 또는 시뮬레이터에서 실행합니다. 앱 시작 후 상태 코드가 401(권한이 부여되지 않음)인 처리되지 않은 예외가 발생했는지 확인합니다.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a>앱에 인증 추가
이 섹션에서는 데이터 표시 전에 로그인 화면을 표시하도록 앱을 수정합니다. 앱이 시작될 때 앱은 모바일 서비스에 연결하지 않으며, 데이터를 표시하지 않습니다. 사용자가 새로 고침 제스처를 처음 수행한 다음 로그인 화면이 나타나고, 로그인이 성공하면 todo 항목 목록이 표시됩니다.

1. 클라이언트 프로젝트에서 **QSTodoService.cs** 파일을 열고 다음 선언을 QSTodoService에 추가합니다.
   
        // Mobile Service logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. 다음 정의를 포함하여 새 메서드 **Authenticate**를 **QSTodoService**에 추가합니다.
   
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
   
   > [!NOTE]
   > Facebook 이외의 ID 공급자를 사용하는 경우, 위의 **LoginAsync**에 전달된 값을 *MicrosoftAccount*, *Twitter*, *Google* 또는 *WindowsAzureActiveDirectory* 중 하나로 변경합니다.
   > 
   > 
3. **QSTodoListViewController.cs**를 열고 **ViewDidLoad**의 메서드 정의를 수정해서 끝 부근의 **RefreshAsync()** 호출을 제거하거나 주석으로 처리합니다.
4. **RefreshAsync** 메서드 정의 상단에서 다음 코드를 추가합니다.
   
        // Add at the start of the RefreshAsync method.
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("You must sign in.");
                return;
            }
        }
   
    **User** 속성이 null인 경우 인증을 시도하기 위해 로그인 화면을 표시합니다. 로그인이 성공하면 **User**가 설정됩니다.
5. **실행** 단추를 눌러 프로젝트를 빌드하고 iPhone 시뮬레이터에서 앱을 시작합니다. 앱이 데이터를 표시하지 않는지 확인합니다. **RefreshAsync()**가 아직 호출되지 않았습니다.
6. 항목 목록을 아래로 끌어서 새로 고침 제스처를 수행하면 **RefreshAsync()**를 호출합니다. 그러면 인증을 시작하기 위해 **Authenticate()**이 호출되고 로그인 화면이 표시됩니다. 인증을 성공한 후에는 앱이 todo 항목 목록을 표시하고 사용자가 데이터를 업데이트할 수 있습니다.

## <a name="next-steps"> </a>다음 단계
다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여][Authorize users with scripts]에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다.

<!-- Anchors. -->
[인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
[테이블 사용 권한을 인증된 사용자로 제한]: #permissions
[앱에 인증 추가]: #add-authentication
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[모바일 서비스 시작]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[JavaScript and HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

<!---HONumber=AcomDC_0727_2016-->