---
title: "Azure Active Directory B2B 공동 작업의 현재 제한 사항 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업 미리 보기의 현재 제한 사항"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 4dae74740d8fd00d36492e5a9fad8848b5d03952
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-current-limitations"></a>Azure Active Directory B2B 공동 작업의 현재 제한 사항

1. Azure AD(Azure Active Directory) B2B 공동 작업 초대 API는 베타 테스트에 포함되어 있습니다. API 표면은 최신 기능을 반영하지만 다른 모든 베타와 마찬가지로 베타 네임스페이스 계약이 적용됩니다. 이 API를 GA 릴리스의 지정된 버전 번호로 이동할 예정입니다.
2. 파트너에게 MFA 정책이 이미 적용된 상태에서 초대하는 조직에서 B2B 공동 작업 MFA가 수행되고 관리되므로 이중 MFA(Multi-Factor Authentication)가 수행될 수 있습니다. 이러한 기능은 모든 ID에 해당되며, B2B 공동 작업의 초대 대상자에 대한 인증 강도를 제어할 수 있으므로 바람직하다고 할 수 있습니다. 그러나 이미 MFA를 설정하고 적용하는 파트너의 경우, 해당 사용자는 홈 조직에서 MFA를 한 번 수행한 다음 초대하는 조직에서 다시 수행해야 합니다. 향후 릴리스에서는 특정 파트너의 MFA를 신뢰하여 이중 MFA 문제를 피하도록 할 수 있는 정책을 도입할 예정입니다.
3. B2B 공동 작업 흐름에서 해당 디렉터리에 사용자를 추가하고, 초대 상환, 앱 할당 등에서 동적으로 업데이트합니다. 업데이트 및 쓰기는 일반적으로 하나의 디렉터리 인스턴스에서 발생하므로 모든 인스턴스 간에 복제해야 합니다. 일부 경우에 복제를 완료하는 데 소요되는 한정된 기간으로 인해 인증 문제로 확인되는 문제가 발생할 수 있다는 사실이 관찰되었습니다. Microsoft는 일반 공급 전에 이러한 문제점을 최소화하거나 해소하기 위해 최선을 다하고 있습니다. 당분간 이러한 사항은 문제가 되지 않을 수 있지만 문제가 될 경우 새로 고치거나 다시 시도하면 도움이 됩니다.

  ## <a name="next-steps"></a>다음 단계

  Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

  * [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
  * [B2B 공동 작업 사용자 속성](active-directory-b2b-user-properties.md)
  * [역할에 B2B 공동 작업 사용자 추가](active-directory-b2b-add-guest-to-role.md)
  * [B2B 공동 작업 초대 위임](active-directory-b2b-delegate-invitations.md)
  * [동적 그룹 및 B2B 공동 작업](active-directory-b2b-dynamic-groups.md)
  * [B2B 공동 작업 코드 및 PowerShell 샘플](active-directory-b2b-code-samples.md)
  * [B2B 공동 작업을 위한 SaaS 앱 구성](active-directory-b2b-configure-saas-apps.md)
  * [B2B 공동 작업 사용자 토큰](active-directory-b2b-user-token.md)
  * [B2B 공동 작업 사용자 클레임 매핑](active-directory-b2b-claims-mapping.md)
  * [Office 365 외부 공유](active-directory-b2b-o365-external-user.md)

