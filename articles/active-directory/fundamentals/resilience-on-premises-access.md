---
title: 애플리케이션 프록시를 사용하여 애플리케이션 액세스에서 복원력 빌드
description: 애플리케이션 프록시를 사용하여 온-프레미스 애플리케이션에 복원 가능한 방식으로 액세스하기 위한 설계자 및 IT 관리자 가이드
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
ms.openlocfilehash: 8fed78d7d2250d749ced7fe343689df76329b60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724663"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>애플리케이션 프록시를 사용하여 애플리케이션 액세스에서 복원력 빌드

애플리케이션 프록시는 사용자가 원격 클라이언트에서 온-프레미스 웹 애플리케이션에 액세스할 수 있게 해주는 Azure AD의 기능입니다. 애플리케이션 프록시는 클라우드에서 실행되는 애플리케이션 프록시 서비스와 온-프레미스 서버에서 실행되는 애플리케이션 프록시 커넥터를 모두 포함하고 있습니다. 

사용자는 애플리케이션 프록시를 통해 게시된 URL로 온-프레미스 리소스에 액세스합니다. 이렇게 하면 Azure AD 로그인 페이지로 리디렉션됩니다. 그런 다음, Azure AD의 애플리케이션 프록시 서비스는 회사 네트워크의 애플리케이션 프록시 커넥터로 토큰을 보내며, 이 커넥터는 해당 토큰을 온-프레미스 Active Directory에 전달합니다. 그러면 인증된 사용자는 온-프레미스 리소스에 액세스할 수 있습니다. 아래 다이어그램에서 [커넥터](../manage-apps/application-proxy-connectors.md)가 [커넥터 그룹](../manage-apps/application-proxy-connector-groups.md)에 표시됩니다.

> [!IMPORTANT]
> 애플리케이션 프록시를 통해 애플리케이션을 게시하는 경우 [애플리케이션 프록시 커넥터에 대해 용량 계획 및 적절한 중복성](../manage-apps/application-proxy-connectors.md#capacity-planning)을 구현해야 합니다.

![애플리케이션 y의 아키텍처 다이어그램](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>애플리케이션 프록시를 구현하려면 어떻게 하나요?

Azure AD 애플리케이션 프록시를 사용하여 원격 액세스를 구현하려면 다음 리소스를 참조하세요.

* [애플리케이션 프록시 배포 계획](../manage-apps/application-proxy-deployment-plan.md)

* [고가용성 및 부하 분산 모범 사례](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [프록시 서버 구성](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [복원력 있는 액세스 제어 전략 디자인](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원력 리소스
 
* [자격 증명 관리를 사용하여 복원력 빌드](resilience-in-credentials.md)

* [디바이스 상태를 사용하여 복원력 빌드](resilience-with-device-states.md)

* [CAE(지속적인 액세스 권한 평가)를 사용하여 복원력 빌드](resilience-with-continuous-access-evaluation.md)

* [외부 사용자 인증에서 복원력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증에서 복원력 빌드](resilience-in-hybrid.md)

개발자를 위한 복원력 리소스

* [애플리케이션에서 IAM 복원력 빌드](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원력 빌드](resilience-b2c.md)