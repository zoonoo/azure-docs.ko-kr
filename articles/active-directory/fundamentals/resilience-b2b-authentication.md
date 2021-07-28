---
title: Azure Active Directory를 이용한 외부 사용자 인증에서 복원력 구축
description: IT 관리자 및 설계자가 외부 사용자를 위한 복원력 있는 인증을 빌드하기 위한 지침
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
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724962"
---
# <a name="build-resilience-in-external-user-authentication"></a>외부 사용자 인증에서 복원력 구축

[Azure Active Directory B2B collaboration](../external-identities/what-is-b2b.md)(Azure AD B2B)은 다른 조직 및 개인과의 협업을 가능하게 하는 [External Identities](../external-identities/delegate-invitations.md) 기능입니다. 자격 증명을 관리하지 않고도 게스트 사용자를 Azure AD 테넌트로 안전하게 온보딩할 수 있습니다. 외부 사용자는 외부 IdP(ID 공급자)에서 ID 및 자격 증명을 가져오므로 새 자격 증명을 기억할 필요가 없습니다. 

## <a name="ways-to-authenticate-external-users"></a>외부 사용자를 인증하는 방법

디렉터리에 대한 외부 사용자 인증 방법을 선택할 수 있습니다. Microsoft IdP 또는 기타 IdP를 사용할 수 있습니다.

어떤 외부 IdP를 사용해도 해당 IdP의 가용성에 종속됩니다. IdP에 연결하는 몇 가지 방법과 함께 사용할 수 있는 복원력 향상 수단도 있습니다.

> [!NOTE] 
> Azure AD B2B에는 모든 [Azure Active Directory](../index.yml) 테넌트 또는 개인 [Microsoft 계정](https://account.microsoft.com/account)을 사용하여 사용자를 인증하는 기본 기능이 있습니다. 이러한 기본 제공 옵션을 사용하여 구성할 필요는 없습니다.

### <a name="considerations-for-resilience-with-other-idps"></a>다른 IdP의 복원력에 대한 고려 사항

게스트 사용자 인증에 외부 IdP를 사용하는 경우 중단을 방지하기 위해 유지 관리해야 하는 특정 구성이 있습니다.

| 인증 방법| 복원력 고려 사항 |
| - | - |
| [Facebook](../external-identities/facebook-federation.md) 또는 [Google](../external-identities/google-federation.md)과 같은 소셜 ID를 통한 페더레이션| IdP를 사용하여 계정을 유지 관리하고 클라이언트 ID 및 클라이언트 암호를 구성해야 합니다. |
| [SAML 및 WS-Federation ID 공급자와의 직접 페더레이션](../external-identities/direct-federation.md)| 종속된 엔드포인트에 액세스하려면 IdP 소유자와 협업해야 합니다. <br>인증서 및 엔드포인트를 포함하는 메타데이터를 유지 관리해야 합니다. |
| [이메일로 일회용 암호 보내기](../external-identities/one-time-passcode.md)| 이 방법을 사용하면 Microsoft의 이메일 시스템, 사용자의 이메일 시스템 및 사용자의 이메일 클라이언트에 종속됩니다. |


 

## <a name="self-service-sign-up-preview"></a>셀프 서비스 등록(미리 보기)

초대 또는 링크를 보내는 대신 [셀프 서비스 등록](../external-identities/self-service-sign-up-overview.md)을 사용하도록 설정할 수 있습니다.  이렇게 하면 외부 사용자가 응용 프로그램에 액세스를 요청할 수 있습니다. 사용자는 [API 커넥터](../external-identities/self-service-sign-up-add-api-connector.md) 를 만들고 사용자 흐름에 연결해야 합니다. 사용자 환경을 정의하는 사용자 흐름을 하나 이상의 응용 프로그램과 연결합니다. 

[API 커넥터](../external-identities/api-connectors-overview.md) 를 사용하여 셀프 서비스 등록 사용자 흐름을 외부 시스템의 API와 통합할 수 있습니다. 이 API 통합은 [사용자 지정 승인 워크플로](../external-identities/self-service-sign-up-add-approvals.md)에 사용되고, [ID 검증](../external-identities/code-samples-self-service-sign-up.md)에도 사용되며, 사용자 특성을 덮어쓰는 등의 기타 작업도 수행할 수 있습니다. API를 사용하려면 다음 종속성을 관리해야 합니다.

* **API 커넥터 인증**: 커넥터를 설정하려면 엔드포인트 URL, 사용자 이름 및 암호가 필요합니다. 이러한 자격 증명을 유지 관리 하는 프로세스를 설정하고, 만료 일정을 확인할 수 있도록 API 소유자와 협업합니다.

* **API 커넥터 응답**: API를 사용할 수 없는 경우 등록 흐름에서 API 커넥터를 정상적으로 장애 조치(failover)하도록 설계합니다. API 개발자에게 [예제 API 응답](../external-identities/self-service-sign-up-add-api-connector.md) 및 [문제 해결 모범 사례](../external-identities/self-service-sign-up-add-api-connector.md)를 제공하고 검토하게 합니다. API 개발 팀과 협력하여 연속성, 유효성 검사 오류 및 차단 응답을 비롯하여 가능한 모든 응답 시나리오를 테스트합니다. 

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원력 리소스
 
* [자격 증명 관리를 사용하여 복원력 빌드](resilience-in-credentials.md)

* [장치 상태를 사용하여 복원력 빌드](resilience-with-device-states.md)

* [CAE(연속 액세스 평가)를 사용하여 복원력 빌드](resilience-with-continuous-access-evaluation.md)

* [하이브리드 인증에서 복원력 빌드](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용하여 응용 프로그램 액세스에서 복원력 빌드](resilience-on-premises-access.md)

개발자를 위한 복원력 리소스

* [응용 프로그램에서 IAM 복원력 빌드](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원력 빌드](resilience-b2c.md)
