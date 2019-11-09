---
title: PIM에서 Azure 리소스 역할에 대 한 감사 보고서 보기-Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 작업 및 감사 기록을 봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2585457d22b773407c41bb6ea82c30a25fbfb88e
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847160"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할에 대 한 활동 및 감사 기록 보기

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 조직 내 Azure 리소스 역할에 대한 작업, 활성화 및 감사 기록을 볼 수 있습니다. 여기에는 구독, 리소스 그룹 및 가상 머신도 포함됩니다. Azure RBAC (역할 기반 액세스 제어) 기능을 활용 하는 Azure Portal 내의 모든 리소스는 Privileged Identity Management의 보안 및 수명 주기 관리 기능을 활용할 수 있습니다.

## <a name="view-activity-and-activations"></a>작업 및 활성화 보기

특정 사용자가 다양한 리소스에서 수행한 작업을 살펴보려면 특정 활성화 기간에 연결된 Azure 리소스 작업을 보면 됩니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 작업 및 활성화를 보려는 리소스를 클릭합니다.

1. **역할** 또는 **멤버**를 클릭합니다.

1. 사용자를 클릭합니다.

    Azure 리소스에서 사용자가 수행한 작업이 날짜별로 그래픽으로 표시됩니다. 같은 기간에 이루어진 최근 역할 활성화도 표시됩니다.

    ![리소스 활동 요약 및 역할 활성화를 사용 하는 사용자 세부 정보](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 특정 역할 활성화를 클릭하면 역할 활성화 세부 정보와 해당 사용자가 활성화된 동안 발생한 해당 Azure 리소스 작업이 표시됩니다.

    ![선택한 역할 활성화 및 날짜에 의해 표시 된 작업 세부 정보](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>자식 요소가 있는 역할 할당 내보내기

감사자에 역할 할당의 전체 목록을 제공해야 하는 규정 준수 요구 사항이 있을 수 있습니다. Privileged Identity Management를 사용 하면 모든 자식 리소스에 대 한 역할 할당을 포함 하 여 특정 리소스에서 역할 할당을 쿼리할 수 있습니다. 이전에는 관리자가 구독의 역할 할당 전체 목록을 가져오기가 어려웠으며, 각 특정 리소스의 역할 할당을 내보내야 했습니다. Privileged Identity Management를 사용 하 여 모든 리소스 그룹 및 리소스에 대 한 역할 할당을 포함 하 여 구독에서 모든 활성 및 적격 역할 할당을 쿼리할 수 있습니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 역할 할당을 내보내려는 리소스(예: 구독)를 클릭합니다.

1. **구성원**을 클릭합니다.

1. **내보내기**를 클릭하여 [멤버 자격 내보내기] 창을 엽니다.

    ![모든 멤버를 내보내기 위해 멤버 자격 창 내보내기](media/azure-pim-resource-rbac/export-membership.png)

1. 모든 역할 할당을 CSV 파일로 내보려면 **모든 멤버 자격 내보내기**를 클릭합니다.

    ![CSV 파일의 역할 할당을 Excel에 표시로 내보냈습니다.](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>리소스 감사 기록 보기

리소스 감사를 통해 리소스에 대한 모든 역할 작업을 볼 수 있습니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 감사 기록을 보려는 리소스를 클릭합니다.

1. **리소스 감사**를 클릭합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![필터를 사용 하는 리소스 감사 목록](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. **감사 유형**으로 **활성화(할당됨 + 활성화됨)** 을 선택합니다.

    ![감사 유형을 활성화 하 여 필터링 되는 리소스 감사 목록](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. **작업** 아래에서, Azure 리소스에서 수행한 작업 세부 정보를 보고 싶은 사용자의 **(작업)** 을 클릭합니다.

    ![특정 작업에 대 한 사용자 활동 세부 정보](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>내 감사 보기

내 감사를 통해 자신의 개인 역할 작업을 볼 수 있습니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 클릭합니다.

1. 감사 기록을 보려는 리소스를 클릭합니다.

1. **내 감사**를 클릭합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![현재 사용자에 대 한 감사 목록](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
- [Privileged Identity Management에서 Azure 리소스 역할에 대 한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
- [Privileged Identity Management에서 Azure AD 역할에 대 한 감사 기록 보기](pim-how-to-use-audit-log.md)
