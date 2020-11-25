---
title: Azure Active Directory를 사용 하 여 복원 력 있는 id 및 액세스 관리 빌드
description: Id 시스템의 중단에 대 한 복원 력을 구축 하는 설계자, IT 관리자 및 개발자를 위한 가이드입니다.
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
ms.openlocfilehash: 7dfd51b0ed43badbc6a4882f619cb718952b0e85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919872"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Azure Active Directory를 사용 하 여 id 및 액세스 관리에 대 한 복원 력 빌드

IAM (id 및 액세스 관리)은 id 및 id에 대 한 관리를 용이 하 게 하는 프로세스, 정책 및 기술의 프레임 워크입니다. 여기에는 사용자 및 시스템의 다른 계정에 대 한 인증 및 권한 부여를 지 원하는 많은 구성 요소가 포함 됩니다.

IAM 복원 력은 시스템 구성 요소에 대 한 중단을 endure 비즈니스, 사용자, 고객 및 운영에 미치는 영향을 최소화 하면서 복구할 수 있는 기능입니다. 포괄적인 오류 처리를 보장 하는 동시에 종속성, 복잡성 및 단일 오류를 줄이는 동시에 복원 력이 증가 합니다.

중단은 IAM 시스템의 모든 구성 요소에서 가져올 수 있습니다. 복원 력 있는 IAM 시스템을 구축 하기 위해 중단이 발생 한다고 가정 하 고 계획을 계획 합니다. 

IAM 솔루션의 복원 력을 계획할 때 다음 요소를 고려 하십시오. 

* IAM 시스템을 사용 하는 응용 프로그램

* 통신 회사, 인터넷 서비스 공급자 및 공개 키 공급자를 비롯 하 여 인증에서 사용 하는 공용 인프라입니다.

* 클라우드 및 온-프레미스 id 공급자

* IAM에 의존 하는 기타 서비스 및이를 연결 하는 Api입니다.

* 시스템의 다른 모든 온-프레미스 구성 요소입니다.

원본, 그에 대 한 대책을 인식 하 고 계획 하는 것이 중요 합니다. 그러나 id 시스템을 추가 하 고 그 결과 종속성과 복잡성을 추가 하면 복원 력이 증가 하는 것이 아니라 복원 력이 줄어들 수 있습니다.

시스템에서 더 많은 복원 력을 빌드하려면 다음 문서를 검토 하세요.

* [IAM 인프라의 빌드 복원 력](resilience-in-infrastructure.md)

* [응용 프로그램의 빌드 IAM 복원 력](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원 력 빌드](resilience-b2c.md)
