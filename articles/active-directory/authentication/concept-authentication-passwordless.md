---
title: Azure Active Directory 암호 없는 로그인 (미리 보기)
description: FIDO2 보안 키 또는 Microsoft Authenticator 앱 (미리 보기)을 사용 하 여 Azure AD에 passwordless 로그인
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfcd46f06035e356f6528a79b749350627541121
ms.sourcegitcommit: 9858ab651a520c26f0ed18215e650efbf1fc5de9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303535"
---
# <a name="what-is-passwordless"></a>암호 없음이란?

MFA (multi-factor authentication)는 조직을 보호 하는 훌륭한 방법 이지만 사용자가 암호를 기억할 필요 없이 추가 계층을 사용 하지 않아도 됩니다. 암호는 제거 되 고 사용자가 알고 있는 것과 다른 항목으로 대체 되기 때문에 암호 없는 인증 방법이 더 편리 합니다.

|   | 보유 한 항목 | 사용자 또는 알고 있는 항목 |
| --- | --- | --- |
| 암호 없음 | 전화 또는 보안 키 | 생체 인식 또는 PIN |

각 조직에는 인증을 할 때 서로 다른 요구 사항이 있습니다. Microsoft는 현재 Windows Pc에 대 한 Windows Hello를 제공 합니다. Microsoft Authenticator app 및 FIDO2 보안 키를 암호 없는 패밀리에 추가 합니다.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 앱

직원의 전화를 암호 없는 인증 방법으로 사용할 수 있습니다. 암호 외에도 편리한 다단계 인증 옵션으로 Microsoft Authenticator 앱을 이미 사용 하 고 있을 수 있습니다. 그러나 이제 암호 없는 옵션으로 사용할 수 있습니다.

![Microsoft Authenticator 앱을 사용 하 여 Microsoft Edge에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

사용자가 휴대폰에 대 한 알림을 받고, 화면에 표시 되는 숫자를 휴대폰에 있는 것과 일치 시키고, 생체 인식 ()을 사용 하 여 사용자가 모든 플랫폼 또는 브라우저에 로그인 할 수 있도록 하 여 iOS 또는 Android 휴대폰을 강력 하 고 암호 없는 자격 증명으로 바꿉니다 ( touch 또는 face)를 확인 합니다.

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO2 보안 키는 모든 폼 팩터에서 제공 될 수 있는 unphishable 표준 기반 암호 없는 인증 방법입니다. Fast Identity Online (FIDO)은 암호 없는 인증을 위한 개방형 표준입니다. 이 기능을 사용 하면 사용자와 조직이 외부 보안 키 또는 장치에 기본 제공 되는 플랫폼 키를 사용 하 여 사용자 이름 또는 암호 없이 해당 리소스에 로그인 할 수 있습니다.

공개 미리 보기의 경우 직원은 외부 보안 키를 사용 하 여 Azure Active Directory 연결 된 Windows 10 컴퓨터 (버전 1809 이상)에 로그인 하 고 클라우드 리소스에 대 한 single sign-on을 받을 수 있습니다. 지원 되는 브라우저에 로그인 할 수도 있습니다.

![보안 키를 사용 하 여 Microsoft Edge에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

FIDO 동맹에 의해 FIDO2 인증 되는 많은 키가 있지만, Microsoft는 최대 보안 및 최상의 환경을 보장 하기 위해 공급 업체에서 구현 해야 하는 FIDO2 CTAP 사양의 몇 가지 선택적 확장을 요구 합니다.

보안 키는 FIDO2 CTAP 프로토콜에서 다음 기능 및 확장을 Microsoft 호환으로 구현 **해야 합니다** .

| # | 기능/확장 신뢰 | 이 기능 또는 확장이 필요한 이유는 무엇 인가요? |
| --- | --- | --- |
| 1 | 상주 키 | 이 기능을 통해 보안 키를 이식할 수 있습니다. 여기서 자격 증명은 보안 키에 저장 됩니다. |
| 2 | 클라이언트 pin | 이 기능을 사용 하면 두 번째 요소로 자격 증명을 보호 하 고 사용자 인터페이스가 없는 보안 키에 적용할 수 있습니다. |
| 3 | hmac-secret | 이 확장을 통해 오프 라인 이거나 비행기 모드에서 장치에 로그인 할 수 있습니다. |
| 4 | RP 당 여러 계정 | 이 기능을 사용 하면 Microsoft 계정 및 Azure Active Directory 같은 여러 서비스에서 동일한 보안 키를 사용할 수 있습니다. |

다음 공급자는 암호 없는 환경과 호환 되는 것으로 알려진 다양 한 폼 팩터를 FIDO2 보안 키를 제공 합니다. Microsoft는 고객이 FIDO 동맹 뿐만 아니라 공급 업체에 연락 하 여 이러한 키의 보안 속성을 평가 하도록 권장 합니다.

| 공급자 | 연락처 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

이 목록에서 장치를 구입 하려는 경우에는 [-1 @no__t](mailto:Fido2Request@Microsoft.com)문의 하세요.

FIDO2 보안 키는 매우 보안이 중요 하거나, 전화를 두 번째 요소로 사용 하지 않거나 사용할 수 없는 시나리오 또는 직원이 있는 기업에 게 유용한 옵션입니다.

## <a name="what-scenarios-work-with-the-preview"></a>어떤 시나리오가 미리 보기에서 작동 하나요?

- 관리자는 테 넌 트에 대해 암호 없는 인증 방법을 사용할 수 있습니다.
- 관리자는 모든 사용자를 대상으로 하거나 각 방법에 대해 테 넌 트 내에서 사용자/그룹을 선택할 수 있습니다.
- 최종 사용자는 자신의 계정 포털에서 이러한 암호 없는 인증 방법을 등록 하 고 관리할 수 있습니다.
- 최종 사용자는 이러한 암호 없는 인증 방법으로 로그인 할 수 있습니다.
   - Microsoft Authenticator 앱: 는 모든 브라우저를 사용 하는 경우, Windows OOBE (Box) 설정 중, 모든 운영 체제에서 통합 된 모바일 앱을 포함 하 여 Azure AD 인증을 사용 하는 시나리오에서 작동 합니다.
   - 보안 키: 는 Windows 10 버전 1809 이상 및 Microsoft Edge와 같은 지원 되는 브라우저의 웹에 대 한 잠금 화면에서 작동 합니다.

## <a name="next-steps"></a>다음 단계

[조직에서 FIDO2 보안 키 passwordlesss 옵션을 사용 하도록 설정](howto-authentication-passwordless-security-key.md)

[조직에서 전화 기반 암호 없는 옵션 사용](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>외부 링크

[FIDO 동맹](https://fidoalliance.org/)

[FIDO2 CTAP 사양](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
