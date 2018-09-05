---
title: PIM에서 Azure 리소스 역할에 대한 요청 승인 또는 거부 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 요청을 승인하거나 거부하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189807"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할에 대한 요청 승인 또는 거부

요청을 승인하거나 거부하기 위해서는 반드시 승인자 목록의 멤버여야 합니다. 

1. PIM에 있는 왼쪽 메뉴의 탭에서 **요청 승인**을 선택하고 요청을 찾습니다.

   ![“요청 승인” 창](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. 요청을 선택하고, 결정에 대한 근거를 입력하고, **승인** 또는 **거부**를 선택합니다. 그러면 요청이 해결됩니다.

   ![자세한 정보가 포함된 선택된 요청](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>워크플로 알림

다음은 워크플로 알림에 대한 몇 가지 사실입니다.

- 역할에 대한 요청이 해당 검토를 보류할 때 승인자 목록의 모든 멤버는 이메일을 통해 알림을 받습니다. 메일 알림에는 승인자가 승인 또는 거부할 수 있는 요청에 대한 직접 링크가 포함되어 있습니다.
- 요청은 승인 또는 거부 권한이 있는 승인자 목록의 첫번째 멤버가 해결합니다. 
- 승인자가 요청에 응답하는 경우 승인자 목록의 모든 멤버에게 작업의 알림이 표시됩니다. 
- 승인된 멤버가 해당 역할에서 활성화될 때 리소스 관리자에게 알림이 표시됩니다. 

>[!Note]
>승인된 멤버를 활성화하지 않아야 한다고 판단하는 경우 리소스 관리자는 PIM의 활성 역할 할당을 제거할 수 있습니다. 리소스 관리자가 승인자 목록의 멤버가 아니면 보류 중인 요청의 알림을 받지 않지만 PIM에서 보류 중인 요청을 확인하여 모든 사용자의 보류 중인 요청을 확인하고 취소할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 디렉터리 역할에 대한 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md)
- [PIM에서 이메일 알림](pim-email-notifications.md)
