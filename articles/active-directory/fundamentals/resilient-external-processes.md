---
title: Azure AD B2C를 사용 하 여 외부 프로세스와의 복원 인터페이스 Microsoft Docs
description: 외부 프로세스를 사용 하 여 복원 가능 인터페이스를 빌드하는 방법
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
ms.openlocfilehash: c96856c988cae891e64ddf460d61851102e4666c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919812"
---
# <a name="resilient-interfaces-with-external-processes"></a>외부 프로세스를 사용 하는 복원 인터페이스

이 문서에서는 사용자 경험에서 RESTFul Api를 계획 하 고 구현 하는 방법에 대 한 지침을 제공 하 고 API 오류에 대 한 응용 프로그램의 복원 력을 향상 합니다.

![외부 프로세스 구성 요소가 포함 된 인터페이스를 보여 주는 이미지](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Api의 올바른 배치 확인

IEF (Identity experience framework) 정책을 사용 하면 [RESTFUL API 기술 프로필](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile)을 사용 하 여 외부 시스템을 호출할 수 있습니다. 외부 시스템은 IEF 런타임 환경에 의해 제어 되지 않으며 잠재적 오류 지점입니다.

### <a name="how-to-manage-external-systems-using-apis"></a>Api를 사용 하 여 외부 시스템을 관리 하는 방법

- 특정 데이터에 액세스 하기 위해 인터페이스를 호출 하는 동안 데이터가 인증 결정을 내릴 지 여부를 확인 합니다. 응용 프로그램의 핵심 기능에 정보가 중요 한지 여부를 평가 합니다. 예를 들어 전자 상거래와 관리와 같은 보조 기능을 비교 합니다. 정보가 인증에 필요 하지 않고 보조 시나리오에만 필요한 경우에는 호출을 응용 프로그램 논리로 이동 하는 것이 좋습니다.

- 인증에 필요한 데이터가 비교적 정적이 고 작고 디렉터리에서 표면화 된 할 다른 비즈니스 이유가 없는 경우 디렉터리에 포함 하는 것이 좋습니다.

- 가능 하면 사전 인증 경로에서 API 호출을 제거 합니다. 사용할 수 없는 경우에는 Api 앞에서 DoS (서비스 거부) 및 DDoS (분산 서비스 거부) 공격에 대해 엄격한 보호를 적용 해야 합니다. 공격자는 로그인 페이지를 로드 하 고 DoS 공격으로 API를 초과 하 여 응용 프로그램을 약화 시킬 수 있습니다. 예를 들어 로그인에 CAPTCHA를 사용 하면 등록 흐름이 도움이 될 수 있습니다.

- 가능 하면 [기본 제공 등록 사용자 흐름의 API 커넥터](https://docs.microsoft.com/azure/active-directory-b2c/api-connectors-overview) 를 사용 하 여 id 공급자를 사용 하 여 로그인 한 후 또는 사용자를 만들기 전에 웹 api와 통합할 수 있습니다. 사용자 흐름이 이미 광범위 하 게 테스트 되었으므로 사용자 흐름 수준 기능, 성능 또는 규모 테스트를 수행 하지 않아도 될 수 있습니다. 응용 프로그램의 기능, 성능 및 확장성을 테스트 해야 합니다.

- Azure AD RESTFul API [기술 프로필](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile) 은 캐싱 동작을 제공 하지 않습니다. 대신, RESTFul API 프로필은 정책에 기본 제공 되는 재시도 논리 및 시간 제한을 구현 합니다.

- 데이터를 작성 해야 하는 Api의 경우 백그라운드 작업자가 이러한 작업을 실행 하도록 작업을 큐에 대기 합니다. [Azure 큐](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction) 와 같은 서비스를 사용할 수 있습니다. 이렇게 하면 API를 통해 정책 실행 성능이 효율적으로 향상 됩니다.  

## <a name="api-error-handling"></a>API 오류 처리

Api가 Azure AD B2C 시스템 외부에 존재할 때 기술 프로필 내에서 적절 한 오류 처리를 수행 해야 합니다. 최종 사용자에 게 적절 한 정보가 있는지 확인 하 고 응용 프로그램이 오류를 정상적으로 처리할 수 있는지 확인 합니다.

### <a name="how-to-gracefully-handle-api-errors"></a>API 오류를 정상적으로 처리 하는 방법

- API는 여러 가지 이유로 실패할 수 있으며 이러한 오류에 대 한 응용 프로그램 복원 력을 향상 합니다. API에서 요청을 완료할 수 없는 경우 [HTTP 4xx 오류 메시지를 반환](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#returning-validation-error-message) 합니다. Azure AD B2C 정책에서 API를 사용할 수 없는 기능을 정상적으로 처리 하 고 축소 된 환경을 렌더링 해 보세요.

- [일시적인 오류를 정상적으로 처리](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#error-handling)합니다. RESTFul API 프로필을 사용 하 여 다양 한 [회로 차단기](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)에 대 한 오류 메시지를 구성할 수 있습니다.

- CICD (연속 통합/지속적인 업데이트)를 사전에 모니터링 하 고 사용 하 여 [기술 프로필 엔진](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile)에서 사용 하는 암호 및 인증서와 같은 API 액세스 자격 증명을 회전 합니다.

## <a name="api-management---best-practices"></a>API management-모범 사례

REST Api를 배포 하 고 RESTful 기술 프로필을 구성 하는 동안 권장 되는 모범 사례를 따르면 일반적인 실수와 간과 되는 것을 방지할 수 있습니다.

### <a name="how-to-manage-apis"></a>Api를 관리 하는 방법

- APIM (API Management)은 Api를 게시, 관리 및 분석 합니다. 또한 APIM은 백 엔드 서비스 및 마이크로 서비스에 대 한 보안 액세스를 제공 하는 인증을 처리 합니다. Api 게이트웨이를 사용 하 여 API 배포, 캐싱 및 부하 분산을 확장 합니다.

- 각 API에 대해 여러 번 호출 하 고 [Azure APIM API를 보호](https://docs.microsoft.com/azure/active-directory-b2c/secure-api-management?tabs=app-reg-ga)하는 대신 사용자 경험을 시작할 때 올바른 토큰을 얻는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 개발자를 위한 복원 력 리소스](resilience-b2c.md)
  - [복원 력 최종 사용자 환경](resilient-end-user-experience.md)
  - [개발자 모범 사례를 통한 복원 력](resilience-b2c-developer-best-practices.md)
  - [모니터링 및 분석을 통한 복원 력](resilience-with-monitoring-alerting.md)
- [인증 인프라의 빌드 복원 력](resilience-in-infrastructure.md)
- [응용 프로그램에서 인증 및 권한 부여의 복원 력 향상](resilience-app-development-overview.md)
