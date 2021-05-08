---
title: FIDO2 암호 없는 인증에 대한 브라우저 지원 | Azure Active Directory
description: 브라우저 및 운영 체제 조합은 Azure Active Directory를 사용하여 앱에 대한 FIDO2 암호 없는 인증을 지원합니다.
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
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632104"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>FIDO2 암호 없는 인증에 대한 브라우저 지원

Azure Active Directory를 사용하여 [FIDO2 보안 키](./concept-authentication-passwordless.md#fido2-security-keys)를 암호 없는 디바이스로 사용할 수 있습니다. Microsoft 계정에 대한 FIDO2 인증 가용성은 [2018에서 발표](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910)되었습니다. 공지에서 설명한 대로 Microsoft 및 Azure Active Directory 계정으로 보안 인증을 지원하려면 특정 옵션 기능 및 FIDO2 CTAP 사양에 대한 확장을 구현해야 합니다. 다음 다이어그램은 Azure Active Directory에서 FIDO2 인증 키를 사용하여 암호 없는 인증을 지원하는 브라우저 및 운영 체제 조합을 보여 줍니다.

## <a name="supported-browsers"></a>지원되는 브라우저

이 표에서는 Azure Active Directory(Azure AD) 및 Microsoft 계정(MSA)을 인증하는 기능을 보여 줍니다. Microsoft 계정은 소비자가 Xbox, Skype 또는 Outlook.com과 같은 서비스를 사용하기 위해 만듭니다. 지원되는 디바이스 유형에는 **USB**, NFC(**근거리 통신**), BLE(**Bluetooth Low Energy**)가 포함됩니다.

| OS | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![Chrome은 AAD 계정에 대해 Windows에서 USB를 지원합니다.][y] | ![Chrome은 AAD 계정에 대해 Windows에서 NFC를 지원합니다.][y] | ![Chrome은 AAD 계정에 대해 Windows에서 BLE를 지원합니다.][y] | ![Edge는 AAD 계정에 대해 Windows에서 USB를 지원합니다.][y] | ![Edge는 AAD 계정에 대해 Windows에서 NFC를 지원합니다.][y] | ![Edge는 AAD 계정에 대해 Windows에서 BLE를 지원합니다.][y] | ![Firefox는 AAD 계정에 대해 Windows에서 USB를 지원합니다.][y] | ![Firefox는 AAD 계정에 대해 Windows에서 NFC를 지원합니다.][y] | ![Firefox는 AAD 계정에 대해 Windows에서 BLE를 지원합니다.][y] |
| **macOS**  | ![Chrome은 AAD 계정에 대해 macOS에서 USB를 지원합니다.][y] | ![Chrome은 AAD 계정에 대해 macOS에서 NFC를 지원하지 않습니다.][n] | ![Chrome은 AAD 계정에 대해 macOS에서 BLE를 지원하지 않습니다.][n] | ![Edge는 AAD 계정에 대해 macOS에서 USB를 지원합니다.][y] | ![Edge는 AAD 계정에 대해 macOS에서 NFC를 지원하지 않습니다.][n] | ![Edge는 AAD 계정에 대해 macOS에서 BLE를 지원하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 macOS에서 USB를 지원하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 macOS에서 NFC를 지원하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 macOS에서 BLE를 지원하지 않습니다.][n] |
| **Linux**  | ![Chrome은 AAD 계정에 대해 Linux에서 USB를 지원합니다.][y] | ![Chrome은 AAD 계정에 대해 Linux에서 NFC를 지원하지 않습니다.][n] | ![Chrome은 AAD 계정에 대해 Linux에서 BLE를 지원하지 않습니다.][n] | ![Edge는 AAD 계정에 대해 Linux에서 USB를 지원하지 않습니다.][n] | ![Edge는 AAD 계정에 대해 Linux에서 NFC를 지원하지 않습니다.][n] | ![Edge는 AAD 계정에 대해 Linux에서 BLE를 지원하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 Linux에서 USB를 지원하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 Linux에서 NFC를 지원하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 Linux에서 BLE를 지원하지 않습니다.][n] |



## <a name="unsupported-browsers"></a>지원되지 않는 브라우저

다음 운영 체제 및 브라우저 조합은 지원되지 않지만 향후 지원 및 테스트를 조사 중입니다. 다른 운영 체제 및 브라우저 지원을 보려면 페이지 맨 아래에 있는 제품 사용자 의견 도구를 사용하여 피드백을 남겨 주세요.

| 운영 체제 | 브라우저 |
| ---- | ---- |
| iOS | Safari, Brave |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>최소 브라우저 버전

최소 브라우저 버전 요구 사항은 다음과 같습니다. 

| 브라우저 | 최소 버전 |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 버전 1903<sup>1</sup> |
| Firefox | 66 |

<sup>1</sup>새 Chromium 기반 Microsoft Edge의 모든 버전은 Fido2를 지원합니다. Microsoft Edge 레거시 지원은 1903에 추가되었습니다.

## <a name="next-steps"></a>다음 단계
[암호 없는 보안 키 로그인 사용(미리 보기)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
