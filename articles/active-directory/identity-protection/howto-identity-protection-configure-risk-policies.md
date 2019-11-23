---
title: Risk policies - Azure Active Directory Identity Protection
description: Enable and configure risk policies in Azure Active Directory Identity Protection
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
ms.openlocfilehash: 37091b2551d68e241c7179949c3eb1db9a381de6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382171"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>How To: Configure and enable risk policies

As we learned in the previous article, [Identity Protection policies](concept-identity-protection-policies.md) we have two risk policies that we can enable in our directory. 

- 로그인 위험 정책
- 사용자 위험 정책

![Security overview page to enable user and sign-in risk policies](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Both policies work to automate the response to risk detections in your environment and allow users to self-remediate when risk is detected. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>전제 조건 

If your organization wants to allow users to self-remediate when risks are detected, users must be registered for both self-service password reset and Azure Multi-Factor Authentication. We recommend [enabling the combined security information registration experience](../authentication/howto-registration-mfa-sspr-combined.md) for the best experience. Allowing users to self-remediate gets them back to a productive state more quickly without requiring administrator intervention. Administrators can still see these events and investigate them after the fact. 

## <a name="choosing-acceptable-risk-levels"></a>Choosing acceptable risk levels

Organizations must decide the level of risk they are willing to accept balancing user experience and security posture. 

Microsoft's recommendation is to set the user risk policy threshold to **High** and the sign-in risk policy to **Medium and above**.

**높음** 임계값을 선택하면 정책이 트리거되는 횟수를 줄이고 사용자에게 미치는 영향을 최소화합니다. However, it excludes **Low** and **Medium** risk detections from the policy, which may not block an attacker from exploiting a compromised identity. Selecting a **Low** threshold introduces additional user interrupts, but increased security posture.

## <a name="exclusions"></a>제외

All of the policies allow for excluding users such as your [emergency access or break-glass administrator accounts](../users-groups-roles/directory-emergency-access.md). Organizations may determine they need to exclude other accounts from specific policies based on the way the accounts are used. All exclusions should be reviewed regularly to see if they are still applicable.

## <a name="enable-policies"></a>Enable policies

To enable the user risk and sign-in risk policies complete the following steps.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. Browse to **Azure Active Directory** > **Security** > **Identity Protection** > **Overview**.
1. Select **Configure user risk policy**.
   1. Under **Assignments**
      1. **Users** - Choose **All users** or **Select individuals and groups** if limiting your rollout.
         1. Optionally you can choose to exclude users from the policy.
      1. **Conditions** - **User risk** Microsoft's recommendation is to set this option to **High**.
   1. Under **Controls**
      1. **Access** - Microsoft's recommendation is to **Allow access** and **Require password change**.
   1. **Enforce Policy** - **On**
   1. **Save** - This action will return you to the **Overview** page.
1. Select **Configure sign-in risk policy**.
   1. Under **Assignments**
      1. **Users** - Choose **All users** or **Select individuals and groups** if limiting your rollout.
         1. Optionally you can choose to exclude users from the policy.
      1. **Conditions** - **Sign-in risk** Microsoft's recommendation is to set this option to **Medium and above**.
   1. Under **Controls**
      1. **Access** - Microsoft's recommendation is to **Allow access** and **Require multi-factor authentication**.
   1. **Enforce Policy** - **On**
   1. **저장**

## <a name="next-steps"></a>다음 단계

- [Enable Azure Multi-Factor Authentication registration policy](howto-identity-protection-configure-mfa-policy.md)

- [What is risk](concept-identity-protection-risks.md)

- [Investigate risk detections](howto-identity-protection-investigate-risk.md)

- [Simulate risk detections](howto-identity-protection-simulate-risk.md)
