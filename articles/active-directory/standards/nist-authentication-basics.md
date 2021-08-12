---
title: NIST 인증 기본 사항 및 Azure Active Directory
description: 이 문서에서는 중요한 용어를 정의하고 NIST에 대한 신뢰할 수 있는 플랫폼 모듈 및 인증 요인에 대해 설명합니다.
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
ms.openlocfilehash: eacdb19dcd5121f1d7fecdf6a3cefbafd609c9a9
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889835"
---
# <a name="nist-authentication-basics"></a>NIST 인증 기본 사항 

NIST(National Institute of Standards and Technology) 지침을 이해하려면 해당 용어를 알아야 합니다. 또한 TPM(신뢰할 수 있는 플랫폼 모듈) 기술 및 인증 요소를 이해해야 합니다. 이 문서에서는 해당 정보를 제공합니다. 

## <a name="terminology"></a>용어

이러한 NIST 문서 전체에서 다음과 같은 용어가 사용됩니다.

|용어| 정의 이 표에는 *기울임꼴* 용어가 정의되어 있습니다.|
| - | - |
| 어설션| *구독자* 에 대한 정보를 포함하는 *신뢰 당사자* 에 대한 *검증 도구* 의 명령문입니다. 어설션에 확인된 특성이 포함될 수 있습니다. |
|인증| *주체* 의 ID를 확인하는 프로세스입니다. |
| 인증 요소| 사용자가 알고 있는 것, 사용자가 소유하고 있는 것 또는 사용자의 신원 정보 모든 *인증자* 에는 하나 이상의 인증 요소가 있습니다. |
| Microsoft 계정용 Authenticator| *청구인* 이 처리하고 제어하는 것으로, *청구인의* ID를 인증하는 데 사용됩니다. |
| 청구인| 하나 이상의 *인증* 프로토콜을 사용하여 ID를 확인할 *주체* 입니다. |
|자격 증명| *구독자* 가 소유하고 제어하는 하나 이상의 *인증자* 에 ID를 정식으로 바인딩하는 개체 또는 데이터 구조입니다. |
| CSP(자격 증명 서비스 공급자)| *구독자 인증자* 를 발급하거나 등록하고 *구독자* 에게 전자 *자격 증명* 을 발급하는, 신뢰할 수 있는 엔터티입니다. |
|신뢰 당사자| 일반적으로 시스템에 대한 액세스 권한을 부여하기 위해 *검증 도구의 어설션* 또는 *청구인의 인증자* 및 *자격 증명* 을 사용하는 엔터티입니다. |
|  제목| 개인, 조직, 디바이스, 하드웨어, 네트워크, 소프트웨어 또는 서비스입니다. |
| 가입자| *CSP* 에서 *자격 증명* 또는 *인증자* 를 받은 당사자입니다. |
|신뢰할 수 있는 플랫폼 모듈  | TPM은 키 생성을 포함하는 암호화 작업을 수행하는 변조 방지 모듈입니다. |
|  검증 도구| *인증자* 에 대한 청구인의 소유권과 제어권을 확인하여 *청구인의* ID를 확인하는 엔터티입니다. |


## <a name="about-trusted-platform-module-technology"></a>신뢰할 수 있는 플랫폼 모듈 기술 정보

신뢰할 수 있는 플랫폼 모듈 기술은 하드웨어 기반의 보안 관련 기능을 제공하도록 설계되었습니다. TPM 칩 또는 하드웨어 TPM은 암호화 키의 생성, 저장 및 사용 제한과 같은 작업에 도움이 되는 안전한 암호화 프로세서입니다. 

Microsoft는 TPM이 Windows에서 작동하는 방식에 대한 중요한 정보를 제공합니다. 자세한 내용은 [신뢰할 수 있는 플랫폼 모듈](/windows/security/information-protection/tpm/trusted-platform-module-top-node)을 참조하세요. 

소프트웨어 TPM은 하드웨어 TPM 기능을 모방하는 에뮬레이터입니다. 

 ## <a name="authentication-factors-and-their-strengths"></a>인증 요소 및 강도

인증 요소는 세 가지 범주로 그룹화할 수 있습니다.

![사용자가 알고 있는 것, 사용자가 소유하고 있는 것 또는 사용자의 신원 정보별로 그룹화한 인증 요소의 예를 제공하는 그래픽.](media/nist-authentication-basics/nist-authentication-basics-0.png)

인증 요소의 강도는 구독자만 알고 있는 것, 소유하고 있는 것, 구독자의 신원 정보라고 확신할 수 있는 정도에 따라 결정됩니다.

NIST는 인증 요소의 상대적 강점에 대한 제한된 지침을 제공합니다. 이 섹션의 나머지 부분에서는 Microsoft에서 이러한 강점을 평가하는 방법을 설명합니다. 

**사용자가 알고 있는 것**. 가장 일반적인 *사용자가 알고 있는 것* 에 해당하는 암호는 가장 큰 공격 표면을 나타냅니다. 다음과 같은 완화는 구독자에 대한 선호도의 신뢰도를 향상시킵니다. 이러한 요소는 무차별 암호 대입 공격, 도청 및 사회 공학 등의 암호 공격을 방지하는 데 효과적입니다.

* [암호 복잡성 요구 사항](https://www.microsoft.com/research/wp-content/uploads/2016/06/Microsoft_Password_Guidance-1.pdf)

* [금지 암호](../authentication/tutorial-configure-custom-password-protection.md)

* [유출된 자격 증명 식별](../identity-protection/overview-identity-protection.md)

* [보안 해시 스토리지](https://aka.ms/AADDataWhitepaper)

* [계정 잠금](../authentication/howto-password-smart-lockout.md)

**사용자가 소유하고 있는 것**. *사용자가 소유하고 있는 것* 의 강도는 구독자가 소유한 상태를 유지할 가능성과 공격자가 이에 대한 액세스 권한을 획득할 때의 어려움을 기준으로 합니다. 예를 들어, 내부 위협으로부터 보호하려는 경우 개인 모바일 디바이스 또는 하드웨어 키의 선호도가 더 높습니다. 따라서 사무실의 데스크톱 컴퓨터보다 더 안전합니다.

**사용자의 신원 정보**. 공격자가 *사용자의 신원 정보* 복사본을 얻거나 생체 인식 정보를 스푸핑할 때의 용이성은 중요합니다. NIST는 생체 인식에 대한 프레임워크의 도안을 만들고 있습니다. 현재 NIST는 생체 인식을 별도의 인증 방법으로 허용하지 않습니다. 다단계 인증 내 요소여야 합니다. 생체 인식은 본질적으로 확률적이기 때문에 이러한 주의가 필요합니다. 즉, 생체 인식은 선호도의 가능성을 결정하는 알고리즘을 사용합니다. 생체 인식이 암호처럼 반드시 정확한 일치를 제공하는 것은 아닙니다. 자세한 내용은 [인증자의 함수 강도 – 생체 인식](https://pages.nist.gov/SOFA/SOFA.html)(SOFA-B)을 참조하세요. 

SOFA-B는 다음을 위한 생체 인식의 강도를 수량화하는 프레임워크를 제공하려고 합니다.
- False 일치율
- False 실패율
- 프레젠테이션 공격 검색 오류율
- 공격을 수행하는 데 필요한 노력 

## <a name="single-factor-authentication"></a>단일 단계 인증

*사용자가 알고 있는 것* 또는 *사용자의 신원 정보* 를 확인하는 단일 단계 인증자를 사용하여 단일 단계 인증을 구현할 수 있습니다. *사용자의 신원 정보* 요소는 인증 요소로 허용되지만 그 자체로는 인증자로 허용되지 않습니다. 

![단일 단계 인증의 작동 방식을 보여 주는 그래픽](media/nist-authentication-basics/nist-authentication-basics-1.png)

## <a name="multifactor-authentication"></a>Multi-Factor Authentication

다단계 인증자를 사용하거나 두 개의 단일 요소 인증자를 사용하여 다단계 인증을 구현할 수 있습니다. 다단계 인증자에는 단일 인증 트랜잭션을 완료하기 위한 두 가지 인증 요소가 필요합니다.

### <a name="multifactor-authentication-by-using-two-single-factor-authenticators"></a>두 개의 단일 단계 인증자를 사용하는 다단계 인증

다단계 인증에는 두 가지 인증 요소가 필요합니다. 이러한 인증자는 독립적일 수 있습니다. 예를 들면 다음과 같습니다. 

* 저장된 비밀(암호) 및 대역외(SMS)

* 저장된 비밀(암호) 및 일회용 암호(하드웨어 또는 소프트웨어)

이러한 방법은 Azure AD(Azure Active Directory)를 사용하여 두 개의 독립 인증 트랜잭션을 수행합니다.

![별도의 두 인증자를 통한 다단계 인증을 설명하는 그래픽](media/nist-authentication-basics/nist-authentication-basics-2.png)


### <a name="multifactor-authentication-by-using-a-single-multifactor-authenticator"></a>단일 다단계 인증자를 사용하는 다단계 인증

다단계 요소 인증에는 두 번째 인증 요소를 잠금 해제하기 위한 한 가지 인증 요소(‘사용자가 알고 있는 것’ 또는 ‘사용자의 신원 정보’)가 필요합니다.  사용자 환경은 일반적으로 여러 독립 인증자의 환경보다 더 용이합니다.

![단일 다단계 인증자를 사용하는 다단계 인증을 보여 주는 그래픽](media/nist-authentication-basics/nist-authentication-basics-3a.png)

한 가지 예는 암호 없는 모드에서 사용되는 Microsoft Authenticator 앱입니다. 이 방법을 사용하면 사용자가 보안 리소스(신뢰 당사자)에 액세스하려고 할 때 인증자 앱에 대한 알림을 받습니다. 사용자는 생체 인식(‘사용자의 신원 정보’) 또는 PIN(‘사용자가 알고 있는 것’)를 제공하여 알림에 응답합니다.  이 요소는 휴대폰에서 암호화 키의 잠금을 해제합니다(‘사용자의 신원 정보’). 그러면 검증 도구가 유효성을 검사합니다.

## <a name="next-steps"></a>다음 단계 

[NIST 개요](nist-overview.md)

[AAL에 대한 자세한 정보](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)

[Azure AD를 사용하여 NIST AAL3 달성](nist-authenticator-assurance-level-3.md)