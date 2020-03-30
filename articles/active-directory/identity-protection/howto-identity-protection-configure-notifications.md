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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120117"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory ID 보호 알림

Azure AD ID 보호는 사용자 위험 및 위험 검색을 관리하는 데 도움이 되는 두 가지 유형의 자동화된 알림 전자 메일을 보냅니다.

- 위험에 노출된 사용자가 검색된 전자 메일
- 주간 다이제스트 전자 메일

이 문서에는 두 알림 전자 메일의 개요가 나와 있습니다.

## <a name="users-at-risk-detected-email"></a>위험에 노출된 사용자가 검색된 전자 메일

위험에 노출된 계정 검색에 대한 응답으로 Azure AD ID 보호는 **위험에 노출된 사용자 검색됨**이란 제목의 전자 메일 경고를 생성합니다. 전자 메일에는 **[위험 플래그가 지정된 사용자](../reports-monitoring/concept-user-at-risk.md)** 보고서에 대한 링크가 포함됩니다. 모범 사례로, 위험에 노출된 사용자를 즉시 조사해야 합니다.

이 경고의 구성을 사용하면 경고를 생성할 사용자 위험 수준을 지정할 수 있습니다. 전자 메일은 사용자의 위험 수준이 지정한 내용에 도달하면 생성됩니다. 그러나 이 사용자 위험 수준으로 이동한 후에는 이 사용자에 대한 전자 메일 경고가 검색될 위험이 있는 새 사용자를 받지 못합니다. 예를 들어 중간 사용자 위험에 대한 경고 정책을 설정하고 사용자 John이 중간 위험으로 이동하면 John에 대한 위험이 감지된 전자 메일을 받게 됩니다. 그러나 John이 높은 위험으로 이동하거나 추가 위험 검색이 있는 경우 위험이 감지된 경고를 받는 두 번째 사용자에게는 받지 못합니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>위험에 처한 사용자 감지된 경고 구성

관리자로서 다음을 설정할 수 있습니다.

- **이 전자 메일의 생성을 트리거하는 사용자 위험 수준** - 기본적으로 위험 수준은 "높음" 위험으로 설정됩니다.
- **이 전자 메일의 받는 사람** - 기본적으로 받는 사람에는 모든 전역 관리자가 포함됩니다. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.
   - 선택적으로 **이** 기능은 미리 보기이며 정의된 사용자는 Azure Portal에서 연결된 보고서를 볼 수 있는 적절한 권한이 있어야 합니다.

**Azure Active Directory** > **보안** > **ID 보호** > 위험 경고에 있는 **Azure 포털에서** 위험이 있는 사용자를**구성합니다.**

## <a name="weekly-digest-email"></a>주간 다이제스트 전자 메일

주간 다이제스트 이메일에는 새로운 위험 탐지에 대한 요약이 포함되어 있습니다.  
 다음을 포함합니다.

- 위험에 노출된 사용자
- 의심스러운 활동
- 감지된 취약점
- ID 보호에서 관련된 보고서에 대한 링크

![주간 다이제스트 전자 메일](./media/howto-identity-protection-configure-notifications/400.png)

기본적으로 수신자는 모든 글로벌 관리자를 포함합니다. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.

### <a name="configure-weekly-digest-email"></a>주간 다이제스트 이메일 구성

관리자는 주간 다이제스트 이메일 전송을 켜거나 끄고 전자 메일을 수신하도록 할당된 사용자를 선택할 수 있습니다.

**Azure Active Directory** > **보안** > **ID 보호** > **주간 다이제스트에서** **Azure 포털에서** 주간 다이제스트 전자 메일을 구성합니다.

## <a name="see-also"></a>참조

- [Azure Active Directory ID 보호](../active-directory-identityprotection.md)
