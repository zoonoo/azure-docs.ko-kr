---
title: FIDO2 암호 없는 인증의 브라우저 지원 | Azure Active Directory
description: 브라우저 및 운영 체제 조합은 Azure Active Directory를 사용 하 여 앱에 대 한 FIDO2 암호 없는 인증을 지원 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e5eb77a27a7c74ad9738a0e1784066a6a859e43
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175371"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>FIDO2 암호 없는 인증에 대 한 브라우저 지원

Azure Active Directory를 사용 하 여 [FIDO2 보안 키](./concept-authentication-passwordless.md#fido2-security-keys) 를 암호 없는 장치로 사용할 수 있습니다. Microsoft 계정에 대 한 FIDO2 인증의 가용성은 [2018에서 발표](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910)되었습니다. 공지에서 설명한 대로 Microsoft 및 Azure Active Directory 계정으로 보안 인증을 지원 하려면 특정 옵션 기능 및 FIDO2 CTAP 사양에 대 한 확장을 구현 해야 합니다. 다음 다이어그램은 Azure Active Directory에서 FIDO2 인증 키를 사용 하 여 암호 없는 인증을 지 원하는 브라우저 및 운영 체제 조합을 보여 줍니다.

## <a name="supported-browsers"></a>지원되는 브라우저

이 표에서는 Azure Active Directory (Azure AD) 및 Microsoft 계정 (MSA)을 인증 하는 기능을 보여 줍니다. Microsoft 계정은 고객이 Xbox, Skype 또는 Outlook.com와 같은 서비스를 위해 만듭니다. 지원 되는 장치 유형에는 **USB**,**NFC**(근거리 통신) 및 bluetooth 저 **에너지 (가장** 작은 에너지)가 포함 됩니다.

| OS | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | 스크램블 | USB | NFC | 스크램블 | USB | NFC | 스크램블 |
| **Windows**  | ![Chrome은 AAD 계정에 대해 Windows에서 USB를 지원 합니다.][y] | ![Chrome은 AAD 계정에 대해 Windows에서 NFC를 지원 합니다.][y] | ![Chrome은 AAD 계정에 대해 Windows에서이를 지원 합니다.][y] | ![Edge는 AAD 계정에 대해 Windows에서 USB를 지원 합니다.][y] | ![Edge는 AAD 계정에 대해 Windows에서 NFC를 지원 합니다.][y] | ![Edge는 AAD 계정에 대해 Windows에서의 것을 지원 합니다.][y] | ![Firefox는 AAD 계정에 대해 Windows에서 USB를 지원 합니다.][y] | ![Firefox는 AAD 계정에 대해 Windows에서 NFC를 지원 합니다.][y] | ![Firefox는 AAD 계정에 대해 Windows에서의 것을 지원 합니다.][y] |
| **macOS**  | ![Chrome은 AAD 계정에 대해 macOS에서 USB를 지원 합니다.][y] | ![Chrome은 AAD 계정에 대 한 macOS에서 NFC를 지원 하지 않습니다.][n] | ![Chrome은 AAD 계정에 대 한 macOS에 대 한 것을 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대해 macOS에서 USB를 지원 합니다.][y] | ![Edge는 AAD 계정에 대 한 macOS에서 NFC를 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대 한 macOS에 대 한 것을 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 macOS에서 USB를 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대 한 macOS에서 NFC를 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대 한 macOS에 대 한 것을 지원 하지 않습니다.][n] |
| **Linux**  | ![Chrome은 AAD 계정에 대해 Linux에서 USB를 지원 합니다.][y] | ![Chrome은 AAD 계정에 대 한 Linux에서 NFC를 지원 하지 않습니다.][n] | ![Chrome은 AAD 계정에 대 한 Linux의 모든 것을 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대해 Linux에서 USB를 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대 한 Linux에서 NFC를 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대 한 Linux의 것을 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 Linux에서 USB를 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대 한 Linux에서 NFC를 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대 한 Linux의 것을 지원 하지 않습니다.][n] |



## <a name="unsupported-browsers"></a>지원 되지 않는 브라우저

다음 운영 체제 및 브라우저 조합은 지원 되지 않지만 향후 지원 및 테스트를 조사 하 고 있습니다. 다른 운영 체제 및 브라우저 지원을 보려면 페이지 맨 아래에 있는 제품 사용자 의견 도구를 사용 하 여 피드백을 남겨 주세요.

| 운영 체제 | 브라우저 |
| ---- | ---- |
| iOS | Safari, 용감한 |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>최소 브라우저 버전

최소 브라우저 버전 요구 사항은 다음과 같습니다. 

| 브라우저 | 최소 버전 |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 버전 1903<sup>1</sup> |
| Firefox | Chrome |
| ChromeOS | 66 |

<sup>1</sup> 새 Chromium 기반 Microsoft Edge의 모든 버전은 Fido2을 지원 합니다. Microsoft Edge legacy에서의 지원은 1903에 추가 되었습니다.

## <a name="next-steps"></a>다음 단계
[암호 없는 보안 키 로그인 사용 (미리 보기)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
