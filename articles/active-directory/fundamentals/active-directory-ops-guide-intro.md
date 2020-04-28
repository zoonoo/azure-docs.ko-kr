---
title: Azure Active Directory 작업 참조 가이드
description: 이 작업 참조 가이드는 id 및 액세스 관리, 인증, 거 버 넌 스 및 작업을 보호 하 고 유지 하기 위해 수행 해야 하는 검사 및 작업에 대해 설명 합니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74535315"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure Active Directory 작업 참조 가이드

이 작업 참조 가이드에서는 다음 영역을 보호 하 고 유지 관리 하기 위해 수행 해야 하는 검사 및 작업에 대해 설명 합니다.

- **[Id 및 액세스 관리](active-directory-ops-guide-iam.md)** -id 및 자격 증명의 수명 주기를 관리 하는 기능입니다.
- **[인증 관리](active-directory-ops-guide-auth.md)** -자격 증명을 관리 하 고, 인증 환경을 정의 하 고, 할당을 위임 하 고, 사용을 측정 하 고, 엔터프라이즈 보안 상태에 따라 액세스 정책을 정의할 수 있습니다.
- **[거 버 넌 스](active-directory-ops-guide-govern.md)** -권한 부여 및 권한 없는 id에 부여 된 액세스를 평가 하 고 증명할 수 있는 기능, 감사 및 환경에 대 한 제어 변경
- **[작업](active-directory-ops-guide-ops.md)** -작업 Azure Active Directory (Azure AD)를 최적화 합니다.

여기에서 몇 가지 권장 사항은 모든 고객 환경에 적용 되지 않을 수 있습니다. 예를 들어 조직에서 암호 해시 동기화를 사용 하는 경우 AD FS 모범 사례는 적용 되지 않을 수 있습니다.

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 따라 최신 이지만 시간이 지남에 따라 변경 될 수 있습니다. 조직에서는 Microsoft 제품 및 서비스가 시간이 지남에 따라 발전 함에 따라 id 사례를 지속적으로 평가 해야 합니다. 조직에서 다른 Azure AD Premium 라이선스를 구독 하는 경우 권장 사항이 변경 될 수 있습니다. 예를 들어 Azure AD Premium P2에는 보다 거 버 넌 스 권장 구성이 포함 됩니다.

## <a name="stakeholders"></a>관련자

이 참조 가이드의 각 섹션에서는 이해 관계자에 게 주요 작업을 계획 하 고 구현 하는 것을 권장 합니다. 다음 표에서는이 가이드의 모든 관련자 목록을 간략하게 설명 합니다.

| 관련자 | 설명 |
| :- | :- |
| IAM 운영 팀 | 이 팀은 Id 및 액세스 관리 시스템의 일상 업무 관리를 처리 합니다. |
| 생산성 팀 | 이 팀은 메일, 파일 공유, 공동 작업, 인스턴트 메시징, 회의 등의 생산성 응용 프로그램을 소유 하 고 관리 합니다. |
| 애플리케이션 소유자 | 이 팀은 비즈니스의 특정 응용 프로그램과 일반적으로 조직의 기술적 관점을 소유 합니다. |
| InfoSec 아키텍처 팀 | 이 팀은 조직의 정보 보안 사례를 계획 하 고 설계 합니다. |
| InfoSec 운영 팀 | 이 팀은 InfoSec 아키텍처 팀의 구현 된 정보 보안 사례를 실행 하 고 모니터링 합니다. |

## <a name="next-steps"></a>다음 단계

[Id 및 액세스 관리 확인 및 작업](active-directory-ops-guide-iam.md)을 시작 합니다.
