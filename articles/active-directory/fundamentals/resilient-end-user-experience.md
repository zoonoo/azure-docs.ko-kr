---
title: Azure AD B2C를 사용 하는 복원 력 최종 사용자 환경 Microsoft Docs
description: Azure AD B2C를 사용 하 여 최종 사용자 환경에서 복원 력을 구축 하는 방법
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
ms.openlocfilehash: 057599cc92e27e423d25d528d5d84b978ff4a911
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919823"
---
# <a name="resilient-end-user-experience"></a>복원 력 최종 사용자 환경

등록 및 로그인 최종 사용자 환경은 다음 요소로 구성 됩니다.

- CSS, HTML 및 JavaScript와 같이 사용자가 상호 작용 하는 인터페이스

- 사용자 흐름 및 사용자가 만든 사용자 지정 정책-등록, 로그인 및 프로필 편집

- 응용 프로그램에 대 한 id 공급자 (IDPs)-로컬 계정 사용자 이름/암호, Outlook, Facebook, Google 등

![최종 사용자 환경 구성 요소를 보여 주는 이미지](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>사용자 흐름과 사용자 지정 정책 중에서 선택  

가장 일반적인 id 작업을 설정 하는 데 도움이 되도록 Azure AD B2C는 구성 가능한 기본 제공 [사용자 흐름](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-overview)을 제공 합니다. 최대의 유연성을 제공 하는 [사용자 고유의 사용자 지정 정책을](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)빌드할 수도 있습니다. 그러나 사용자 지정 정책을 사용 하 여 복잡 한 시나리오를 처리 하는 것이 좋습니다.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>사용자 흐름과 사용자 지정 정책을 결정 하는 방법

비즈니스 요구 사항을 충족할 수 있는 경우 기본 제공 사용자 흐름을 선택 합니다. Microsoft에서 광범위 하 게 테스트 했으므로 이러한 id 사용자 흐름의 정책 수준 기능, 성능 또는 규모의 유효성을 검사 하는 데 필요한 테스트를 최소화할 수 있습니다. 응용 프로그램의 기능, 성능 및 확장성을 테스트 해야 합니다.

비즈니스 요구 사항으로 인해 [사용자 지정 정책을 선택](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) 해야 하는 경우 응용 프로그램 수준 테스트 외에도 기능, 성능 또는 규모에 대 한 정책 수준 테스트를 수행 해야 합니다.

[사용자 흐름과 사용자 지정 정책을 비교](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview#comparing-user-flows-and-custom-policies) 하 여 결정 하는 데 도움이 되는 문서를 참조 하세요.

## <a name="choose-multiple-idps"></a>여러 IDPs 선택

외부 [id 공급자](https://docs.microsoft.com/azure/active-directory-b2c/technical-overview#external-identity-providers) (예: Facebook)를 사용 하는 경우 외부 공급자를 사용할 수 없게 되는 경우 대체 요금제를 사용 해야 합니다.

### <a name="how-to-set-up-multiple-idps"></a>여러 IDPs를 설정 하는 방법

외부 id 공급자 등록 프로세스의 일부로 사용자의 휴대폰 번호 또는 전자 메일 주소와 같은 확인 된 id 클레임을 포함 합니다. 확인 된 클레임을 기본 Azure AD B2C directory 인스턴스에 커밋합니다. 외부 공급자를 사용할 수 없는 경우 확인 된 id 클레임으로 되돌리고 인증 방법으로 전화 번호로 대체 합니다. 또 다른 옵션은 사용자가 로그인 할 수 있도록 일회용 암호를 사용자에 게 보내는 것입니다.

 [대체 인증 경로를 빌드하려면](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)다음 단계를 수행 합니다.

 1. 로컬 계정 및 외부 IDPs에서 등록을 허용 하도록 등록 정책을 구성 합니다.

 2. 사용자가 로그인 한 후에 [다른 id를 자신의 계정에 연결할](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) 수 있도록 프로필 정책을 구성 합니다.

 3. 사용자에 게 알리고 중단 중에 [대체 IDP 전환](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#configure-dynamic-custom-page-content-uri) 하도록 허용 합니다.

## <a name="availability-of-multi-factor-authentication"></a>Multi-factor authentication의 가용성

[MFA (multi-factor authentication)에 전화 서비스](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)를 사용 하는 경우 대체 서비스 공급자를 고려해 야 합니다. 로컬 Telco 또는 phone 서비스 공급자의 서비스가 중단 될 수 있습니다.

### <a name="how-to-choose-an-alternate-mfa"></a>대체 MFA를 선택 하는 방법  

Azure AD B2C 서비스는 기본 제공 전화 기반 MFA 공급자를 사용 하 여 시간 기반 Otp (일회성 암호)를 제공 합니다. 사용자의 미리 등록 된 전화 번호에 대 한 음성 통화 및 문자 메시지의 형식입니다. 다양 한 시나리오에서 다음과 같은 대체 방법을 사용할 수 있습니다.

**사용자 흐름** 을 사용 하는 경우 복원 력을 구축 하는 방법에는 두 가지가 있습니다.

- **사용자 흐름 구성 변경**: 휴대폰 기반 OTP 배달의 중단을 감지 하는 경우 otp 배달 방법을 휴대폰 기반에서 전자 메일 기반으로 변경 하 고 사용자 흐름을 다시 배포 하 여 응용 프로그램을 변경 하지 않고 그대로 둡니다.

![로그인 등록을 보여 주는 스크린샷](media/resilient-end-user-experiences/create-sign-in.png)

- **응용 프로그램 변경**: 등록 및 로그인과 같은 각 id 작업에 대해 두 개의 사용자 흐름 집합을 정의 합니다. 전화 기반 OTP를 사용 하 고 두 번째는 전자 메일 기반 OTP를 사용 하도록 첫 번째 집합을 구성 합니다. 전화 기반 OTP 배달의 중단을 감지 하는 경우 응용 프로그램을 변경 하 고 다시 배포 하 여 첫 번째 사용자 흐름 집합에서 두 번째 흐름으로 전환 하 고 사용자 흐름은 변경 되지 않은 상태로 둡니다.  

**사용자 지정 정책을** 사용 하는 경우 복원 력을 구축 하는 방법에는 네 가지가 있습니다. 아래 목록에는 복잡성이 나와 있으며 업데이트 된 정책을 다시 배포 해야 합니다.

- **사용자가 전화 기반 otp 또는 메일 기반 otp** 중 하나를 선택할 수 있습니다. 사용자에 게 두 옵션을 모두 표시 하 고 사용자가 옵션 중 하나를 직접 선택할 수 있습니다. 정책이 나 응용 프로그램을 변경할 필요는 없습니다.

- **휴대폰 기반 otp와 전자 메일 기반 otp 사이에서 동적 전환**: 등록 시 전화 및 전자 메일 정보를 모두 수집 합니다. 전화를 통해 전자 메일 기반 OTP 배달용으로 전화 중단 시 조건에 따라 전환 하기 위해 미리 사용자 지정 정책을 정의 합니다. 정책이 나 응용 프로그램을 변경할 필요는 없습니다.

- Authenticator **앱 사용**: [authenticator 앱](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp)을 사용 하도록 사용자 지정 정책을 업데이트 합니다. 정상적인 MFA가 전화 기반 또는 메일 기반 OTP 인 경우 사용자 지정 정책을 다시 배포 하 여 인증자 앱을 사용 하도록 전환 합니다.

>[!Note]
>등록 하는 동안 사용자는 Authenticator 앱 통합을 구성 해야 합니다.

- **보안 질문 사용**: 위의 방법이 적용 되지 않는 경우에는 보안 질문을 백업으로 구현 합니다. 온 보 딩 또는 프로필 편집 중 사용자에 대 한 보안 질문을 설정 하 고 디렉터리 이외의 별도의 데이터베이스에 답변을 저장 합니다. 이 방법은 "사용자가 보유 한" (예: 휴대폰)의 MFA 요구 사항은 충족 하지 않지만 보조 "사용자가 알고 있는 것"을 제공 합니다.

## <a name="use-a-content-delivery-network"></a>Content delivery network 사용

CDNs (Content delivery network)는 사용자 지정 사용자 흐름 UI의 저장소에 대 한 blob 저장소 보다 성능이 뛰어나고 비용이 저렴 합니다. 웹 페이지 콘텐츠가 항상 사용 가능한 서버의 지리적으로 분산 된 네트워크에서 더 빠르게 배달 됩니다.  

종단 간 시나리오 및 부하 테스트를 통해 CDN의 가용성과 콘텐츠 배포의 성능을 정기적으로 테스트 합니다. 프로 모션 또는 휴일 트래픽으로 인해 예정 된 서 수를 계획 하는 경우 부하 테스트에 대 한 예상 값을 수정 합니다.
  
## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 개발자를 위한 복원 력 리소스](resilience-b2c.md)
  
  - [외부 프로세스를 사용 하는 복원 인터페이스](resilient-external-processes.md)
  - [개발자 모범 사례를 통한 복원 력](resilience-b2c-developer-best-practices.md)
  - [모니터링 및 분석을 통한 복원 력](resilience-with-monitoring-alerting.md)
- [인증 인프라의 빌드 복원 력](resilience-in-infrastructure.md)
- [응용 프로그램에서 인증 및 권한 부여의 복원 력 향상](resilience-app-development-overview.md)
