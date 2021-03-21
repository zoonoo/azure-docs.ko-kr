---
title: Azure Active Directory에서 외부 공동 작업 보안
description: 내부 리소스에 대 한 외부 액세스 보안에 대 한 설계자 및 IT 관리자 가이드
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8ec364380fc5cb9f6e33a29cd8dac96ea1fb69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219830"
---
# <a name="securing-external-collaboration-in-azure-active-directory-and-microsoft-365"></a>Azure Active Directory 및 Microsoft 365에서 외부 공동 작업 보안

외부 파트너와의 보안 공동 작업은 올바른 외부 파트너에 게 올바른 기간 동안 내부 리소스에 대 한 적절 한 액세스 권한이 있는지 확인 합니다. 전체적인 거 버 넌 스 방식을 통해 보안 위험을 줄이고, 규정 준수 목표를 충족 하 고, 액세스 권한이 있는 사용자를 확인할 수 있습니다.

비관리 공동 작업은 액세스 소유권을 명확 하 게 하지 않으며 중요 한 리소스가 노출 될 가능성을 초래 합니다. 안전 하 고 관리 되는 공동 작업으로 전환 하면 외부 사용자의 액세스에 대 한 소유권 및 책임을 명확 하 게 줄 수 있습니다. 다음 내용이 포함됩니다.

* 외부 조직 및 리소스에 대 한 액세스 권한이 있는 사용자를 관리 합니다.

* 해당 하는 경우 액세스를 적절 하 고 검토 하 고 시간을 제한 합니다.

* 비즈니스 소유자가 IT에서 만든 가드 레일 내의 공동 작업을 관리할 수 있도록 합니다.

규정 준수 프레임 워크를 충족 해야 하는 경우 관리 되는 공동 작업을 통해 액세스의 적합성을 증명할 수 있습니다.

Microsoft는 외부 액세스 보안을 위한 포괄적인 도구 모음을 제공 합니다.  Azure AD (Azure Active Directory) B2B 공동 작업은 모든 외부 공동 작업 계획의 중심에 있습니다. Azure AD B2B는 외부 액세스를 보호 하 고 관리할 수 있도록 Azure AD의 다른 도구 및 Microsoft 365 서비스의 도구와 통합할 수 있습니다.

이 문서 집합은 임시 또는 느슨하게 관리 되는 외부 공동 작업에서 더 안전한 상태로 전환할 수 있도록 설계 되었습니다. 

## <a name="next-steps"></a>다음 단계

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.


1. [외부 액세스를 위한 보안 상태 결정](1-secure-access-posture.md)

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거 버 넌 스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용 하 여 안전 하 게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용 하 여 안전 하 게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft 팀, OneDrive 및 SharePoint에 대 한 액세스 보호](9-secure-access-teams-sharepoint.md)
