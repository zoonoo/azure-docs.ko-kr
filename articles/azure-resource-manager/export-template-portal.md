---
title: Azure portal을 사용 하 여 Azure Resource Manager 템플릿 내보내기
description: 구독의 리소스에서 Azure Resource Manager 템플릿 내보내기에 Azure portal을 사용 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: ea9499da3dac67635a48704f439f6592c6ed467e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515386"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Azure portal에서 템플릿에 단일 및 다중 리소스 내보내기

Azure Resource Manager 템플릿 생성을 지원 하기 위해 기존 리소스에서 템플릿을 내보낼 수 있습니다. 내보낸된 템플릿을 JSON 구문 및 리소스를 배포 하는 속성을 이해 하도록 도와줍니다. 향후 배포를 자동화 하려면 내보낸된 템플릿을 사용 하 여 시작 하 고 시나리오에 대 한 수정 합니다.

Resource Manager를 사용 하면을 템플릿으로 내보내기에 대 한 하나 이상의 리소스를 선택할 수 있습니다. 정확 하 게 필요한 리소스를 템플릿에 집중할 수 있습니다.

## <a name="choose-the-right-export-option"></a>오른쪽 내보내기 옵션을 선택 합니다.

두 가지 방법으로 템플릿을 내보낼 수 있습니다.

* **리소스 그룹 또는 리소스에서 내보낸**합니다. 이 옵션에는 기존 리소스에서 새 템플릿을 생성합니다. 내보낸된 템플릿은 리소스 그룹의 현재 상태 "스냅숏"입니다. 전체 리소스 그룹 또는 리소스 그룹 내 특정 리소스를 내보낼 수 있습니다.

* **기록에서 배포 전이나 내보내기**합니다. 이 옵션에는 배포에 사용 되는 템플릿의 정확한 복사본을 검색 합니다.

선택한 옵션에 따라 내보낸된 템플릿을 다른 품질 있어야 합니다.

| 리소스 그룹 또는 리소스에서 | 배포 전 또는 기록에서 |
| --------------------- | ----------------- |
| 서식 파일 스냅숏 리소스의 현재 상태입니다. 수동 배포 후 변경한 내용이 포함 됩니다. | 템플릿 배포 시 리소스 상태를 보여 줍니다. 모든 수동 변경한 후 배포는 포함 되지 않습니다. |
| 내보낼 리소스 그룹에서 리소스를 선택할 수 있습니다. | 특정 배포에 대 한 모든 리소스가 포함 되어 있습니다. 이러한 리소스의 하위 집합을 선택 하거나 다른 시간에 추가 된 리소스를 추가할 수 없습니다. |
| 템플릿을 배포 하는 동안 일반적으로 설정 하지 않습니다. 일부 속성을 포함 하 여 리소스에 대 한 모든 속성을 포함 합니다. 제거 하거나 템플릿을 다시 사용 하기 전에 이러한 속성을 정리 수 있습니다. | 템플릿은 배포에 필요한 속성만 포함 합니다. 템플릿이 사용 준비 됩니다. |
| 템플릿 아마도 포함 하지 않습니다 모든 다시 사용 하기 위해 필요한 매개 변수. 대부분의 속성 값 서식 파일에 하드 코드 됩니다. 다른 환경에서 서식 파일을 다시 배포 하려면 리소스를 구성 하는 기능을 증가 하는 매개 변수를 추가 해야 합니다. | 템플릿은 다양 한 환경에서 쉽게 다시 배포할 수 있도록 하는 매개 변수를 포함 합니다. |

리소스 그룹 또는 리소스에서 템플릿을 내보낼 때:

* 원래 배포 후 수행한 리소스에 대 한 변경 내용을 캡처하고 해야 합니다.
* 내보낸 리소스를 선택 해야 합니다.

배포 전 또는 기록에서 템플릿을 내보낼 때:

* 재사용을 쉽게 템플릿을 해야합니다.
* 원래 배포 후 변경한 내용을 포함 하지 않아도 됩니다.

## <a name="export-template-from-resource-group"></a>리소스 그룹에서 템플릿 내보내기

리소스 그룹에서 하나 이상의 리소스를 내보내려면:

1. 내보낼 리소스를 포함 하는 리소스 그룹을 선택 합니다.

1. 리소스 그룹의 모든 리소스를 내보내려면 모두 선택 차례로 **템플릿 내보내기**합니다. 합니다 **템플릿 내보내기** 옵션만 가능 리소스가 하나 이상 선택한 후 합니다.

   ![모든 리소스 내보내기](./media/export-template-portal/select-all-resources.png)

1. 내보내기에 대 한 특정 리소스를 선택 하려면 해당 리소스 옆에 있는 확인란을 선택 합니다. 그런 다음 선택 **템플릿 내보내기**합니다.

   ![내보낼 리소스 선택](./media/export-template-portal/select-resources.png)

1. 내보낸된 템플릿에 표시 되 고 다운로드할 수 있습니다.

   ![템플릿 표시](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>리소스에서 템플릿 내보내기

하나의 리소스를 내보내려면:

1. 내보낼 리소스를 포함 하는 리소스 그룹을 선택 합니다.

1. 내보낼 리소스를 선택 합니다.

   ![리소스 선택](./media/export-template-portal/select-link-resource.png)

1. 해당 리소스에 대 한 선택 **템플릿 내보내기** 왼쪽된 창에서.

   ![리소스 내보내기](./media/export-template-portal/export-single-resource.png)

1. 내보낸된 템플릿에 표시 되 고 다운로드할 수 있습니다. 단일 리소스 템플릿을 포함합니다.

## <a name="export-template-before-deployment"></a>배포 하기 전에 템플릿 내보내기

1. 배포 하려는 Azure 서비스를 선택 합니다.

1. 새 서비스에 대 한 값을 입력 합니다.

1. 유효성 검사를 전달한 후 있지만 배포를 시작 하기 전에 선택 **자동화에 대 한 템플릿을 다운로드**합니다.

   ![템플릿 다운로드](./media/export-template-portal/download-before-deployment.png)

1. 템플릿에 표시 되 고 다운로드할 수 있습니다.

   ![템플릿 표시](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>배포 후 템플릿 내보내기

기존 리소스를 배포 하는 데 사용 된 템플릿을 내보낼 수 있습니다. 표시 템플릿은 배포에 사용 된 것과 정확히입니다.

1. 내보내려는 리소스 그룹을 선택 합니다.

1. 아래의 링크를 선택 **배포**합니다.

   ![배포 기록 선택](./media/export-template-portal/select-deployment-history.png)

1. 배포 기록에서 배포 중 하나를 선택 합니다.

   ![배포를 선택 합니다.](./media/export-template-portal/select-details.png)

1. 선택 **템플릿**합니다. 이 배포에 사용 된 템플릿을 표시 되 면 및 다운로드할 수 있습니다.

   ![템플릿 선택](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 알아보려면 [Azure Resource Manager 개요](./resource-group-overview.md)합니다.
- Resource Manager 템플릿 구문에 알아보려면 [구조 및 Azure Resource Manager 템플릿의 구문 이해](./resource-group-authoring-templates.md)합니다.
- 템플릿을 개발 하는 방법에 알아보려면 참조를 [단계별 자습서](/azure/azure-resource-manager/)합니다.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)합니다.