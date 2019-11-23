---
title: Azure Active Directory Identity Protection security overview
description: Learn how the Security overview gives you an insight into your organization’s security posture.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382193"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection - 보안 개요

The [Security overview](https://aka.ms/IdentityProtectionRefresh) in the Azure portal gives you an insight into your organization’s security posture. 잠재적 공격을 식별하고 정책의 효과를 이해하는 데 도움이 됩니다.

‘보안 개요’는 크게 다음 두 개의 섹션으로 나뉩니다.

- 왼쪽 추세는 조직에 있는 위험의 타임라인을 제공합니다.
- 오른쪽 타일은 조직의 주요 현안을 강조 표시하고 빠르게 조치를 취하는 방법을 제안합니다.

![보안 개요](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>동향

### <a name="new-risky-users-detected"></a>새로운 위험한 사용자가 탐지됨

이 차트는 선택된 기간에 탐지된 새로운 위험한 사용자 수를 보여 줍니다. 사용자 위험 수준(낮음, 중간, 높음)을 기준으로 이 차트의 보기를 필터링할 수 있습니다. UTC 날짜 증분을 가리키면 해당 날짜에 탐지된 위험한 사용자 수가 표시됩니다. 이 차트를 클릭하면 ‘위험한 사용자’ 보고서로 이동됩니다. 위험에 처한 사용자의 문제를 해결하려면 사용자의 암호를 변경하는 것이 좋습니다.

### <a name="new-risky-sign-ins-detected"></a>새로운 위험한 로그인이 탐지됨

이 차트는 선택된 기간에 탐지된 위험한 로그인 수를 보여 줍니다. 로그인 위험 유형(실시간 또는 집계) 또는 로그인 위험 수준(낮음, 중간, 높음)을 기준으로 이 차트의 보기를 필터링할 수 있습니다. 보호되지 않는 로그인은 MFA가 요청되지 않은 성공한 실시간 위험 로그인입니다. (Note: Sign-ins that are risky because of offline detections cannot be protected in real-time by sign-in risk policies). UTC 날짜 증분을 가리키면 해당 날짜에 탐지된 위험한 로그인 수가 표시됩니다. 이 차트를 클릭하면 ‘위험한 로그인’ 보고서로 이동됩니다.

## <a name="tiles"></a>타일
 
### <a name="high-risk-users"></a>위험 수준이 높은 사용자

‘위험 수준이 높은 사용자’ 타일은 ID 손상 가능성이 큰 최근 사용자 수를 표시합니다. 해당 사용자는 최우선으로 조사해야 합니다. ‘위험 수준이 높은 사용자’ 타일을 클릭하면 위험 수준이 높은 사용자만 표시하는 ‘위험한 사용자’ 보고서의 필터링된 보기로 리디렉션됩니다. 이 보고서를 사용하여 자세히 알아보고 암호 재설정을 통해 해당 사용자의 문제를 해결할 수 있습니다.

![보안 개요](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>위험 수준이 중간인 사용자
‘위험 수준이 중간인 사용자’ 타일은 ID 손상 가능성이 중간인 최근 사용자 수를 표시합니다. ‘위험 수준이 중간인 사용자’ 타일을 클릭하면 위험 수준이 중간인 사용자만 표시하는 ‘위험한 사용자’ 보고서의 필터링된 보기로 리디렉션됩니다. 이 보고서를 사용하여 해당 사용자를 추가로 조사하고 문제를 해결할 수 있습니다.

### <a name="unprotected-risky-sign-ins"></a>보호되지 않는 위험한 로그인

The ‘Unprotected risky sign-ins' tile shows the last week’s count of successful, real-time risky sign-ins that were not blocked or MFA challenged by a Conditional Access policy, Identity Protection risk policy, or per-user MFA. 성공했으며 MFA가 요청되지 않은 잠재적으로 손상된 로그인입니다. 나중에 해당 로그인을 보호하려면 로그인 위험 정책을 적용합니다. ‘보호되지 않는 위험한 로그인’ 타일을 클릭하면 로그인 위험 정책 구성 블레이드로 리디렉션됩니다. 이 블레이드에서 지정된 위험 수준으로 로그인할 경우 MFA를 요구하도록 로그인 위험 정책을 구성할 수 있습니다.

### <a name="legacy-authentication"></a>레거시 인증

‘레거시 인증’ 타일은 조직에서 발생한 지난주의 레거시 인증 수를 표시합니다. 레거시 인증 프로토콜은 MFA와 같은 최신 보안 방법을 지원하지 않습니다. 레거시 인증을 방지하려면 조건부 액세스 정책을 적용하면 됩니다. ‘레거시 인증’ 타일을 클릭하면 ‘ID 보안 점수’로 리디렉션됩니다.

### <a name="identity-secure-score"></a>ID 보안 점수

ID 보안 점수는 보안 상태를 측정하고 업계 패턴과 비교합니다. If you click on ‘Identity Secure Score (Preview)’ tile, it will redirect to the ‘Identity Secure Score’ blade where you can learn more about improving your security posture.

## <a name="next-steps"></a>다음 단계

- [What is risk](concept-identity-protection-risks.md)

- [Policies available to mitigate risks](concept-identity-protection-policies.md)
