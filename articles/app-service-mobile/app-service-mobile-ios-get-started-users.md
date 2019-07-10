---
title: Azure Mobile Apps를 사용하여 iOS에서 인증 추가
description: Azure Mobile Apps를 사용하여 AAD, Google, Facebook, Twitter 및 Microsoft를 포함한 다양한 ID 공급자를 통해 iOS 앱 사용자를 인증하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 88e278ced5cbddb132cdc2f760864df119762088
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449130"
---
# <a name="add-authentication-to-your-ios-app"></a>iOS 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발을 위한 중앙 새 및 통합 서비스에 투자 합니다. 개발자가 사용할 수 **빌드**를 **테스트** 하 고 **배포** 연속 통합 및 배달 파이프라인을 설정 하는 서비스입니다. 개발자 상태 및 사용 하 여 해당 앱의 사용량을 모니터링할 수 있습니다, 앱을 배포한 후 합니다 **Analytics** 하 고 **진단** , 서비스를 사용 하 여 사용자와 소통 하세요를 **푸시** 서비스입니다. 개발자가 활용할 수도 있습니다 **인증** 해당 사용자를 인증 하 고 **데이터** 유지 하 고 클라우드에 앱 데이터 동기화 서비스. 체크 아웃 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-users) 지금 합니다.
>

이 자습서에서는 지원되는 ID 공급자를 사용하여 [iOS 빠른 시작] 에 인증을 추가합니다. 이 자습서는 먼저 완료해야 하는 [iOS 빠른 시작] 를 기반으로 합니다.

## <a name="register"></a>인증을 위해 앱 등록 및 App Service 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>허용되는 외부 리디렉션 URL에 앱 추가

보안 인증을 위해서는 앱에 대한 새로운 URL 체계를 정의해야 합니다.  이를 통해 인증 시스템은 인증 프로세스가 완료되면 앱으로 다시 리디렉션될 수 있습니다.  이 자습서에서는 전체적으로 URL 체계 _appname_을 사용합니다.  그러나 선택한 어떤 URL 체계도 사용 가능합니다.  이 체계는 모바일 애플리케이션에 고유해야 합니다.  서버 쪽에서 리디렉션을 사용하도록 설정하려면

1. [Azure Portal]에서 해당 App Service를 선택합니다.

2. **인증/권한 부여** 메뉴 옵션을 클릭합니다.

3. **인증 공급자** 섹션에서 **Azure Active Directory**를 클릭합니다.

4. **관리 모드**를 **고급**으로 설정합니다.

5. **허용되는 외부 리디렉션 URL**에서 `appname://easyauth.callback`을 입력합니다.  이 문자열의 _appname_은 모바일 애플리케이션에 대한 URL 체계입니다.  이 체계는 프로토콜에 대한 일반 URL 사양을 따라야 합니다(문자 및 숫자만 사용하고 문자로 시작).  여러 위치에서 URL 체계에 따라 모바일 애플리케이션 코드를 조정해야 할 경우 선택한 문자열을 적어두어야 합니다.

6. **확인**을 클릭합니다.

7. **저장**을 클릭합니다.

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Xcode에서 **실행** 을 눌러 앱을 시작합니다. 앱이 인증되지 않은 사용자로 백 엔드에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 예외가 발생합니다.

## <a name="add-authentication"></a>앱에 인증 추가
**Objective-C**:

1. Mac에서 Xcode를 사용하여 *QSTodoListViewController.m*을 열고 다음 메서드를 추가합니다.

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Google을 ID 공급자로 사용하지 않는 경우 *google*을 *microsoftaccount*, *twitter*, *facebook* 또는 *windowsazureactivedirectory*로 변경합니다. Facebook을 사용 해야 합니다 [Facebook 도메인을 허용 목록][1] 앱에서.

    **urlScheme**을 애플리케이션에 대한 고유한 이름으로 바꿉니다.  urlScheme은 Azure Portal의 **외부 리디렉션 Url 허용** 필드에 지정한 URL 체계 프로토콜과 같아야 합니다. urlScheme은 인증 요청이 완료된 후 인증 콜백에서 애플리케이션으로 다시 전환하는 데 사용됩니다.

2. *QSTodoListViewController.m*의 `viewDidLoad`에 있는 `[self refresh]`를 다음 코드로 바꿉니다.

    ```Objective-C
    [self loginAndGetData];
    ```

3. `QSAppDelegate.h` 파일을 열고 다음 코드를 추가합니다.

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. `QSAppDelegate.m` 파일을 열고 다음 코드를 추가합니다.

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   `#pragma mark - Core Data stack` 줄 바로 앞에 다음 코드를 추가합니다.  대체는 _appname_ 1 단계에서 사용한 urlScheme 값입니다.

5. `AppName-Info.plist` 파일을 열고(AppName을 앱 이름으로 바꿈) 다음 코드를 추가합니다.

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    이 코드는 `<dict>` 요소 안에 추가해야 합니다.  _appname_ 문자열(**CFBundleURLSchemes**에 대한 배열 내에 있음)를 1단계에서 선택한 앱 이름으로 바꿉니다.  이러한 사항을 plist 편집기에서 변경할 수 있습니다. XCode에서 `AppName-Info.plist` 파일을 클릭하여 plist 편집기를 엽니다.

    **CFBundleURLName**에 대한 `com.microsoft.azure.zumo` 문자열을 Apple 번들 식별자로 바꿉니다.

6. *Run*을 눌러 앱을 시작한 다음 로그인합니다. 로그인할 때 할 일 목록을 보고 업데이트할 수 있어야 합니다.

**Swift**:

1. Mac의 Xcode에서 *ToDoTableViewController.swift*를 열고 다음 메서드를 추가합니다.

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Google을 ID 공급자로 사용하지 않는 경우 *google*을 *microsoftaccount*, *twitter*, *facebook* 또는 *windowsazureactivedirectory*로 변경합니다. Facebook을 사용 해야 합니다 [Facebook 도메인을 허용 목록][1] 앱에서.

    **urlScheme**을 애플리케이션에 대한 고유한 이름으로 바꿉니다.  urlScheme은 Azure Portal의 **외부 리디렉션 Url 허용** 필드에 지정한 URL 체계 프로토콜과 같아야 합니다. urlScheme은 인증 요청이 완료된 후 인증 콜백에서 애플리케이션으로 다시 전환하는 데 사용됩니다.

2. *ToDoTableViewController.swift*에 있는 `viewDidLoad()`의 끝에서 `self.refreshControl?.beginRefreshing()` 및 `self.onRefresh(self.refreshControl)` 줄을 제거합니다. 그 자리에 `loginAndGetData()` 에 대한 호출을 추가합니다.

    ```swift
    loginAndGetData()
    ```

3. `AppDelegate.swift` 파일을 열고 `AppDelegate` 클래스에 다음 줄을 추가합니다.

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    대체는 _appname_ 1 단계에서 사용한 urlScheme 값입니다.

4. `AppName-Info.plist` 파일을 열고(AppName을 앱 이름으로 바꿈) 다음 코드를 추가합니다.

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    이 코드는 `<dict>` 요소 안에 추가해야 합니다.  _appname_ 문자열(**CFBundleURLSchemes**에 대한 배열 내에 있음)를 1단계에서 선택한 앱 이름으로 바꿉니다.  이러한 사항을 plist 편집기에서 변경할 수 있습니다. XCode에서 `AppName-Info.plist` 파일을 클릭하여 plist 편집기를 엽니다.

    **CFBundleURLName**에 대한 `com.microsoft.azure.zumo` 문자열을 Apple 번들 식별자로 바꿉니다.

5. *Run*을 눌러 앱을 시작한 다음 로그인합니다. 로그인할 때 할 일 목록을 보고 업데이트할 수 있어야 합니다.

App Service 인증은 Apples Inter-App Communication을 사용합니다.  이 주제에 대 한 자세한 내용은 참조는 [Apple 설명서][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[iOS 빠른 시작]: app-service-mobile-ios-get-started.md

