---
title: Azure Active Directory 암호없는 로그인(미리 보기)
description: FIDO2 보안 키 또는 Microsoft 인증자 앱을 사용하여 Azure Active Directory에 암호 없는 로그인 옵션에 대해 자세히 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 402eaecbf03fd52fbb5e871fdd196da2bc9a3e1f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743525"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory에 대한 암호없는 인증 옵션

MFA(다단계 인증)는 조직을 보호하는 좋은 방법이지만 사용자는 암호를 기억해야 하는 추가 보안 계층에 좌절감을 느낍니다. 암호없는 인증 방법은 암호가 제거되고 당신이 가지고있는 무언가로 대체되기 때문에 더 편리합니다, 플러스 당신이 뭔가 또는 당신이 알고있는 무언가.

|   | 당신이 가지고있는 것 | 당신이 알고 있는 것 |
| --- | --- | --- |
| 암호 없음 | 윈도우 10 장치, 전화, 또는 보안 키 | 생체 인식 또는 PIN |

각 조직에는 인증과 관련하여 서로 다른 요구 사항이 있습니다. Microsoft는 Azure Active Directory(Azure AD)와 통합되는 다음과 같은 세 가지 암호 없는 인증 옵션을 제공합니다.

- 비즈니스용 Windows Hello
- Microsoft Authenticator 앱
- FIDO2 보안 키

![인증: 보안과 편의성](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

비즈니스용 Windows Hello는 지정된 Windows PC를 소유한 정보 작업자에게 이상적입니다. 생체 인식 및 PIN은 사용자의 PC에 직접 연결되어 있어 소유자가 아닌 다른 사람의 액세스를 방지할 수 있습니다. PKI(공개 키 인프라) 통합 및 단일 사인온(SSO)에 대한 기본 제공 지원을 통해 Windows Hello for Business는 온-프레미스 및 클라우드에서 회사 리소스에 원활하게 액세스할 수 있는 편리한 방법을 제공합니다.

![비즈니스용 Windows Hello를 사용 하 여 사용자 로그인의 예](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

다음 단계는 로그인 프로세스가 Azure Active Directory에서 작동하는 방식을 보여 주며,

![비즈니스용 Windows Hello를 통해 사용자 로그인과 관련된 단계를 간략하게 설명하는 다이어그램](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. 사용자가 생체 인식 또는 PIN 제스처를 사용하여 Windows에 로그합니다. 제스처는 비즈니스용 Windows Hello 개인 키의 잠금을 해제하고 클라우드 *AP 공급자라고*하는 클라우드 인증 보안 지원 공급자에게 전송됩니다.
1. 클라우드 AP 공급자는 Azure AD에서 nonce를 요청합니다.
1. Azure AD는 5분 동안 유효한 nonce를 반환합니다.
1. 클라우드 AP 공급자는 사용자의 개인 키를 사용하여 nonce에 서명하고 서명된 nonce를 Azure AD에 반환합니다.
1. Azure AD는 nonce 서명에 대해 사용자의 안전하게 등록된 공개 키를 사용하여 서명된 nonce의 유효성을 검사합니다. 서명의 유효성을 검사한 후 Azure AD는 반환된 서명된 nonce의 유효성을 검사합니다. nonce의 유효성이 검사되면 Azure AD는 장치의 전송 키에 암호화된 세션 키가 있는 기본 새로 고침 토큰(PRT)을 만들고 이를 클라우드 AP 공급자에게 반환합니다.
1. 클라우드 AP 공급자는 세션 키를 가진 암호화된 PRT를 수신합니다. 클라우드 AP 공급자는 장치의 개인 전송 키를 사용하여 세션 키를 해독하고 장치의 신뢰할 수 있는 플랫폼 모듈(TPM)을 사용하여 세션 키를 보호합니다.
1. 클라우드 AP 공급자는 Windows에 대한 성공적인 인증 응답을 반환합니다. 그러면 사용자는 SSO(다시 인증할 필요 없이 클라우드 및 온-프레미스 응용 프로그램)에 Windows에 액세스할 수 있습니다.

비즈니스용 Windows Hello [계획 가이드를](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) 사용하면 비즈니스용 Windows Hello 배포 유형과 고려해야 할 옵션에 대한 결정을 내릴 수 있습니다.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 앱

직원의 휴대폰을 암호 없는 인증 방법으로 허용합니다. 이미 Microsoft 인증자 앱을 암호 외에 편리한 다단계 인증 옵션으로 사용하고 있을 수 있습니다. 인증자 앱을 암호 없는 옵션으로 사용할 수도 있습니다.

![마이크로소프트 인증자 응용 프로그램과 함께 마이크로소프트 가장자리에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

인증자 앱은 모든 iOS 또는 Android 휴대폰을 암호가 없는 강력한 자격 증명으로 바꿉니다. 사용자는 휴대폰에 알림을 받고 화면에 표시된 번호를 휴대폰의 번호와 일치한 다음 생체 인식(터치 또는 얼굴) 또는 PIN을 사용하여 확인하여 모든 플랫폼 또는 브라우저에 로그인할 수 있습니다. 설치 세부 정보는 [다운로드를 참조하여 Microsoft 인증자 앱을 설치하십시오.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)

인증자 앱을 사용한 암호 없는 인증은 비즈니스용 Windows Hello와 동일한 기본 패턴을 따릅니다. Azure AD가 사용 중인 Microsoft 인증자 앱 버전을 찾을 수 있도록 사용자를 식별해야 하므로 약간 더 복잡합니다.

![Microsoft 인증자 앱에서 사용자 로그인과 관련된 단계를 간략하게 설명하는 다이어그램](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. 사용자는 사용자 이름을 입력합니다.
1. Azure AD는 사용자에게 강력한 자격 증명이 있음을 감지하고 강력한 자격 증명 흐름을 시작합니다.
1. 알림은 iOS 기기의 Apple 푸시 알림 서비스(APNS)를 통해 또는 안드로이드 기기의 파이어베이스 클라우드 메시징(FCM)을 통해 앱으로 전송됩니다.
1. 사용자는 푸시 알림을 받고 앱을 엽니다.
1. 앱은 Azure AD를 호출하고 현재 증명 챌린지 및 nonce를 받습니다.
1. 사용자는 생체 인식 또는 PIN을 입력하여 개인 키의 잠금을 해제하여 챌린지를 완료합니다.
1. nonce는 개인 키로 서명되고 Azure AD로 다시 전송됩니다.
1. Azure AD는 공개/개인 키 유효성 검사를 수행하고 토큰을 반환합니다.

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO2 보안 키는 모든 폼 팩터에 올 수 있는 phishable 표준 기반 암호 없는 인증 방법입니다. 빠른 ID 온라인 (FIDO) 암호없는 인증을위한 개방형 표준입니다. FIDO를 사용하면 사용자와 조직이 외부 보안 키 또는 장치에 내장된 플랫폼 키를 사용하여 사용자 이름이나 암호 없이 표준을 활용하여 리소스에 로그인할 수 있습니다.

공개 미리 보기의 경우 직원은 보안 키를 사용하여 Azure AD 또는 하이브리드 Azure AD에 가입하여 Windows 10 장치에 가입하고 클라우드 및 온-프레미스 리소스에 단일 로그온을 받을 수 있습니다. 사용자는 지원되는 브라우저에 로그인할 수도 있습니다. FIDO2 보안 키는 보안에 매우 민감하거나 시나리오가 있거나 휴대폰을 두 번째 요소로 사용할 의사가 없거나 사용할 수 없는 직원에게 적합한 옵션입니다.

![보안 키로 Microsoft Edge에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

다음 프로세스는 사용자가 FIDO2 보안 키로 로인할 때 사용됩니다.

![FIDO2 보안 키를 사용 하 여 사용자 로그인에 관련 된 단계를 설명 하는 다이어그램](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. 사용자는 FIDO2 보안 키를 컴퓨터에 연결합니다.
2. Windows에서 FIDO2 보안 키를 검색합니다.
3. Windows에서 인증 요청을 보냅니다.
4. Azure AD는 nonce를 다시 보냅니다.
5. 사용자는 제스처를 완료하여 FIDO2 보안 키의 보안 영토에 저장된 개인 키의 잠금을 해제합니다.
6. FIDO2 보안 키는 개인 키와 nonce에 서명합니다.
7. 서명된 nonce가 있는 기본 새로 고침 토큰(PRT) 토큰 요청이 Azure AD로 전송됩니다.
8. Azure AD는 FIDO2 공개 키를 사용하여 서명된 nonce를 확인합니다.
9. Azure AD는 PRT를 반환하여 온-프레미스 리소스에 대한 액세스를 활성화합니다.

FIDO 얼라이언스에서 인증한 FIDO2 키가 많지만, Microsoft는 최대 보안과 최상의 환경을 보장하기 위해 공급업체에서 구현하기 위해 FIDO2 클라이언트-인증 프로토콜(CTAP) 사양의 일부 선택적 확장을 요구합니다.

보안 키는 Microsoft와 호환되려면 FIDO2 CTAP 프로토콜의 다음 기능과 확장을 **구현해야 합니다.**

| # | 기능 / 확장 트러스트 | 이 기능 또는 확장이 필요한 이유는 무엇입니까? |
| --- | --- | --- |
| 1 | 상주 키 | 이 기능을 사용하면 자격 증명이 보안 키에 저장되는 보안 키를 이식할 수 있습니다. |
| 2 | 클라이언트 핀 | 이 기능을 사용하면 두 번째 요소로 자격 증명을 보호할 수 있으며 사용자 인터페이스가 없는 보안 키에 적용됩니다. |
| 3 | hmac-비밀 | 이 확장을 사용하면 오프라인 또는 비행기 모드에서 기기에 로그인할 수 있습니다. |
| 4 | RP당 여러 계정 | 이 기능을 사용하면 Microsoft 계정 및 Azure Active Directory와 같은 여러 서비스에서 동일한 보안 키를 사용할 수 있습니다. |

다음 공급자는 암호없는 환경과 호환되는 것으로 알려진 다양한 폼 팩터의 FIDO2 보안 키를 제공합니다. FIDO 얼라이언스뿐만 아니라 공급업체에 문의하여 이러한 키의 보안 속성을 평가하는 것이 좋습니다.

| 공급자 | 연락처 |
| --- | --- |
| 유비코 주 | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| 페이티안 ()과 인 | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| Hid | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| 보증 (영재) | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| 오텐 트렌드 | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| 젬알토 (탈레스 그룹) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| 원스팬 | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| 아이드멜론 테크놀로지스 | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> NFC 기반 보안 키를 구입하고 사용하려는 경우 보안 키에 지원되는 NFC 판독기가 필요합니다. NFC 판독기는 Azure 요구 사항 또는 제한이 아닙니다. 지원되는 NFC 리더 목록의 경우 공급업체에 NFC 기반 보안 키를 확인하십시오.

공급업체이고 지원되는 장치 목록에 장치를 얻으려면 에 문의하십시오. [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)

## <a name="what-scenarios-work-with-the-preview"></a>미리 보기와 함께 작동하는 시나리오는 무엇입니까?

- 관리자는 테넌트에 대해 암호 없는 인증 방법을 사용하도록 설정할 수 있습니다.
- 관리자는 모든 사용자를 대상으로 지정하거나 각 메서드에 대해 테넌트 내의 사용자/그룹을 선택할 수 있습니다.
- 최종 사용자는 계정 포털에서 이러한 암호없는 인증 방법을 등록하고 관리할 수 있습니다.
- 최종 사용자는 이러한 암호 없는 인증 방법으로 로그인할 수 있습니다.
   - Microsoft 인증자 앱: 모든 브라우저, Windows 10 사용 가능(OOBE) 설정 중 및 모든 운영 체제에서 통합모바일 앱을 포함하여 Azure AD 인증이 사용되는 시나리오에서 작동합니다.
   - 보안 키: 윈도우에 대 한 잠금 화면에서 작업 10 마이크로소프트 가장자리 같은 지원 되는 브라우저에서 웹 (레거시 와 새로운 가장자리 모두).

## <a name="choose-a-passwordless-method"></a>암호 없는 방법 선택

이 세 가지 암호 없는 옵션 중에서 선택할 수 있는 옵션은 회사의 보안, 플랫폼 및 앱 요구 사항에 따라 달라집니다.

다음은 Microsoft 암호 없는 기술을 선택할 때 고려해야 할 몇 가지 요소입니다.

||**비즈니스용 Windows Hello**|**Microsoft 인증자 앱으로 암호 없는 로그인**|**FIDO2 보안 키**|
|:-|:-|:-|:-|
|**필수 조건**| Windows 10, version 1809 이상<br>Azure Active Directory| Microsoft Authenticator 앱<br>전화 (안드로이드 6.0 이상을 실행하는 아이폰 OS와 안드로이드 장치.)|Windows 10, version 1809 이상<br>Azure Active Directory|
|**모드**|플랫폼|소프트웨어|하드웨어|
|**시스템 및 장치**|신뢰할 수 있는 플랫폼 모듈(TPM)이 내장된 PC<br>PIN 및 생체 인식 |전화의 PIN 및 생체 인식|Microsoft 호환되는 FIDO2 보안 장치|
|**사용자 환경**|Windows 장치에서 PIN 또는 생체 인식(얼굴, 홍채 또는 지문)을 사용하여 로그인합니다.<br>Windows Hello 인증은 장치에 연결되어 있습니다. 사용자는 회사 리소스에 액세스하기 위해 장치와 PIN 또는 생체 인식 요소와 같은 로그인 구성 요소가 모두 필요합니다.|지문 스캔, 얼굴 또는 홍채 인식 또는 PIN이 있는 휴대폰을 사용하여 로그인합니다.<br>사용자는 PC 또는 휴대폰에서 직장 또는 개인 계정에 로그인합니다.|FIDO2 보안 장치(생체 인식, PIN 및 NFC)를 사용하여 로그인<br>사용자는 조직 제어를 기반으로 장치에 액세스하고 USB 보안 키 및 NFC 지원 스마트 카드, 키 또는 웨어러블과 같은 장치를 사용하여 PIN, 생체 인식을 기반으로 인증할 수 있습니다.|
|**활성화된 시나리오**| Windows 장치에 대한 암호없는 환경.<br>장치 및 응용 프로그램에 대한 단일 사인온 기능이 있는 전용 작업 PC에 적용됩니다.|휴대 전화를 사용하여 어디서나 암호가없는 솔루션.<br>모든 장치에서 웹에서 작업 또는 개인 응용 프로그램에 액세스하는 데 적용됩니다.|생체 인식, PIN 및 NFC를 사용하는 작업자를 위한 암호 가 없는 환경입니다.<br>공유 PC 및 휴대 전화가 실행 가능한 옵션이 아닌 경우(예: 헬프 데스크 직원, 공용 키오스크 또는 병원 팀) 적용 가능|

다음 표를 사용하여 요구 사항 및 사용자를 지원하는 방법을 선택합니다.

|인물|시나리오|Environment|암호 없는 기술|
|:-|:-|:-|:-|
|**관리자**|관리 작업을 위한 장치에 대한 보안 액세스|할당된 Windows 10 장치|비즈니스 용 Windows 안녕하세요 및/또는 FIDO2 보안 키|
|**관리자**|Windows 이외의 장치에서 관리 작업| 모바일 또는 비창 장치|Microsoft 인증자 앱으로 암호 없는 로그인|
|**정보 근로자**|생산성 작업|할당된 Windows 10 장치|비즈니스 용 Windows 안녕하세요 및/또는 FIDO2 보안 키|
|**정보 근로자**|생산성 작업| 모바일 또는 비창 장치|Microsoft 인증자 앱으로 암호 없는 로그인|
|**일선 근로자**|공장, 공장, 소매 또는 데이터 항목의 키오스크|공유 된 윈도우 10 장치|FIDO2 보안 키|

## <a name="next-steps"></a>다음 단계

[조직에서 FIDO2 보안 키 암호 없는 옵션 사용](howto-authentication-passwordless-security-key.md)

[조직에서 전화 기반 암호 없는 옵션 사용](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>외부 링크

[FIDO 얼라이언스](https://fidoalliance.org/)

[FIDO2 CTAP 사양](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
