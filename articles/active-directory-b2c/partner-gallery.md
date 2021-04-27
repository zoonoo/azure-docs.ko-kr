---
title: Azure AD B2C에 대한 ISV 파트너 갤러리
titleSuffix: Azure AD B2C
description: ISV 파트너와 통합하여 사용자의 요구에 맞게 최종 사용자 환경을 조정하는 방법을 알아봅니다. 파트너 네트워크는 솔루션 기능을 확장합니다. MFA, 보안 고객 인증, 역할 기반 액세스 제어를 사용하도록 설정하고 ID 검증 교정을 통해 사기 행위를 방지합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e0125f14570e917b228c5f713e636335755581d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024271"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV 파트너

ISV 파트너 네트워크는 원활한 최종 사용자 환경을 구축하는 데 도움이 되는 솔루션 기능을 확장합니다. Azure AD B2C를 사용하면 ISV 파트너와 통합하여 MFA(다단계 인증)를 사용하도록 설정하고, 역할 기반 액세스 제어를 수행하고, ID 검증 및 교정 기능을 사용하도록 설정하고, 봇 검색 및 사기 방지를 사용하여 보안을 강화하고, PSD2(결제 서비스 지시어 2)의 SCA(강력한 고객 인증) 요구 사항을 충족합니다. 자세한 샘플 연습을 통해 앱을 ISV 파트너와 통합하는 방법을 알아봅니다.

>[!NOTE]
>[GitHub의 Azure Active Directory B2C 커뮤니티 사이트](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)는 커뮤니티의 샘플 사용자 지정 정책을 제공합니다.

## <a name="identity-verification-and-proofing"></a>ID 검증 및 교정

Microsoft는 ID 검증 및 교정을 위해 다음 ISV와 협력합니다.

| ISV 파트너 | 설명 및 통합 연습 |
|:-------------------------|:--------------|
|![Experian 로고의 스크린샷](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md)은 사기 행위를 방지하기 위해 사용자 특성을 기반으로 위험 평가를 수행하는 ID 검증 및 교정 공급자입니다. |
|![IDology 로고의 스크린샷](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md)는 ID 검증 솔루션, 사기 행위 방지 솔루션, 규정 준수 솔루션 등을 제공하는 ID 검증 및 교정 공급자입니다.|
|![Jumio 로고의 스크린샷](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md)는 ID 확인 서비스이며, 이 서비스를 사용하면 실시간 자동화 ID를 확인하고 고객 데이터를 보호할 수 있습니다. |
| ![LexisNexis 로고의 스크린샷](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md)는 사용자 ID를 확인하고 사용자의 디바이스에 따라 포괄적인 위험 평가를 제공하는 프로파일링 및 ID 유효성 검사 공급자입니다. |
| ![Onfido 로고의 스크린샷](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md)는 문서 ID 및 얼굴 인식 검증 솔루션이며 회사가 실시간으로 *고객 확인* 및 ID 요구 사항을 충족할 수 있도록 합니다.  |

## <a name="mfa-and-passwordless-authentication"></a>MFA 및 암호 없는 인증

Microsoft는 MFA 및 암호 없는 인증을 위해 다음 ISV와 파트너 관계를 맺습니다.

| ISV 파트너 | 설명 및 통합 연습 |
|:-------------------------|:--------------|
| ![Hypr 로고의 스크린샷](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md)는 암호를 공개 키 암호화로 바꿔 사기 행위, 피싱 및 자격 증명 재사용을 제거하는 암호 없는 인증 공급자입니다. |
| ![itsme 로고의 스크린샷](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md)는 eiDAS(전자 ID, 인증 및 신뢰 서비스) 호환 디지털 ID 솔루션으로 사용자가 카드 판독기, 암호, 2단계 인증 및 여러 PIN 코드 없이도 안전하게 로그인 할 수 있도록 합니다. |
|![Keyless 로고의 스크린샷](./media/partner-gallery/keyless-logo.png) | [Keyless](./partner-keyless.md)는 얼굴 생체 인식 검색의 형태로 인증을 제공하고 사기 행위, 피싱 및 자격 증명 재사용을 제거하는 암호 없는 인증 공급자입니다.
| ![Nevis 로고 스크린샷](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md)는 암호 없는 인증을 가능하게 하며, 강력한 고객 인증을 위해 Nevis Access 앱을 통해 모바일 중심의 완전한 최종 사용자 환경을 제공하고 PSD2 트랜잭션 요구 사항을 준수합니다. |
| ![Trusona 로고의 스크린샷](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) 통합을 사용하면 안전한 로그인이 가능하며 암호 없는 인증, MFA 및 디지털 라이선스 검색을 사용할 수 있습니다. |
| ![Twilio 로고의 스크린샷](./media/partner-gallery/twilio-logo.png) | [Twilio Verify 앱](./partner-twilio.md)은 여러 솔루션을 제공하여 SMS OTP (일회성 암호), TOTP(시간 기반 일회용 암호) 및 푸시 알림을 통해 MFA를 사용하도록 설정하고, PSD2에 대한 SCA 요구 사항을 준수합니다. |
| ![TypingDNA 로고의 스크린샷](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md)는 사용자의 입력 패턴을 분석하여 강력한 고객 인증을 가능하게 합니다. 이를 통해 기업은 자동 MFA를 사용하도록 설정하고 PSD2에 대한 SCA 요구 사항을 준수할 수 있습니다. |
| ![WhoIAM 로고의 스크린샷](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md)은 조직이 음성, SMS 및 이메일을 기준으로 사용자를 확인할 수 있도록 하는 BRIMS(Branded Identity Management System) 애플리케이션입니다. |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어 
 
Microsoft는 역할 기반 액세스 제어를 위해 다음 ISV와 협력 합니다.

| ISV 파트너 | 설명 및 통합 연습 |
|:-------------------------|:--------------|
| ![N8identity 로고의 스크린샷](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md)는 Microsoft Azure에서 실행되는 CSR(고객 서비스 요청) 및 고객 계정 마이그레이션 관련 솔루션을 제공하는 서비스 제공 서비스 관리 IDaaS(Identity as a Service) 거버넌스 플랫폼입니다. |
| ![Saviynt 로고의 스크린샷](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) 클라우드 네이티브 플랫폼은 지능적인 분석 및 IT 현대화를 간소화하기 위한 애플리케이션 간 통합을 통해 더 나은 보안, 규정 준수 및 거버넌스를 촉진합니다. |

## <a name="security"></a>보안

Microsoft는 보안을 위해 다음 ISV와 협력합니다.

| ISV 파트너 | 설명 및 통합 연습 |
|:-------------------------|:--------------|
| ![Arkose lab 로고의 스크린샷](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md)는 조직이 봇 공격, 계정 인수 공격 및 사기성 계정 입구를 방지하는 데 도움이 되는 사기 행위 방지 솔루션 공급자입니다. |
| ![Microsoft Dynamics 365 로고의 스크린샷](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Microsoft Dynamics 365 Fraud Protection](./partner-dynamics-365-fraud-protection.md)는 조직에서 디바이스 핑거프린팅을 통해 사기성 계정 입구를 보호하는 데 도움이 되는 솔루션입니다. |
| ![Ping 로고의 스크린샷](./media/partner-gallery/ping-logo.png) | [Ping ID](./partner-ping-identity.md)는 여러 클라우드에서 온-프레미스 레거시 애플리케이션에 대한 안전한 하이브리드 액세스를 가능하게 합니다. |
| ![Strata 로고의 스크린샷](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md)는 일관된 액세스 정책을 적용하고 ID를 동기화된 상태로 유지하고 레거시 ID 시스템에서 Azure AD B2C에 의해 제공되는 표준 기반 인증 및 액세스 제어로 애플리케이션을 간단하게 전환할 수 있도록 하여 온-프레미스 애플리케이션에 대한 안전한 하이브리드 액세스를 제공합니다. |
| ![Zscaler 로고의 스크린샷](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md)는 VPN의 비용, 번거로움 또는 보안 위험 없이 프라이빗 애플리케이션 및 자산에 대한 정책 기반 보안 액세스를 제공합니다. |

## <a name="additional-information"></a>추가 정보

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>다음 단계

언급된 테이블에서 파트너를 선택하여 파트너의 솔루션을 Azure AD B2C와 통합하는 방법을 알아봅니다.
