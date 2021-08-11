---
title: Azure Active Directory를 사용하여 복원력 있는 ID 및 액세스 관리 빌드
description: ID 시스템 중단 복원력을 구축하는 설계자, IT 관리자 및 개발자를 위한 가이드입니다.
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
ms.openlocfilehash: 39021fecb79f6b8e7536daad592378ed998760a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454340"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Azure Active Directory를 사용하여 ID 및 액세스 관리에 대한 복원력 구현

IAM(ID 및 액세스 관리)은 ID 및 액세스 대상 관리를 지원하는 프로세스, 정책 및 기술의 프레임워크입니다. 여기에는 사용자 및 시스템의 기타 계정에 대한 인증과 권한 부여를 지원하는 많은 구성 요소가 포함됩니다.

IAM 복원력은 시스템 구성 요소의 중단을 견디고 비즈니스, 사용자, 고객 및 운영에 미치는 영향을 최소화하면서 복구할 수 있는 기능입니다. 포괄적인 오류 처리를 보장하면서 종속성, 복잡성 및 단일 실패 지점을 줄이면 복원력이 향상됩니다.

중단은 IAM 시스템의 모든 구성 요소에서 발생할 수 있습니다. 복원력 있는 IAM 시스템을 빌드하기 위해 중단이 발생한다고 가정하고 계획을 세웁니다. 

IAM 솔루션의 복원력을 계획할 때 다음 요소를 고려하십시오. 

* IAM 시스템에 의존하는 애플리케이션입니다.

* 통신 회사, 인터넷 서비스 공급자 및 공개 키 공급자를 비롯하여 인증에서 사용하는 공용 인프라입니다.

* 클라우드 및 온-프레미스 ID 공급자입니다.

* IAM에 의존하는 기타 서비스 및 이를 연결하는 API입니다.

* 시스템의 기타 모든 온-프레미스 구성 요소입니다.

원본이 무엇이든, 그에 대한 대책을 인식하고 계획하는 것이 중요합니다. 그러나 ID 시스템을 추가한 결과 종속성과 복잡성이 증가하면 복원력이 증가하는 것이 아니라 오히려 축소될 수 있습니다.

시스템에서 더 많은 복원력을 빌드하려면 다음 문서를 검토하세요.

* [IAM 인프라의 복원력 빌드](resilience-in-infrastructure.md)

* [애플리케이션에서 IAM 복원력 빌드](resilience-app-development-overview.md)

* [CIAM(고객 ID 및 액세스 관리) 시스템에서 복원력 빌드](resilience-b2c.md)
