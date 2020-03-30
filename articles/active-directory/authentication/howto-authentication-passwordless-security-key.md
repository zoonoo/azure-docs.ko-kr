---
title: 암호없는 보안 키 로그인 (미리 보기) - Azure Active Directory
description: FIDO2 보안 키를 사용하여 Azure AD에 암호 없는 보안 키 로그인 활성화(미리 보기)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e088d239a91edeff34ecd1a7dc5be7a9f8628da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129153"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>암호 없는 보안 키 로그인(미리 보기) 활성화

현재 암호를 사용하고 공유 PC 환경을 사용하는 기업의 경우 보안 키는 사용자가 사용자 이름이나 암호를 입력하지 않고도 원활하게 인증할 수 있는 방법을 제공합니다. 보안 키는 작업자의 생산성을 향상시키고 보안을 향상시키며 더 나은 보안을 제공합니다.

이 문서에서는 보안 키 기반 암호 없는 인증을 사용하도록 설정하는 데 중점을 둡니다. 이 문서의 끝에서 FIDO2 보안 키를 사용 하 여 Azure AD 계정으로 웹 기반 응용 프로그램에 로그인할 수 있습니다.

|     |
| --- |
| FIDO2 보안 키는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

## <a name="requirements"></a>요구 사항

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [결합된 보안 정보 등록 미리 보기](concept-registration-mfa-sspr-combined.md)
- 호환 [FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN 필요 윈도우 10 버전 1809 이상**

웹 앱 및 서비스에 로그인할 때 보안 키를 사용하려면 WebAuthN 프로토콜을 지원하는 브라우저가 있어야 합니다. 여기에는 마이크로소프트 에지, 크롬, 파이어 폭스 및 사파리가 포함됩니다.

## <a name="prepare-devices-for-preview"></a>미리 보기를 위한 장치 준비

파일럿을 실행하는 Azure AD 조인 장치는 Windows 10 버전 1809 이상을 실행해야 합니다. 최상의 경험은 윈도우에 10 버전 1903 이상.

하이브리드 Azure AD 조인 된 장치는 Windows 10 내부자 빌드 18945 또는 최신을 실행해야 합니다.

## <a name="enable-passwordless-authentication-method"></a>암호 없는 인증 방법 사용

### <a name="enable-the-combined-registration-experience"></a>결합된 등록 환경 사용

암호 없는 인증 방법에 대 한 등록 기능은 결합 된 등록 미리 보기에 의존 합니다. 결합된 등록 미리 보기를 사용하려면 다음 문서의 단계를 [따르십시오.](howto-registration-mfa-sspr-combined.md)

### <a name="enable-fido2-security-key-method"></a>FIDO2 보안 키 방법 사용

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. Azure **Active Directory** > **보안** > **인증 방법** > **인증 방법 정책(미리 보기)으로**찾아봅니다.
1. **FIDO2 보안 키**방법에서 다음 옵션을 선택합니다.
   1. **사용 -** 예 또는 아니요
   1. **대상** - 모든 사용자 또는 사용자 선택
1. 구성을 **저장합니다.**

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 보안 키의 사용자 등록 및 관리

1. 을 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)찾아보십시오.
1. 아직 로그인하지 않은 경우 로그인합니다.
1. **보안 정보를 클릭합니다.**
   1. 사용자가 이미 하나 이상의 Azure 다단계 인증 방법을 등록한 경우 FIDO2 보안 키를 즉시 등록할 수 있습니다.
   1. 등록된 Azure 다단계 인증 방법이 하나 이상 없는 경우 하나를 추가해야 합니다.
1. **메서드 추가를** 클릭하고 보안 키를 선택하여 FIDO2 보안 **키를**추가합니다.
1. **USB 장치** 또는 **NFC 장치를**선택합니다.
1. 키를 준비하고 **다음을**선택합니다.
1. 상자가 나타나고 사용자에게 보안 키에 대한 PIN을 생성/입력한 다음 생체 인식 또는 터치로 키에 필요한 제스처를 수행하도록 요청합니다.
1. 사용자는 결합된 등록 환경으로 돌아가고 키에 대한 의미 있는 이름을 제공하라는 메시지가 요청되므로 사용자가 여러 항목이 있는 경우 어느 이름을 식별할 수 있는지 확인할 수 있습니다. **다음**을 클릭합니다.
1. **완료를** 클릭하여 프로세스를 완료합니다.

## <a name="sign-in-with-passwordless-credential"></a>암호 없는 자격 증명으로 로그인

아래 예제에서 사용자가 이미 FIDO2 보안 키를 프로비전했습니다. 사용자는 Windows 10 버전 1809 이상에서 지원되는 브라우저 내부에 FIDO2 보안 키를 사용하여 웹에서 로그인하도록 선택할 수 있습니다.

![보안 키 로그인 마이크로 소프트 에지](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>문제 해결 및 피드백

이 기능을 미리 볼 때 피드백을 공유하거나 문제가 발생하려면 다음 단계를 사용하여 Windows 피드백 허브 앱을 통해 공유하십시오.

1. **피드백 허브를** 시작하고 로그인했는지 확인합니다.
1. 다음 분류에 따라 피드백을 제출합니다.
   - 카테고리: 보안 및 개인 정보 보호
   - 하위 범주: FIDO
1. 로그를 캡처하려면 **내 문제를 다시 만드는** 옵션을 사용합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="security-key-provisioning"></a>보안 키 프로비저닝

보안 키의 관리자 프로비저닝 및 프로비저닝 해제는 공개 미리 보기에서 사용할 수 없습니다.

### <a name="upn-changes"></a>UPN 변경 사항

하이브리드 Azure AD 조인 및 Azure AD 조인 장치에서 UPN 변경을 허용하는 기능을 지원하기 위해 노력하고 있습니다. 사용자의 UPN이 변경되면 변경을 설명하기 위해 FIDO2 보안 키를 더 이상 수정할 수 없습니다. 해결 방법은 장치를 재설정하는 것이며 사용자는 다시 등록해야 합니다.

## <a name="next-steps"></a>다음 단계

[FIDO2 보안 키 윈도우 10 로그인](howto-authentication-passwordless-security-key-windows.md)

[온-프레미스 리소스에 FIDO2 인증 사용](howto-authentication-passwordless-security-key-on-premises.md)

[기기 등록에 대해 자세히 알아보기](../devices/overview.md)

[Azure Multi-Factor Authentication에 대해 자세히 알아보기](../authentication/howto-mfa-getstarted.md)
