---
title: Azure Active Directory를 사용 하 여 IAM 인프라에서 복원 력 빌드
description: IAM 인프라의 중단에 대 한 설계자 및 IT 관리자의 비 빌드 탄력성을 위한 가이드입니다.
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
ms.openlocfilehash: 3ad97a822aaa6477616a6661a579df6c4ec82729
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919895"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Id 및 액세스 관리 인프라의 빌드 복원 력

Azure Active Directory는 조직의 리소스에 대 한 인증 및 권한 부여와 같은 중요 한 서비스를 제공 하는 글로벌 클라우드 id 및 액세스 관리 시스템입니다. 이 문서에서는 Azure Active Directory (Azure AD)를 사용 하는 리소스에 대 한 인증 또는 권한 부여 서비스의 중단 위험을 이해 하 고, 포함 하 고, 완화할 수 있는 지침을 제공 합니다. 

문서 집합은 다음을 위해 설계 되었습니다.

* Id 설계자

* Id 서비스 소유자

* Id 작업 팀

[응용 프로그램 개발자](https://aka.ms/azureadresilience/developer) 와 [Azure AD B2C 시스템](resilience-b2c.md)에 대 한 설명서도 참조 하십시오.

## <a name="what-is-resilience"></a>복원 력 이란?

Id 인프라의 컨텍스트에서 복원 력은 비즈니스, 사용자 및 운영에 영향을 주지 않고 인증 및 권한 부여, 기타 구성 요소의 실패 등의 서비스에 대 한 중단을 endure 수 있는 기능입니다. 중단이 발생할 경우 심각한 영향을 받을 수 있으며, 복원 력을 위해서는 철저 계획이 필요 합니다.

## <a name="why-worry-about-disruption"></a>중단에 대해 걱정 하는 이유는 무엇 인가요?

Azure AD에 대 한 호출 체인의 구성 요소 중 하나라도 실패 하면 인증 시스템에 대 한 모든 호출이 중단 될 수 있습니다. 즉, 사용자가 필요한 응용 프로그램에 액세스할 수 없기 때문에 인프라의 어느 부분에도 문제 작업을 중단할 수 있습니다. 따라서 이러한 호출에서 인증 호출 수와 종속성 수를 줄이는 것이 복원 력에 중요 합니다. 응용 프로그램 개발자는 토큰을 요청 하는 빈도에 대 한 일부 제어를 어설션할 수 있습니다. 예를 들어 가능한 경우 응용 프로그램에 대해 Azure AD 관리 Id를 사용 하 고 있는지 확인 하기 위해 개발자와 협력 합니다. 

Azure AD와 같은 토큰 기반 인증 시스템에서 사용자의 응용 프로그램 (클라이언트)은 응용 프로그램 또는 기타 리소스에 액세스 하기 전에 id 시스템에서 보안 토큰을 획득 해야 합니다. 유효 기간 동안 클라이언트는 동일한 토큰을 여러 번 제공 하 여 응용 프로그램에 액세스할 수 있습니다.

응용 프로그램에 제공 된 토큰이 만료 되 면 응용 프로그램은 토큰을 거부 하 고 클라이언트는 Azure AD에서 새 토큰을 획득 해야 합니다. 새 토큰을 획득 하려면 자격 증명 프롬프트와 같은 사용자 조작이 필요할 수 있습니다. 수명이 긴 토큰을 사용 하 여 인증 호출의 빈도를 줄이면 이러한 위험이 줄어듭니다. 그러나 토큰 수명에는 정책 평가를 줄여 생성 된 위험에 따라 균형을 맞춰야 합니다. 토큰 수명을 관리 하는 방법에 대 한 자세한 내용은 [재인증 프롬프트 최적화](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime)에 대 한 문서를 참조 하세요.

## <a name="ways-to-increase-resilience"></a>복원 력을 높이는 방법
다음 다이어그램에서는 복원 력을 높일 수 있는 6 가지 구체적인 방법을 보여 줍니다. 각 방법은이 문서의 다음 단계에 연결 된 문서에 자세히 설명 되어 있습니다.
  
![관리자 복원 력 개요를 보여 주는 다이어그램](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원 력 리소스
 
* [자격 증명 관리를 사용 하 여 복원 력 빌드](resilience-in-credentials.md)

* [장치 상태를 사용 하 여 복원 력 빌드](resilience-with-device-states.md)

* [CAE (연속 액세스 평가)를 사용 하 여 복원 력 빌드](resilience-with-continuous-access-evaluation.md)

* [외부 사용자 인증에서 복원 력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증의 빌드 복원 력](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용 하 여 응용 프로그램 액세스에서 복원 력 빌드](resilience-on-premises-access.md)

개발자 용 복원 력 리소스

* [응용 프로그램의 빌드 IAM 복원 력](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원 력 빌드](resilience-b2c.md)
