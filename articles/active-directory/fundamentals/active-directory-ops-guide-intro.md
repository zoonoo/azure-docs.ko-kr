---
title: Azure Active Directory 작업 참조 가이드
description: 이 작업 참조 가이드에서는 ID 및 액세스 관리, 인증, 거버넌스 및 작업을 보호하고 유지하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74535315"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure Active Directory 작업 참조 가이드

이 작업 참조 가이드에서는 다음 영역을 보호하고 유지 관리하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.

- **[ID 및 액세스 관리](active-directory-ops-guide-iam.md)** - ID 및 자격 증명의 수명 주기를 관리하는 기능입니다.
- **[인증 관리](active-directory-ops-guide-auth.md)** - 자격 증명을 관리하고, 인증 경험을 정의하고, 할당을 위임하며, 사용량을 측정하고, 엔터프라이즈 보안 태세에 따라 액세스 정책을 정의할 수 있습니다.
- **[거버넌스](active-directory-ops-guide-govern.md)** - 권한 없는 ID와 권한 있는 ID에 부여된 액세스 권한, 감사 및 환경에 대한 제어 변경 사항을 평가하고 증명할 수 있는 기능입니다.
- **[작업](active-directory-ops-guide-ops.md)** - 작업 Azure AD(Azure Active Directory)를 최적화합니다.

여기에서 몇 가지 권장 사항은 모든 고객 환경에 적용되지 않을 수 있습니다. 예를 들어 조직에서 암호 해시 동기화를 사용하는 경우 AD FS 모범 사례는 적용되지 않을 수 있습니다.

> [!NOTE]
> 이러한 권장 사항은 게시 시점에서는 최신이지만 시간이 지남에 따라 변경될 수 있습니다. 조직에서는 Microsoft 제품 및 서비스가 시간이 지남에 따라 발전하므로 ID 사례를 지속적으로 평가해야 합니다. 조직에서 다른 Azure AD Premium 라이선스를 구독하는 경우 권장 사항이 변경될 수 있습니다. 예를 들어 Azure AD Premium P2에는 더 많은 거버넌스 권장 사항이 포함됩니다.

## <a name="stakeholders"></a>관련자

이 참조 가이드의 각 섹션에서는 주요 작업을 계획하고 구현하도록 관련자에게 할당하는 것을 권장합니다. 다음 표에서는 이 가이드의 모든 관련자 목록을 간략하게 설명합니다.

| 관련자 | Description |
| :- | :- |
| IAM 운영 팀 | 이 팀은 ID 및 액세스 관리 시스템의 일상 업무 관리를 처리합니다. |
| 생산성 팀 | 이 팀은 메일, 파일 공유, 공동 작업, 인스턴트 메시징, 회의 등의 생산성 애플리케이션을 소유하고 관리합니다. |
| 애플리케이션 소유자 | 이 팀에는 비즈니스의 특정 애플리케이션과 조직의 일반적인 기술적 관점이 있습니다. |
| InfoSec 아키텍처 팀 | 이 팀은 조직의 정보 보안 사례를 계획하고 설계합니다. |
| InfoSec 운영 팀 | 이 팀은 InfoSec 아키텍처 팀의 구현된 정보 보안 사례를 실행하고 모니터링합니다. |

## <a name="next-steps"></a>다음 단계

[ID 및 액세스 관리 확인 및 작업](active-directory-ops-guide-iam.md)을 시작합니다.
