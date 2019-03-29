---
title: PIM-Azure Active Directory에서에서 Azure 리소스 역할에 대 한 작업 및 감사 기록 보기 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 작업 및 감사 기록을 봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/24/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6fb97c416d0e34635b8aa4c19fc6b94889f566c
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578138"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할에 대한 작업 및 감사 기록 보기

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 조직 내 Azure 리소스 역할에 대한 작업, 활성화 및 감사 기록을 볼 수 있습니다. 여기에는 구독, 리소스 그룹 및 가상 머신도 포함됩니다. Azure Portal에서 Azure RBAC(역할 기반 액세스 제어) 기능을 사용하는 모든 리소스는 PIM의 보안 및 수명 주기 관리 기능을 모두 사용할 수 있습니다.

## <a name="view-activity-and-activations"></a>작업 및 활성화 보기

특정 사용자가 다양한 리소스에서 수행한 작업을 살펴보려면 특정 활성화 기간에 연결된 Azure 리소스 작업을 보면 됩니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 작업 및 활성화를 보려는 리소스를 클릭합니다.

1. **역할** 또는 **멤버**를 클릭합니다.

1. 사용자를 클릭합니다.

    Azure 리소스에서 사용자가 수행한 작업이 날짜별로 그래픽으로 표시됩니다. 같은 기간에 이루어진 최근 역할 활성화도 표시됩니다.

    ![사용자 세부 정보](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 특정 역할 활성화를 클릭하면 역할 활성화 세부 정보와 해당 사용자가 활성화된 동안 발생한 해당 Azure 리소스 작업이 표시됩니다.

    ![역할 활성화 선택](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>자식 요소가 있는 역할 할당 내보내기

감사자에 역할 할당의 전체 목록을 제공해야 하는 규정 준수 요구 사항이 있을 수 있습니다. PIM을 사용하면 특정 리소스에서 역할 할당을 쿼리할 수 있으며, 여기에는 모든 자식 리소스에 대한 역할 할당이 포함됩니다. 이전에는 관리자가 구독의 역할 할당 전체 목록을 가져오기가 어려웠으며, 각 특정 리소스의 역할 할당을 내보내야 했습니다. PIM을 사용하면 모든 리소스 그룹 및 리소스에 대한 역할 할당을 포함하여 구독의 모든 활성 및 적격 역할 할당을 쿼리할 수 있습니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 역할 할당을 내보내려는 리소스(예: 구독)를 클릭합니다.

1. **구성원**을 클릭합니다.

1. **내보내기**를 클릭하여 [멤버 자격 내보내기] 창을 엽니다.

    ![[멤버 자격 내보내기] 창](media/azure-pim-resource-rbac/export-membership.png)

1. 모든 역할 할당을 CSV 파일로 내보려면 **모든 멤버 자격 내보내기**를 클릭합니다.

    ![CSV 파일 내보내기](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>리소스 감사 기록 보기

리소스 감사를 통해 리소스에 대한 모든 역할 작업을 볼 수 있습니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 감사 기록을 보려는 리소스를 클릭합니다.

1. **리소스 감사**를 클릭합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![리소스 감사 필터링](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. **감사 유형**으로 **활성화(할당됨 + 활성화됨)** 을 선택합니다.

    ![작업 세부 정보](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. **작업** 아래에서, Azure 리소스에서 수행한 작업 세부 정보를 보고 싶은 사용자의 **(작업)** 을 클릭합니다.

    ![사용자 작업 세부 정보](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>내 감사 보기

내 감사를 통해 자신의 개인 역할 작업을 볼 수 있습니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 감사 기록을 보려는 리소스를 클릭합니다.

1. **내 감사**를 클릭합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![개인 역할 작업](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
- [PIM에서 Azure 리소스 역할에 대한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
- [PIM에서 Azure AD 역할에 대 한 감사 기록 보기](pim-how-to-use-audit-log.md)
