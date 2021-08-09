---
title: Azure AD B2C를 사용하여 고객 ID 및 액세스 관리에서 복원력 구축 | Microsoft Docs
description: Azure AD B2C를 사용하여 고객 ID 및 액세스 관리에서 복원력을 구축하는 방법
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
ms.openlocfilehash: b40918db03c260f899c36d306c892b787cc6371c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724894"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 고객 ID 및 액세스 관리에서 복원력 구축

[Azure Active Directory(AD) B2C](../../active-directory-b2c/overview.md)는 중요한 고객 연결 애플리케이션을 성공적으로 시작할 수 있도록 설계된 CIAM(고객 ID 및 액세스 관리) 플랫폼입니다. 서비스를 요구에 맞게 확장하고 잠재적인 중단 상황에 직면하여 복원력을 향상시키도록 설계된 [복원력](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)을 위한 많은 기본 제공 기능을 제공합니다. 또한 중요 업무용 애플리케이션을 시작할 때 애플리케이션의 다양한 설계 및 구성 요소와 Azure AD B2C 내에서 애플리케이션이 구성되는 방식을 고려하여 중단 또는 실패 시나리오에 대한 응답으로 복원력 있는 동작을 수행하는 것이 중요합니다. 이 문서에서는 복원력을 높이는 데 도움이 되는 몇 가지 모범 사례에 대해 설명합니다.

복원력 있는 서비스는 중단에도 불구하고 계속 작동합니다. 다음과 같은 방법으로 서비스의 복원력을 향상시킬 수 있습니다.

- 모든 구성 요소 이해

- 단일 실패 지점 제거

- 실패한 구성 요소를 격리하여 영향 제한

- 빠른 장애 조치(failover) 메커니즘 및 복구 경로를 사용하여 중복도 제공

애플리케이션을 개발할 때 솔루션의 ID 구성 요소를 사용하여 [애플리케이션에서 인증 및 권한 부여 복원력을 향상](resilience-app-development-overview.md)시키는 방법을 고려하는 것이 좋습니다. 이 문서에서는 Azure AD B2C 애플리케이션과 관련된 복원력 향상을 해결하려고 합니다. 권장 사항은 CIAM 함수를 기준으로 그룹화됩니다.

![CIAM 구성 요소를 보여 주는 이미지](media/resilience-b2c/high-level-components.png) 이후 섹션에서는 다음 영역에서 복원력을 구축하는 방법을 잘 안내합니다.

- [최종 사용자 환경](resilient-end-user-experience.md): 인증 흐름에 대한 대체 계획을 사용하고 Azure AD B2C 인증 서비스 중단으로 인한 잠재적 영향을 완화합니다.

- [외부 프로세스를 사용하는 인터페이스](resilient-external-processes.md): 오류를 복구하여 애플리케이션 및 인터페이스의 복원력을 구축합니다.  

- [개발자 모범 사례:](resilience-b2c-developer-best-practices.md)일반적인 사용자 지정 정책 문제로 인한 취약성을 방지하고 클레임 검증 도구, 타사 애플리케이션 및 REST API와의 상호 작용과 같은 영역에서 오류 처리를 개선합니다.

- [모니터링 및 분석](resilience-with-monitoring-alerting.md): 주요 지표를 모니터링하여 서비스 상태를 평가하고 경고를 통해 장애 및 성능 중단을 감지합니다.

- [인증 인프라에 복원력 구축](resilience-in-infrastructure.md)

- [애플리케이션에서 인증 및 권한 부여 복원력 향상](resilience-app-development-overview.md)

Azure AD B2C를 사용하여 탄력적이고 확장 가능한 흐름을 빌드하는 방법을 알아보려면 이 동영상을 시청하세요.
>[!Video https://www.youtube.com/embed/8f_Ozpw9yTs]
