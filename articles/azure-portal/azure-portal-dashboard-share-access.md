---
title: 역할 기반 Access Control를 사용 하 여 Azure Portal 대시보드 공유
description: 이 문서에서는 역할 기반 Access Control을 사용하여 Azure Portal에서 대시보드를 공유하는 방법을 설명합니다.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 4eef5a9e3f010e19871471d007ff2a0cc24d3834
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461381"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>역할 기반 Access Control을 사용하여 Azure 대시보드 공유

대시보드를 구성한 후에는 이를 게시하고 조직 내의 다른 사용자와 공유할 수 있습니다. Azure RBAC ( [역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md) )를 사용 하 여 다른 사용자가 대시보드를 볼 수 있도록 허용 합니다. 사용자 또는 사용자 그룹을 역할에 할당 합니다. 해당 역할은 해당 사용자가 게시 된 대시보드를 보거나 수정할 수 있는지 여부를 정의 합니다.

모든 게시 된 대시보드는 Azure 리소스로 구현 됩니다. 이러한 항목은 구독 내에서 관리 가능한 항목으로 존재 하며 리소스 그룹에 포함 되어 있습니다. 액세스 제어 관점에서 대시보드는 가상 머신 또는 스토리지 계정과 같은 다른 리소스와 차이가 없습니다.

> [!TIP]
> 대시보드의 개별 타일은 표시하는 리소스에 따라 고유한 액세스 제어 요구 사항을 적용합니다. 개별 타일에서 데이터를 보호 하면서 대시보드를 광범위 하 게 공유할 수 있습니다.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>대시보드에 대한 액세스 제어 이해

RBAC(역할 기반 Access Control)를 사용하여 세 개의 다른 수준 범위로 역할에 사용자를 할당할 수 있습니다.

* subscription
* 리소스 그룹
* 리소스

할당 하는 권한은 구독에서 리소스에 대해 상속 됩니다. 게시된 대시보드는 리소스입니다. 게시 된 대시보드에 적용 되는 구독에 대 한 역할에 할당 된 사용자가 이미 있을 수 있습니다.

Azure 구독을 보유하고 구독의 *소유자*, *참여자* 또는 *읽기 권한자* 역할에 할당된 팀의 여러 멤버가 있다고 가정해 보겠습니다. 소유자 또는 참여자 인 사용자는 구독 내에서 대시보드를 나열, 보기, 만들기, 수정 또는 삭제할 수 있습니다. 읽기 권한자 인 사용자는 대시보드를 나열 및 볼 수 있지만 수정 하거나 삭제할 수는 없습니다. 판독기 액세스 권한이 있는 사용자는 문제를 해결 하는 경우와 같이 게시 된 대시보드에 대 한 로컬 편집을 수행할 수 있지만 이러한 변경 내용을 서버에 다시 게시할 수는 없습니다. 대시보드는 자신을 위한 전용 대시보드 복사본을 만들 수 있습니다.

여러 대시보드 또는 개별 대시보드를 포함 하는 리소스 그룹에 권한을 할당할 수도 있습니다. 예를 들어, 사용자 그룹이 구독에 걸친 제한된 사용 권한을 갖지만 특정 대시보드에 더 큰 액세스 권한을 가져야 한다고 결정할 수 있습니다. 해당 대시보드에 대 한 역할에 해당 사용자를 할당 합니다.

## <a name="publish-dashboard"></a>대시보드 게시

구독에서 사용자 그룹과 공유 하려는 대시보드를 구성 한다고 가정해 보겠습니다. 다음 단계에서는 저장소 관리자 라는 그룹에 대시보드를 공유 하는 방법을 보여 줍니다. 원하는 대로 그룹의 이름을 지정할 수 있습니다. 자세한 내용은 [Azure Active Directory에서 그룹 관리](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조 하세요.

액세스를 할당하기 전에 대시보드를 게시해야 합니다.

1. 대시보드에서 **공유**를 선택합니다.

    ![대시보드에 대해 공유를 선택 합니다.](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. **공유 + access control**에서 **게시**를 선택 합니다.

    ![대시보드 게시](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     기본적으로 공유는 대시보드를 **대시보드**라는 리소스 그룹에 게시 합니다. 다른 리소스 그룹을 선택 하려면 확인란의 선택을 취소 합니다.

대시보드가 이제 게시되었습니다. 구독에서 상속 된 권한이 적절 한 경우 더 이상 작업을 수행할 필요가 없습니다. 조직의 다른 사용자는 구독 수준 역할에 따라 대시보드에 액세스 하 고 수정할 수 있습니다.

## <a name="assign-access-to-a-dashboard"></a>대시보드에 액세스 할당

해당 대시보드에 대 한 역할에 사용자 그룹을 할당할 수 있습니다.

1. 대시보드를 게시 한 후 **공유 또는 공유** 해제 **옵션을** 선택 하 여 **공유 + 액세스 제어**에 액세스 합니다.

1. **공유 + 액세스 제어**에서 **사용자 관리**를 선택 합니다.

    ![대시보드에 대 한 사용자 관리](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. **역할 할당** 을 선택 하 여이 대시보드의 역할이 이미 할당 된 기존 사용자를 확인 합니다.

1. 새 사용자 또는 그룹을 추가 하려면 **추가** 를 선택 하 고 **역할 할당 추가**를 선택 합니다.

    ![대시보드에 액세스 하기 위한 사용자 추가](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. 부여할 권한을 나타내는 역할을 선택 합니다. 이 예에서는 **참가자**를 선택합니다.

1. 역할에 할당할 사용자 또는 그룹을 선택 합니다. 목록에서 찾으려는 사용자나 그룹이 표시 되지 않는 경우 검색 상자를 사용 합니다. 사용 가능한 그룹 목록은 Active Directory에서 만든 그룹에 따라 달라 집니다.

1. 사용자 또는 그룹을 추가 하는 작업이 완료 되 면 **저장**을 선택 합니다.

## <a name="next-steps"></a>다음 단계

* 역할 목록은 [Azure 리소스에 대 한 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조 하세요.
* 리소스를 관리 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 Azure 리소스 관리](resource-group-portal.md)를 참조 하세요.

