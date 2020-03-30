---
title: 리소스 그룹 관리 - Azure 포털
description: Azure 포털을 사용하여 Azure 리소스 관리자를 통해 리소스 그룹을 관리합니다. 리소스 그룹을 만들고, 나열하고, 삭제하는 방법을 보여 주면 됩니다.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 8087baf7595bfd2497f97fbff4822b356cd1b146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274568"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 리소스 관리자 리소스 그룹 관리

Azure [리소스 관리자와](overview.md) 함께 [Azure 포털을](https://portal.azure.com) 사용하여 Azure 리소스 그룹을 관리하는 방법을 알아봅니다. Azure 리소스를 관리하기 위해 [Azure 포털을 사용하여 Azure 리소스 관리를](manage-resources-portal.md)참조하십시오.

리소스 그룹 관리에 대한 기타 문서:

- [Azure CLI를 사용하여 Azure 리소스 그룹 관리](manage-resources-cli.md)
- [Azure PowerShell을 사용하여 Azure 리소스 그룹 관리](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>리소스 그룹이란 무엇입니까?

리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다. 일반적으로 쉽게 배포, 업데이트하고 그룹으로 삭제할 수 있도록 동일한 리소스 그룹에 대해 동일한 수명 주기를 공유하는 리소스를 추가합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 따라서 리소스 그룹의 위치를 지정하면 메타데이터가 저장된 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 리소스 그룹의 위치를 지정하면 메타데이터가 저장되는 위치를 지정하게 됩니다.

## <a name="create-resource-groups"></a>리소스 그룹 만들기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. **리소스 그룹** 선택

    ![리소스 그룹 추가](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. **추가**를 선택합니다.
4. 다음 값을 입력합니다.

   - **구독**: Azure 구독을 선택합니다. 
   - **리소스 그룹**: 새 리소스 그룹 이름을 입력합니다. 
   - **지역**: 미국 중부와 같은 Azure 위치를 **선택합니다.**

     ![리소스 그룹 만들기](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. **검토 + 만들기** 선택
6. **만들기**를 선택합니다. 리소스 그룹을 만드는 데 몇 초 정도 걸립니다.
7. 최상위 메뉴에서 **새로 고침을** 선택하여 리소스 그룹 목록을 새로 고치고 새로 만든 리소스 그룹을 선택하여 엽니다. 또는 상단에서 **알림(벨**아이콘)을 선택한 다음 **리소스 그룹으로 이동을** 선택하여 새로 만든 리소스 그룹을 엽니다.

    ![리소스 그룹으로 이동](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>리소스 그룹 나열

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 리소스 그룹을 나열하려면 **리소스 그룹을** 선택합니다.

    ![리소스 그룹 찾아보기](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. 리소스 그룹에 대해 표시되는 정보를 사용자 지정하려면 **열 편집을 선택합니다.** 다음 스크린샷은 디스플레이에 추가할 수 있는 추가 열을 보여 주었습니다.

## <a name="open-resource-groups"></a>리소스 그룹 열기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. **리소스 그룹을**선택합니다.
3. 열려는 리소스 그룹을 선택합니다.

## <a name="delete-resource-groups"></a>리소스 그룹 삭제

1. 삭제할 리소스 그룹을 엽니다.  [리소스 그룹 열기를](#open-resource-groups)참조하십시오.
2. **리소스 그룹 삭제**를 선택합니다.

    ![Azure 리소스 그룹 삭제](./media/manage-resource-groups-portal/delete-group.png)

Azure 리소스 관리자가 리소스 삭제를 명령하는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 리소스 그룹 삭제를](delete-resource-group.md)참조하십시오.

## <a name="deploy-resources-to-a-resource-group"></a>리소스 그룹에 리소스 배포

리소스 관리자 템플릿을 만든 후 Azure 포털을 사용하여 Azure 리소스를 배포할 수 있습니다. 템플릿을 만들려면 [빠른 시작: Azure 포털을 사용하여 Azure 리소스 관리자 템플릿 만들기 및 배포를](../templates/quickstart-create-templates-use-the-portal.md)참조하세요. 포털을 사용하여 템플릿을 배포하는 경우 [리소스 관리자 템플릿 및 Azure 포털을 사용하여 리소스 배포 를 참조하세요.](../templates/deploy-portal.md)

## <a name="move-to-another-resource-group-or-subscription"></a>다른 리소스 그룹 또는 구독으로 이동

그룹의 리소스를 다른 리소스 그룹으로 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

## <a name="lock-resource-groups"></a>리소스 그룹 잠금

잠금을 사용하면 조직의 다른 사용자가 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요한 리소스를 실수로 삭제하거나 수정할 수 없습니다. 

1. 삭제할 리소스 그룹을 엽니다.  [리소스 그룹 열기를](#open-resource-groups)참조하십시오.
2. 왼쪽 창에서 **잠금을**선택합니다.
3. 리소스 그룹에 잠금을 추가하려면 **에 추가를**선택합니다.
4. **잠금 이름,** **잠금 유형**및 **메모를**입력합니다. 잠금 유형에는 **읽기 전용**및 **삭제**가 포함됩니다.

    ![Azure 리소스 그룹 잠금](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

자세한 내용은 [예기치 않은 변경을 방지하려면 리소스 잠금 을](lock-resources.md)참조하십시오.

## <a name="tag-resource-groups"></a>리소스 그룹에 태그

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 자세한 내용은 [태그 사용을 사용하여 Azure 리소스를 구성합니다.](tag-resources.md#portal)

## <a name="export-resource-groups-to-templates"></a>리소스 그룹을 템플릿으로 내보내기

템플릿 내보내기에 대한 자세한 내용은 [템플릿 - 포털로 단일 및 다중 리소스 내보내기를](../templates/export-template-portal.md)참조하십시오.

## <a name="manage-access-to-resource-groups"></a>리소스 그룹에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure 리소스 관리자에 대해 알아보려면 [Azure 리소스 관리자 개요를](overview.md)참조하십시오.
- 리소스 관리자 템플릿 구문을 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해를](../templates/template-syntax.md)참조하십시오.
- 템플릿을 개발하는 방법에 대한 자세한 내용은 [단계별 자습서를](/azure/azure-resource-manager/)참조하십시오.
- Azure 리소스 관리자 템플릿 스키마를 보려면 [템플릿 참조를](/azure/templates/)참조하십시오.