---
title: Azure Portal에서 템플릿 내보내기
description: Azure Portal을 사용하여 구독의 리소스에서 Azure Resource Manager 템플릿을 내보냅니다.
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 59eb3add338e25e3fbd43e3bad5a04d16dcf78b5
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963316"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Azure Portal에서 템플릿으로 단일 리소스 및 다중 리소스 내보내기

Azure Resource Manager 템플릿을 만드는 데 도움이 되도록 기존 리소스에서 템플릿을 내보낼 수 있습니다. 내보낸 템플릿을 사용하면 리소스를 배포하는 JSON 구문 및 속성을 이해할 수 있습니다. 향후 배포를 자동화하려면 내보낸 템플릿부터 시작하여 시나리오에 맞게 수정합니다.

Resource Manager를 사용하면 템플릿으로 내보내기 위해 하나 이상의 리소스를 선택할 수 있습니다. 템플릿에 필요한 리소스에 정확히 집중할 수 있습니다.

이 문서에서는 포털을 통해 템플릿을 내보내는 방법을 보여 줍니다. [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates), [REST API](/rest/api/resources/resourcegroups/exporttemplate)도 사용할 수 있습니다.

## <a name="choose-the-right-export-option"></a>적절한 내보내기 옵션 선택

두 가지 방법으로 템플릿을 내보낼 수 있습니다.

* **리소스 그룹 또는 리소스에서 내보냅니다**. 이 옵션은 기존 리소스에서 새 템플릿을 생성합니다. 내보낸 템플릿은 리소스 그룹의 현재 상태에 대한 "스냅샷"입니다. 전체 리소스 그룹 또는 해당 리소스 그룹 내의 특정 리소스를 내보낼 수 있습니다.

* **배포 전 또는 기록에서 내보냅니다**. 이 옵션은 배포에 사용되는 템플릿의 정확한 복사본을 검색합니다.

선택하는 옵션에 따라 내보낸 템플릿의 품질이 달라집니다.

| 리소스 그룹 또는 리소스에서 | 배포 전 또는 기록에서 |
| --------------------- | ----------------- |
| 템플릿은 리소스의 현재 상태를 보여 주는 스냅샷입니다. 여기에는 배포 후에 수행된 모든 수동 변경 내용이 포함됩니다. | 템플릿은 배포 시 리소스의 상태만 표시합니다. 배포 후에 수행된 수동 변경 내용은 포함되지 않습니다. |
| 리소스 그룹에서 내보낼 리소스를 선택할 수 있습니다. | 특정 배포의 모든 리소스가 포함됩니다. 이 리소스의 하위 세트를 선택하거나 다른 시간에 추가된 리소스를 추가할 수 없습니다. |
| 템플릿에는 배포 중에 일반적으로 설정하지 않는 속성을 포함하여 리소스에 관한 모든 속성이 포함됩니다. 템플릿을 다시 사용하기 전에 해당 속성을 제거하거나 정리하는 것이 좋습니다. | 템플릿에는 배포에 필요한 속성만 포함됩니다. 템플릿을 바로 사용할 수 있습니다. |
| 템플릿에 다시 사용해야 하는 모든 매개 변수가 포함되지 않을 수 있습니다. 대부분의 속성 값은 템플릿에 하드 코딩되어 있습니다. 다른 환경에서 템플릿을 다시 배포하려면 리소스를 구성하는 기능을 개선하는 매개 변수를 추가해야 합니다.  고유 매개 변수를 작성할 수 있도록 **매개 변수 포함** 을 선택 취소할 수 있습니다. | 템플릿에는 다양한 환경에서 쉽게 다시 배포할 수 있는 매개 변수가 포함되어 있습니다. |

다음의 경우 리소스 그룹 또는 리소스에서 템플릿을 내보냅니다.

* 원래 배포 후에 적용된 리소스의 변경 내용을 캡처해야 합니다.
* 내보낼 리소스를 선택합니다.

다음과 같은 경우 배포 전이나 기록에서 템플릿을 내보냅니다.

* 다시 사용하기 쉬운 템플릿이 필요합니다.
* 원래 배포 후에 변경한 내용을 포함할 필요가 없습니다.

## <a name="limitations"></a>제한 사항

리소스 그룹 또는 리소스에서 내보낼 때 내보낸 템플릿은 각 리소스 종류의 [게시된 스키마](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas)에서 생성됩니다. 스키마에 리소스 종류의 최신 버전이 없는 경우도 있습니다. 내보낸 템플릿을 확인하여 필요한 속성이 포함되어 있는지 확인합니다. 필요한 경우, 필요한 API 버전을 사용하도록 내보낸 템플릿을 편집합니다.

템플릿 내보내기 기능은 Azure Data Factory 리소스 내보내기를 지원하지 않습니다. Data Factory 리소스를 내보내는 방법에 관한 자세한 내용은 [Azure Data Factory에서 데이터 팩터리 복사 또는 복제](../../data-factory/copy-clone-data-factory.md)를 참조하세요.

클래식 배포 모델을 통해 만든 리소스를 내보내려면 [Resource Manager 배포 모델로 마이그레이션](../../virtual-machines/migration-classic-resource-manager-overview.md)해야 합니다.

리소스 종류를 내보내지 못했음을 나타내는 템플릿을 내보낼 때 경고가 표시되면 해당 리소스의 속성을 계속 검색할 수 있습니다. 리소스 속성을 볼 수 있는 다양한 옵션에 관한 자세한 내용은 [리소스 속성 검색](view-resources.md)을 참조하세요. 리소스 종류의 [Azure REST API](/rest/api/azure/)도 살펴볼 수 있습니다.

내보낸 템플릿을 만들 리소스 그룹의 리소스는 200개로 제한됩니다. 200개가 넘는 리소스가 있는 리소스 그룹을 내보내려는 경우 `Export template is not supported for resource groups more than 200 resources` 오류 메시지가 표시됩니다.

## <a name="export-template-from-a-resource-group"></a>리소스 그룹에서 템플릿 내보내기

리소스 그룹에서 하나 이상의 리소스를 내보내려면 다음을 수행합니다.

1. 내보낼 리소스를 포함하는 리소스 그룹을 선택합니다.

1. 확인란을 선택하여 하나 이상의 리소스를 선택합니다.  모두 선택하려면 **이름** 왼쪽에 있는 확인란을 선택합니다. **템플릿 내보내기** 메뉴 항목은 하나 이상의 리소스를 선택한 후에만 사용하도록 설정됩니다.

   ![모든 리소스 내보내기](./media/export-template-portal/select-all-resources.png)

    스크린샷에는 스토리지 계정만 선택되어 있습니다.
1. **템플릿 내보내기** 를 선택합니다.

1. 내보낸 템플릿이 표시되며 다운로드하고 배포할 수 있습니다.

   ![템플릿 표시](./media/export-template-portal/show-template.png)

   기본적으로 **매개 변수 포함** 이 선택됩니다.  선택하면 템플릿을 생성할 때 모든 템플릿 매개 변수가 포함됩니다. 고유 매개 변수를 작성하려면 매개 변수를 포함하지 않도록 이 확인란을 토글합니다.

## <a name="export-template-from-a-resource"></a>리소스에서 템플릿 내보내기

한 리소스를 내보내려면 다음을 수행합니다.

1. 내보내려는 리소스를 포함하는 리소스 그룹을 선택합니다.

1. 리소스를 열기 위해 내보낼 리소스를 선택합니다.

1. 해당 리소스에 대해 왼쪽 창에서 **템플릿 내보내기** 를 선택합니다.

   ![리소스 내보내기](./media/export-template-portal/export-single-resource.png)

1. 내보낸 템플릿이 표시되며 다운로드하고 배포할 수 있습니다. 템플릿에는 단일 리소스만 포함됩니다. 기본적으로 **매개 변수 포함** 이 선택됩니다.  선택하면 템플릿을 생성할 때 모든 템플릿 매개 변수가 포함됩니다. 고유 매개 변수를 작성하려면 매개 변수를 포함하지 않도록 이 확인란을 토글합니다.

## <a name="export-template-before-deployment"></a>배포 전 템플릿 내보내기

1. 배포하려는 Azure 서비스를 선택합니다.

1. 새 서비스의 값을 입력합니다.

1. 유효성 검사를 통과한 후 배포를 시작하기 전에 **자동화를 위한 템플릿 다운로드** 를 선택합니다.

   ![템플릿 다운로드](./media/export-template-portal/download-before-deployment.png)

1. 템플릿이 표시되며 다운로드하고 배포할 수 있습니다.


## <a name="export-template-after-deployment"></a>배포 후 템플릿 내보내기

기존 리소스를 배포하는 데 사용된 템플릿을 내보낼 수 있습니다. 배포에 사용된 것과 똑같은 템플릿을 얻을 수 있습니다.

1. 내보내려는 리소스 그룹을 선택합니다.

1. **배포** 에서 링크를 선택합니다.

   ![배포 기록 선택](./media/export-template-portal/select-deployment-history.png)

1. 배포 기록에서 배포 중 하나를 선택합니다.

   ![배포 선택](./media/export-template-portal/select-details.png)

1. **템플릿** 을 선택합니다. 이 배포에 사용되는 템플릿이 표시되며 다운로드할 수 있습니다.

   ![템플릿 선택](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>다음 단계

- [Azure CLI](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates), [REST API](/rest/api/resources/resourcegroups/exporttemplate)를 사용하여 템플릿을 내보내는 방법을 알아봅니다.
- Resource Manager 템플릿 구문에 대해 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
- 템플릿을 개발하는 방법을 알아보려면 [단계별 자습서](../index.yml)를 참조하세요.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조하세요.