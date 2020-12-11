---
title: Azure AD B2C에 대 한 ISV 파트너 갤러리
titleSuffix: Azure AD B2C
description: ISV 파트너와 통합 하 여 사용자의 요구에 맞게 최종 사용자 환경을 조정 하는 방법을 알아봅니다. 파트너 네트워크는 솔루션 기능을 확장 합니다. MFA, 보안 고객 인증, 역할 기반 액세스 제어를 사용 하도록 설정 Id 확인 교정을 통해 사기 행위를 방지 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbcb3367f4a1b99ae6689a5d9fa33b7629145ead
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092575"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>ISV 파트너 Azure Active Directory B2C

ISV 파트너 네트워크는 원활한 최종 사용자 환경을 구축 하는 데 도움이 되는 솔루션 기능을 확장 합니다. Azure AD B2C를 사용 하면 ISV 파트너와 통합 하 여 MFA (Multi-factor authentication)를 사용 하도록 설정 하 고, 역할 기반 액세스 제어를 수행 하 고, id 확인 및 교정 기능을 사용 하도록 설정 하 고, 봇 검색 및 사기 방지를 사용 하 여 보안을 강화 하 고, 결제 서비스 지시어 2 (PSD2)의 SCA (Secure Customer Authentication) 요구 사항을 충족할 앱을 ISV 파트너와 통합 하는 방법을 알아보려면 자세한 샘플 연습을 사용 하세요.

>[!NOTE]
>[GitHub의 Azure Active Directory B2C 커뮤니티 사이트](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) 는 커뮤니티의 샘플 사용자 지정 정책을 제공 합니다.

## <a name="identity-verification-and-proofing"></a>Id 확인 및 교정

Azure AD B2C 파트너를 통해 고객은 계정 등록 또는 액세스를 허용 하기 전에 최종 사용자의 id 확인 및 언어를 사용할 수 있습니다. Id 확인 및 교정은 문서, 지식 기반 정보 및 선거의을 확인할 수 있습니다.

개략적인 아키텍처 다이어그램은 흐름을 설명 합니다.

![Id 교정 흐름을 보여 주는 다이어그램](./media/partner-gallery/third-party-identity-proofing.png)

Microsoft는 id 확인 및 교정을 위해 다음 Isv와 협력 합니다.

| ISV 파트너 | 설명 및 통합 연습 |
|:-------------------------|:--------------|
|![Experian 로고의 스크린샷](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) 는 사기 행위를 방지 하기 위해 사용자 특성을 기반으로 위험 평가를 수행 하는 id 확인 및 교정 공급자입니다. |
|![IDology 로고의 스크린샷](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) 는 id 확인 솔루션, 사기 행위 방지 솔루션, 규정 준수 솔루션 및 기타를 제공 하는 id 확인 및 교정 공급자입니다.|
|![Jumio 로고의 스크린샷](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) 는 id 확인 서비스 이며,이 서비스를 사용 하면 실시간 자동화 id를 확인 하 고 고객 데이터를 보호 합니다. |
| ![LexisNexis 로고의 스크린샷](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) 은 사용자 id를 확인 하 고 사용자의 장치에 따라 포괄적인 위험 평가를 제공 하는 프로 파일링 및 id 유효성 검사 공급자입니다. |
| ![Onfido 로고의 스크린샷](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) 는 회사에서 고객 및 id 요구 사항을 실시간으로 *파악* 하는 데 사용할 수 있는 문서 ID 및 얼굴 인식 확인 솔루션입니다.  |

## <a name="mfa-and-passwordless-authentication"></a>MFA 및 Passwordless 인증

Microsoft는 MFA 및 암호 없는 인증을 위해 다음 Isv와 파트너를 사용 합니다.

| ISV 파트너 | 설명 및 통합 연습 |
|:-------------------------|:--------------|
| ![Hypr 로고의 스크린샷](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) 는 암호를 공개 키 암호화로 바꿔서 사기 행위, 피싱 및 자격 증명 재사용을 제거 하는 암호 없는 인증 공급자입니다. |
| ![Itsme 로고의 스크린샷](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) 는 전자 Id, 인증 및 신뢰 서비스 (eidas) 호환 디지털 ID 솔루션으로, 사용자가 카드 판독기, 암호, 2 단계 인증 및 여러 PIN 코드 없이 안전 하 게 로그인 할 수 있도록 합니다. |
| ![Trusona 로고의 스크린샷](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) 통합을 사용 하면 안전 하 게 로그인 하 고 암호 없는 인증, MFA 및 디지털 라이선스 검색을 사용할 수 있습니다. |
| ![Twilio 로고의 스크린샷](./media/partner-gallery/twilio-logo.png) | [Twilio Verify 앱](./partner-twilio.md) 은 여러 솔루션을 제공 하 여 SMS OTP (일회성 암호), 시간 기반 일회용 암호 (totp) 및 푸시 알림을 통해 MFA를 사용 하도록 설정 하 고, PSD2에 대 한 SCA 요구 사항을 준수 합니다. |
| ![TypingDNA 로고의 스크린샷](./media/partner-gallery/typingdna-logo.png) | [Typingdna](./partner-twilio.md) 는 사용자의 입력 패턴을 분석 하 여 강력한 고객 인증을 가능 하 게 합니다. 이를 통해 기업은 자동 MFA를 사용 하도록 설정 하 고 PSD2에 대 한 SCA 요구 사항을 준수할 수 있습니다. |
| ![Whoiam 로고의 스크린샷](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) 는 조직이 음성, SMS 및 전자 메일을 기준으로 사용자를 확인할 수 있도록 하는 BRIMS (브랜드 Identity Management System) 응용 프로그램입니다. |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어 
 
Microsoft는 역할 기반 액세스 제어를 위해 다음 Isv와 협력 합니다.

| ISV 파트너 | 설명 및 통합 연습 |
|:-------------------------|:--------------|
| ![N8identity 로고의 스크린샷](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) 는 고객 계정 마이그레이션 및 CSR (고객 서비스 요청) 관리를 Microsoft Azure에서 실행 하는 솔루션을 제공 하는 서비스 제공 서비스 관리 플랫폼입니다. |
| ![Saviynt 로고의 스크린샷](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) 클라우드 네이티브 플랫폼은 지능적인 분석 및 응용 프로그램 간 통합을 통해 더 나은 보안, 규정 준수 및 거 버 넌 스를 촉진 하 여 IT 현대화를 간소화 합니다. |

## <a name="security"></a>보안

Microsoft는 보안을 위해 다음 Isv와 협력 합니다.

| ISV 파트너 | 설명 및 통합 연습 |
|:-------------------------|:--------------|
| ![Arkose lab 로고의 스크린샷](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) 는 조직이 봇 공격, 계정 인수 공격 및 사기성 계정 입구를 방지 하는 데 도움이 되는 사기 행위 방지 솔루션 공급자입니다. |
| ![층 로고의 스크린샷](./media/partner-gallery/strata-logo.png) | [층](./partner-strata.md) 은 일관 된 액세스 정책을 적용 하 고 id를 동기화 된 상태로 유지 하 고 레거시 id 시스템에서 Azure AD B2C 제공 하는 표준 기반 인증 및 액세스 제어로 응용 프로그램을 간단 하 게 전환할 수 있도록 하 여 온-프레미스 응용 프로그램에 대 한 보안 하이브리드 액세스를 제공 합니다. |
| ![Zscaler 로고의 스크린샷](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) 는 VPN의 비용, 부담이 나 보안 위험 없이 개인 응용 프로그램 및 자산에 대 한 정책 기반 보안 액세스를 제공 합니다. |

## <a name="additional-information"></a>추가 정보

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

## <a name="next-steps"></a>다음 단계

설명 된 테이블에서 파트너를 선택 하 여 솔루션을 Azure AD B2C와 통합 하는 방법을 알아보세요.
