---
title: PIM에서 전자 메일 알림-Azure Active Directory | Microsoft Docs
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
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe57a44a5a6fa9a631604d92419fd8f5ebcce50a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394733"
---
# <a name="email-notifications-in-pim"></a>PIM에서 이메일 알림

PIM (Privileged Identity Management)을 통해 역할이 할당 되거나 활성화 되는 경우와 같이 Azure Active Directory (Azure AD) 조직에서 중요 한 이벤트가 발생 하는 시기를 알 수 있습니다. Privileged Identity Management은 사용자와 다른 참가자 전자 메일 알림을 보내 사용자에 게 알려줍니다. 이러한 이메일에는 역할 활성화 또는 갱신과 같은 관련 작업에 대한 링크도 포함될 수 있습니다. 이 문서에서는 이러한 이메일이 어떻게 구성되고 언제 전송되며 누가 이메일을 받는지에 대해 설명합니다.

## <a name="sender-email-address-and-subject-line"></a>보낸 사람 이메일 주소 및 제목 줄

Azure AD 및 Azure 리소스 역할에 대 한 Privileged Identity Management에서 보낸 전자 메일의 보낸 사람 전자 메일 주소는 다음과 같습니다.

- 전자 메일 주소:  **azure-noreply \@ microsoft.com**
- 표시 이름: Microsoft Azure

이러한 이메일은 제목 줄에 **PIM** 접두사를 포함합니다. 예를 들면 다음과 같습니다.

- PIM: Alain Charon이 백업 판독기 역할에 영구적으로 할당 되었습니다.

## <a name="notifications-for-azure-ad-roles"></a>Azure AD 역할에 대 한 알림

Azure AD 역할에 대해 다음과 같은 이벤트가 발생할 때 Privileged Identity Management 전자 메일을 보냅니다.

- 권한 있는 역할 활성화가 승인 보류 중인 경우
- 권한 있는 역할 활성화 요청이 완료된 경우
- Azure AD Privileged Identity Management 사용 하도록 설정 된 경우

Azure AD 역할에 대한 이러한 이메일을 받는 사람은 역할, 이벤트 및 알림 설정에 따라 달라집니다.

| 사용자 | 역할 활성화가 승인 보류 중임 | 역할 활성화 요청이 완료됨 | PIM이 사용하도록 설정됨 |
| --- | --- | --- | --- |
| 권한 있는 역할 관리자</br>(활성화/적격) | yes</br>(명시적인 승인자가 지정되지 않은 경우만) | 예* | yes |
| 보안 관리자</br>(활성화/적격) | 예 | 예* | yes |
| 전역 관리자</br>(활성화/적격) | 예 | 예* | yes |

\*[**알림** 설정](pim-how-to-change-default-settings.md#notifications)이 **사용** 으로 설정된 경우

다음에서는 사용자가 가상의 Contoso 조직에 대한 Azure AD 역할을 활성화할 때 전송되는 예제 이메일을 보여줍니다.

![Azure AD 역할에 대 한 새 Privileged Identity Management 전자 메일](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Azure AD 역할에 대 한 주별 Privileged Identity Management 다이제스트 전자 메일

Azure AD 역할에 대 한 주별 Privileged Identity Management 요약 전자 메일은 Privileged Identity Management를 사용 하도록 설정 된 권한 있는 역할 관리자, 보안 관리자 및 전역 관리자에 게 전송 됩니다. 이 주간 전자 메일은 권한 있는 역할 할당 뿐만 아니라 주에 대 한 Privileged Identity Management 작업의 스냅숏을 제공 합니다. 공용 클라우드의 Azure AD 조직 에서만 사용할 수 있습니다. 예를 들면 다음과 같습니다.

![Azure AD 역할에 대 한 주별 Privileged Identity Management 다이제스트 전자 메일](./media/pim-email-notifications/email-directory-weekly.png)

이메일에는 4개의 타일이 포함됩니다.

| Tile | Description |
| --- | --- |
| **Users activated(사용자가 활성화함)** | 사용자가 조직 내에서 적격 역할을 활성화 한 횟수입니다. |
| **Users made permanent(사용자 영구 지정)** | 적격 할당이 있는 사용자가 영구 지정된 횟수입니다. |
| **Privileged Identity Management의 역할 할당** | Privileged Identity Management 내에서 사용자에 게 적합 한 역할이 할당 된 횟수입니다. |
| **Role assignments outside of PIM(PIM 외부 역할 할당)** | 사용자가 Azure AD 내에서 Privileged Identity Management 외부에서 영구 역할을 할당 하는 횟수입니다. |

**최상위 역할의 개요** 섹션에는 각 역할에 대 한 영구 및 적격 관리자의 총 수에 따라 조직의 상위 5 개 역할이 나열 되어 있습니다. **작업 수행** 링크를 클릭하면 [PIM 마법사](pim-security-wizard.md)가 열리며 여기서 영구 관리자를 적격 관리자로 일괄 변환할 수 있습니다.

## <a name="email-timing-for-activation-approvals"></a>활성화 승인에 대 한 전자 메일 타이밍

사용자가 자신의 역할을 활성화 하 고 역할 설정에 승인이 필요 하면 승인자는 각 승인에 대해 두 개의 전자 메일을 받게 됩니다.

- 사용자의 활성화 요청 승인 또는 거부 요청 (요청 승인 엔진에서 전송)
- 사용자의 요청이 승인 됩니다 (요청 승인 엔진에서 보냄).

또한 전역 관리자 및 권한 있는 역할 관리자는 각 승인에 대해 전자 메일을 받습니다.

- 사용자의 역할이 활성화 됩니다 (Privileged Identity Management에서 보냄).

요청 승인 엔진에서 전송 하는 처음 두 개의 전자 메일이 지연 될 수 있습니다. 현재 90%의 전자 메일에는 3 ~ 10 분이 걸리지만 1% 고객의 경우 최대 15 분까지 훨씬 더 길어질 수 있습니다.

첫 번째 전자 메일이 전송 되기 전에 Azure Portal 승인 요청이 승인 되 면 첫 번째 전자 메일은 더 이상 트리거되지 않으며 승인 요청의 전자 메일을 통해 다른 승인자에 게 알리지 않습니다. 이는 전자 메일을 받지 못했지만 예상 된 동작 이라고 표시 될 수 있습니다.

## <a name="pim-emails-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 PIM 이메일

Azure 리소스 역할에 대해 다음과 같은 이벤트가 발생할 때 Privileged Identity Management는 소유자 및 사용자 액세스 관리자에 게 전자 메일을 보냅니다.

- 역할 할당을 승인 보류 중인 경우
- 역할이 할당된 경우
- 역할이 곧 만료되는 경우
- 역할을 확장할 수 있는 경우
- 최종 사용자가 역할을 갱신하는 경우
- 역할 활성화 요청이 완료된 경우

Azure 리소스 역할에 대해 다음과 같은 이벤트가 발생할 때 최종 사용자에 게 전자 메일을 보냅니다 Privileged Identity Management.

- 사용자에게 역할이 할당된 경우
- 사용자의 역할이 만료된 경우
- 사용자의 역할이 확장된 경우
- 사용자의 역할 활성화 요청이 완료된 경우

다음에서는 사용자가 가상의 Contoso 조직에 대한 Azure 리소스 역할을 할당받을 때 전송되는 예제 이메일을 보여 줍니다.

![Azure 리소스 역할에 대 한 새 Privileged Identity Management 전자 메일](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure AD 역할에 대 한 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md)
