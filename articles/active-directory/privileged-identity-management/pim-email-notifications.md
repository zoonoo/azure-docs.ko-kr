---
title: PIM-Azure Active Directory에서에서 알림 전자 메일 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)의 이메일 알림에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa9da83a7a6e97f5b721dad550831fe2645ffd3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60289190"
---
# <a name="email-notifications-in-pim"></a>PIM에서 이메일 알림

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 역할을 할당 하거나 활성화 하는 경우와 같은 중요 한 이벤트가 발생할 때를 알 수 있습니다. PIM은 사용자 및 다른 참가자에게 이메일 알림을 보내 계속해서 정보를 제공합니다. 이러한 이메일에는 역할 활성화 또는 갱신과 같은 관련 작업에 대한 링크도 포함될 수 있습니다. 이 문서에서는 이러한 이메일이 어떻게 구성되고 언제 전송되며 누가 이메일을 받는지에 대해 설명합니다.

## <a name="sender-email-address-and-subject-line"></a>보낸 사람 이메일 주소 및 제목 줄

Azure AD 및 Azure 리소스 역할 모두에 대해 PIM에서 보낸 이메일은 다음과 같은 보낸 사람 이메일 주소를 포함합니다.

- 전자 메일 주소: **azure noreply\@microsoft.com**
- 표시 이름: Microsoft Azure

이러한 이메일은 제목 줄에 **PIM** 접두사를 포함합니다. 예를 들면 다음과 같습니다.

- PIM: Alain Charon에게는 Backup 읽기 권한자 역할이 영구적으로 할당되었습니다.

## <a name="pim-emails-for-azure-ad-roles"></a>Azure AD 역할에 대한 PIM 이메일

Azure AD 역할에 대해 다음 이벤트가 발생하면 PIM이 이메일을 보냅니다.

- 권한 있는 역할 활성화가 승인 보류 중인 경우
- 권한 있는 역할 활성화 요청이 완료된 경우
- Azure AD PIM이 사용되도록 설정된 경우

Azure AD 역할에 대한 이러한 이메일을 받는 사람은 역할, 이벤트 및 알림 설정에 따라 달라집니다.

| 사용자 | 역할 활성화가 승인 보류 중임 | 역할 활성화 요청이 완료됨 | PIM이 사용하도록 설정됨 |
| --- | --- | --- | --- |
| 권한 있는 역할 관리자</br>(활성화/적격) | 예</br>(명시적인 승인자가 지정되지 않은 경우만) | 예* | 예 |
| 보안 관리자</br>(활성화/적격) | 아닙니다. | 예* | 예 |
| 전역 관리자</br>(활성화/적격) | 아닙니다. | 예* | 예 |

\* [**알림** 설정](pim-how-to-change-default-settings.md#notifications)이 **사용**으로 설정된 경우

다음에서는 사용자가 가상의 Contoso 조직에 대한 Azure AD 역할을 활성화할 때 전송되는 예제 이메일을 보여줍니다.

![Azure AD 역할에 대한 새로운 PIM 이메일](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Azure AD 역할에 대한 주간 PIM 다이제스트 이메일

Azure AD 역할에 대한 주간 PIM 요약 이메일은 PIM을 사용하도록 설정된 권한 있는 역할 관리자, 보안 관리자 및 글로벌 관리자에게 전송됩니다. 이 주간 이메일은 권한 있는 역할 할당뿐만 아니라 해당 주의 PIM 활동에 대한 스냅샷도 제공합니다. 공용 클라우드의 테넌트에만 제공됩니다. 예를 들면 다음과 같습니다.

![Azure AD 역할에 대한 주간 PIM 다이제스트 이메일](./media/pim-email-notifications/email-directory-weekly.png)

이메일에는 4개의 타일이 포함됩니다.

| 타일 | 설명 |
| --- | --- |
| **Users activated(사용자가 활성화함)** | 사용자가 테넌트 내부에서 적격 역할을 활성화한 횟수입니다. |
| **Users made permanent(사용자 영구 지정)** | 적격 할당이 있는 사용자가 영구 지정된 횟수입니다. |
| **Role assignments in PIM(PIM 내부 역할 할당)** | PIM 내부에서 사용자에게 적격 역할이 할당된 횟수입니다. |
| **Role assignments outside of PIM(PIM 외부 역할 할당)** | PIM 외부(Azure AD 내부)에서 사용자에게 영구 역할이 할당된 횟수입니다. |

**상위 역할 개요** 섹션에는 각 역할에 대한 총 영구 및 적격 관리자 수를 기준으로 테넌트에 있는 상위 5개의 역할이 나열됩니다. **작업 수행** 링크를 클릭하면 [PIM 마법사](pim-security-wizard.md)가 열리며 여기서 영구 관리자를 적격 관리자로 일괄 변환할 수 있습니다.

## <a name="pim-emails-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 PIM 이메일

Azure 리소스 역할에 대해 다음 이벤트가 발생할 때 PIM은 소유자 및 사용자 액세스 관리자에게 이메일을 보냅니다.

- 역할 할당을 승인 보류 중인 경우
- 역할이 할당된 경우
- 역할이 곧 만료되는 경우
- 역할을 확장할 수 있는 경우
- 최종 사용자가 역할을 갱신하는 경우
- 역할 활성화 요청이 완료된 경우

Azure 리소스 역할에 대해 다음 이벤트가 발생할 때 PIM은 최종 사용자에게 이메일을 보냅니다.

- 사용자에게 역할이 할당된 경우
- 사용자의 역할이 만료된 경우
- 사용자의 역할이 확장된 경우
- 사용자의 역할 활성화 요청이 완료된 경우

다음에서는 사용자가 가상의 Contoso 조직에 대한 Azure 리소스 역할을 할당받을 때 전송되는 예제 이메일을 보여 줍니다.

![Azure 리소스 역할에 대한 새로운 PIM 이메일](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
- [승인 또는 거부 PIM에서 Azure AD 역할에 대 한 요청](azure-ad-pim-approval-workflow.md)
