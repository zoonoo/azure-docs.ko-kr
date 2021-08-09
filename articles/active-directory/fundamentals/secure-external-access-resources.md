---
title: Azure Active Directory에서 외부 협업 보안
description: 내부 리소스에 대한 외부 액세스 보안에 관한 설계자 및 IT 관리자 가이드
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219830"
---
# <a name="securing-external-collaboration-in-azure-active-directory-and-microsoft-365"></a>Azure Active Directory 및 Microsoft 365에서 외부 협업 보안

외부 파트너와의 협업이 안전할 때 알맞은 외부 파트너가 적절한 기간 동안 내부 리소스에 대해 적절한 액세스 권한을 갖게 됩니다. 전체적인 거버넌스 방식을 통해 보안 위험을 줄이고 규정 준수 목표에 부합하며 누구에게 액세스 권한이 있는지 확인할 수 있습니다.

협업을 관리하지 않으면 액세스 소유권이 불분명해지고 중요한 리소스가 노출될 수 있습니다. 관리되는 안전한 협업으로 전환하면 외부 사용자의 액세스에 대한 소유권과 책임이 명확해집니다. 다음 내용이 포함됩니다.

* 리소스에 대한 액세스 권한이 있는 외부 조직과 조직 내 사용자를 관리합니다.

* 액세스 권한의 적절성, 검토, 시간 제한(가능한 경우)을 보장할 수 있습니다.

* 비즈니스 소유자가 IT에서 만든 보호책 내에서 협업을 관리할 수 있습니다.

규정 준수 프레임워크를 충족해야 하는 경우 협업을 관리하면 액세스의 적합성을 증명할 수 있습니다.

Microsoft는 외부 액세스 보안을 위한 종합적인 도구 모음을 제공합니다.  Azure AD(Azure Active Directory) B2B Collaboration은 모든 외부 협업 계획의 중심에 있습니다. Azure AD B2B는 외부 액세스의 보호와 관리를 위해 Azure AD의 다른 도구 및 Microsoft 365 서비스의 도구와 통합할 수 있습니다.

이 문서 집합은 임시 또는 느슨하게 관리되는 외부 협업에서 더 안전한 상태로 전환할 수 있도록 설계되었습니다. 

## <a name="next-steps"></a>다음 단계

리소스에 대한 외부 액세스 보안에 관해 다음 문서를 참조하세요. 나열된 순서대로 작업을 수행하는 것이 좋습니다.


1. [외부 액세스에 대한 보안 태세 결정](1-secure-access-posture.md)

2. [현재 상태 파악](2-secure-access-current-state.md)

3. [거버넌스 계획 수립](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용한 보안 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용한 보안 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용한 보안 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams, OneDrive, SharePoint에 대한 보안 액세스](9-secure-access-teams-sharepoint.md)
