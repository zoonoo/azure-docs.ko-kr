---
title: ID 보호 및 B2B 사용자 - Azure Active Directory
description: B2B 사용자 ID 보호 사용 방법 및 알려진 제한 사항
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
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88949199"
---
# <a name="identity-protection-and-b2b-users"></a>ID 보호 및 B2B 사용자

Azure AD B2B 협업을 통해 조직은 ID 보호를 사용하여 B2B 사용자에 대한 위험 기반 정책을 적용할 수 있습니다. 해당 정책은 두 가지 방법으로 구성됩니다:

- 관리자는 게스트 사용자를 포함하여 모든 앱에 적용되는 기본 제공 ID 보호 위험 기반 정책을 구성할 수 있습니다.
- 관리자는 게스트 사용자를 포함하여 로그인 위험을 조건으로 사용하는 조건부 액세스 정책을 구성할 수 있습니다.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>B2B 협업 사용자에 대한 위험 평가 방법

B2B 협업 사용자에 대한 위험은 홈 디렉터리에서 평가됩니다. 해당 사용자의 실시간 로그인 위험은 리소스에 액세스하려고 할 때 리소스 디렉터리에서 평가됩니다.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B 협업 사용자용 ID 보호 제한 사항

홈 디렉터리에 ID가 존재하기 때문에 리소스 디렉터리에서 B2B 협업 사용자를 위한 ID 보호 구현에 제한 사항이 있습니다. 기본 제한 사항은 다음과 같습니다:

- 게스트 사용자가 ID 보호 사용자 위험 정책을 트리거하여 암호를 재설정하면 **차단됩니다**. 해당 차단은 리소스 디렉터리에서 암호를 재설정할 수 없기 때문에 발생합니다.
- **게스트 사용자는 위험 사용자 보고서에 표시되지 않습니다**. 표시되지 않는 이유는 B2B 사용자의 홈 디렉터리에서 발생하는 위험 평가 때문입니다.
- 관리자는 리소스 디렉터리에서 **위험 B2B 협업 사용자를 해제하거나 수정할 수 없습니다**. 기능을 사용할 수 없는 이유는 리소스 디렉터리의 관리자가 B2B 사용자의 홈 디렉터리에 액세스할 수 없기 때문입니다.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>내 디렉터리에서 위험 B2B 협업 사용자를 수정할 수 없는 이유는 무엇입니까?

B2B 사용자에 대한 위험 평가 및 수정은 홈 디렉터리에서 발생합니다. 이로 인해 게스트 사용자는 리소스 디렉터리의 위험 사용자 보고서에 표시되지 않으며 리소스 디렉터리 관리자는 해당 사용자에 대해 보안 암호 재설정을 강제할 수 없습니다.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>조직의 위험 기반 정책으로 B2B 협업 사용자가 차단된 경우 어떻게 해야 하나요?

디렉터리의 위험 B2B 사용자가 위험 기반 정책에 의해 차단되는 경우 사용자는 홈 디렉터리에서 해당 위험을 수정해야 합니다. 사용자는 홈 디렉터리에서 보안 암호를 재설정하여 해당 위험을 수정할 수 있습니다. 홈 디렉터리에서 셀프 서비스 암호 재설정을 사용하도록 설정하지 않은 경우 조직의 IT 담당자에게 문의하여 관리자가 수동으로 해당 위험을 해제하거나 암호를 재설정하도록 해야 합니다.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>B2B 협업 사용자가 위험 기반 정책의 영향을 받지 않도록 하려면 어떻게 해야 하나요?

조직의 위험 기반 조건부 액세스 정책에서 B2B 사용자를 제외하면 B2B 사용자가 위험 평가에 영향을 받거나 차단되지 않습니다. 해당 B2B 사용자를 제외하려면 Azure AD에서 조직의 모든 게스트 사용자를 포함하는 그룹을 만듭니다. 그런 다음 이 그룹을 기본 제공 ID 보호 사용자 위험 및 로그인 위험 정책과 로그인 위험을 조건으로 사용하는 조건부 액세스 정책에서 제외합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](../external-identities/what-is-b2b.md)