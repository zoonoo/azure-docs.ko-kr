---
title: 리소스 그룹 관리-Azure Portal
description: Azure Portal를 사용 하 여 Azure Resource Manager를 통해 리소스 그룹을 관리할 수 있습니다. 리소스 그룹을 만들고, 나열 하 고, 삭제 하는 방법을 보여 줍니다.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: feee3a0d6de97ac6aaa1f9a92f20860f3f461ae3
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827234"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Resource Manager 리소스 그룹 관리

[Azure Resource Manager](overview.md) 에서 [Azure Portal](https://portal.azure.com) 를 사용 하 여 Azure 리소스 그룹을 관리 하는 방법을 알아봅니다. Azure 리소스 관리에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 azure 리소스 관리](manage-resources-portal.md)를 참조 하세요.

리소스 그룹 관리에 대 한 기타 문서:

- [Azure CLI를 사용 하 여 Azure 리소스 그룹 관리](manage-resources-cli.md)
- [Azure PowerShell를 사용 하 여 Azure 리소스 그룹 관리](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>리소스 그룹 이란?

리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다. 일반적으로 쉽게 배포, 업데이트하고 그룹으로 삭제할 수 있도록 동일한 리소스 그룹에 대해 동일한 수명 주기를 공유하는 리소스를 추가합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 따라서 리소스 그룹의 위치를 지정하면 메타데이터가 저장된 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.


## <a name="create-resource-groups"></a>리소스 그룹 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 그룹** 선택

    ![리소스 그룹 추가](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. **추가**를 선택합니다.
4. 다음 값을 입력합니다.

   - **구독**: Azure 구독을 선택 합니다. 
   - **리소스 그룹**: 새 리소스 그룹 이름을 입력합니다. 
   - **지역**: **미국 중부**와 같은 Azure 위치를 선택 합니다.

     ![리소스 그룹 만들기](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. **검토 및 만들기**를 선택합니다.
6. **만들기**를 선택합니다. 리소스 그룹을 만드는 데 몇 초 정도 걸립니다.
7. 상단 메뉴에서 **새로 고침** 을 선택 하 여 리소스 그룹 목록을 새로 고친 다음 새로 만든 리소스 그룹을 선택 하 여 엽니다. 또는 위쪽에서 **알림**(벨 아이콘)을 선택 하 고 **리소스 그룹으로 이동** 을 선택 하 여 새로 만든 리소스 그룹을 엽니다.

    ![리소스 그룹으로 이동](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>리소스 그룹 나열

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 리소스 그룹을 나열 하려면 **리소스 그룹** 을 선택 합니다.

    ![리소스 그룹 찾아보기](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. 리소스 그룹에 대해 표시 되는 정보를 사용자 지정 하려면 **열 편집**을 선택 합니다. 다음 스크린샷에서는 표시에 추가할 수 있는 추가 열을 보여 줍니다.

## <a name="open-resource-groups"></a>리소스 그룹 열기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 그룹**을 선택 합니다.
3. 열려는 리소스 그룹을 선택 합니다.

## <a name="delete-resource-groups"></a>리소스 그룹 삭제

1. 삭제 하려는 리소스 그룹을 엽니다.  [리소스 그룹 열기](#open-resource-groups)를 참조 하세요.
2. **리소스 그룹 삭제**를 선택합니다.

    ![azure 리소스 그룹 삭제](./media/manage-resource-groups-portal/delete-group.png)

리소스 삭제 Azure Resource Manager 주문 하는 방법에 대 한 자세한 내용은 [리소스 그룹 삭제 Azure Resource Manager](delete-resource-group.md)를 참조 하세요.

## <a name="deploy-resources-to-a-resource-group"></a>리소스 그룹에 리소스 배포

리소스 관리자 템플릿을 만든 후에는 Azure Portal를 사용 하 여 Azure 리소스를 배포할 수 있습니다. 템플릿을 만들려면 [빠른 시작: Azure Portal를 사용 하 여 Azure Resource Manager 템플릿 만들기 및 배포](../templates/quickstart-create-templates-use-the-portal.md)를 참조 하세요. 포털을 사용 하 여 템플릿을 배포 하는 방법에 대 한 자세한 내용은 [리소스 관리자 템플릿과 Azure Portal 사용 하 여 리소스 배포](../templates/deploy-portal.md)를 참조 하세요.

## <a name="move-to-another-resource-group-or-subscription"></a>다른 리소스 그룹 또는 구독으로 이동

그룹의 리소스를 다른 리소스 그룹으로 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

## <a name="lock-resource-groups"></a>리소스 그룹 잠금

잠금은 조직의 다른 사용자가 실수로 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요 한 리소스를 삭제 하거나 수정 하는 것을 방지 합니다. 

1. 잠글 리소스 그룹을 엽니다.  [리소스 그룹 열기](#open-resource-groups)를 참조 하세요.
2. 왼쪽 창에서 **잠금**을 선택 합니다.
3. 리소스 그룹에 잠금을 추가 하려면 **추가**를 선택 합니다.
4. **잠금 이름**, **잠금 유형**및 **메모**를 입력 합니다. 잠금 유형에는 **읽기**전용 및 **삭제가**포함 됩니다.

    ![azure 리소스 그룹 잠금](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

자세한 내용은 [예기치 않은 변경을 방지 하기 위해 리소스 잠그기](lock-resources.md)를 참조 하세요.

## <a name="tag-resource-groups"></a>리소스 그룹 태그

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 자세한 내용은 [태그를 사용 하 여 Azure 리소스 구성](tag-resources.md#portal)을 참조 하세요.

## <a name="export-resource-groups-to-templates"></a>템플릿으로 리소스 그룹 내보내기

템플릿 내보내기에 대 한 자세한 내용은 [template에 대 한 단일 및 다중 리소스 내보내기-포털](../templates/export-template-portal.md)을 참조 하세요.

## <a name="manage-access-to-resource-groups"></a>리소스 그룹에 대 한 액세스 관리

Azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 는 azure에서 리소스에 대 한 액세스를 관리 하는 방법입니다. 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 대 한 자세한 내용은 [Azure Resource Manager 개요](overview.md)를 참조 하세요.
- 리소스 관리자 템플릿 구문에 대 한 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../templates/template-syntax.md)를 참조 하세요.
- 템플릿을 개발 하는 방법을 알아보려면 단계별 [자습서](../index.yml)를 참조 하세요.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조 하세요.
