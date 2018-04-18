---
title: Azure 리소스에 대한 Privileged Identity Management - 역할 할당 | Microsoft Docs
description: PIM의 역할을 할당하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management - 리소스 역할 - 할당

## <a name="assign-roles"></a>역할 할당

사용자 또는 그룹을 역할에 할당하려면 역할을 선택하거나(역할을 보는 경우) 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

작업 모음에서 추가를 클릭합니다(멤버 보기에 있는 경우).

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


멤버 탭에서 사용자 또는 그룹을 추가하는 경우 다음을 수행해야 합니다. 

1. 사용자 또는 그룹을 선택하기 전에 추가 메뉴에서 역할을 선택합니다.

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. 디렉터리에서 사용자 또는 그룹을 선택합니다.

3. 드롭다운 메뉴에서 적절한 할당 유형을 선택합니다. 

    - **Just-In-Time 할당:** 지정된 시간 동안 또는 무기한(역할 설정에서 구성한 경우)으로 사용자 또는 그룹 구성원에게 역할에 대해 적합하지만 비영구적인 액세스 권한을 제공합니다. 
    - **직접 할당:** 사용자 또는 그룹 구성원이 역할 할당을 활성화할 필요가 없습니다(영구적 액세스라고 함). 작업이 완료될 때 액세스할 필요가 없는 대기 중 이동(on-call shift) 또는 시간이 중요한 활동과 같은 단기 사용에 대해 직접 할당을 사용하는 것이 좋습니다.

할당 유형 드롭다운 아래의 확인란을 사용하면 할당이 영구적이어야 하는지(Just-In-Time 할당 활성화가 영구적으로 적합한지/직접 할당이 영구적 활성인지)를 지정할 수 있습니다.

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>다른 관리자가 역할 설정에서 각 할당 유형에 대해 최대 할당 기간을 지정한 경우에는 해당 확인란을 수정할 수 없습니다.

 특정 할당 기간을 지정하려면 확인란의 선택을 취소하고 시작 및/또는 종료 날짜/시간 필드를 수정합니다.

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>역할 할당 관리

관리자는 왼쪽 탐색 영역에서 [역할] 또는 [구성원]을 선택하여 역할 할당을 관리할 수 있습니다. [역할]을 선택하면 관리자가 관리 작업 범위를 특정 역할로 지정할 수 있으며, [구성원]에서는 해당 리소스에 대한 모든 사용자 및 그룹의 역할 할당을 표시합니다.

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
활성화가 보류 중인 역할이 있는 경우 구성원 자격을 볼 때 알림 배너가 페이지 위쪽에 표시됩니다.


## <a name="modify-existing-assignments"></a>기존 할당 수정

사용자/그룹 세부 정보 보기에서 기존 할당을 수정하려면 페이지 위쪽의 작업 모음에서 [설정 변경]을 선택합니다. 할당 유형을 [Just-In-Time 할당] 또는 [직접 할당]으로 변경합니다.

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
