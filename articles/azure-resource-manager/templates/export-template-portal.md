---
title: Azure 포털에서 템플릿 내보내기
description: Azure 포털을 사용하여 구독의 리소스에서 Azure 리소스 관리자 템플릿을 내보냅니다.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273736"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Azure 포털의 템플릿으로 단일 및 다중 리소스 내보내기

Azure 리소스 관리자 템플릿을 만드는 데 도움이 있으므로 기존 리소스에서 템플릿을 내보낼 수 있습니다. 내보낸 템플릿은 리소스를 배포하는 JSON 구문 및 속성을 이해하는 데 도움이 됩니다. 향후 배포를 자동화하려면 내보낸 템플릿으로 시작하여 시나리오에 맞게 수정합니다.

리소스 관리자를 사용하면 템플릿으로 내보낼 리소스를 하나 이상 선택할 수 있습니다. 템플릿에 필요한 리소스에 정확히 집중할 수 있습니다.

이 문서에서는 포털을 통해 템플릿을 내보내는 방법을 보여 주며 있습니다. Azure [CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates)Azure [PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)또는 [REST API를](/rest/api/resources/resourcegroups/exporttemplate)사용할 수도 있습니다.

## <a name="choose-the-right-export-option"></a>올바른 내보내기 옵션 선택

두 가지 방법으로 템플릿을 내보낼 수 있습니다.

* **리소스 그룹 또는 리소스에서 내보내기**. 이 옵션은 기존 리소스에서 새 템플릿을 생성합니다. 내보낸 템플릿은 리소스 그룹의 현재 상태의 "스냅샷"입니다. 전체 리소스 그룹 또는 해당 리소스 그룹 내에서 특정 리소스를 내보낼 수 있습니다.

* **배포 전 또는 기록에서 내보내기.** 이 옵션은 배포에 사용되는 템플릿의 정확한 복사본을 검색합니다.

선택한 옵션에 따라 내보낸 템플릿의 품질이 다릅니다.

| 리소스 그룹 또는 리소스에서 | 배포 전 또는 기록에서 |
| --------------------- | ----------------- |
| 템플릿은 리소스의 현재 상태에 대한 스냅샷입니다. 배포 후 수동으로 변경한 내용도 포함됩니다. | 템플릿은 배포 시 리소스의 상태만 표시합니다. 배포 후 변경한 수동 변경 사항은 포함되지 않습니다. |
| 리소스 그룹에서 내보낼 리소스를 선택할 수 있습니다. | 특정 배포에 대한 모든 리소스가 포함됩니다. 이러한 리소스의 하위 집합을 선택하거나 다른 시간에 추가된 리소스를 추가할 수 없습니다. |
| 템플릿에는 배포 중에 일반적으로 설정하지 않는 일부 속성을 포함하여 리소스에 대한 모든 속성이 포함됩니다. 템플릿을 다시 사용하기 전에 이러한 속성을 제거하거나 정리할 수 있습니다. | 템플릿에는 배포에 필요한 속성만 포함됩니다. 템플릿은 바로 사용할 수 있습니다. |
| 템플릿에는 재사용에 필요한 모든 매개 변수가 포함되지 않을 수 있습니다. 대부분의 속성 값은 템플릿에서 하드 코딩됩니다. 다른 환경에서 템플릿을 다시 배포하려면 리소스를 구성하는 기능을 향상시키는 매개 변수를 추가해야 합니다.  고유한 매개변수를 작성할 수 있도록 **매개변수 포함을** 선택 취소할 수 있습니다. | 템플릿에는 다양한 환경에서 쉽게 다시 배포할 수 있는 매개 변수가 포함되어 있습니다. |

리소스 그룹 또는 리소스에서 템플릿을 내보내면 다음과 같은 경우

* 원래 배포 후 만들어진 리소스에 대한 변경 내용을 캡처해야 합니다.
* 내보낼 리소스를 선택하려고 합니다.

배포 전 또는 기록에서 템플릿을 내보내면 다음과 같은 경우

* 재사용이 간편한 템플릿을 원합니다.
* 원래 배포 후 변경한 내용을 포함할 필요가 없습니다.

## <a name="limitations"></a>제한 사항

리소스 그룹 또는 리소스에서 내보낼 때 내보낸 템플릿은 각 리소스 유형에 대해 [게시된 스키마에서](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) 생성됩니다. 경우에 따라 스키마에 리소스 유형에 대한 최신 버전이 없는 경우가 있습니다. 내보낸 템플릿에 필요한 속성이 포함되어 있는지 확인합니다. 필요한 경우 내보낸 템플릿을 편집하여 필요한 API 버전을 사용합니다.

내보내기 템플릿 기능은 Azure 데이터 팩토리 리소스 내보내기를 지원하지 않습니다. 데이터 팩터리 리소스를 내보내는 방법에 대해 자세히 알아보려면 [Azure Data Factory에서 데이터 팩터리 복사 또는 복제를](https://aka.ms/exportTemplateViaAdf)참조하세요.

클래식 배포 모델을 통해 생성된 리소스를 내보내려면 [리소스 관리자 배포 모델로 마이그레이션해야](https://aka.ms/migrateclassicresourcetoarm)합니다.

## <a name="export-template-from-a-resource-group"></a>리소스 그룹에서 템플릿 내보내기

리소스 그룹에서 하나 이상의 리소스를 내보내려면 다음을 수행합니다.

1. 내보낼 리소스가 포함된 리소스 그룹을 선택합니다.

1. 확인란을 선택하여 하나 이상의 리소스를 선택합니다.  모두를 선택하려면 **이름**왼쪽에 있는 확인란을 선택합니다. **템플릿 내보내기** 메뉴 항목은 하나 이상의 리소스를 선택한 후에만 활성화됩니다.

   ![모든 리소스 내보내기](./media/export-template-portal/select-all-resources.png)

    스크린샷에서는 저장소 계정만 선택됩니다.
1. **템플릿 내보내기**를 선택합니다.

1. 내보낸 템플릿이 표시되고 다운로드하여 배포할 수 있습니다.

   ![템플릿 표시](./media/export-template-portal/show-template.png)

   **포함 매개변수는** 기본적으로 선택됩니다.  이 옵션을 선택하면 템플릿이 생성될 때 모든 템플릿 매개변수가 포함됩니다. 사용자 고유의 매개 변수를 작성하려면 이 확인란을 포함하지 않도록 전환합니다.

## <a name="export-template-from-a-resource"></a>리소스에서 템플릿 내보내기

하나의 리소스를 내보내려면 다음을 수행하십시오.

1. 내보낼 리소스가 포함된 리소스 그룹을 선택합니다.

1. 내보낼 리소스를 선택하여 리소스를 엽니다.

1. 해당 리소스의 경우 왼쪽 창에서 **템플릿 내보내기를** 선택합니다.

   ![리소스 내보내기](./media/export-template-portal/export-single-resource.png)

1. 내보낸 템플릿이 표시되고 다운로드하여 배포할 수 있습니다. 템플릿에는 단일 리소스만 포함됩니다. **포함 매개변수는** 기본적으로 선택됩니다.  이 옵션을 선택하면 템플릿이 생성될 때 모든 템플릿 매개변수가 포함됩니다. 사용자 고유의 매개 변수를 작성하려면 이 확인란을 포함하지 않도록 전환합니다.

## <a name="export-template-before-deployment"></a>배포 전에 템플릿 내보내기

1. 배포할 Azure 서비스를 선택합니다.

1. 새 서비스에 대한 값을 입력합니다.

1. 유효성 검사를 통과한 후 배포를 시작하기 전에 **자동화를 위한 템플릿 다운로드를**선택합니다.

   ![템플릿 다운로드](./media/export-template-portal/download-before-deployment.png)

1. 템플릿이 표시되고 다운로드 및 배포할 수 있습니다.


## <a name="export-template-after-deployment"></a>배포 후 템플릿 내보내기

기존 리소스를 배포하는 데 사용된 템플릿을 내보낼 수 있습니다. 얻을 수 있는 템플릿은 배포에 사용된 템플릿입니다.

1. 내보낼 리소스 그룹을 선택합니다.

1. 배포 에서 링크를 **선택합니다.**

   ![배포 기록 선택](./media/export-template-portal/select-deployment-history.png)

1. 배포 기록에서 배포 중 하나를 선택합니다.

   ![배포 선택](./media/export-template-portal/select-details.png)

1. **템플릿**을 선택합니다. 이 배포에 사용되는 템플릿이 표시되고 다운로드할 수 있습니다.

   ![템플릿 선택](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>다음 단계

- [Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates)Azure PowerShell 또는 [REST API를](/rest/api/resources/resourcegroups/exporttemplate)사용하여 [템플릿을](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)내보내는 방법에 대해 알아봅니다.
- 리소스 관리자 템플릿 구문을 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해를](template-syntax.md)참조하십시오.
- 템플릿을 개발하는 방법에 대한 자세한 내용은 [단계별 자습서를](/azure/azure-resource-manager/)참조하십시오.
- Azure 리소스 관리자 템플릿 스키마를 보려면 [템플릿 참조를](/azure/templates/)참조하십시오.
