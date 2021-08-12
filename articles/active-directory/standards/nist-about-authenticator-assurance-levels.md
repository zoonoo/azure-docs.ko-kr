---
title: Azure Active Directory를 사용하여 NIST 인증자 보증 수준
description: Azure Active Directory에 적용되는 인증자 보증 수준 개요
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
ms.openlocfilehash: 34cf1b096963c5b83c359c6583573c709655bc1c
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109517729"
---
# <a name="about-authenticator-assurance-levels"></a>인증자 보증 수준 정보

NIST(미국 국립표준기술원)는 ID 솔루션을 구현하는 미국 연방 기관에 대한 기술 요구 사항을 작성합니다. [NIST SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)는 디지털 인증 구현에 대한 기술 지침을 정의합니다. 이는 AAL(인증자 보증 수준) 프레임워크를 사용하여 수행합니다. AAL은 디지털 ID의 인증 강도를 특징으로 합니다. 또한 이 지침은 해지를 포함하여 인증자의 수명 주기 관리에 대해 설명합니다. 

표준에는 11가지 요구 사항 범주에 대한 AAL 요구 사항이 포함되어 있습니다.

* 허용되는 인증자 유형

* FIPS 140(연방 정보 처리 표준 140) 확인 수준(fips [FIPS 140-2](https://csrc.nist.gov/publications/detail/fips/140/2/final) 또는 최신 수정 버전에 의해 FIPS 140 요구 사항이 충족됨)

* 재인증

* 보안 컨트롤

* MitM(Man-in-the-middle) 저항

* 검증 도구-가장 저항(피싱 저항)

* 검증 도구 손상 저항

* 재생 저항

* 인증 의도

* 레코드 보존 정책

* 개인 정보 보호 제어

## <a name="applying-nist-aals-in-your-environment"></a>사용자 환경에서 NIST AAL 적용

> [!TIP]
> 비즈니스적인 이유, 산업 표준 또는 규정 준수 요구 사항에 따라 AAL3을 충족해야 하는 경우가 아니면 최소 AAL2 이상을 충족하는 것이 좋습니다.

일반적으로 AAL1은 암호 전용 솔루션을 허용하고 암호는 가장 쉽게 손상된 형태의 인증이므로 권장되지 않습니다. [Pa$$word는 중요하지 않습니다](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)를 참조하세요. 

NIST에서는 AAL3까지 검증 도구 가장(자격 증명 피싱이라고도 함)이 필요하지 않지만, 모든 수준에서 이 위협을 해결하는 것이 좋습니다. Azure AD 조인 디바이스 또는 하이브리드 Azure AD 조인 디바이스를 요구하는 것처럼 검증 도구 가장 저항을 제공하는 인증자를 선택할 수 있습니다. Office 365를 사용하는 경우 Office 365 Advanced Threat Protection 및 특히 [피싱 방지 정책](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies)을 사용하여 해결할 수 있습니다.

조직에 적절한 NIST AAL을 평가할 때 전체 조직이 NIST 표준을 충족해야 하는지, 아니면 분리할 수 있는 특정 사용자 및 리소스 그룹이 있고, NIST AAL 구성은 특정 사용자 및 리소스 그룹에만 적용되는지 여부를 고려할 수 있습니다. 

## <a name="security-controls-privacy-controls-records-retention-policy"></a>보안 제어, 개인 정보 컨트롤, 레코드 보존 정책

Azure 및 Azure Government는 공동 권한 부여 위원회(Joint Authorization Board)로부터 [NIST SP 800-53 High 영향 수준](https://nvd.nist.gov/800-53/Rev4/impact/high)의 임시 운영 권한(P-ATO)을 획득했습니다. 이는 Azure 및 Azure Government를 사용하여 매우 중요한 데이터를 처리할 수 있는 권한을 부여하는 FedRAMP 인증의 최고 기준입니다.

이러한 Azure 및 Azure Government 인증은 AAL1, AAL2, AAL3에 대한 보안 제어, 개인 정보 보호 제어 및 레코드 보존 정책 요구 사항을 충족합니다.

Azure 및 Azure Government의 FedRAMP 감사에는 인프라, 개발, 운영, 관리 및 범위 내 서비스 지원을 포함하는 정보 보안 관리 시스템이 포함되었습니다. P-ATO가 부여되도 클라우드 서비스 공급자는 함께 작업하는 모든 정부 기관으로부터 권한(ATO)을 획득해야 합니다. Azure의 경우 정부 기관 또는 함께 작업하는 조직은 자체 보안 권한 부여 프로세스에서 Azure P-ATO를 사용할 수 있으며 FedRAMP 요구 사항을 충족하는 기관 ATO를 발급하기 위한 기준으로 사용할 수 있습니다.

Azure는 FedRAMP High 영향 수준에서 다른 클라우드 공급자보다 더 많은 서비스를 계속 지원합니다. 또한 Azure 퍼블릭 클라우드의 FedRAMP High는 많은 미국 정부 고객의 요구 사항을 충족하지만, 더 엄격한 요구 사항이 있는 기관은 강화된 직원 심사와 같은 추가 보호 조치를 제공하는 Azure Government를 계속 사용합니다. Microsoft는 당해 연도에 계획된 서비스뿐 아니라 현재 FedRAMP High 경계까지 Azure Government에서 사용할 수 있는 모든 Azure 퍼블릭 서비스를 나열합니다.

또한 Microsoft는 명확하게 명시된 레코드 보존 정책을 사용하여 [고객 데이터를 보호하고 관리](https://www.microsoft.com/trust-center/privacy/data-management)하기 위해 최선을 다하고 있습니다. 전 세계 거의 모든 국가에 고객이 있는 글로벌 기업인 Microsoft는 고객을 지원하기 위한 강력한 규정 준수 포트폴리오를 보유하고 있습니다. 규정 준수 제품의 전체 목록을 보려면 [Microsoft 규정 준수 제품](https://docs.microsoft.com/compliance/regulatory/offering-home)을 참조하세요. 

## <a name="next-steps"></a>다음 단계 

[NIST 개요](nist-overview.md)

[AAL에 대한 자세한 정보](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)

[Azure AD를 사용하여 NIST AAL3 달성](nist-authenticator-assurance-level-3.md) 