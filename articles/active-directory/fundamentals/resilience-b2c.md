---
title: Azure AD B2C를 사용 하 여 고객 Id 및 액세스 관리에서 복원 력 빌드 Microsoft Docs
description: Azure AD B2C를 사용 하 여 고객 Id 및 액세스 관리에서 복원 력을 구축 하는 방법
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
ms.openlocfilehash: fda9378a41e0d98276957a6fad75db2c20e6e4e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920051"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 고객 id 및 액세스 관리에서 복원 력 빌드

[AD (Azure Active Directory) B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview) 는 중요 한 고객 연결 응용 프로그램을 성공적으로 시작할 수 있도록 설계 된 고객 Id 및 액세스 관리 (ciam) 플랫폼입니다. Microsoft는 서비스를 요구에 맞게 확장 하 고 잠재적인 중단 상황에서 복원 력을 향상 시 키도 록 설계 된 [복원 력을](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) 위한 많은 기본 제공 기능을 제공 합니다. 또한 중요 업무용 응용 프로그램을 시작 하는 경우 응용 프로그램의 다양 한 디자인 및 구성 요소와 Azure AD B2C 내에서 응용 프로그램을 구성 하는 방법을 고려 하 여 중단 또는 실패 시나리오에 대 한 응답으로 복원 력 있는 동작을 수행 하는 것이 중요 합니다. 이 문서에서는 복원 력을 높이는 데 도움이 되는 몇 가지 모범 사례에 대해 설명 합니다.

복원 력 있는 서비스는 중단에도 불구 하 고 계속 작동 합니다. 다음과 같은 방법으로 서비스의 복원 력을 향상 시킬 수 있습니다.

- 모든 구성 요소 이해

- 단일 실패 지점이 제거 됩니다.

- 실패 한 구성 요소를 격리 하 여 영향 제한

- 빠른 장애 조치 (failover) 메커니즘 및 복구 경로를 사용 하 여 중복성 제공

응용 프로그램을 개발할 때 솔루션의 id 구성 요소를 사용 하 여 [응용 프로그램에서 인증 및 권한 부여의 복원 력을 높이](resilience-app-development-overview.md) 는 방법을 고려 하는 것이 좋습니다. 이 문서에서는 Azure AD B2C 응용 프로그램과 관련 된 복원 력 향상을 해결 하려고 합니다. 권장 사항은 CIAM 함수를 기준으로 그룹화 됩니다.

![이미지는 다음 섹션에서 CIAM 구성 요소를 보여 줍니다 ](media/resilience-b2c/high-level-components.png) . 다음 영역에서 복원 력을 구축 하는 방법을 안내 합니다.

- [최종 사용자 환경](resilient-end-user-experience.md): 인증 흐름에 대해 대체 (fallback) 계획을 사용 하도록 설정 하 고 Azure AD B2C 인증 서비스의 중단으로 인 한 잠재적 영향을 완화 합니다.

- [외부 프로세스를 사용 하는 인터페이스](resilient-external-processes.md): 오류를 복구 하 여 응용 프로그램 및 인터페이스에서 복원 력을 빌드 합니다.  

- [개발자 모범 사례](resilience-b2c-developer-best-practices.md): 클레임 확인자, 타사 응용 프로그램 및 REST api와의 상호 작용 같은 영역에서 일반적인 사용자 지정 정책 문제로 인해 취약을 방지 하 고 오류 처리를 개선 합니다.

- [모니터링 및 분석](resilience-with-monitoring-alerting.md): 키 표시기를 모니터링 하 고 경고를 통해 오류 및 성능 중단을 감지 하 여 서비스의 상태를 평가 합니다.

- Azure AD B2C 개발자를 위한 복원 력 리소스
  - [복원 력 최종 사용자 환경](resilient-end-user-experience.md)
  - [외부 프로세스를 사용 하는 복원 인터페이스](resilient-external-processes.md)
  - [개발자 모범 사례를 통한 복원 력](resilience-b2c-developer-best-practices.md)
  - [모니터링 및 분석을 통한 복원 력](resilience-with-monitoring-alerting.md)
- [인증 인프라의 빌드 복원 력](resilience-in-infrastructure.md)
- [응용 프로그램에서 인증 및 권한 부여의 복원 력 향상](resilience-app-development-overview.md)
