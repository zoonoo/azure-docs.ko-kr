---
title: Azure Active Directory를 사용하여 NIST AAL2 달성
description: Azure Active Directory를 사용하여 NIST AAL 2(인증자 보증 수준 2)를 달성하는 방법에 대한 지침입니다.
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
ms.openlocfilehash: aaa40141eaa0617c34a0ae8c4a7cf396d624c1b6
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108294214"
---
# <a name="achieving-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>Azure Active Directory를 사용하여 NIST 인증자 보증 수준 2 달성

NIST(미국 국립표준기술원)는 ID 솔루션을 구현하는 미국 연방 기관에 대한 기술 요구 사항을 작성합니다. 연방 기관과 협업하는 조직도 이러한 요구 사항을 충족해야 합니다. 이 문서는 NIST AAL2(인증 보증 수준 2)를 달성하는 방법을 안내합니다. 

AAL 2를 달성하려고 하기 전에 참조할 만한 리소스는 다음과 같습니다.
* [NIST 개요](nist-overview.md) - 다른 AAL 수준 이해
* [인증 기본 사항](nist-authentication-basics.md) - 중요한 용어 및 인증 유형
* [NIST 인증자 유형](nist-authenticator-types.md) - 각 인증자 유형 이해
* [NIST AAL](nist-about-authenticator-assurance-levels.md) - AAL의 구성 요소 및 해당 구성 요소에 Microsoft Azure Active Directory 인증 방법이 매핑되는 방법

## <a name="permitted-authenticator-types"></a>허용되는 인증자 유형


| Azure AD 인증 방법| NIST 인증자 유형 | 
| - | - |
| **권장 방법** |   | 
| 암호 없는 iOS용 Microsoft Authenticator 앱<br>소프트웨어 TPM을 사용하는 비즈니스용 Windows Hello | 다단계 암호화 소프트웨어 |
| FIDO 2 보안 키<br>암호 없는 Android용 Microsoft Authenticator 앱<br>하드웨어 TPM을 사용하는 비즈니스용 Windows Hello<br>스마트 카드(ADFS) | 다단계 암호화 하드웨어 |
| **추가 방법** |  |
| 암호 + 휴대폰(SMS) | 저장된 비밀 + 대역 외 |
| 암호 + Microsoft Authenticator 앱(OTP)<br>암호 + SF OTP | 저장된 비밀 + 단일 단계 일회용 암호 |
| 암호 + 소프트웨어 TPM과 조인된 Azure AD <br>암호 + 규격 모바일 디바이스<br>암호 + 소프트웨어 TPM과 조인된 하이브리드 Azure AD <br>암호 + Microsoft Authenticator 앱(알림) | 저장된 비밀 + 단일 단계 암호화 SW |
| 암호 + 하드웨어 TPM과 조인된 Azure AD <br>암호 + 하드웨어 TPM과 조인된 하이브리드 Azure AD | 저장된 비밀 + 단일 단계 암호화 하드웨어 |


### <a name="our-recommendations"></a>권장 사항

다단계 암호화 하드웨어 또는 소프트웨어 인증자를 사용하여 AAL2를 달성하는 것이 좋습니다. 암호 없는 인증은 가장 큰 공격 표면(암호)을 제거하고 사용자에게 간소화된 인증 방법을 제공합니다. 

암호 없는 인증 방법을 선택하는 방법에 대한 자세한 지침은 [Azure Active Directory에서 암호 없는 인증 배포 계획](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-deployment)을 참조하세요.

비즈니스용 Windows Hello를 구현하는 방법에 대한 자세한 내용은 [비즈니스용 Windows Hello 배포 가이드](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)를 참조하세요.

## <a name="fips-140-validation"></a>FIPS 140 검증

다음 정보는 FIPS 140 검증을 받기 위한 가이드입니다.

### <a name="verifier-requirements"></a>검증 도구 요구 사항

Azure AD는 모든 인증 관련 암호화 작업에 Windows FIPS 140 수준 1의 전체   
검증 암호화 모듈을 사용합니다. 따라서 이는 정부 기관에서 요구하는 FIPS 140 준수 검증 도구입니다.

### <a name="authenticator-requirements"></a>인증자 요구 사항

정부 기관의 암호화 인증자는 FIPS 140 수준 1의 전체 검증을 받아야 합니다. 이는 비정부 기관에 대한 요구 사항이 아닙니다. 다음 Azure AD 인증자는 [FIPS 140 승인 작업 모드의 Windows](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)에서 실행할 때 해당 요구 사항을 충족합니다.

* 암호

* 소프트웨어 또는 하드웨어 TPM과 조인된 Azure AD

* 소프트웨어 또는 하드웨어 TPM과 조인된 하이브리드 Azure AD

* 소프트웨어 또는 하드웨어 TPM을 사용하는 비즈니스용 Windows Hello

* 스마트 카드(ADFS) 

FIDO2 보안 키 및 Microsoft Authenticator 앱(알림, OTP, 암호 없는 모드 등 모든 모드에서)은 본 문서의 작성 시점을 기준으로 FIPS 140 수준 1 전체 검증에 대한 정부 기관 요구 사항을 충족하지 않습니다.

* Microsoft Authenticator 앱이 FIPS 140 승인 암호화를 사용하고 있지만 FIPS 140 수준 1의 전체 검증을 받지 못했습니다. 

* FIDO2 키는 가장 최근의 혁신 기술이므로 아직 FIPS 인증을 진행 중입니다.

## <a name="reauthentication"></a>재인증 

AAL2에서 NIST는 사용자 활동과 관계없이 12시간 간격으로, 그리고 30분 이상 동안 비활성 상태가 지속되면 재인증을 요구합니다. 세션 비밀은 사용자가 보유한 사항이므로 사용자가 알고 있는 정보 또는 사용자의 신원 정보를 나타내야 합니다.

사용자 활동과 관계없는 재인증에 대한 요구 사항을 충족하려면 [사용자 로그인 빈도](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)를 12시간으로 구성하는 것이 좋습니다. 

NIST는 또한 보상 컨트롤을 사용하여 구독자의 현재 상태를 확인하도록 지원합니다.

* Microsoft System Center Configuration Manager(SCCM), GPO(그룹 정책 개체) 또는 Intune을 활용하여 OS 수준에서 디바이스를 잠그면 세션 비활성 시간 제한 30분을 달성할 수 있습니다. 또한 잠금을 해제하려면 구독자에 대한 로컬 인증을 요구해야 합니다.

* 활동과 관계없이 12시간 후에 머신을 잠그는 예약된 작업(SCCM, GPO 또는 Intune 활용)을 실행하여 활동과 관계없는 시간 제한을 구현할 수 있습니다.

## <a name="man-in-the-middle-mitm-resistance"></a>MitM(Man-in-the-middle) 저항 

청구인과 Azure AD 간의 모든 통신은 MitM 공격에 대한 저항을 제공하기 위해 인증된 보호된 채널을 통해 수행됩니다. 이는 AAL1, AAL2, AAL3에 대한 MitM 저항 요구 사항을 충족합니다.

## <a name="replay-resistance"></a>재생 저항

AAL2의 모든 Azure AD 인증 방법은 nonce 또는 챌린지를 사용하며, 재생된 인증 트랜잭션이 적절한 nonce 또는 적시성 데이터를 포함하지 않기 때문에 검증 도구가 이를 쉽게 탐지할 수 있으므로 공격 재생 저항력이 있습니다.

## <a name="next-steps"></a>다음 단계 

[NIST 개요](nist-overview.md)

[AAL에 대한 자세한 정보](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)

[Azure AD를 사용하여 NIST AAL3 달성](nist-authenticator-assurance-level-3.md)  