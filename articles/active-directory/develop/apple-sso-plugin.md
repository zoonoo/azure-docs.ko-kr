---
title: 애플 장치에 대 한 마이크로소프트 엔터프라이즈 SSO 플러그인
titleSuffix: Microsoft identity platform | Azure
description: iOS 및 macOS 장치에 대한 Microsoft의 Azure Active 디렉터리 SSO 플러그인에 대해 알아봅니다.
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
ms.openlocfilehash: 7233a0bba5cf30eab018bfa744a7322303300604
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550319"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>애플 장치에 대 한 마이크로소프트 엔터프라이즈 SSO 플러그인 (미리 보기)

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

*Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인은* Apple의 엔터프라이즈 단일 사인온 기능을 지원하는 모든 응용 프로그램에서 Active Directory 계정에 대한 단일 사인온(SSO)을 제공합니다. [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) Microsoft는 Apple과 긴밀히 협력하여 이 플러그인을 개발하여 응용 프로그램의 유용성을 높이는 동시에 Apple과 Microsoft가 제공할 수 있는 최상의 보호 기능을 제공했습니다.

이 공개 미리 보기 릴리스에서 엔터프라이즈 SSO 플러그인은 iOS 장치에서만 사용할 수 있으며 특정 Microsoft 응용 프로그램에 배포됩니다.

엔터프라이즈 SSO 플러그인의 첫 번째 사용은 우리의 새로운 [공유 장치 모드](msal-ios-shared-devices.md) 기능입니다.

## <a name="features"></a>기능

Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인은 다음과 같은 이점을 제공합니다.

- Apple의 엔터프라이즈 단일 사인온 기능을 지원하는 모든 응용 프로그램에서 Active Directory 계정에 대한 SSO를 제공합니다.
- Microsoft 인증기에서 자동으로 제공되며 모든 MDM(모바일 장치 관리) 솔루션에서 사용할 수 있습니다.

## <a name="requirements"></a>요구 사항

애플 장치에 대 한 마이크로소프트 엔터프라이즈 SSO 플러그인을 사용 하려면:

- iOS 13.0 이상은 장치에 설치해야 합니다.
- Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인을 제공하는 Microsoft 응용 프로그램을 장치에 설치해야 합니다. 공개 미리 보기의 경우 이러한 응용 프로그램에는 [Microsoft 인증자 앱이](../user-help/user-help-auth-app-overview.md)포함됩니다.
- 장치는 MDM에 등록되어 있어야 합니다(예: Microsoft Intune).
- 장치에서 Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인을 사용하려면 구성을 장치에 푸시해야 합니다. 이 보안 제약 조건은 Apple에서 요구합니다.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>모바일 장치 관리(MDM)를 사용하여 SSO 확장 사용

Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인을 사용하려면 MDM 서비스를 통해 장치를 신호를 보내야 합니다. Microsoft는 [Microsoft 인증자 앱에](..//user-help/user-help-auth-app-overview.md)엔터프라이즈 SSO 플러그인을 포함하므로 MDM을 사용하여 Microsoft 엔터프라이즈 SSO 플러그인을 사용하도록 앱을 구성합니다.

다음 매개 변수를 사용하여 Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인을 구성합니다.

- **유형**: 리디렉션
- **확장 ID**:`com.microsoft.azureauthenticator.ssoextension`
- **팀 ID**:`SGGM6D27TK`
- **URL**:
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

Microsoft Intune을 MDM 서비스로 사용하여 Microsoft 엔터프라이즈 SSO 플러그인의 구성을 용이하게 할 수 있습니다. 자세한 내용은 [Intune 구성 설명서를](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)참조하십시오.

## <a name="using-the-sso-extension-in-your-application"></a>응용 프로그램에서 SSO 확장 사용

애플 장치 버전 1.1.0 [이상에 대한 마이크로 소프트 인증 라이브러리 (MSAL)는](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 애플 장치에 대한 마이크로 소프트 엔터프라이즈 SSO 플러그인을 지원합니다.

Apple 장치에 대한 Microsoft 엔터프라이즈 SSO 플러그인에서 제공하는 공유 장치 모드를 지원하려면 응용 프로그램에서 지정된 최소 MSAL 버전을 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

iOS의 공유 장치 모드에 대한 자세한 내용은 [iOS 장치의 공유 장치 모드를](msal-ios-shared-devices.md)참조하십시오.
