---
title: NIST 인증 기본 사항 및 Azure Active Directory
description: NIST의 용어 및 인증 요소에 대한 설명입니다.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 028ce5fc6ae3cef586803e1b04948006b58fd17e
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294217"
---
# <a name="nist-authentication-basics"></a>NIST 인증 기본 사항 

NIST 지침을 이해하려면 TPM(신뢰할 수 있는 플랫폼 모듈) 및 인증 요소의 용어와 개념에 대한 확실한 기초 교육이 필요합니다.

## <a name="terminology"></a>용어

이러한 NIST 관련 문서 전체에서 다음과 같은 용어가 사용됩니다.

|용어| 정의 - 이 표에는 *기울임꼴* 용어가 정의되어 있습니다.|
| - | - |
| 어설션| *구독자* 에 대한 정보를 포함하는 *신뢰 당사자* 에 대한 *검증 도구* 의 명령문입니다. 확인된 특성을 포함할 수 있습니다. |
|인증| *주체* 의 ID를 확인하는 프로세스입니다. |
| 인증 요소| 사용자가 알고 있는 것, 사용자가 소유하고 있는 것 또는 사용자의 신원 정보: 모든 *인증자* 에는 하나 이상의 인증 요소가 있습니다. |
| Microsoft 계정용 Authenticator| *청구인* 이 처리하고 제어하는 것으로, *청구인의* ID를 인증하는 데 사용됩니다. |
| 청구인| 하나 이상의 인증 프로토콜을 사용하여 ID를 확인할 *주체* 입니다. |
|자격 증명| *구독자* 가 소유하고 제어하는 하나 이상의 *인증자* 에 ID를 정식으로 바인딩하는 개체 또는 데이터 구조입니다. |
| CSP(자격 증명 서비스 공급자)| *구독자 인증자* 를 발급하거나 등록하고 *구독자* 에게 전자 *자격 증명* 을 발급하는, 신뢰할 수 있는 엔터티입니다. |
|신뢰 당사자| 일반적으로 시스템에 대한 액세스 권한을 부여하기 위해 *검증 도구의 어설션* 또는 *청구인의 인증자* 및 *자격 증명* 을 사용하는 엔터티입니다. |
|  제목| 개인, 조직, 디바이스, 하드웨어, 네트워크, 소프트웨어 또는 서비스입니다. |
| 가입자| *CSP* 에서 *자격 증명* 또는 *인증자* 를 받은 당사자입니다. |
|TPM(신뢰할 수 있는 플랫폼 모듈)  | TPM은 키 생성을 포함하는 암호화 작업을 수행하는 변조 방지 모듈입니다. |
|  검증 도구| *인증자* 에 대한 청구인의 소유권과 제어권을 확인하여 *청구인의* ID를 확인하는 엔터티입니다. |


## <a name="about-trusted-platform-modules"></a>신뢰할 수 있는 플랫폼 모듈 정보 

TPM(신뢰할 수 있는 플랫폼 모듈) 기술은 하드웨어 기반의 보안 관련 기능을 제공하도록 설계되었습니다. TPM 칩 또는 하드웨어 TPM은 암호화 키의 생성, 저장 및 사용 제한과 같은 작업에 도움이 되는 안전한 암호화 프로세서입니다. 

Microsoft는 TPM이 Microsoft Windows에서 작동하는 방식에 대한 중요한 정보를 제공합니다. 자세한 내용은 [신뢰할 수 있는 플랫폼 모듈](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-top-node)에 대한 이 문서를 참조하세요. 

소프트웨어 TPM은 이 기능을 모방하는 에뮬레이터입니다. 

 ## <a name="authentication-factors-and-their-strengths"></a>인증 요소 및 강도

인증 요소는 세 가지 범주로 그룹화할 수 있습니다. 다음 표는 각 그룹화에 속하는 요소 유형에 대한 예를 보여 줍니다.

![사용자가 알고 있는 것, 사용자가 소유하고 있는 것, 사용자의 신원 정보를 그림으로 표현한 것.](media/nist-authentication-basics/nist-authentication-basics-0.png)

인증 요소의 강도는 구독자만 알고 있는 것, 소유하고 있는 것, 구독자의 신원 정보라고 확신할 수 있는 정도에 따라 결정됩니다.

NIST에는 인증 요소의 상대적 강도에 대한 제한된 지침이 있습니다. Microsoft에서는 다음과 같이 강도를 평가합니다. 

**사용자가 알고 있는 것**: 사용자가 알고 있는 가장 일반적인 것은 암호로, 가장 큰 공격 표면을 나타냅니다. 다음과 같은 완화는 구독자에 대한 선호도의 신뢰도를 향상시키고, 무차별 암호 대입 공격, 도청 및 사회 공학 등의 암호 공격을 방지하는 데 효과적입니다.

* [암호 복잡성 요구 사항](https://www.microsoft.com/research/wp-content/uploads/2016/06/Microsoft_Password_Guidance-1.pdf)

* [금지 암호](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-configure-custom-password-protection)

* [유출된 자격 증명 식별](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

* [보안 해시 스토리지](https://aka.ms/AADDataWhitepaper)

* [계정 잠금](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)

**사용자가 소유하고 있는 것**: 사용자가 소유하고 있는 것의 강도는 구독자가 소유한 상태를 유지할 가능성과 공격자가 이에 대한 액세스 권한을 획득할 때의 어려움을 기준으로 합니다. 예를 들어 개인 모바일 디바이스 또는 하드웨어 키는 선호도가 높기 때문에 내부 위협으로부터 보호하려고 할 때 사무실에 있는 데스크톱 컴퓨터보다 더 안전합니다.

**사용자의 신원 정보**: 공격자가 사용자의 신원 정보 복사본을 얻거나 생체 인식 정보를 스푸핑할 때의 용이성은 중요합니다. NIST는 생체 인식에 대한 프레임워크의 도안을 만들고 있습니다. 현재 NIST는 생체 인식을 별도의 인증 방법으로 허용하지 않습니다. 다단계 인증 내 요소여야 합니다. 생체 인식은 기본적으로 확률론적이기 때문입니다. 즉, 생체 인식은 동일한 사람일 가능성을 결정하는 알고리즘을 사용합니다. 암호처럼 꼭 정확히 일치하는 것은 아닙니다. [인증자의 함수 강도 – 생체 인식](https://pages.nist.gov/SOFA/SOFA.html)(SOFA-B)에 대한 이 문서를 참조하세요. SOFA-B는 잘못된 일치율, 거짓, 실패율, 프레젠테이션 공격 감지 오류율 및 공격을 시작하는 데 필요한 노력을 기준으로 생체 인식의 강도를 정량화하는 프레임워크를 제공하려고 합니다. 

## <a name="single-factor-authentication"></a>단일 단계 인증

사용자가 알고 있는 것 또는 사용자의 신원 정보를 구성하는 단일 단계 인증자를 사용하여 단일 단계 인증을 구현할 수 있습니다. "사용자의 신원 정보" 인증 요소는 인증 요소로 허용되지만 그 자체로 인증자로 허용되지 않습니다. 

![단일 요소 인증의 개념 이미지.](media/nist-authentication-basics/nist-authentication-basics-1.png)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

다단계 인증은 다단계 인증자 또는 두 개의 단일 요소 인증자의 조합으로 구현할 수 있습니다. 다단계 인증자에는 단일 인증 트랜잭션을 실행하기 위한 두 가지 인증 요소가 필요합니다.

### <a name="multi-factor-authentication-using-two-single-factor-authenticators"></a>두 개의 단일 단계 인증자를 사용하는 다단계 인증

다단계 인증에는 두 가지 인증 요소가 필요합니다. 다음과 같은 두 가지 독립적인 인증자가 여기에 해당될 수 있습니다. 

* 저장된 비밀[암호] 및 대역외[SMS]

* 저장된 비밀[암호] 및 일회용 암호[하드웨어 또는 소프트웨어]

이러한 방법은 Azure AD를 사용하여 두 개의 독립 인증 트랜잭션을 수행합니다.

![별도의 두 인증자를 사용하는 다단계 인증의 개념 이미지.](media/nist-authentication-basics/nist-authentication-basics-2.png)


### <a name="multi-factor-authentication-using-a-single-multi-factor-authenticator"></a>단일 다단계 인증자를 사용하는 다단계 인증

다단계 요소 인증에는 두 번째 인증 요소를 잠금 해제하기 위한 한 가지 인증 요소(사용자가 알고 있는 것 또는 사용자의 신원 정보)가 필요합니다. 이는 일반적으로 여러 독립적인 인증자보다 더 간단한 사용자 환경입니다.

![다단계 인증, 단일 다단계 인증자의 개념 이미지.](media/nist-authentication-basics/nist-authentication-basics-3a.png)

한 가지 예는 암호 없는 모드에서 사용되는 Microsoft Authenticator 앱입니다. 이 방법을 사용하면 사용자가 보안 리소스(신뢰 당사자)에 액세스하려고 할 때 인증자 앱에 대한 알림을 받습니다. 사용자는 생체 인식(사용자의 신원 정보) 또는 PIN(사용자가 알고 있는 것)을 제공하여 알림에 응답하고, 휴대폰(사용자가 소유하고 있는 것)에서 암호화 키의 잠금을 해제합니다. 그러면 검증 도구에서 유효성을 검사합니다.

## <a name="next-steps"></a>다음 단계 

[NIST 개요](nist-overview.md)

[AAL에 대한 자세한 정보](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)

[Azure AD를 사용하여 NIST AAL3 달성](nist-authenticator-assurance-level-3.md) 