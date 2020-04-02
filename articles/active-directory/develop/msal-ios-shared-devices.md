---
title: iOS 기기용 공유 장치 모드
titleSuffix: Microsoft identity platform | Azure
description: Firstline 작업자가 iOS 장치를 공유할 수 있도록 공유 장치 모드를 사용하도록 설정하는 방법 알아보기
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
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550254"
---
# <a name="shared-device-mode-for-ios-devices"></a>iOS 기기용 공유 장치 모드

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

소매 직원, 승무원 및 현장 서비스 근로자와 같은 일선 근로자는 종종 공유 모바일 장치를 사용하여 작업을 수행합니다. 이러한 공유 장치는 사용자가 의도적으로 또는 사용하지 않는 암호 또는 PIN을 공유하여 공유 장치에서 고객 및 비즈니스 데이터에 액세스하는 경우 보안 위험을 초래할 수 있습니다.

공유 장치 모드를 사용하면 iOS 13 이상의 장치를 직원들이 보다 쉽고 안전하게 공유할 수 있도록 구성할 수 있습니다. 직원은 로그인하여 고객 정보에 빠르게 액세스할 수 있습니다. 교대 근무 나 작업이 끝나면 장치에서 로그 아웃 할 수 있으며 다음 직원이 즉시 사용할 준비가됩니다.

또한 공유 장치 모드는 장치의 Microsoft ID 지원 관리를 제공합니다.

이 기능은 [Microsoft 인증자 앱을](../user-help/user-help-auth-app-overview.md) 사용하여 장치의 사용자를 관리하고 [Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인을](apple-sso-plugin.md)배포합니다.

## <a name="create-a-shared-device-mode-app"></a>공유 장치 모드 앱 만들기

공유 장치 모드 앱을 만들려면 개발자와 클라우드 장치 관리자가 함께 작업합니다.

1. **응용 프로그램 개발자는** 단일 계정 앱을 작성하고(다중 계정 앱은 공유 장치 모드에서 지원되지 않음) 공유 장치 로그아웃과 같은 작업을 처리하는 코드를 작성합니다.

1. **장치 관리자는** Microsoft Intune과 같은 모바일 장치 관리(MDM) 공급자를 사용하여 장치를 공유하여 조직의 장치를 관리하도록 준비합니다. MDM은 Microsoft 인증자 앱을 장치에 푸시하고 장치에 대한 프로필 업데이트를 통해 각 장치에 대해 "공유 모드"를 켭니다. 이 공유 모드 설정은 장치에서 지원되는 앱의 동작을 변경합니다. MDM 공급자의 이 구성은 장치에 대한 공유 장치 모드를 설정하고 공유 장치 모드에 필요한 [Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인을](apple-sso-plugin.md) 활성화합니다.

1. [**공개 미리 보기 중만 필요]** 그런 다음 [클라우드 장치 관리자](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) 역할을 가진 사용자는 Microsoft [인증자 앱을](../user-help/user-help-auth-app-overview.md) 실행하고 해당 장치를 조직에 조인해야 합니다.

    Azure 포털에서 조직 역할의 구성원을 구성하려면 Azure **Active Directory** > **역할 및 관리자** > **클라우드 장치 관리자**

다음 섹션에서는 공유 장치 모드를 지원하도록 응용 프로그램을 업데이트하는 데 도움이 됩니다.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Intune을 사용하여 공유 장치 모드 & SSO 확장을 활성화합니다.

> [!NOTE]
> 다음 단계는 공개 미리 보기 중에만 필요합니다.

공유 장치 모드를 지원하도록 장치를 구성해야 합니다. iOS 13+ 설치 되어 있어야 하며 MDM에 등록 되어 있어야 합니다. MDM 구성은 또한 [애플 장치에 대 한 마이크로소프트 엔터프라이즈 SSO 플러그인을](apple-sso-plugin.md)사용 해야 합니다. SSO 확장에 대한 자세한 내용은 [Apple 비디오를](https://developer.apple.com/videos/play/tech-talks/301/)참조하십시오.

1. Intune 구성 포털에서 다음 구성을 사용하여 [Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인을](apple-sso-plugin.md) 사용하도록 장치에 알려줍니다.

    - **유형**: 리디렉션
    - **확장 ID**: com.microsoft.azure인증기.ssoextension
    - **팀 ID**: SGGM6D27TK
    - **URL**:https://login.microsoftonline.com
    - 구성할 추가 데이터:
      - 키: 공유디바이스모드
      - 형식: Boolean
      - 값: 트루

    Intune으로 구성에 대한 자세한 내용은 [Intune 구성 설명서를](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)참조하십시오.

1. 다음으로 MDM 프로필을 통해 Microsoft 인증자 앱을 장치에 푸시하도록 MDM을 구성합니다.

    공유 장치 모드를 켜려면 다음 구성 옵션을 설정합니다.

    - 구성 1:
      - 키: 공유디바이스모드
      - 형식: Boolean
      - 값: 트루

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>공유 장치 모드를 지원하도록 iOS 응용 프로그램 수정

사용자는 사용자의 데이터가 다른 사용자에게 유출되지 않도록 사용자에 의존합니다. 다음 섹션에서는 변경이 발생하여 처리해야 함을 응용 프로그램에 나타내는 유용한 신호를 제공합니다.

앱이 사용될 때마다 기기의 사용자 상태를 확인한 다음 이전 사용자의 데이터를 지운 다음 지워야 합니다. 여기에는 멀티태스킹에서 백그라운드에서 다시 로드되는 경우가 포함됩니다.

사용자 변경 시 이전 사용자의 데이터가 모두 지워지고 응용 프로그램에 표시되는 캐시된 데이터가 모두 제거되었는지 확인해야 합니다. 공유 장치 모드를 지원하도록 앱을 업데이트한 후 사용자와 회사에서 보안 검토 프로세스를 수행하는 것이 좋습니다.

### <a name="detect-shared-device-mode"></a>공유 장치 모드 감지

공유 장치 모드를 검색하는 것은 응용 프로그램에 중요합니다. 대부분의 응용 프로그램은 공유 장치에서 응용 프로그램을 사용할 때 사용자 환경(UX)을 변경해야 합니다. 예를 들어 응용 프로그램에 "등록" 기능이 있을 수 있는데, 이는 이미 계정이 있기 때문에 일선 작업자에게 는 적합하지 않습니다. 또한 공유 장치 모드에 있는 경우 응용 프로그램의 데이터 처리에 보안을 추가할 수도 있습니다.

에서 `getDeviceInformationWithParameters:completionBlock:` `MSALPublicClientApplication` API를 사용하여 공유 장치 모드에서 앱이 장치에서 실행되고 있는지 확인합니다.

다음 코드 조각은 `getDeviceInformationWithParameters:completionBlock:` API 사용의 예를 보여 준다.

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>로그인한 사용자를 얻고 사용자가 장치에서 변경되었는지 확인합니다.

공유 장치 모드를 지원하는 또 다른 중요한 부분은 사용자가 변경되었거나 장치에 사용자가 전혀 없는 경우 장치의 사용자 상태를 결정하고 응용 프로그램 데이터를 지우는 것입니다. 데이터가 다른 사용자에게 유출되지 않도록 할 책임은 사용자에게 있습니다.

API를 `getCurrentAccountWithParameters:completionBlock:` 사용하여 장치에서 현재 로그인한 계정을 쿼리할 수 있습니다.

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

장치가 공유 장치로 구성된 경우 응용 프로그램은 API를 `acquireTokenWithParameters:completionBlock:` 호출하여 계정에 로그인할 수 있습니다. 계정은 계정의 첫 번째 앱 로그인 후 기기의 모든 적격 앱에 대해 전 세계적으로 사용할 수 있습니다.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>전역적으로 사용자 로그아웃

다음 코드는 로그인된 계정을 제거하고 앱뿐만 아니라 공유 장치 모드에 있는 장치에서도 캐시된 토큰을 지웁니다. 그러나 응용 프로그램의 *데이터를* 지우지는 않습니다. 응용 프로그램에서 데이터를 지우고 응용 프로그램이 사용자에게 표시할 수 있는 캐시된 데이터를 지워야 합니다.

#### <a name="clear-browser-state"></a>브라우저 상태 지우기

> [!NOTE]
> 다음 단계는 공개 미리 보기 중에만 필요합니다.

이 공개 미리 보기 버전에서는 [Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인이](apple-sso-plugin.md) 응용 프로그램에 대해서만 상태를 지웁습니다. Safari 브라우저에서 상태가 명확하지 않습니다. 사용자 상태의 흔적이 남지 않도록 브라우저 세션을 수동으로 지우는 것이 좋습니다. 아래 표시된 선택적 `signoutFromBrowser` 속성을 사용하여 쿠키를 지울 수 있습니다. 이렇게 하면 브라우저가 장치에서 잠시 실행됩니다.

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

GitHub의 다음 코드 샘플에는 공유 장치 모드에서 iOS 장치에서 Firstline Worker 앱을 실행하는 예제가 포함됩니다.

[MSAL iOS 스위프트 마이크로 소프트 그래프 API 샘플](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
