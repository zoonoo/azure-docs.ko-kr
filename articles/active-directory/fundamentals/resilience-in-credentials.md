---
title: Azure Active Directory에서 자격 증명 관리를 사용 하 여 복원 력 빌드
description: 탄력적 자격 증명 전략을 구축 하는 데 필요한 설계자 및 IT 관리자를 위한 가이드입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5fd5cf419adb137df5c578e3b17b88749215be5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919980"
---
# <a name="build-resilience-with-credential-management"></a>자격 증명 관리를 사용 하 여 복원 력 빌드

자격 증명이 토큰 요청에서 Azure AD에 표시 되 면 유효성 검사에 사용할 수 있는 여러 종속성이 있습니다. 첫 번째 인증 요소는 Azure AD 인증을 사용 하 고 일부 경우에는 온-프레미스 인프라에 의존 합니다. 하이브리드 인증 아키텍처에 대 한 자세한 내용은 [하이브리드 인프라에서 복원 력 빌드](resilience-in-hybrid.md)를 참조 하세요. 

두 번째 요소를 구현 하는 경우 두 번째 요소에 대 한 종속성은 첫 번째 요소에 대 한 종속성에 추가 됩니다. 예를 들어 첫 번째 요소가 PTA를 통해 수행 되 고 두 번째 요인이 SMS 인 경우 종속성은 다음과 같습니다.

* Azure AD 인증 서비스

* Azure MFA 서비스

* 온-프레미스 인프라

* 전화 통신 회사

* 사용자의 장치 (그림 없음)

 
![인증 방법 및 종속성 이미지](./media/resilience-in-credentials/admin-resilience-credentials.png)

자격 증명 전략은 각 인증 유형의 종속성을 고려해 야 하며 단일 실패 지점을 방지 하는 방법을 프로 비전 해야 합니다. 

인증 방법에는 서로 다른 종속성이 있기 때문에 사용자가 가능한 한 많은 초 옵션에 등록할 수 있도록 하는 것이 좋습니다. 가능 하면 종속성이 서로 다른 두 번째 요소를 포함 해야 합니다. 예를 들어 음성 통화와 SMS가 두 번째 요소로 동일한 종속성을 공유 하므로이 옵션을 선택 하면 위험을 완화할 수 없습니다.

가장 탄력적으로 사용 되는 자격 증명 전략은 암호 없는 인증을 사용 하는 것입니다. Windows Hello for Business 및 FIDO 2.0 보안 키에는 두 가지 별도의 요소를 사용 하는 강력한 인증 보다 종속성이 줄어듭니다. Microsoft Authenticator 앱, 비즈니스용 Windows Hello 및 Fido 2.0 보안 키가 가장 안전 합니다. 

두 번째 요인의 경우 시간 기반 일회용 암호 (TOTP) 또는 OATH 하드웨어 토큰을 사용 하는 Microsoft Authenticator 앱 또는 다른 Authenticator 앱에는 최소한의 종속성이 있으므로 복원 력이 향상 됩니다.

## <a name="how-do-multiple-credentials-help-resilience"></a>여러 자격 증명을 통해 복원 력을 어떻게 지원 하나요?

여러 자격 증명 유형을 프로 비전 하면 사용자의 기본 설정 및 환경 제약 조건을 수용할 수 있는 옵션이 제공 됩니다. 따라서 사용자에 게 Multi-factor authentication에 대 한 메시지가 표시 되는 대화형 인증은 요청 시 특정 종속성을 사용할 수 없게 됩니다. [Multi-factor authentication에 대 한 재인증 프롬프트를 최적화할](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)수 있습니다.

위에서 설명한 개별 사용자 복원 력 외에도 기업은 잘못 된 구성, 자연 재해 또는 온-프레미스 페더레이션 서비스 (특히 Multi-factor authentication에 사용 되는 경우)에 대 한 엔터프라이즈 수준의 리소스 중단을 소개 하는 운영 오류와 같은 대규모 중단에 대 한 문제를 해결 해야 합니다. 

## <a name="how-do-i-implement-resilient-credentials"></a>복원 력 자격 증명을 구현 어떻게 할까요??

* 비즈니스용 Windows Hello, 전화 인증 및 FIDO2 보안 키와 같은 [Passwordless 자격 증명](../authentication/howto-authentication-passwordless-deployment.md) 을 배포 하 여 종속성을 줄입니다.

* 두 번째 요소로 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md) 을 배포 합니다.

* Windows Server Active Directory에서 동기화 되는 하이브리드 계정에 대 한 [암호 해시 동기화](../hybrid/whatis-phs.md) 를 설정 합니다. 이 옵션은 AD FS와 같은 페더레이션 서비스와 함께 사용 하도록 설정할 수 있으며, 페더레이션 서비스에 오류가 발생 하는 경우에는 대체를 제공 합니다.

* [Multi-factor authentication 방법의 사용을 분석](https://docs.microsoft.com/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) 하 여 사용자 환경을 개선 합니다.

* [복원 력 있는 액세스 제어 전략 구현](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원 력 리소스
 
* [장치 상태를 사용 하 여 복원 력 빌드](resilience-with-device-states.md)

* [CAE (연속 액세스 평가)를 사용 하 여 복원 력 빌드](resilience-with-continuous-access-evaluation.md)

* [외부 사용자 인증에서 복원 력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증의 빌드 복원 력](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용 하 여 응용 프로그램 액세스에서 복원 력 빌드](resilience-on-premises-access.md)

개발자 용 복원 력 리소스

* [응용 프로그램의 빌드 IAM 복원 력](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원 력 빌드](resilience-b2c.md)
