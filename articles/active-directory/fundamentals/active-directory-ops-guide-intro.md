---
title: Azure Active Directory 작업 참조 가이드
description: 이 작업 참조 가이드에서는 ID 및 액세스 관리, 인증, 거버넌스 및 작업을 보호하고 유지 관리하는 데 취해야 하는 검사 및 작업에 대해 설명합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535315"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure Active Directory 작업 참조 가이드

이 작업 참조 가이드에서는 다음 영역을 보호하고 유지 관리하기 위해 수행해야 하는 검사 및 작업에 대해 설명합니다.

- **[ID 및 액세스 관리](active-directory-ops-guide-iam.md)** - ID 및 해당 권한의 수명 주기를 관리할 수 있는 기능입니다.
- **[인증 관리](active-directory-ops-guide-auth.md)** - 자격 증명을 관리하고, 인증 환경을 정의하고, 대리 할당을 위임하고, 사용량을 측정하고, 엔터프라이즈 보안 태세를 기반으로 액세스 정책을 정의하는 기능입니다.
- **[거버넌스](active-directory-ops-guide-govern.md)** - 권한이 없는 및 권한 있는 ID, 감사 및 환경 변경 제어 권한을 부여받은 액세스를 평가하고 증명하는 기능입니다.
- **[작업](active-directory-ops-guide-ops.md)** - Azure Active Directory(Azure AD) 작업을 최적화합니다.

여기에 있는 일부 권장 사항은 모든 고객환경에 적용되지 않을 수 있습니다(예: 조직에서 암호 해시 동기화를 사용하는 경우 AD FS 모범 사례는 적용되지 않을 수 있습니다).

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 기준으로 최신이지만 시간이 지남에 따라 변경될 수 있습니다. Microsoft 제품 및 서비스가 시간이 지남에 따라 발전함에 따라 조직은 ID 관행을 지속적으로 평가해야 합니다. 권장 사항은 조직이 다른 Azure AD Premium 라이선스를 구독할 때 변경될 수 있습니다. 예를 들어 Azure AD Premium P2에는 더 많은 거버넌스 권장 사항이 포함됩니다.

## <a name="stakeholders"></a>이해 관계자

이 참조 가이드의 각 섹션에서는 주요 작업을 성공적으로 계획하고 구현하기 위해 이해 관계자를 할당하는 것이 좋습니다. 다음 표에서는 이 가이드의 모든 이해 관계자 목록을 간략하게 설명합니다.

| 관련자 | 설명 |
| :- | :- |
| IAM 운영 팀 | 이 팀은 ID 및 액세스 관리 시스템의 일상적인 작업을 관리합니다. |
| 생산성 팀 | 이 팀은 전자 메일, 파일 공유 및 공동 작업, 인스턴트 메시징 및 회의와 같은 생산성 응용 프로그램을 소유하고 관리합니다. |
| 애플리케이션 소유자 | 이 팀은 비즈니스에서 특정 응용 프로그램을 소유하고 일반적으로 조직의 기술적 관점을 소유합니다. |
| 인포섹 아키텍처 팀 | 이 팀은 조직의 정보 보안 관행을 계획하고 설계합니다. |
| 인포섹 운영 팀 | 이 팀은 InfoSec 아키텍처 팀의 구현된 정보 보안 사례를 실행하고 모니터링합니다. |

## <a name="next-steps"></a>다음 단계

ID 및 [액세스 관리 검사 및 작업을](active-directory-ops-guide-iam.md)시작하십시오.
