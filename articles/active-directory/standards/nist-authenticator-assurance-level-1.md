---
title: Azure Active Directory를 사용하여 NIST AAL1 달성
description: Azure Active Directory로 NIST 인증자 보증 수준 1(AAL 1)을 달성하는 방법에 대한 참고 자료입니다.
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
ms.openlocfilehash: 640c401beb42b07465f533fa14b5b7fd4b0200e5
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294199"
---
# <a name="achieving-nist-authenticator-assurance-level-1-with-azure-active-directory"></a>Azure Active Directory를 사용하여 NIST 인증자 보증 수준 1 달성

NIST(미국 국립표준기술원)는 ID 솔루션을 구현하는 미국 연방 기관에 대한 기술 요구 사항을 개발합니다. 연방 기관과 함께 일하는 조직도 이러한 요구 사항을 충족해야 합니다. 이 문서는 NIST 인증 보증 수준 1(AAL1)을 구현하는 방법을 안내합니다. 

AAL 1을 달성하려고 하기 전에 확인할 리소스는 다음과 같습니다.
* [NIST 개요](nist-overview.md) -다른 AAL 수준 이해
* [인증 기본 사항](nist-authentication-basics.md) - 중요한 용어 및 인증 유형
* [NIST 인증자 유형](nist-authenticator-types.md)- 각 인증자 유형 이해
* [NIST AAL](nist-about-authenticator-assurance-levels.md) - AAL의 구성 요소, Microsoft Azure Active Directory 인증 방법이 그것들을 매핑하는 방법 및 TPM(신뢰할 수 있는 플랫폼 모듈) 이해 

## <a name="permitted-authenticator-types"></a>허용되는 인증자 유형

 NIST 단일 또는 다단계 [허용되는 인증자](nist-authenticator-types.md)를 사용하여 AAL1을 달성할 수 있습니다. 다음 표에는 [AAL2](nist-authenticator-assurance-level-2.md) 및 [AAL3](nist-authenticator-assurance-level-2.md)에서 다루지 않는 내용이 나와 있습니다.

| Azure AD 인증 방법| NIST 인증자 유형 |
| - | - |
| 암호 |저장된 비밀 |
| 휴대폰(SMS)|  대역 외 |
|  FIDO 2 보안 키 <br>암호 없는 iOS용 Microsoft Authenticator 앱<br>소프트웨어 TPM을 사용하는 비즈니스용 Windows Hello <br>스마트 카드(ADFS) |  다단계 암호화 소프트웨어 |

> [!TIP]
> 비즈니스적인 이유, 산업 표준 또는 규정 준수 요구 사항에 따라 AAL3을 충족해야 하는 경우가 아니면 최소 AAL 2 이상을 충족하는 것이 좋습니다.

## <a name="fips-140-validation"></a>FIPS 140 유효성 검사

### <a name="verifier-requirements"></a>검증 도구 요구 사항

Azure AD는 Windows FIPS 140 Level 1 전체에 대해 validate한 암호화를 사용하고 있습니다.   
‎모든 인증 관련 암호화 작업에 대한 모듈입니다. 따라서 이는 정부 기관에서 요구하는 FIPS 140 준수 검증 도구입니다.

## <a name="man-in-the-middle-mitm-resistance"></a>MitM(man-in-the-middle) 저항 

클레임 청구인과 Azure AD 간의 모든 통신은 MitM 공격에 대한 저항을 제공하기 위해 인증된 보호된 채널을 통해 수행됩니다. 이는 AAL1, AAL2 및 AAL3에 대한 MitM 저항 요구 사항을 충족합니다.

## <a name="next-steps"></a>다음 단계 

[NIST 개요](nist-overview.md)

[AAL에 대해 알아보기](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)

[Azure AD를 사용하여 NIST AAL3 달성](nist-authenticator-assurance-level-3.md) 