---
title: Azure Active Directory 암호 없는 로그인 (미리 보기)
description: FIDO2 보안 키 또는 Microsoft Authenticator 앱 (미리 보기)를 사용 하 여 Azure AD에 암호 없는 로그인
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712086"
---
# <a name="what-is-passwordless"></a>암호 없는 란?

Multi-factor authentication (MFA), 조직을 보호 하는 좋은 방법 이지만 사용자가 자신의 암호를 기억할 필요가 맨 위에 추가 계층을 사용 하 여 걷 잡을 수 있습니다. 암호 없는 인증 메서드는 암호는 제거 되 고 사용자와 사용자 또는 알고로 대체 하기 때문에 편리 합니다.

|   | 오류가 발생 | 또는 인지 알고 있어야 하는 항목 |
| --- | --- | --- |
| 암호 없음 | 전화 또는 보안 키 | 생체 인식 또는 PIN |

인증에 있어 각 조직 마다 요구 사항이 인식 합니다. Microsoft 현재는 Windows Hello를 Windows PC에 대 한 프리미어 암호 없는 경험 합니다. 새 자격 증명 암호 없는 패밀리에 추가 합니다. Microsoft Authenticator 앱 및 FIDO2 보안 키입니다.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 앱

암호 없는 인증 메서드를 직원의 전화를 허용 합니다. 이미 사용할 수는 Microsoft Authenticator 앱 암호 외에도 편리한 multi-factor authentication 인증 옵션으로 합니다. 이지만 이제 암호 없는 옵션으로 사용할 수 있습니다.

모든 iOS 또는 Android 휴대폰으로 바뀝니다 강력 하 고 암호 없는 자격 증명을 사용자가 휴대폰으로 알림 가져오기, 휴대폰에 한 화면에 표시 된 숫자를 일치 하는 자신의 생체 인식 (사용 하 여 모든 플랫폼 또는 브라우저에 로그인 할 수 있도록 하 여 touch 또는 안 면) 또는 확인을 위해 PIN입니다.

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO2 보안 키는 모든 폼 팩터에서 발생할 수 있는 unphishable 암호 없는 인증 표준 기반 방법입니다. 빠른 온라인 FIDO (Id)은 암호 없는 인증을 위한 개방형 표준입니다. 사용자 조직과 표준을 활용 하 여 사용자 이름 또는 장치에 기본 제공 되는 플랫폼 키나 외부 보안 키를 사용 하 여 암호 없이 해당 리소스에 로그인 할 수 있습니다.

공개 미리 보기에 대 한 직원 외부 보안 키를 사용 하 여 (1809 이상 버전을 실행)가 Azure Active Directory 가입 Windows 10 컴퓨터에 로그인 하 고 single-sign 클라우드 리소스를 가져올 수 있습니다. 지원 되는 브라우저 로그인도 합니다.

FIDO2 FIDO Alliance에 의해 인증 된 많은 키 있기는 최대 보안 및 최상의 환경을 위해 공급 업체에서 구현 되어야 FIDO2 CTAP 사양의 일부 선택적 확장 해야 합니다.

보안 키 **해야** 다음 기능 및 Microsoft 호환 되도록 FIDO2 CTAP 프로토콜의 확장을 구현 합니다.

| # | 기능 확장 신뢰 / | 이유는 필요 합니까? |
| --- | --- | --- |
| 1 | 상주 키 | 이 기능을 사용 하면 보안 키 자격 증명 보안 키에 저장 된 경우 이식 가능 하며 되도록 합니다. |
| 2 | 클라이언트 pin | 이 기능은 두 번째 요소를 사용 하 여 자격 증명을 보호할 수 있도록 하 고 사용자 인터페이스가 없는 보안 키에 적용 합니다. |
| 3 | hmac-secret | 이 확장에 로그인 할 수 있습니다 장치를 비행기 모드 또는 오프 라인 상태일 때 확인 합니다. |
| 4 | RP 당 여러 계정 | 이 기능을 사용 하면 Microsoft 계정과 Azure Active Directory와 같은 여러 서비스에서 동일한 보안 키를 사용할 수 있습니다. |

다음 공급자 paswordless 환경과 호환 가능한 것으로 알려져 있는 다양 한 폼 팩터로의 FIDO2 보안 키를 제공 합니다. FIDO Alliance 뿐만 아니라 공급 업체에 문의 하 여 이러한 키의 보안 속성을 평가 하는 고객이 사용 하는 것이 좋습니다.

| 공급자 | Contact |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

공급 업체 및이 목록에 장치를 가져오려면 원하는 있다면에 문의 [ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com)합니다.

FIDO2 보안 키는 중요 한 매우 보안 하거나 시나리오 또는 또는 휴대폰을 사용 하 여 두 번째 단계로 능력 부재 아닌 직원이 기업에 적합 한 옵션입니다.

## <a name="what-scenarios-work-with-the-preview"></a>미리 보기는 시나리오에서 작동 합니까?

1. 관리자는 테 넌 트에 대 한 암호 없는 인증 방법을 설정할 수 있습니다.
1. 관리자가 모든 사용자를 대상 수 또는 각 메서드에 대 한 테 넌 트 내에서 사용자/그룹 선택
1. 최종 사용자가 등록 하 고 해당 계정 포털에서 이러한 암호 없는 인증 방법을 관리 수 있습니다.
1. 이러한 암호 없는 인증 방법을 사용 하 여 최종 사용자 로그인 수 있습니다.
   1. Microsoft Authenticator 앱: Azure AD 인증을 사용 하는 모든 경우에는 작업 간의 모든 브라우저를 비롯 한 Windows 10 아웃 상자 (OOBE) 설치 프로그램을 사용 하 여는 동안 통합 것 모든 운영 체제에서 모바일 앱입니다.
   1. 보안 키: Windows 10 버전 1809 이상에 대 한 잠금 화면 및 웹의 Microsoft Edge 등 지원 되는 브라우저에서 작동 합니다.

## <a name="next-steps"></a>다음 단계

[조직에서 암호 없는 옵션을 사용 하도록 설정](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>외부 링크

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP 사양](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
