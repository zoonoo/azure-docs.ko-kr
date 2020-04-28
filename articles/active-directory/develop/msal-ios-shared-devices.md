---
title: iOS 디바이스의 공유 디바이스 모드
titleSuffix: Microsoft identity platform | Azure
description: Firstline Worker가 iOS 장치를 공유할 수 있도록 공유 장치 모드를 사용 하도록 설정 하는 방법을 알아봅니다.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550254"
---
# <a name="shared-device-mode-for-ios-devices"></a>iOS 디바이스의 공유 디바이스 모드

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

소매 연결, 비행 crew 구성원 및 현장 서비스 작업자와 같은 firstline Worker는 종종 공유 모바일 장치를 사용 하 여 작업을 수행 합니다. 사용자가 자신의 암호나 Pin을 공유 장치에서 고객 및 비즈니스 데이터에 액세스 하는 경우 이러한 공유 장치는 보안 위험을 나타낼 수 있습니다.

공유 장치 모드를 사용 하면 iOS 13 이상 장치를 직원 들이 보다 쉽고 안전 하 게 공유할 수 있도록 구성할 수 있습니다. 직원은 로그인 하 여 고객 정보에 신속 하 게 액세스할 수 있습니다. Shift 또는 작업을 완료 하면 장치에서 로그 아웃할 수 있으며 다음 직원은 즉시 사용할 수 있습니다.

공유 장치 모드는 장치의 Microsoft id 지원 관리도 제공 합니다.

이 기능은 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md) 을 사용 하 여 장치의 사용자를 관리 하 고 [Apple 장치용 Microsoft Enterprise SSO 플러그](apple-sso-plugin.md)인을 배포 합니다.

## <a name="create-a-shared-device-mode-app"></a>공유 장치 모드 앱 만들기

공유 장치 모드 앱을 만들기 위해 개발자와 클라우드 장치 관리자는 함께 작동 합니다.

1. **응용 프로그램 개발자** 는 단일 계정 앱 (여러 계정 앱은 공유 장치 모드에서 지원 되지 않음)을 작성 하 고 공유 장치 로그 아웃 같은 항목을 처리 하는 코드를 작성 합니다.

1. **장치 관리자** 는 조직의 장치를 관리 하는 MICROSOFT INTUNE 같은 MDM (모바일 장치 관리) 공급자를 사용 하 여 장치를 공유할 준비를 합니다. MDM은 Microsoft Authenticator 앱을 장치에 푸시 하 고 장치에 대 한 프로필 업데이트를 통해 각 장치에 대해 "공유 모드"를 설정 합니다. 이 공유 모드 설정은 장치에서 지원 되는 앱의 동작을 변경 합니다. MDM 공급자의이 구성은 장치에 대 한 공유 장치 모드를 설정 하 고 공유 장치 모드에 필요한 [Apple 장치에 대 한 Microsoft Enterprise SSO 플러그](apple-sso-plugin.md) 인을 사용 하도록 설정 합니다.

1. [**공개 미리 보기 중에만 필요**] [클라우드 장치 관리자](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) 역할이 있는 사용자는 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md) 을 시작 하 고 조직에 장치를 연결 해야 합니다.

    Azure Portal에서 조직 역할의 멤버 자격을 구성 하려면: **Azure Active Directory** > **역할 및 관리자** > **클라우드 장치 관리자**

다음 섹션에서는 공유 장치 모드를 지원 하도록 응용 프로그램을 업데이트 하는 방법을 설명 합니다.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Intune을 사용 하 여 공유 장치 모드 & SSO 확장을 사용 하도록 설정

> [!NOTE]
> 다음 단계는 공개 미리 보기로 제공 되는 경우에만 필요 합니다.

공유 장치 모드를 지원 하도록 장치를 구성 해야 합니다. IOS 13 이상이 설치 되어 있고 MDM에 등록 되어 있어야 합니다. 또한 MDM 구성은 [Apple 장치에 대해 Microsoft Enterprise SSO 플러그](apple-sso-plugin.md)인을 사용 하도록 설정 해야 합니다. SSO 확장에 대해 자세히 알아보려면 [Apple 비디오](https://developer.apple.com/videos/play/tech-talks/301/)를 참조 하세요.

1. Intune 구성 포털에서 다음 구성을 사용 하 여 [Apple 장치에 대 한 Microsoft Enterprise SSO 플러그](apple-sso-plugin.md) 인을 사용 하도록 장치에 지시 합니다.

    - **유형**: 리디렉션
    - **확장 ID**: com ssoextension
    - **팀 ID**: SGGM6D27TK
    - **Url**:https://login.microsoftonline.com
    - 구성할 추가 데이터:
      - 키: sharedDeviceMode
      - 형식: Boolean
      - 값: True

    Intune을 사용 하 여를 구성 하는 방법에 대 한 자세한 내용은 [intune 구성 설명서](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)를 참조 하세요.

1. 다음으로 mdm 프로필을 통해 장치에 Microsoft Authenticator 앱을 푸시하는 MDM을 구성 합니다.

    공유 장치 모드를 설정 하려면 다음 구성 옵션을 설정 합니다.

    - 구성 1:
      - 키: sharedDeviceMode
      - 형식: Boolean
      - 값: True

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>공유 장치 모드를 지원 하도록 iOS 응용 프로그램 수정

사용자는 자신의 데이터가 다른 사용자에 게 유출 되지 않도록 하는 데 의존 합니다. 다음 섹션에서는 변경이 발생 하 여 처리 해야 함을 응용 프로그램에 알리는 데 유용한 신호를 제공 합니다.

앱을 사용할 때마다 장치에서 사용자의 상태를 확인 한 다음 이전 사용자의 데이터를 지울 책임이 있습니다. 이는 다중 태스킹의 백그라운드에서 다시 로드 되는 경우를 포함 합니다.

사용자가 변경 하는 경우 이전 사용자의 데이터를 모두 지우고 응용 프로그램에 표시 되는 캐시 된 데이터가 모두 제거 되었는지 확인 해야 합니다. 공유 장치 모드를 지원 하도록 앱을 업데이트 한 후 사용자와 회사에서 보안 검토 프로세스를 수행 하는 것이 좋습니다.

### <a name="detect-shared-device-mode"></a>공유 장치 모드 검색

응용 프로그램에는 공유 장치 모드를 검색 하는 것이 중요 합니다. 응용 프로그램을 공유 장치에서 사용 하는 경우 많은 응용 프로그램에서 사용자 환경 (UX)을 변경 해야 합니다. 예를 들어 응용 프로그램에 이미 계정이 있을 가능성이 있으므로 Firstline 작업자에 게 적합 하지 않은 "등록" 기능이 있을 수 있습니다. 또한 공유 장치 모드에 있는 경우 응용 프로그램의 데이터 처리에 추가 보안을 추가할 수 있습니다.

에서 API `getDeviceInformationWithParameters:completionBlock:` 를 사용 `MSALPublicClientApplication` 하 여 공유 장치 모드의 장치에서 앱이 실행 되 고 있는지 확인 합니다.

다음 코드 조각에서는 API를 `getDeviceInformationWithParameters:completionBlock:` 사용 하는 예제를 보여 줍니다.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>로그인 한 사용자를 가져오고 사용자가 장치에서 변경 되었는지 확인 합니다.

공유 장치 모드를 지 원하는 또 다른 중요 한 부분은 장치의 사용자 상태를 확인 하 고 사용자가 변경 되었거나 장치에 사용자가 없는 경우 응용 프로그램 데이터를 지우는 것입니다. 다른 사용자가 데이터를 유출 하지 않도록 할 책임이 있습니다.

API를 사용 `getCurrentAccountWithParameters:completionBlock:` 하 여 장치에서 현재 로그인 된 계정을 쿼리할 수 있습니다.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>전역적으로 사용자 로그인

장치가 공유 장치로 구성 된 경우 응용 프로그램은 `acquireTokenWithParameters:completionBlock:` API를 호출 하 여 계정에 로그인 할 수 있습니다. 계정에서 첫 번째 앱 로그인 후 장치에서 모든 적격 앱에 대해 전역적으로 계정을 사용할 수 있습니다.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>전역적으로 사용자 로그아웃

다음 코드는 로그인 된 계정을 제거 하 고 앱 뿐만 아니라 공유 장치 모드에 있는 장치 에서도 캐시 된 토큰을 지웁니다. 그러나 응용 프로그램에서 *데이터* 를 지워야 하는 것은 아닙니다. 응용 프로그램에서 데이터를 지우고 사용자에 게 표시 될 수 있는 캐시 된 데이터를 지워야 합니다.

#### <a name="clear-browser-state"></a>브라우저 상태 지우기

> [!NOTE]
> 다음 단계는 공개 미리 보기로 제공 되는 경우에만 필요 합니다.

이 공개 미리 보기 버전에서 [Apple 장치에 대 한 Microsoft Enterprise SSO 플러그](apple-sso-plugin.md) 인은 응용 프로그램에 대해서만 상태를 지웁니다. Safari 브라우저에서 상태를 지우지 않습니다. 사용자 상태에 대 한 추적을 유지 하지 않으려면 브라우저 세션을 수동으로 삭제 하는 것이 좋습니다. 아래 표시 된 선택적 `signoutFromBrowser` 속성을 사용 하 여 쿠키를 지울 수 있습니다. 이렇게 하면 브라우저를 장치에서 잠깐 시작 합니다.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>다음 단계

작동 중인 공유 장치 모드를 확인 하기 위해 GitHub의 다음 코드 샘플에는 공유 장치 모드의 iOS 장치에서 Firstline Worker 앱을 실행 하는 예가 포함 되어 있습니다.

[MSAL iOS Swift Microsoft Graph API 샘플](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
