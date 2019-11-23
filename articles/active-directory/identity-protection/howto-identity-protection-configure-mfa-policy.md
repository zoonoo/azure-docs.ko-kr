---
title: Configure the MFA registration policy - Azure Active Directory Identity Protection
description: Azure AD ID 보호 다단계 인증 등록 정책을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382135"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>How To: Configure the Azure Multi-Factor Authentication registration policy

Azure AD Identity Protection helps you manage the roll-out of Azure Multi-Factor Authentication (MFA) registration by configuring a Conditional Access policy to require MFA registration no matter what modern authentication app you are signing in to.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>What is the Azure Multi-Factor Authentication registration policy?

Azure Multi-Factor Authentication provides a means to verify who you are using more than just a username and password. It provides a second layer of security to user sign-ins. In order for users to be able to respond to MFA prompts, they must first register for Azure Multi-Factor Authentication.

We recommend that you require Azure Multi-Factor Authentication for user sign-ins because it:

- Delivers strong authentication through a range of verification options.
- Plays a key role in preparing your organization to self-remediate from risk detections in Identity Protection.

For more information on Azure Multi-Factor Authentication, see [What is Azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>정책 구성

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. Browse to **Azure Active Directory** > **Security** > **Identity Protection** > **MFA registration policy**.
   1. Under **Assignments**
      1. **Users** - Choose **All users** or **Select individuals and groups** if limiting your rollout.
         1. Optionally you can choose to exclude users from the policy.
   1. Under **Controls**
      1. Ensure the checkbox **Require Azure MFA registration** is checked and choose **Select**.
   1. **Enforce Policy** - **On**
   1. **저장**

## <a name="user-experience"></a>사용자 환경

Azure Active Directory Identity Protection will prompt your users to register the next time they sign in interactively and they will have 14 days to complete registration. During this 14-day period, they can bypass registration but at the end of the period they will be required to register before they can complete the sign-in process.

관련 사용자 환경에 대한 개요는 다음을 참조하세요.

- [Azure AD ID 보호를 사용하는 로그인 환경](concept-identity-protection-user-experience.md)  

## <a name="next-steps"></a>다음 단계

- [Enable sign-in and user risk policies](howto-identity-protection-configure-risk-policies.md)

- [Enable Azure AD self-service password reset](../authentication/howto-sspr-deployment.md)

- [Enable Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
