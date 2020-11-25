---
title: 응용 프로그램 프록시를 사용 하 여 응용 프로그램 액세스에서 복원 력 빌드
description: 응용 프로그램 프록시를 사용 하 여 온-프레미스 응용 프로그램에 복원 가능한 액세스를 위한 설계자 및 IT 관리자 가이드
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
ms.openlocfilehash: f8bfc3fb239f30911eddf0aa27496a465e36c486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919883"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>응용 프로그램 프록시를 사용 하 여 응용 프로그램 액세스에서 복원 력 빌드

애플리케이션 프록시는 사용자가 원격 클라이언트에서 온-프레미스 웹 애플리케이션에 액세스할 수 있게 해주는 Azure AD의 기능입니다. 응용 프로그램 프록시는 클라우드의 응용 프로그램 프록시 서비스와 온-프레미스 서버에서 실행 되는 응용 프로그램 프록시 커넥터를 모두 포함 합니다. 

사용자는 응용 프로그램 프록시를 통해 게시 된 URL을 통해 온-프레미스 리소스에 액세스 합니다. Azure AD 로그인 페이지로 리디렉션됩니다. 그런 다음 Azure AD의 응용 프로그램 프록시 서비스는 토큰을 온-프레미스 Active Directory 전달 하는 회사 네트워크의 응용 프로그램 프록시 커넥터에 토큰을 보냅니다. 그러면 인증 된 사용자가 온-프레미스 리소스에 액세스할 수 있습니다. 아래 다이어그램에서 커넥터는 [커넥터 그룹](../manage-apps/application-proxy-connector-groups.md)에 [표시 됩니다.](../manage-apps/application-proxy-connectors.md)

> [!IMPORTANT]
> 응용 프로그램 프록시를 통해 응용 프로그램을 게시 하는 경우 [응용 프로그램 프록시 커넥터에 대 한 용량 계획 및 적절 한 중복성](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning)을 구현 해야 합니다.

![응용 프로그램 y의 아키텍처 다이어그램](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>응용 프로그램 프록시를 구현할 어떻게 할까요? 있나요?

Azure AD 응용 프로그램 프록시를 사용 하 여 원격 액세스를 구현 하려면 다음 리소스를 참조 하세요.

* [애플리케이션 프록시 배포 계획](../manage-apps/application-proxy-deployment-plan.md)

* [고가용성 및 부하 분산 모범 사례](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [프록시 서버 구성](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [복원 력 있는 액세스 제어 전략 디자인](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원 력 리소스
 
* [자격 증명 관리를 사용 하 여 복원 력 빌드](resilience-in-credentials.md)

* [장치 상태를 사용 하 여 복원 력 빌드](resilience-with-device-states.md)

* [CAE (연속 액세스 평가)를 사용 하 여 복원 력 빌드](resilience-with-continuous-access-evaluation.md)

* [외부 사용자 인증에서 복원 력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증의 빌드 복원 력](resilience-in-hybrid.md)

개발자 용 복원 력 리소스

* [응용 프로그램의 빌드 IAM 복원 력](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원 력 빌드](resilience-b2c.md)
