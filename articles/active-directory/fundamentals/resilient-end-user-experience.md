---
title: Azure AD B2C를 사용하여 복원력 있는 최종 사용자 환경 구축 | Microsoft Docs
description: Azure AD B2C를 사용하여 최종 사용자 환경의 복원력을 구축하는 방법
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c570a7f646f9fc76fcb858748c59f438b46bf033
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572135"
---
# <a name="resilient-end-user-experience"></a>복원력 있는 최종 사용자 환경

등록과 로그인 최종 사용자 환경은 다음과 같은 요소로 구성됩니다.

- 사용자가 상호작용하는 인터페이스 – 예: CSS, HTML, JavaScript

- 사용자 흐름 및 사용자 지정 정책 – 예: 가입, 로그인, 프로필 편집

- 애플리케이션의 ID 공급자(IdP) – 예: 로컬 계정 사용자 이름/암호, Outlook, Facebook, Google

![최종 사용자 환경 구성 요소를 보여 주는 이미지](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>사용자 흐름과 사용자 지정 정책 중에서 선택  

가장 일반적인 ID 태스크 구성에 도움을 드리기 위해 Azure AD B2C는 구성 가능한 기본 제공 [사용자 흐름](../../active-directory-b2c/user-flow-overview.md)을 제공합니다. 최대의 유연성을 제공하는 사용자 고유의 [사용자 지정 정책](../../active-directory-b2c/custom-policy-overview.md)을 빌드할 수도 있습니다. 그러나 사용자 지정 정책을 사용하여 복잡한 시나리오를 처리하는 것이 좋습니다.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>사용자 흐름과 사용자 지정 정책 중에서 결정하는 방법

비즈니스 요구 사항을 충족할 수 있는 경우 기본 제공 사용자 흐름을 선택합니다. Microsoft의 광범위한 테스트를 거쳤기 때문에 이러한 ID 사용자 흐름의 정책 수준 기능, 성능 또는 규모의 유효성을 검사하는 데 필요한 테스트를 최소화할 수 있습니다. 그러나 애플리케이션의 기능, 성능 및 확장성은 테스트해야 합니다.

비즈니스 요구 사항으로 인해 [사용자 지정 정책을 선택](../../active-directory-b2c/user-flow-overview.md)해야 하는 경우 애플리케이션 수준 테스트 외에도 기능, 성능 또는 규모에 대한 정책 수준 테스트를 수행해야 합니다.

[사용자 흐름과 사용자 지정 정책을 비교](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies)하는 문서를 확인하면 결정하는 데 도움이 됩니다.

## <a name="choose-multiple-idps"></a>여러 IdP 선택

[외부 ID 공급자](../../active-directory-b2c/add-identity-provider.md)(예: Facebook)를 사용하는 경우 외부 공급자를 사용할 수 없게 되는 경우에 대비한 계획이 있어야 합니다.

### <a name="how-to-set-up-multiple-idps"></a>다중 IdP 설정 방법

외부 ID 공급자 등록 프로세스의 일부로 사용자의 휴대폰 번호 또는 전자 메일 주소와 같은 확인된 ID 클레임을 포함합니다. 확인된 클레임을 기본 Azure AD B2C Directory 인스턴스에 커밋합니다. 외부 공급자를 사용할 수 없는 경우 확인된 ID 클레임으로 되돌리고 인증 방법을 전화 번호로 대체합니다. 또 다른 옵션은 사용자가 로그인할 수 있도록 일회용 암호를 사용자에게 보내는 것입니다.

 [대체 인증 경로를 빌드](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)하려면 다음 단계를 수행합니다.

 1. 로컬 계정 및 외부 IdP에서 등록을 허용하도록 등록 정책을 구성합니다.

 2. 사용자가 로그인한 후에 [다른 ID를 자신의 계정에 연결](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking)할 수 있도록 프로필 정책을 구성합니다.

 3. 사용자에게 알리고 중단 중에 [대체 IdP로 전환](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)하도록 허용합니다.

## <a name="availability-of-multi-factor-authentication"></a>다단계 인증 가용성

[MFA(다단계 인증)에 전화 서비스](../../active-directory-b2c/phone-authentication-user-flows.md)를 사용하는 경우 대체 서비스 공급자를 고려해야 합니다. 로컬 Telco 또는 전화 서비스 공급자의 서비스가 중단될 수 있습니다.

### <a name="how-to-choose-an-alternate-mfa"></a>대체 MFA를 선택하는 방법  

Azure AD B2C 서비스는 기본 제공 전화 기반 MFA 공급자를 사용하여 시간 기반 OTP(일회성 암호)를 제공합니다. OTP는 미리 등록된 사용자 전화 번호에 대해 음성 통화 및 문자 메시지가 전송되는 형식입니다. 다양한 시나리오에서 다음과 같이 대체할 방법을 사용할 수 있습니다.

**사용자 흐름** 을 사용하는 경우 복원력을 구축하는 방법에는 두 가지가 있습니다.

- **사용자 흐름 구성 변경**: 전화 기반 OTP 제공 중단이 감지될 경우 OTP 제공 방법을 전화 기반에서 이메일 기반으로 변경하고 사용자 흐름을 다시 배포하여 애플리케이션을 변경하지 않고 그대로 둡니다.

![로그인 등록을 보여 주는 스크린샷](media/resilient-end-user-experiences/create-sign-in.png)

- **애플리케이션 변경**: 등록 및 로그인과 같은 각 ID 작업에 대해 두 개의 사용자 흐름 집합을 정의합니다. 첫 번째는 전화 기반 OTP를 사용하고 두 번째는 이메일 기반 OTP를 사용하도록 집합을 구성합니다. 전화 기반 OTP 제공 중단이 감지될 경우 애플리케이션을 변경하고 다시 배포하여 첫 번째 사용자 흐름 집합에서 두 번째 흐름으로 전환하고 사용자 흐름은 변경되지 않은 상태로 둡니다.  

**사용자 지정 정책** 을 사용하는 경우 복원력을 구축하는 방법에는 네 가지가 있습니다. 아래 목록은 복잡성 순서대로 나열되어 있으며 업데이트된 정책을 다시 배포해야 합니다.

- **사용자가 전화 기반 OTP나 이메일 기반 OTP 중 하나를 선택할 수 있음**:사용자에게 두 옵션을 모두 표시하고 사용자가 옵션 중 하나를 직접 선택할 수 있습니다. 정책이나 애플리케이션을 변경할 필요가 없습니다.

- **휴대폰 기반 OTP와 이메일 기반 OTP 사이에서 동적 전환**: 등록 시 전화와 이메일 정보를 모두 수집합니다. 전화가 중단될 경우 전화 기반에서 이메일 기반 OTP 제공으로 전환되도록 사전에 사용자 지정 정책을 정의합니다. 정책이나 애플리케이션을 변경할 필요가 없습니다.

- **Authenticator 앱 사용**: [Authenticator 앱](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp)을 사용하도록 사용자 지정 정책을 업데이트합니다. 정상적인 MFA가 전화 기반 또는 메일 기반 OTP인 경우 사용자 지정 정책을 다시 배포하여 Authenticator 앱을 사용하도록 전환합니다.

>[!Note]
>사용자는 등록 중 Authenticator 앱 통합을 구성해야 합니다.

- **본인 확인 질문 사용**: 위의 방법이 적용되지 않는 경우에는 본인 확인 질문을 백업으로 구현합니다. 온보딩이나 프로필 편집 중 사용자에 대한 본인 확인 질문을 설정하고 디렉터리 이외의 별도의 데이터베이스에 답변을 저장합니다. 이 방법은 "사용자가 가지고 있는 것(예: 휴대폰)"이라는 MFA 요구 사항은 충족하지 않지만 "사용자가 알고 있는 것"이라는 보조 요구 사항을 제공합니다.

## <a name="use-a-content-delivery-network"></a>콘텐츠 배달 네트워크 사용

CDNs(콘텐츠 배달 네트워크)는 사용자 지정 사용자 흐름 UI의 스토리지에 대한 Blob 저장소보다 성능이 뛰어나고 비용이 저렴합니다. 웹 페이지 콘텐츠가 항상 사용 가능한 서버의 지리적으로 분산된 네트워크에서 더 빠르게 배달됩니다.  

엔드투엔드 시나리오 및 부하 테스트를 통해 CDN의 가용성과 콘텐츠 배포의 성능을 정기적으로 테스트합니다. 프로모션이나 휴일 트래픽으로 인한 부하 서지에 대비하여 계획하는 경우 부하 테스트에 대한 예상 값을 수정합니다.
  
## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 개발자를 위한 복원력 있는 리소스](resilience-b2c.md)
  
  - [외부 프로세스를 사용하는 복원력 있는 인터페이스](resilient-external-processes.md)
  - [개발자 모범 사례를 통한 복원력](resilience-b2c-developer-best-practices.md)
  - [모니터링 및 분석을 통한 복원력](resilience-with-monitoring-alerting.md)
- [인증 인프라의 빌드 복원력 구축](resilience-in-infrastructure.md)
- [애플리케이션에서 인증 및 권한 부여 복원력 향상](resilience-app-development-overview.md)