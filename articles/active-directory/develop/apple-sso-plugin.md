---
title: Apple 디바이스용 Microsoft Enterprise SSO 플러그 인
titleSuffix: Microsoft identity platform | Azure
description: IOS 및 macOS 장치에 대 한 Microsoft의 Azure Active Directory SSO 플러그 인에 대해 알아봅니다.
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
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982585"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인 (미리 보기)

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

*Apple 장치에 대 한 Microsoft Enterprise SSO 플러그* 인은 apple의 [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) 기능을 지 원하는 모든 응용 프로그램에서 Azure Active Directory (Azure AD) 계정에 대 한 Single Sign-On (SSO) 계정을 제공 합니다. Microsoft는 apple과 긴밀 하 게 협력 하 여 Apple 및 Microsoft가 제공할 수 있는 최상의 보호 기능을 제공 하는 동시에 응용 프로그램의 유용성을 향상 시키기 위해이 플러그 인을 개발 했습니다.

이 공개 미리 보기 릴리스에서 Enterprise SSO 플러그 인은 iOS 장치에만 사용할 수 있으며 특정 Microsoft 응용 프로그램에 배포 됩니다.

Enterprise SSO 플러그 인을 처음 사용 하는 것은 새로운 [공유 장치 모드](msal-ios-shared-devices.md) 기능을 사용 하는 것입니다.

## <a name="features"></a>기능

Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인은 다음과 같은 이점을 제공 합니다.

- Apple의 Enterprise Single Sign-On 기능을 지 원하는 모든 응용 프로그램에서 Azure AD 계정에 대 한 SSO를 제공 합니다.
- Microsoft Authenticator에서 자동으로 배달 되며 모든 MDM (모바일 장치 관리) 솔루션에서 사용 하도록 설정할 수 있습니다.

## <a name="requirements"></a>요구 사항

Apple 장치에 Microsoft Enterprise SSO 플러그 인을 사용 하려면 다음을 수행 합니다.

- iOS 13.0 이상이 장치에 설치 되어 있어야 합니다.
- Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 제공 하는 Microsoft 응용 프로그램을 장치에 설치 해야 합니다. 공개 미리 보기의 경우 이러한 응용 프로그램에는 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md)이 포함 됩니다.
- 장치가 MDM에 등록 되어야 합니다 (예: Microsoft Intune).
- 장치에서 Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 사용 하도록 설정 하려면 장치에 구성을 푸시 해야 합니다. 이 보안 제약 조건은 Apple에 필요 합니다.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>MDM (모바일 장치 관리)을 사용 하 여 SSO 확장을 사용 하도록 설정

Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 사용 하도록 설정 하려면 장치에서 MDM 서비스를 통해 신호를 전송 해야 합니다. Microsoft는 [Microsoft Authenticator 앱](..//user-help/user-help-auth-app-overview.md)에 Enterprise SSO 플러그 인을 포함 하므로 microsoft Enterprise SSO 플러그 인을 사용 하도록 앱을 구성 하려면 MDM을 사용 합니다.

다음 매개 변수를 사용 하 여 Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인을 구성 합니다.

- **유형**: 리디렉션
- **확장 ID**:`com.microsoft.azureauthenticator.ssoextension`
- **팀 ID**:`SGGM6D27TK`
- **Url**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Microsoft Intune를 MDM 서비스로 사용 하 여 Microsoft Enterprise SSO 플러그 인을 쉽게 구성할 수 있습니다. 자세한 내용은 [Intune 구성 설명서](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)를 참조 하세요.

## <a name="using-the-sso-extension-in-your-application"></a>응용 프로그램에서 SSO 확장 사용

[Apple 장치용 MSAL (Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-objc) ) 버전 1.1.0 이상에서는 Apple 장치용 microsoft Enterprise SSO 플러그 인을 지원 합니다.

Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인에서 제공 하는 공유 장치 모드를 지원 하려는 경우 응용 프로그램이 지정 된 최소의 MSAL 버전을 사용 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

IOS의 공유 장치 모드에 대 한 자세한 내용은 [ios 장치에 대 한 공유 장치 모드](msal-ios-shared-devices.md)를 참조 하세요.
