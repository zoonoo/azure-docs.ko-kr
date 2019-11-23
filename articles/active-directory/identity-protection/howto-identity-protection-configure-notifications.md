---
title: Azure Active Directory ID 보호 알림
description: 알림에서 조사 활동을 지원하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382165"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory ID 보호 알림

Azure AD Identity Protection sends two types of automated notification emails to help you manage user risk and risk detections:

- 위험에 노출된 사용자가 검색된 전자 메일
- 주간 다이제스트 전자 메일

이 문서에는 두 알림 전자 메일의 개요가 나와 있습니다.

## <a name="users-at-risk-detected-email"></a>위험에 노출된 사용자가 검색된 전자 메일

위험에 노출된 계정 검색에 대한 응답으로 Azure AD ID 보호는 **위험에 노출된 사용자 검색됨**이란 제목의 전자 메일 경고를 생성합니다. 전자 메일에는 **[위험 플래그가 지정된 사용자](../reports-monitoring/concept-user-at-risk.md)** 보고서에 대한 링크가 포함됩니다. 모범 사례로, 위험에 노출된 사용자를 즉시 조사해야 합니다.

The configuration for this alert allows you to specify at what user risk level you want the alert to be generated. The email will be generated when the user's risk level reaches what you have specified; however, you will not receive new users at risk detected email alerts for this user after they move to this user risk level. For example, if you set the policy to alert on medium user risk and your user John moves to medium risk, you will receive the users at risk detected email for John. However, you will not receive a second user at risk detected alert if John then moves to high risk or has additional risk detections.

![위험에 노출된 사용자가 검색된 전자 메일](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configure users at risk detected alerts

관리자로서 다음을 설정할 수 있습니다.

- **The user risk level that triggers the generation of this email** - By default, the risk level is set to “High” risk.
- **이 전자 메일의 받는 사람** - 기본적으로 받는 사람에는 모든 전역 관리자가 포함됩니다. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.
   - Optionally you can **Add additional emails to receive alert notifications** this feature is a preview and users defined must have the appropriate permissions to view the linked reports in the Azure portal.

Configure the users at risk email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Users at risk detected alerts**.

## <a name="weekly-digest-email"></a>주간 다이제스트 전자 메일

The weekly digest email contains a summary of new risk detections.  
다음을 포함합니다.

- 위험에 노출된 사용자
- 의심스러운 활동
- 감지된 취약점
- ID 보호에서 관련된 보고서에 대한 링크

![주간 다이제스트 전자 메일](./media/howto-identity-protection-configure-notifications/400.png)

By default, recipients include all Global Admins. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.

### <a name="configure-weekly-digest-email"></a>Configure weekly digest email

As an administrator, you can switch sending a weekly digest email on or off and choose the users assigned to receive the email.

Configure the weekly digest email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>참고 항목

- [Azure Active Directory ID 보호](../active-directory-identityprotection.md)
