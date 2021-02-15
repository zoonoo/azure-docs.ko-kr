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
ms.openlocfilehash: a0a8f96fabdff3543222077f5113b0bd602997b7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417427"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>FIDO2 암호 없는 인증에 대 한 브라우저 지원

Azure Active Directory를 사용 하 여 [FIDO2 보안 키](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-keys) 를 암호 없는 장치로 사용할 수 있습니다. Microsoft 계정에 대 한 FIDO2 인증의 가용성은 [2018에서 발표](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910)되었습니다. 공지에서 설명한 대로 FIDO2 CTAP 사양에 대 한 특정 옵션 기능 및 확장은 Microsoft 및 Azure Active Directory 계정으로 보안 인증을 지원 하기 위해 구현 되어야 합니다. 다음 다이어그램은 Azure Active Directory에서 FIDO2 인증 키를 사용 하 여 암호 없는 인증을 지 원하는 브라우저 및 운영 체제 조합을 보여 줍니다.

## <a name="supported-browsers"></a>지원되는 브라우저

이 표에서는 Azure Active Directory (Azure AD) 및 Microsoft 계정 (MSA)을 인증 하는 기능을 보여 줍니다. Microsoft 계정은 고객이 Xbox, Skype 또는 Outlook.com와 같은 서비스를 위해 만듭니다. 지원 되는 장치 유형에는 **USB**,**NFC**(근거리 통신) 및 bluetooth 저 **에너지 (가장** 작은 에너지)가 포함 됩니다.

|  | Chrome |  |  | Edge |  |  | Firefox |  |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | 스크램블 | USB | NFC | 스크램블 | USB | NFC | 스크램블 |
| **Windows**  | ![Chrome은 AAD 계정에 대해 Windows에서 USB를 지원 합니다.][y] | ![Chrome은 AAD 계정에 대해 Windows에서 NFC를 지원 합니다.][y] | ![Chrome은 AAD 계정에 대해 Windows에서이를 지원 합니다.][y] | ![Edge는 AAD 계정에 대해 Windows에서 USB를 지원 합니다.][y] | ![Edge는 AAD 계정에 대해 Windows에서 NFC를 지원 합니다.][y] | ![Edge는 AAD 계정에 대해 Windows에서의 것을 지원 합니다.][y] | ![Firefox는 AAD 계정에 대해 Windows에서 USB를 지원 합니다.][y] | ![Firefox는 AAD 계정에 대해 Windows에서 NFC를 지원 합니다.][y] | ![Firefox는 AAD 계정에 대해 Windows에서의 것을 지원 합니다.][y] |
| **macOS**  | ![Chrome은 AAD 계정에 대해 macOS에서 USB를 지원 합니다.][y] | ![Chrome은 AAD 계정에 대 한 macOS에서 NFC를 지원 하지 않습니다.][n] | ![Chrome은 AAD 계정에 대 한 macOS에 대 한 것을 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대해 macOS에서 USB를 지원 합니다.][y] | ![Edge는 AAD 계정에 대 한 macOS에서 NFC를 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대 한 macOS에 대 한 것을 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 macOS에서 USB를 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대 한 macOS에서 NFC를 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대 한 macOS에 대 한 것을 지원 하지 않습니다.][n] |
| **Linux**  | ![Chrome은 AAD 계정에 대해 Linux에서 USB를 지원 합니다.][y] | ![Chrome은 AAD 계정에 대 한 Linux에서 NFC를 지원 하지 않습니다.][n] | ![Chrome은 AAD 계정에 대 한 Linux의 모든 것을 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대해 Linux에서 USB를 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대 한 Linux에서 NFC를 지원 하지 않습니다.][n] | ![Edge는 AAD 계정에 대 한 Linux의 것을 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대해 Linux에서 USB를 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대 한 Linux에서 NFC를 지원 하지 않습니다.][n] | ![Firefox는 AAD 계정에 대 한 Linux의 것을 지원 하지 않습니다.][n] |

## <a name="operating-system-versions-tested"></a>테스트 된 운영 체제 버전

위의 표에 나와 있는 정보는 다음 운영 체제 버전에 대해 테스트 되었습니다.

| 운영 체제 | 테스트 된 최신 버전 |
| --- | --- |
| Windows | Windows 10 20H2 1904 |
| macOS | OS X 11 빅 성 |
| Linux | Fedora 32 워크스테이션 |

## <a name="next-steps"></a>다음 단계
[암호 없는 보안 키 로그인 사용 (미리 보기)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
