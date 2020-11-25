---
title: Azure Active Directory를 사용 하 여 외부 사용자 인증에서 복원 력 빌드
description: 외부 사용자에 대 한 복원 력 있는 인증을 빌드하기 위한 IT 관리자 및 설계자를 위한 가이드
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
ms.openlocfilehash: ae8e24341c321fbfffb84d9b072abc4cf925aff3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920064"
---
# <a name="build-resilience-in-external-user-authentication"></a>외부 사용자 인증에서 복원 력 빌드

[AZURE ACTIVE DIRECTORY B2B 공동 작업](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) (AZURE AD B2B)은 다른 조직과 개인의 공동 작업을 가능 하 게 하는 [외부 id](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations) 의 기능입니다. 자격 증명을 관리 하지 않고도 게스트 사용자를 Azure AD 테 넌 트에 안전 하 게 온 보 딩 할 수 있습니다. 외부 사용자는 IdP (외부 id 공급자)에서 id 및 자격 증명을 가져옵니다. 따라서 새 자격 증명을 기억할 필요가 없습니다. 

## <a name="ways-to-authenticate-external-users"></a>외부 사용자를 인증 하는 방법

디렉터리에 대 한 외부 사용자 인증 방법을 선택할 수 있습니다. Microsoft IdPs 또는 기타 IdPs를 사용할 수 있습니다.

모든 외부 IdP에서 해당 IdP의 가용성에 대 한 종속성을 가져옵니다. IdPs에 연결 하는 몇 가지 방법을 사용 하 여 복원 력을 향상 시키기 위해 수행할 수 있는 작업은 있습니다.

> [!NOTE] 
> Azure AD B2B는 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory) 테 넌 트 또는 개인 [Microsoft 계정을](https://account.microsoft.com/account)사용 하 여 모든 사용자를 인증 하는 기본 제공 기능을 제공 합니다. 이러한 기본 제공 옵션을 사용 하 여 구성을 수행할 필요는 없습니다.

### <a name="considerations-for-resilience-with-other-idps"></a>다른 IdPs를 사용 하 여 복원에 대 한 고려 사항

게스트 사용자 인증에 외부 IdPs를 사용 하는 경우 중단을 방지 하기 위해 유지 관리를 보장 해야 하는 특정 구성이 있습니다.

| 인증 방법| 복원 력 고려 사항 |
| - | - |
| [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) 또는 [Google](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation)과 같은 소셜 idps와 페더레이션.| IdP를 사용 하 여 계정을 유지 관리 하 고 클라이언트 ID 및 클라이언트 암호를 구성 해야 합니다. |
| [SAML 및 WS-Federation Id 공급자로 직접 페더레이션](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| 사용자가 종속 된 끝점에 액세스 하려면 IdP 소유자와 공동 작업 해야 합니다. <br>인증서와 끝점이 포함 된 메타 데이터를 유지 관리 해야 합니다. |
| [일회성 암호를 전자 메일로 보내기](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| 이 방법을 사용 하면 Microsoft의 메일 시스템, 사용자의 전자 메일 시스템 및 사용자의 전자 메일 클라이언트에 따라 달라 집니다. |


 

## <a name="self-service-sign-up-preview"></a>셀프 서비스 등록 (미리 보기)

초대 또는 링크를 보내는 대신 [셀프 서비스 등록](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview)을 사용 하도록 설정할 수 있습니다.  이렇게 하면 외부 사용자가 응용 프로그램에 대 한 액세스를 요청할 수 있습니다. [API 커넥터](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) 를 만들고 사용자 흐름과 연결 해야 합니다. 사용자 환경을 정의 하는 사용자 흐름을 하나 이상의 응용 프로그램과 연결 합니다. 

[Api 커넥터](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) 를 사용 하 여 셀프 서비스 등록 사용자 흐름을 외부 시스템의 api와 통합할 수 있습니다. 이 API 통합은 사용자 [지정 승인 워크플로에](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals)사용 하 고, [id를 확인 하](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up)고, 사용자 특성을 덮어쓰는 등의 기타 작업을 수행할 수 있습니다. Api를 사용 하려면 다음 종속성을 관리 해야 합니다.

* **API 커넥터 인증**: 커넥터를 설정 하려면 끝점 URL, 사용자 이름 및 암호가 필요 합니다. 이러한 자격 증명을 유지 관리 하는 프로세스를 설정 하 고, 만료 일정을 확인할 수 있도록 API 소유자에 게 작업 합니다.

* **Api 커넥터 응답**: api를 사용할 수 없는 경우 등록 흐름에서 api 커넥터를 정상적으로 장애 조치 (failover) 하도록 설계 합니다. API 개발자 [예제 api 응답](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) 및 [문제 해결에 대 한 모범 사례](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector)를 검토 하 고 제공 합니다. API 개발 팀과 협력 하 여 연속, 유효성 검사 오류 및 차단 응답을 비롯 하 여 가능한 모든 응답 시나리오를 테스트 합니다. 

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원 력 리소스
 
* [자격 증명 관리를 사용 하 여 복원 력 빌드](resilience-in-credentials.md)

* [장치 상태를 사용 하 여 복원 력 빌드](resilience-with-device-states.md)

* [CAE (연속 액세스 평가)를 사용 하 여 복원 력 빌드](resilience-with-continuous-access-evaluation.md)

* [하이브리드 인증의 빌드 복원 력](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용 하 여 응용 프로그램 액세스에서 복원 력 빌드](resilience-on-premises-access.md)

개발자 용 복원 력 리소스

* [응용 프로그램의 빌드 IAM 복원 력](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원 력 빌드](resilience-b2c.md)
 
