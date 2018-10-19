---
title: PIM에서 Azure 리소스 역할이 있는 사용자 보기 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할이 있는 사람이 누구인지 확인합니다.
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
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: ce7c96d92938c4e3b4cc0b53271df48350083754
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465234"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할이 있는 사용자 보기

Azure Active Directory PIM(Privileged Identity Management)을 사용하면 조직 내에서 Azure Resource에 대한 액세스를 관리, 제어 및 모니터링할 수 있습니다. 여기에는 구독, 리소스 그룹 및 가상 머신도 포함됩니다. Azure Portal에서 Azure RBAC(역할 기반 액세스 제어) 기능을 사용하는 모든 리소스는 Azure AD PIM의 보안 및 수명 주기 관리 기능을 모두 사용할 수 있습니다. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>리소스 관리자에 유용한 Azure Resource용 PIM

- 관리하는 Azure 리소스에 대해 역할이 할당된 사용자 및 그룹 보기
- 주문형 "Just-In-Time" 액세스를 통해 구독, 리소스 그룹 등과 같은 리소스 관리
- 새 시간 범위 할당 설정을 통해 할당된 사용자/그룹 리소스 액세스 자동 만료
- 빠른 작업 또는 대기 중(on-call) 일정에 대한 임시 리소스 액세스 권한 할당
- 모든 기본 제공 또는 사용자 지정 역할에서 리소스 액세스에 대한 Multi-Factor Authentication 적용 
- 사용자의 활성 세션 동안 리소스 액세스와 상관 관계가 있는 리소스 활동에 대한 보고서 얻기
- 새 사용자 또는 그룹에 리소스 액세스 권한이 할당될 때 및 적격 할당을 활성화할 때 경고 받기

## <a name="view-activation-and-azure-resource-activity"></a>활성화 및 Azure 리소스 활동 보기

특정 사용자가 다양한 리소스에서 수행한 작업을 확인해야 하는 경우 적격 사용자에 대해 지정된 활성화 기간과 관련된 Azure 리소스 활동을 검토할 수 있습니다. [구성원] 보기 또는 특정 역할의 구성원 목록에서 사용자를 선택하여 시작합니다. 결과에서는 Azure 리소스의 날짜별 사용자 작업 및 동일한 기간 동안의 최근 역할 활성화가 그래픽으로 표시됩니다.

![](media/azure-pim-resource-rbac/user-details.png)

특정 역할 활성화를 선택하면 역할 활성화 세부 정보와 이 사용자가 활성 상태인 동안 발생한 해당 Azure 리소스 활동이 표시됩니다.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>구독에 액세스 권한이 있는 사용자 검토

[구독]에서 역할 할당을 검토하려면 왼쪽 탐색 영역에서 [구성원] 탭을 선택하거나, 역할을 선택한 다음 구성원을 검토할 특정 역할을 선택합니다. 

작업 모음에서 [검토]를 선택하여 기존 액세스 검토를 보고 [추가]를 선택하여 새 검토를 만듭니다.

![](media/azure-pim-resource-rbac/owner.png)

[액세스 검토에 대해 자세히 알아보기](pim-how-to-perform-security-review.md)

>[!NOTE]
검토는 현재 구독 리소스 종류에 대해서만 지원됩니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
- [PIM에서 Azure 리소스 역할에 대한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
- [Azure의 기본 제공 역할](../../role-based-access-control/built-in-roles.md)
