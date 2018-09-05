---
title: PIM에서 Azure AD 디렉터리 역할에 대한 요청 승인 또는 거부 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 디렉터리 역할에 대한 요청을 승인하거나 거부하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189161"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>PIM에서 Azure AD 디렉터리 역할에 대한 요청 승인 또는 거부

PIM(Privileged Identity Management)을 사용하면, 활성화에 대한 승인이 필요한 역할을 구성하고 하나 이상의 사용자 또는 그룹을 위임된 승인자로 선택할 수 있습니다.

## <a name="view-pending-approvals-requests"></a>보류 중인 승인(요청) 보기

요청 승인이 보류 중일 때는 위임된 승인자가 전자 메일 알림을 받게 됩니다. PIM 포털에서 이러한 요청을 보려면 새로운 탐색 창의 대시보드에서 왼쪽 탐색 모음의 "보류 중 승인 요청" 탭을 선택합니다.

![](media/azure-ad-pim-approval-workflow/image023.png)

여기에서 승인 보류 중인 요청 목록이 표시됩니다.

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>역할 상승 요청(단일 및/또는 대량) 승인 또는 거부

승인하거나 거부하려는 요청을 선택하고 결정에 해당하는 작업 모음의 단추를 클릭합니다.

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>승인/거부에 대한 근거 제공

이 경우 한 번에 여러 요청을 승인하거나 거부할 수 있는 새 블레이드가 열립니다. 결정에 대한 근거를 입력하고, 아래쪽 또는 블레이드에서 승인(또는 거부)를 클릭합니다.

![](media/azure-ad-pim-approval-workflow/image029.png)

요청 프로세스가 완료되면 상태 기호에 사용자가 내린 결정이 반영됩니다(이 예에서는 승인 결정임).

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할에 대한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
- [PIM에서 이메일 알림](pim-email-notifications.md)
