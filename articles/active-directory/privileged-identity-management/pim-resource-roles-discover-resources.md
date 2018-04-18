---
title: Azure 리소스에 대한 Privileged Identity Management - Azure 리소스 검색 및 관리 | Microsoft Docs
description: Azure 리소스를 보호하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Azure 리소스 검색 및 관리

조직이 이미 Azure AD PIM을 사용하여 디렉터리에서 관리자를 보호하거나 프로덕션 리소스 보호를 모색하는 구독 소유자인 경우 이 방법을 사용하는 것이 좋습니다.

먼저 Azure 리소스에 대한 PIM을 설정할 때 PIM에서 보호할 리소스를 검색하고 선택해야 합니다. PIM으로 관리할 수 있는 리소스의 수는 제한되지 않지만 가장 중요한(프로덕션) 리소스를 먼저 시작하는 것이 좋습니다.

> [!Note]
> 관리를 위해 구독 리소스만을 검색하고 선택할 수 있습니다. PIM의 구독을 관리하도록 선택하면 모든 자식 리소스를 관리할 수도 있습니다.

## <a name="discover-resources"></a>리소스 검색

Azure AD PIM로 이동하고, 왼쪽 탐색 메뉴의 관리 섹션에서 Azure 리소스를 선택합니다.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

처음으로 Azure 리소스에 대해 PIM을 사용하는 경우 검색을 실행하여 관리할 리소스를 찾아야 합니다.
검색 환경을 시작하려면 화면 중앙에 있는 "리소스 검색" 단추를 클릭합니다.

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

조직에서 다른 리소스나 디렉터리 관리자가 이미 PIM을 사용하여 Azure 리소스를 관리하고 있거나 리소스에 대한 적격 역할 할당이 있는 경우 "리소스를 검색하거나 계속할 적격 역할 할당을 활성화합니다."라는 메시지가 목록 보기에 포함됩니다. 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

작업 모음 또는 리소스를 검색할 화면 가운데에 있는 단추를 선택하면 관리에 사용할 수 있는 구독 목록이 표시됩니다. 이 시점에서 강조 표시된 구독은 PIM에 의해 보호된다는 것을 의미합니다.

> [!Note]
> 다른 리소스 관리자가 PIM 설정을 제거하지 않도록 방지하기 위해 일단 구독을 관리하면 다시 관리하지 않을 수 없습니다.

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

PIM을 사용하여 보호하려는 구독을 마우스로 가리키고, 행의 맨 왼쪽에 있는 상자를 선택합니다. 한 번에 여러 구독을 선택할 수 있습니다.

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

등록 프로세스를 시작하려면 화면 위쪽에 있는 표시줄에 "리소스 관리" 단추를 선택합니다.

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

선택한 리소스는 이제 PIM에 의해 관리됩니다. 페이지의 상단 오른쪽 모서리에 있는 "X"를 사용하여 검색 화면을 닫고, Azure 리소스 관리 화면의 맨 아래에 있는 표시줄에서 새로 고침을 클릭하여 PIM 설정 및 할당 멤버를 시작합니다.

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>다음 단계

[역할 설정 구성](pim-resource-roles-configure-role-settings.md)

[PIM에서 역할 할당](pim-resource-roles-assign-roles.md)
