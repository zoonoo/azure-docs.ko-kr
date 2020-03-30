---
title: 역할 기반 액세스 제어를 사용하여 Azure 포털 대시보드 공유
description: 이 문서에서는 역할 기반 Access Control을 사용하여 Azure Portal에서 대시보드를 공유하는 방법을 설명합니다.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 711e8a7ae31888c9754252d88404d90f24e8030e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131990"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>역할 기반 Access Control을 사용하여 Azure 대시보드 공유

대시보드를 구성한 후에는 이를 게시하고 조직 내의 다른 사용자와 공유할 수 있습니다. RBAC(Azure [역할 기반 액세스 제어)를](../role-based-access-control/role-assignments-portal.md) 사용하여 다른 사용자가 대시보드를 볼 수 있도록 허용합니다. 사용자 또는 사용자 그룹을 역할에 할당합니다. 이 역할은 해당 사용자가 게시된 대시보드를 보거나 수정할 수 있는지 여부를 정의합니다.

게시된 모든 대시보드는 Azure 리소스로 구현됩니다. 구독 내에서 관리 가능한 항목으로 존재하며 리소스 그룹에 포함됩니다. 액세스 제어 관점에서 대시보드는 가상 머신 또는 스토리지 계정과 같은 다른 리소스와 차이가 없습니다.

> [!TIP]
> 대시보드의 개별 타일은 표시하는 리소스에 따라 고유한 액세스 제어 요구 사항을 적용합니다. 개별 타일의 데이터를 보호하면서 대시보드를 광범위하게 공유할 수 있습니다.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>대시보드에 대한 액세스 제어 이해

RBAC(역할 기반 Access Control)를 사용하여 세 개의 다른 수준 범위로 역할에 사용자를 할당할 수 있습니다.

* subscription
* 리소스 그룹
* resource

할당한 사용 권한은 구독에서 리소스로 상속됩니다. 게시된 대시보드는 리소스입니다. 게시된 대시보드에 적용되는 구독의 역할에 할당된 사용자가 이미 있을 수 있습니다.

Azure 구독을 보유하고 구독의 *소유자*, *참여자* 또는 *읽기 권한자* 역할에 할당된 팀의 여러 멤버가 있다고 가정해 보겠습니다. 소유자 또는 기여자인 사용자는 구독 내에서 대시보드를 나열, 보기, 생성, 수정 또는 삭제할 수 있습니다. 독자인 사용자는 대시보드를 나열하고 볼 수 있지만 대시보드를 수정하거나 삭제할 수는 없습니다. 리더 액세스 권한이 있는 사용자는 문제 해결 시기와 같이 게시된 대시보드를 로컬로 편집할 수 있지만 이러한 변경 내용을 서버에 다시 게시할 수는 없습니다. 대시보드의 개인 복사본을 만들 수 있습니다.

여러 대시보드가 포함된 리소스 그룹또는 개별 대시보드에 사용 권한을 할당할 수도 있습니다. 예를 들어, 사용자 그룹이 구독에 걸친 제한된 사용 권한을 갖지만 특정 대시보드에 더 큰 액세스 권한을 가져야 한다고 결정할 수 있습니다. 해당 사용자를 해당 대시보드의 역할에 할당합니다.

## <a name="publish-dashboard"></a>대시보드 게시

구독의 사용자 그룹과 공유할 대시보드를 구성한다고 가정해 보겠습니다. 다음 단계에서는 저장소 관리자라는 그룹에 대시보드를 공유하는 방법을 보여 주었습니다. 원하는 대로 그룹의 이름을 지정할 수 있습니다. 자세한 내용은 [Azure Active Directory의 그룹 관리를](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)참조하십시오.

액세스를 할당하기 전에 대시보드를 게시해야 합니다.

1. 대시보드에서 **공유**를 선택합니다.

    ![대시보드에 대한 공유 선택](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. **공유 + 액세스 제어에서** **게시를**선택합니다.

    ![대시보드 게시](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     기본적으로 공유는 대시보드라는 리소스 그룹에 **대시보드를 게시합니다.** 다른 리소스 그룹을 선택하려면 확인란을 선택합니다.

대시보드가 이제 게시되었습니다. 구독에서 상속된 사용 권한이 적합한 경우 더 이상 수행할 필요가 없습니다. 조직의 다른 사용자는 구독 수준 역할에 따라 대시보드에 액세스하고 수정할 수 있습니다.

## <a name="assign-access-to-a-dashboard"></a>대시보드에 액세스 할당

해당 대시보드의 역할에 사용자 그룹을 할당할 수 있습니다.

1. 대시보드를 게시한 후 **공유** 또는 **공유 해제** 옵션을 선택하여 공유 + 액세스 **제어에**액세스합니다.

1. **공유 + 액세스 제어에서**사용자 **관리를**선택합니다.

    ![대시보드에 대한 사용자 관리](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. **역할 할당을** 선택하여 이 대시보드에 대한 역할이 이미 할당된 기존 사용자를 확인합니다.

1. 새 사용자 또는 그룹을 추가하려면 **역할 할당** **추가를** 선택합니다.

    ![대시보드에 액세스할 수 있는 사용자 추가](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. 부여할 권한을 나타내는 역할을 선택합니다. 이 예에서는 **참가자**를 선택합니다.

1. 역할에 할당할 사용자 또는 그룹을 선택합니다. 목록에서 찾고 있는 사용자 나 그룹이 표시되지 않으면 검색 상자를 사용합니다. 사용 가능한 그룹 목록은 Active Directory에서 만든 그룹에 따라 다릅니다.

1. 사용자 또는 그룹 추가가 완료되면 **에 대한 저장을**선택합니다.

## <a name="next-steps"></a>다음 단계

* 역할 목록은 [Azure 리소스에 대한 기본 제공 역할을](../role-based-access-control/built-in-roles.md)참조하십시오.
* 리소스 관리에 대해 자세히 알아보려면 [Azure 포털을 사용하여 Azure 리소스 관리를](resource-group-portal.md)참조하세요.

