---
title: 리소스 관리 - Azure 포털
description: Azure 포털 및 Azure 리소스 관리자를 사용하여 리소스를 관리합니다. 리소스를 배포하고 삭제하는 방법을 보여 주면 됩니다.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248347"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 리소스 관리

Azure [리소스 관리자와](overview.md) 함께 [Azure 포털을](https://portal.azure.com) 사용하여 Azure 리소스를 관리하는 방법을 알아봅니다. 리소스 그룹을 관리하기 위해 [Azure 포털을 사용하여 Azure 리소스 그룹 관리를](manage-resource-groups-portal.md)참조하십시오.

리소스 관리에 대한 기타 문서:

- [Azure CLI를 사용하여 Azure 리소스 관리](manage-resources-cli.md)
- [Azure PowerShell을 사용하여 Azure 리소스 관리](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>리소스 그룹에 리소스 배포

리소스 관리자 템플릿을 만든 후 Azure 포털을 사용하여 Azure 리소스를 배포할 수 있습니다. 템플릿을 만들려면 [빠른 시작: Azure 포털을 사용하여 Azure 리소스 관리자 템플릿 만들기 및 배포를](../templates/quickstart-create-templates-use-the-portal.md)참조하세요. 포털을 사용하여 템플릿을 배포하는 경우 [리소스 관리자 템플릿 및 Azure 포털을 사용하여 리소스 배포 를 참조하세요.](../templates/deploy-portal.md)

## <a name="open-resources"></a>리소스 열기

Azure 리소스는 Azure 서비스 및 리소스 그룹별로 구성됩니다. 다음 절차는 **mystorage0207라는**저장소 계정을 여는 방법을 보여 주며 있습니다. 가상 시스템은 **mystorage0207rg라는**리소스 그룹에 있습니다.

서비스 유형별로 리소스를 여는 경우:

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 왼쪽 창에서 Azure 서비스를 선택합니다. 이 경우 **저장소 계정**.  나열된 서비스가 표시되지 않으면 **모든 서비스를**선택한 다음 서비스 유형을 선택합니다.

    ![포털에서 azure 리소스 열기](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. 열려는 리소스를 선택합니다.

    ![포털에서 azure 리소스 열기](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    저장소 계정은 다음과 같습니다.

    ![포털에서 azure 리소스 열기](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

리소스 그룹별로 리소스를 열려면 다음을 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 왼쪽 창에서 리소스 **그룹을** 선택하여 그룹 내에서 리소스를 나열합니다.
3. 열려는 리소스를 선택합니다. 

## <a name="manage-resources"></a>리소스 관리

포털에서 리소스를 볼 때 특정 리소스 관리 옵션이 표시됩니다.

![Azure 리소스 관리](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

스크린샷에는 Azure 가상 시스템에 대한 관리 옵션이 표시됩니다. 가상 컴퓨터를 시작, 다시 시작 및 중지하는 등의 작업을 수행할 수 있습니다.

## <a name="delete-resources"></a>리소스 삭제

1. 포털에서 리소스를 엽니다. 단계는 리소스 [열기를](#open-resources)참조하십시오.
2. **삭제를 선택합니다.** 다음 스크린샷은 가상 시스템에 대한 관리 옵션을 보여 주었습니다.

    ![Azure 리소스 삭제](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. 삭제를 확인하려면 리소스 이름을 입력한 다음 **삭제를**선택합니다.

Azure 리소스 관리자가 리소스 삭제를 명령하는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 리소스 그룹 삭제를](delete-resource-group.md)참조하십시오.

## <a name="move-resources"></a>리소스 이동

1. 포털에서 리소스를 엽니다. 단계는 리소스 [열기를](#open-resources)참조하십시오.
2. **이동을**선택합니다. 다음 스크린샷은 저장소 계정에 대한 관리 옵션을 보여 주었습니다.

    ![Azure 리소스 이동](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. 필요에 따라 **다른 리소스 그룹으로 이동하거나** **다른 구독으로 이동을** 선택합니다.

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

## <a name="lock-resources"></a>리소스 잠금

잠금을 사용하면 조직의 다른 사용자가 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요한 리소스를 실수로 삭제하거나 수정할 수 없습니다. 

1. 포털에서 리소스를 엽니다. 단계는 리소스 [열기를](#open-resources)참조하십시오.
2. **잠금을**선택합니다. 다음 스크린샷은 저장소 계정에 대한 관리 옵션을 보여 주었습니다.

    ![Azure 리소스 잠금](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. **추가를**선택한 다음 잠금 속성을 지정합니다.

자세한 내용은 [Azure 리소스 관리자를 사용 하 여 리소스 잠금](lock-resources.md)을 참조 합니다.

## <a name="tag-resources"></a>리소스 태그 지정

태그 지정은 리소스 그룹과 리소스를 논리적으로 구성하는 데 도움이 됩니다. 

1. 포털에서 리소스를 엽니다. 단계는 리소스 [열기를](#open-resources)참조하십시오.
2. **태그**를 선택합니다. 다음 스크린샷은 저장소 계정에 대한 관리 옵션을 보여 주었습니다.

    ![Azure 리소스 태그](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. 태그 속성을 지정한 다음 **저장을**선택합니다.

자세한 내용은 [태그 사용을 사용하여 Azure 리소스를 구성합니다.](tag-resources.md#portal)

## <a name="monitor-resources"></a>리소스 모니터링

리소스를 열면 포털에 해당 리소스 유형을 모니터링하기 위한 기본 그래프와 테이블이 표시됩니다. 다음 스크린샷은 가상 시스템에 대한 그래프를 보여 주며,

![Azure 리소스 모니터링](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

그래프의 오른쪽 상단에 있는 핀 아이콘을 선택하여 그래프를 대시보드에 고정할 수 있습니다. 대시보드를 사용하는 방법을 알아보려면 [Azure 포털에서 대시보드 만들기 및 공유](../../azure-portal/azure-portal-dashboards.md)를 참조하세요.

## <a name="manage-access-to-resources"></a>리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure 리소스 관리자에 대해 알아보려면 [Azure 리소스 관리자 개요를](overview.md)참조하십시오.
- 리소스 관리자 템플릿 구문을 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해를](../templates/template-syntax.md)참조하십시오.
- 템플릿을 개발하는 방법에 대한 자세한 내용은 [단계별 자습서를](/azure/azure-resource-manager/)참조하십시오.
- Azure 리소스 관리자 템플릿 스키마를 보려면 [템플릿 참조를](/azure/templates/)참조하십시오.
