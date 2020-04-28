---
title: PIM의 이메일 알림 - Azure Active Directory | 마이크로 소프트 문서
description: Azure AD PIM(Privileged Identity Management)의 이메일 알림에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 201abd24bc4056337f1ffecd2dabd002ae352c74
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866431"
---
# <a name="email-notifications-in-pim"></a>PIM에서 이메일 알림

PIM(권한 있는 ID 관리)을 사용하면 역할이 할당되거나 활성화되는 경우와 같이 Azure Active Directory(Azure AD) 조직에서 중요한 이벤트가 발생하는 시기를 알 수 있습니다. 권한 있는 ID 관리는 사용자와 다른 참가자에게 이메일 알림을 전송하여 정보를 유지합니다. 이러한 이메일에는 역할 활성화 또는 갱신과 같은 관련 작업에 대한 링크도 포함될 수 있습니다. 이 문서에서는 이러한 이메일이 어떻게 구성되고 언제 전송되며 누가 이메일을 받는지에 대해 설명합니다.

## <a name="sender-email-address-and-subject-line"></a>보낸 사람 이메일 주소 및 제목 줄

Azure AD 및 Azure 리소스 역할에 대해 권한 있는 ID 관리에서 보낸 전자 메일에는 다음과 같은 보낸 사람 전자 메일 주소가 있습니다.

- 전자 메일 주소: **azure-noreply\@microsoft.com**
- 표시 이름: Microsoft Azure

이러한 이메일은 제목 줄에 **PIM** 접두사를 포함합니다. 예를 들면 다음과 같습니다.

- PIM: 알린 샤론이 백업 리더 역할을 영구적으로 할당되었습니다.

## <a name="notifications-for-azure-ad-roles"></a>Azure AD 역할에 대한 알림

권한 있는 ID 관리는 Azure AD 역할에 대해 다음 이벤트가 발생할 때 전자 메일을 보냅니다.

- 권한 있는 역할 활성화가 승인 보류 중인 경우
- 권한 있는 역할 활성화 요청이 완료된 경우
- Azure AD 권한 ID 관리를 사용하도록 설정된 경우

Azure AD 역할에 대한 이러한 이메일을 받는 사람은 역할, 이벤트 및 알림 설정에 따라 달라집니다.

| 사용자 | 역할 활성화가 승인 보류 중임 | 역할 활성화 요청이 완료됨 | PIM이 사용하도록 설정됨 |
| --- | --- | --- | --- |
| 권한 있는 역할 관리자</br>(활성화/적격) | 예</br>(명시적인 승인자가 지정되지 않은 경우만) | 예* | 예 |
| 보안 관리자</br>(활성화/적격) | 예 | 예* | 예 |
| 전역 관리자</br>(활성화/적격) | 예 | 예* | 예 |

\*[**알림** 설정](pim-how-to-change-default-settings.md#notifications)이 **사용**으로 설정된 경우

다음에서는 사용자가 가상의 Contoso 조직에 대한 Azure AD 역할을 활성화할 때 전송되는 예제 이메일을 보여줍니다.

![Azure AD 역할에 대한 새 권한 ID 관리 전자 메일](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Azure AD 역할에 대한 주간 권한 ID 관리 다이제스트 이메일

Azure AD 역할에 대한 주간 권한 ID 관리 요약 전자 메일은 권한 있는 ID 관리를 사용하도록 설정한 권한 있는 역할 관리자, 보안 관리자 및 전역 관리자에게 전송됩니다. 이 주간 전자 메일은 해당 주에 대한 권한 있는 ID 관리 활동의 스냅샷과 권한 있는 역할 할당을 제공합니다. 퍼블릭 클라우드의 테넌트에만 제공됩니다. 예를 들면 다음과 같습니다.

![Azure AD 역할에 대한 주간 권한 ID 관리 다이제스트 이메일](./media/pim-email-notifications/email-directory-weekly.png)

이메일에는 4개의 타일이 포함됩니다.

| 타일 | 설명 |
| --- | --- |
| **Users activated(사용자가 활성화함)** | 사용자가 테넌트 내부에서 적격 역할을 활성화한 횟수입니다. |
| **Users made permanent(사용자 영구 지정)** | 적격 할당이 있는 사용자가 영구 지정된 횟수입니다. |
| **권한 있는 ID 관리의 역할 할당** | 사용자에게 권한 있는 ID 관리 내에서 적격 역할이 할당된 횟수입니다. |
| **Role assignments outside of PIM(PIM 외부 역할 할당)** | 사용자에게 권한 있는 ID 관리(Azure AD 내부) 외부에서 영구적인 역할이 할당된 횟수입니다. |

**상위 역할 개요** 섹션에는 각 역할에 대한 총 영구 및 적격 관리자 수를 기준으로 테넌트에 있는 상위 5개의 역할이 나열됩니다. **작업 수행** 링크를 클릭하면 [PIM 마법사](pim-security-wizard.md)가 열리며 여기서 영구 관리자를 적격 관리자로 일괄 변환할 수 있습니다.

## <a name="email-timing-for-activation-approvals"></a>활성화 승인을 위한 이메일 시간

사용자가 자신의 역할을 활성화하고 역할 설정에 승인이 필요하면 승인자는 각 승인에 대해 세 개의 이메일을 받게 됩니다.

- 사용자의 활성화 요청을 승인 하거나 거부 하는 요청 (요청 승인 엔진에 의해 전송)
- 사용자의 요청이 승인됨(요청 승인 엔진에서 전송).
- 사용자의 역할이 활성화됩니다(권한 있는 ID 관리에서 전송).

요청 승인 엔진에서 보낸 처음 두 전자 메일은 지연될 수 있습니다. 현재 이메일의 90%는 3~10분이 걸리지만 1%의 경우 최대 15분까지 훨씬 더 길어질 수 있습니다.

첫 번째 전자 메일을 보내기 전에 Azure 포털에서 승인 요청이 승인되면 첫 번째 전자 메일이 더 이상 트리거되지 않으며 다른 승인자는 승인 요청에 대한 전자 메일로 알림을 받지 않습니다. 전자 메일을 받지 못한 것처럼 보일 수 있지만 예상되는 동작입니다.

## <a name="pim-emails-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 PIM 이메일

권한 있는 ID 관리 Azure 리소스 역할에 대 한 다음 이벤트가 발생 하는 경우 소유자 및 사용자 액세스 관리자에 게 전자 메일을 보냅니다.

- 역할 할당을 승인 보류 중인 경우
- 역할이 할당된 경우
- 역할이 곧 만료되는 경우
- 역할을 확장할 수 있는 경우
- 최종 사용자가 역할을 갱신하는 경우
- 역할 활성화 요청이 완료된 경우

권한 있는 ID 관리 Azure 리소스 역할에 대 한 다음 이벤트가 발생 하는 경우 최종 사용자에 게 전자 메일을 보냅니다.

- 사용자에게 역할이 할당된 경우
- 사용자의 역할이 만료된 경우
- 사용자의 역할이 확장된 경우
- 사용자의 역할 활성화 요청이 완료된 경우

다음에서는 사용자가 가상의 Contoso 조직에 대한 Azure 리소스 역할을 할당받을 때 전송되는 예제 이메일을 보여 줍니다.

![Azure 리소스 역할에 대한 새 권한 ID 관리 전자 메일](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
- [권한 있는 ID 관리에서 Azure AD 역할에 대한 요청을 승인 하거나 거부합니다.](azure-ad-pim-approval-workflow.md)
